---
layout: post
title: 'xBindContacts'
tags: [UWP, promgramming]
comments: true
---

DataBound 动态数据绑定

需要有人告诉 GridView 数据更新了 >.<

```cpp
 //private List<Contact> Contacts;
 private ObservableCollection<Contact> Contacts;

ItemsSource="{x:Bind Contacts}  //ちょっと　UPDATE


   //Contacts = new List<Contact>();
            Contacts = new ObservableCollection<Contact>();

```
