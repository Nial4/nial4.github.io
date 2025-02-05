---
layout: post
title: 'Java 反射是怎么一回事'
tags: [Java, promgramming]
comments: true
---

Q:反射有什么用?  
A:我们可以动态的选择我们需要的类型。

Q:为什么这样说呢?  
A:在我们遇到项目时，我们需要使用另一个类，然而在程序里 new 的对象是写死的，我们需要修改代码重写 new 一个新的 编译才能运行，这样不是很麻烦吗?  
我们可以新建一个配置文件，里面记录需要的类包，让 main 去读这个文件，通过反射获得实例。<del>其实我自己也是懵的，到底有啥用啊</del>  
A:能不用就不用...

```java
//获取类对象
    Class stu2 = stu1.getClass();
    Class stu2 = stu1.class;
    Class stu2 = Class.forName("fanshe.Student");
    Student.TYPE;


//所有"公有的"构造方法
public Constructor[] getConstructors();
//获取所有的构造方法(包括私有、受保护、默认、公有)
public Constructor[] getDeclaredConstructors();
//调用
con.newInstance()


//获取所有的"公有字段"
Field[] getFields();
//获取所有字段
Field[] getDeclaredFields();
//设置字段
Object obj = stuClass.getConstructor().newInstance();
Field strName = stuClass.getField("name");
strName.set(obj,"Kivior")


//获取成员公有方法
public Method[] getMethods();
//所有方法
public Method[] getDeclaredMethods();
//调用方法
Object obj = stu1.getConstructor().newInstance();//实例化一个Student对象
Method m = stu1.getMethod("show", String.class);
m.invoke(obj, "Show,Kivior");


```
