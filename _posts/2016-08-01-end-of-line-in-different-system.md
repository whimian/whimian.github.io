---
layout: post
title: 不同系统的endOfLine问题
date: 2016-08-01 16:20
categories: basic
---
最近由于要参加老师要求参加的中海油举办的 $\pi-Frame$ 软件编程比赛，准备复习一下C++，结果在使用`cin.getline()`接受输入时发现得到的`string`长度有问题，导致比较的时候不能得到预期结果。。。

![打字机](https://gitee.com/whimian/images/raw/master/green_typewriter.jpg
)

经过一番纠结，结果发现原来还是end of line问题。如果看过老式打印机就会知道，打字的时候想要换行要进行两个操作，一个是LF（line feed），就是拧旋钮把纸张向上移动一行；另一个是CR（carriage return），就是板一下把手把打字的那个头移动到一行的最前面。在ASCII码中，LF对应着10，而CR对应着13，一般用字符表示成`'\n'`和`'\r'`。

Dex | Char | str
---|---|---
10 | LF | '\n'
13 | CR | '\r'


由于历史原因，不同系统在处理字符行结尾的时候会加不同的字符。

Windows | Unix-like | Old Mac(pre-OSX)
---|---|---
'\n\r' | '\n' | '\r'

因此，文本文件在不同系统下创建和使用时就会出现不兼容的问题。

我的问题是，我在Windows上使用Cygwin，将gcc作为编译器，在输入字符时，接受的字符串是以`'\n\r'`结尾的，而`getline()`则以`'\n'`结尾的方式对其进行处理。

解决的办法是在`getline()`中特别指定结束符，`getline(cin, text, '\r')`，可是这么写的代码在别的情形下貌似会出问题。期待有更加完美的解决方法。
