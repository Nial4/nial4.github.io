---
layout: post
title: 'Golang Receiver'
tags: [golang, promgramming]
comments: true
---

刚刚用一个类似JSP的Go的前端模版Pongo2时，发现了个小bug，才知道Golang中关于 类的方法，  
可以定义在 值接收器（value receiver） 上，也可以定义在 指针接收器（pointer receiver） 上。  

```golang
func (o Object) String() string {
func (o *Object) String2() string {
```

之前一直没有注意这种微小的区别，Go的编译器
```golang
var o Object = XXX
o.String2() // 即使String2()定义在指针上，Go也会自动转为(&o).String2()
```

值接收器既可以通过值调用，也可以通过指针调用。  
指针接收器只能通过指针调用（值调用会自动转为指针，但这仅在直接调用时有效，反射/模板中不会自动转换），所以我的前端模版就出bug了。  
