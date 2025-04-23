---
layout: post
title: '从NullString看Golang 哲学'
tags: [golang, promgramming]
comments: true
---

起因是最近写业务，用到了golang sql库的NullString，虽然以前也不是没用过，但没有仔细研究过这个东西。今天上班比较闲就打开瞅了瞅发现还挺有趣的，做个记录。

Go语言 NullString 到反射的设计哲学

## 理解 database/sql 背后的思考
### 1. 为什么有 NullString？
在 SQL 语言里，NULL 代表「不存在」，
但 Go 里的 string 不能表达「不存在」，只能是 ""（空字符串）。

所以 Go 标准库 database/sql 提供了 sql.NullString 结构体，
专门用来区分：

- 是 NULL？ → Valid == false
- 是 ""？ → Valid == true 且 String == ""

```golang
type NullString struct {
    String string
    Valid  bool // true 代表这个值有效（非 NULL）
}
```
这是一种非常 简单、显式、零额外开销 的设计。



### 2. NullString.Scan 的核心逻辑
Scan 是数据库标准接口 sql.Scanner 的一部分，
它的作用是：把数据库里的一个字段（可能是 NULL）安全地复制到 NullString 里。

```golang
func (ns *NullString) Scan(value any) error {
    if value == nil {
        ns.String, ns.Valid = "", false // 数据库是 NULL
        return nil
    }
    ns.Valid = true
    return convertAssign(&ns.String, value)
}
```

核心点：
- 判断 value == nil 来区分是否是 SQL NULL。
- 非 NULL 时，调用 convertAssign 把数据库值转（字符串，整数，甚至是Time类型）换成 string。

### 3.走进 convertAssign —— 为什么不用反射？
把数据库值转（字符串，整数，甚至是Time类型）换成 string的源码（convertAssignRows）：
```golang
switch s := src.(type) {
case string:
    switch d := dest.(type) {
    case *string:
        if d == nil {
            return errNilPtr
        }
        *d = s
        return nil
    case *[]byte:
        if d == nil {
            return errNilPtr
        }
        *d = []byte(s)
        return nil
    }
....

case time.Time:
    switch d := dest.(type) {
    case *time.Time:
        *d = s
        return nil
    case *string:
        *d = s.Format(time.RFC3339Nano)
        return nil

...
}

```
- 完全是 type switch，没有用 reflect。
- 转换逻辑是 编译期就固定的，不会在运行时动态推断类型。

### 4. 为什么不直接用 reflect？
反射的劣势：
- 每次都要解析 reflect.Type → 慢
- 每次都要动态判断 Kind → 慢
- 每次设置都要 Value.Set → 慢
- 无法编译器优化（无法 inline、无法 escape analysis）

Go 哲学：  
  
「编译期就能确定的，就不留到运行时。」  
  
所以，基础库（database/sql）避免反射，  
用 type switch 这种低成本、直接的写法。  

### 5. 那 ORM（如 GORM, sqlx）为什么要用反射？
ORM 的目标是：
  
「你写个 struct，我帮你自动填字段，省事！」
  
但 struct 字段名、tag、类型顺序都不一定对得上，  
必须要运行时动态解析： 
```golang
t := reflect.TypeOf(dest).Elem()
v := reflect.ValueOf(dest).Elem()

for i := 0; i < t.NumField(); i++ {
    field := t.Field(i)
    tag := field.Tag.Get("db")
    v.Field(i).Set(...)
}
```
这就是为什么 ORM 必须用反射，但 database/sql 不用。
database/sql 中，类型映射方式全靠手动 Scan，靠 type switch，快！

### 6. 反射为什么慢？
这个问题稍微有些复杂和偏题，就至少为总结一下：  
  
- TypeOf：每次解析类型元数据表  
- ValueOf：包装类型和值的组合  
- Kind 判断：运行时判断，不能优化  
- Set 操作：每次都要检查类型一致性  
- 无法内联优化：编译器不能省略函数调用开销  
- 可能引入堆分配：为了通用性，很多时候必须分配堆内存  

实际上在benchmark中，反射赋值可能会比普通赋值慢上个百倍。

### 7.题外话
其实sql库的convertAssignRows方法中，最后还是用了反射的：
前面不用反射的原因是：
- 数据库返回的类型是有限的（string, []byte, time.Time, nil, 等）。
- *string, *int, *[]byte 这些接收类型也有限。
- 这些类型之间的转换用 type switch 快速匹配，性能非常好（编译器优化、跳表查找）。
    
大部分实际场景（90% 以上）会在前面的 switch 就 return 了。

最后还是用反射兜底:
Go 的 Scan 接口必须支持：
- 自定义类型（用户定义了 type MyInt int）。
- sql.Scanner 接口类型（比如 NullString）。
用户可能传进任意 struct、指针、alias。
- type switch 写不完所有组合（*MyInt、*MyFloat、*UserDefinedDecimal……）。

例子：
```golang
type MyInt int64

var myval MyInt
row.Scan(&myval)
```
数据库可能返回 int64，但是：  
type switch 里没写 *MyInt。  
反射能发现：  
- MyInt 是 int64 的 alias。  
- 类型 ConvertibleTo。    

如果不兜底反射，这种场景就会 panic 或 unsupported。

#### 所以
Go 的 database/sql 并不是完全反对反射，而是用一种「最小必要原则」去使用它。
