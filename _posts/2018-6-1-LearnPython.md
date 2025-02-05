---
layout: post
title: 'Python 入门'
tags: [Python, promgramming]
comments: true
---

Python 入门

- [HelloWorld](#HelloWorld)
- [Function](#Function)
- [结构特性](#结构特性)
- [高阶函数](#高阶函数)
- [模块](#模块)
- [访问和限制](#访问和限制)
- [高级 OOP](#高级OOP)
- [异常处理](#异常处理)
- [IO](#IO)
- [JSON](#JSON)

[[Top]](#top)

# <a name="HelloWorld"></a>HelloWorld

## 1.1 简单的输入输出

```python
print('hello, world')

name = input()
```

python 是不需要要类型声明的。
</br></br>

## 1.2 编码

要把 bytes 变为 str，就需要用<b> decode() </b>方法  
以 Unicode 表示的 str 通过<b> encode() </b>方法
</br></br>

## 1.3 基本数据结构

### # List -> 有序表

可以使用下标，可变，[方括号]

```python
classmates = ['Michael', 'Bob', 'Tracy']

classmates.append('Adam') # 追加
classmates.insert(1, 'Jack')
classmates.pop()   # 删除末尾
classmates[1] = 'Sarah'
```

</br>

### # tuple -> 元组

定长，不可修改，(圆括号)

```python
classmates = ('Michael', 'Bob', 'Tracy')

>>> t = ('a', 'b', ['A', 'B'])
>>> t[2][0] = 'X'
>>> t[2][1] = 'Y'
>>> t
('a', 'b', ['X', 'Y'])
```

</br>

### # dict -> 字典,MAP

Key：Value，{花括号}

```python
d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
d['Adam'] = 67  # 设定value
'Thomas' in d  # 是否存在
d.get('Thomas')
d.pop('Bob')    # delete Key with Value
```

</br>

### # set

一组 Key，不存储 Value，Key 不重复  
要创建一个 set，需要提供一个 list 作为输入集合

```python
s = set([1, 1, 2, 2, 3, 3，4])
s.remove(4)
s
->{1, 2, 3}
```

</br></br>

## 1.4 基本逻辑条件

```python
# if
age = 20
if age >= 6:
    print('teenager')
elif age >= 18:
    print('adult')
else:
    print('kid')


#  循环
n = 1
while n <= 100:
    if n > 10:
        break
    print(n)
    n = n + 1
print('END')
```

[[Top]](#top)

# <a name="Function"></a>Function

## # 定义函数

```python
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
```

[[Top]](#top)

# <a name="结构特性"></a>结构特性

## 3.1 切片

```python
 L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']

 L[0:3] # 0开始，3结束
 L[-2:] # 取倒数第二个
 L[:]   # 复制一个list

'ABCDEFG'[:3]
->'ABC'
'ABCDEFG'[::2]
->'ACEG'
```

## 3.2 迭代

循环遍历，python 的 for 有更高级的抽象

```python
isinstance('abc', Iterable) # str是否可迭代
for x, y in [(1, 1), (2, 4), (3, 9)]:
    print(x, y)
```

## 3.3 列表生成器 range()

生成[1x1, 2x2, 3x3, ..., 10x10]

```python
 L = []
for x in range(1, 11):
    L.append(x * x)

-> L
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

# 另一种写法
[x * x for x in range(1, 11)]
# 还可以加条件
[x * x for x in range(1, 11) if x % 2 == 0]
# 甚至嵌套
[m + n for m in 'ABC' for n in 'XYZ']
```

## 3.4 生成器 generator

如果 list 受内存限制，我们可以不必存储他
generator 可以断推算出 list  
只要把一个列表生成式的[]改成()，就创建了一个 generator  
next()函数获得 generator 的下一个返回值

```python
g = (x * x for x in range(10))
    for n in g:
        print(n)

# 关键字 yield 函数与generator ...略
```

[[Top]](#top)

# <a name="高阶函数"></a>高阶函数

函数可以被传入

```python
f=abs  # 不要括号

# 神奇把...
def add(x, y, f):
    return f(x) + f(y)
```

## 4.1 Map 不四 dic 那个 map 呀

map()函数接收两个参数，一个是函数，一个是 Iterable

```python
def f(x):
        return x * x

r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
list(r)
-> [1, 4, 9, 16, 25, 36, 49, 64, 81]

# 把这个list所有数字转为字符串
list(map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
```

## 4.2 reduce 累计运算

```python
def add(x, y):
        return x + y

reduce(add, [1, 3, 5, 7, 9])
-> 25

# 把str转换为int的函数,把序列[1, 3, 5, 7, 9]变换成整数13579
DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}

# s='13579'
def str2int(s):
    def fn(x, y):
        return x * 10 + y
    def char2num(s):
        return DIGITS[s]
    return reduce(fn, map(char2num, s))

-> 13579
```

## 4.3 sorted 排序

```python
# 反向，忽略大小写
sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reverse=True)
```

## 4.4 匿名函数

lambda x: x \* x 实际上就是：

```python
def f(x):
    return x * x
```

## 4.5 装饰器 decorator

代码运行期间动态增加功能的方式

```python
def log(func):   # 打印日至
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper

# 这样
@log
def now():
    print('2015-3-25')
# 或者这样
now = log(now)

```

## 4.6 偏函数

把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数

```python
int('12345', base=8)
-> 5349

int2 = functools.partial(int, base=2)
int2('1000000')

-> 64
```

[[Top]](#top)

# <a name="模块"></a>模块

## 5.1 使用模块

    import sys

# OOP

## 6.1 类和实例

```python
class Student(object):

    def __init__(self, name, score):  #构造函数?
        self.name = name
        self.score = score

# -----
 bart = Student('Bart Simpson', 59)
```

[[Top]](#top)

# <a name="访问和限制"></a>访问和限制

```python
class Student(object):

    def __init__(self, name, score):
        self.__name = name     #注意这个下划线，私有
        self.__score = score

    def get_name(self):
        return self.__name

    def get_score(self):
        return self.__score
    def set_score(self, score):
        self.__score = score
```

## 6.3 继承和多台

```python
class Animal(object):
    def run(self):
        print('Animal is running...')

class Dog(Animal):
    def run(self):
        print('Dog is running...')

    def eat(self):
        print('Eating meat...')

class Cat(Animal):
    pass


# 鸭子特性，不必继承Animal，但又run方法，那么他看起来像只鸭子他就是鸭子
class Timer(object):
    def run(self):
        print('Start...')
```

## 6.4 获取类型

```python
type(123)
-> <class 'int'>

a = Animal()
d = Dog()
h = Husky()

isinstance(h, Husky)
-> True
```

[[Top]](#top)

# <a name="高级OOP"></a>高级 OOP

## 7.1**slots**

我们可以给该实例绑定任何属性和方法，这就是动态语言的灵活性。

```python
s = Student()
s.name = 'Michael' # 动态给实例绑定一个属性

# ------
def set_age(self, age): # 定义一个函数作为实例方法
        self.age = age

from types import MethodType
s.set_age = MethodType(set_age, s) # 给实例绑定一个方法
```

但是，给一个实例绑定的方法，对另一个实例是不起作用的  
为了给所有实例都绑定方法，可以给 class 绑定方法：

```python
Student.set_score = set_score
```

限制实例的属性,即用**slots**

```python
class Student(object):
    __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称
```

## 7.2 @property

把一个方法变成属性调用

```python
class Student(object):

    @property    # getter
    def score(self):
        return self._score

    @score.setter
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value

# ----
s = Student()
s.score = 60 # OK，实际转化为s.set_score(60)
s.score # OK，实际转化为s.get_score()
-> 60

s.score = 9999
-> Traceback (most recent call last):
->  ...
-> ValueError: score must between 0 ~ 100!
```

## 7.3 多重继承

    class Dog(Mammal, Runnable):
        pass

## 7.4 定制类

```python
# __str__ = toString()

# __iter__= 可被循环的
def __iter__(self):
        return self # 实例本身就是迭代对象，故返回自己

# __call__ ...梅高东有啥用
```

## 7.4 枚举 Enum

```python
Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))
```

[[Top]](#top)

# <a name="异常处理"></a>异常处理

try...catch...finally  
断言：  
 assert n != 0, 'n is zero!'

[[Top]](#top)

# <a name="IO"></a>IO

## 文件读写

```python
f=open('/Users/michael/notfound.txt', 'r',encoding='gbk', errors='ignore')
f.read()
f.close()

# or

with open('/path/to/file', 'r') as f:
    print(f.read())


with open('/Users/michael/test.txt', 'w') as f:
    f.write('Hello, world!')
```

## 序列化 pickle

```python
d = dict(name='Bob', age=20, score=88)
pickle.dumps(d)

# 反序列化
pickle.loads()
```

[[Top]](#top)

# <a name="JSON"></a>JSON

```python
json.dumps(d)
json.loads(json_str)

# JSON一个类时，我们需要写一个方法
def student2dict(std):
    return {
        'name': std.name,
        'age': std.age,
        'score': std.score
    }
# -----
json.dumps(s, default=student2dict)


# 反序列化
def dict2student(d):
    return Student(d['name'], d['age'], d['score'])

json.loads(json_str, object_hook=dict2student)
```
