---
layout: post
title: JS语言内功
description: "整理一些难懂的、零散的、对比性的JS语言知识"
tags: [javascript]
modified: 2017-06-26
image:
  feature: abstract-12.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
comments: true
share: true
---

我在重新学习JS语言基础的时候又发现一些概念模糊的地方，在此记录一下我的总结以及引发的思考。

### 函数作用域

在一些类似 C 语言的编程语言中，花括号内的每一段代码都具有各自的作用域，而且变量在声明它们的代码段之外是不可见的，我们称为块级作用域（block scope），而 JavaScript 中没有块级作用域。JavaScript 取而代之地使用了函数作用域（function scope），变量在声明它们的函数体以及这个函数体嵌套的任意函数体内都是有定义的。比如：

```javascript
function test() {
    for (var k = 0; k < 10; k++) { // k在函数体内是有定义的，不仅仅是在循环内
        console.log(k); 
    }
    console.log(k); // 输出10
}
```
<!--more-->

这跟我们学过的其他的编程语言不太一样，在java中，块级作用域中定义的变量（这里的变量k）只能在块中（这里的for循环体中）使用。
因为javascript中变量声明提前的特性，所以变量在声明之前已经是可用的。
由于 JavaScript 没有块级作用域，因此一些程序员特意将变量声明放在函数体顶部，而不是将声明靠近放在使用变量之处。这种做法使得他们的源代码非常清晰地反映了真实的变量作用域。

### 执行上下文

执行上下文又叫执行上下文环境、运行期上下文。
函数每被调用一次，都会产生一个新的执行上下文环境。因为不同的调用可能就会有不同的参数。
给执行上下文环境下一个通俗的定义——在执行代码之前，把将要用到的所有的变量都事先拿出来，有的直接赋值了，有的（只是指普通变量）先用undefined占个空。

全局代码的上下文环境数据内容为：
普通变量（包括函数表达式），如： var a = 10; | 声明（默认赋值为undefined）
----|------
函数声明，如： function fn() { } | 赋值
this | 赋值

如果代码段是函数体，那么在此基础上需要附加：
参数 | 赋值
----|------
arguments | 赋值
自由变量的取值作用域 | 赋值

执行上下文栈：处于活动状态的执行上下文环境只有一个。当函数执行完毕，执行上下文被销毁。

### 作用域和执行上下文区别

作用域只是一个“地盘”，一个抽象的概念。函数在定义的时候（不是调用的时候），就已经确定了函数体内部自由变量的作用域。
自由变量如何取值？要到创建这个函数的那个作用域中取值——是“创建”，而不是“调用”，切记切记——其实这就是所谓的“静态作用域”。
作用域中变量的值是在执行过程中确定的，而作用域却是在函数创建时就确定了。
如果要查找一个作用域下某个变量的值，就需要找到这个作用域对应的执行上下文环境（可能有多个值，因为可能有多个调用），再在其中寻找变量的值。

代码执行过程中的作用域和上下文环境结合请看：[【作用域】和【上下文环境】](http://www.cnblogs.com/wangfupeng1988/p/3991995.html)


### 作用域链和执行上下文

每一个Javascript函数都被表示为对象，它是一个函数实例。它包含我们编程定义的可访问属性，和一系列不能被程序访问，仅供Javascript引擎使用的内部属性，其中一个内部属性是[[Scope]]，由ECMA-262标准第三版定义。
内部[[Scope]]属性包含一个函数被创建的作用域中对象的集合。此集合被称为函数的作用域链，它决定哪些数据可以由函数访问。此函数中作用域链中每个对象被称为一个可变对象，以“键值对”表示。当一个函数创建以后，它的作用域链被填充以对象，这些对象代表创建此函数的环境中可访问的数据。

一个执行上下文有自己的作用域链，用于解析标识符。

当代码在一个环境中执行时，会创建变量对象的一个**作用域链**（scope chain）。作用域链的用途，是保证对执行环境有权访问的所有变量和函数的有序访问。作用域链的前端，始终都是当前执行的代码所在环境的变量对象。如果这个环境是函数，则将其**活动对象**（activation object）作为变量对象。活动对象在最开始时只包含一个变量，即 `arguments` 对象（这个对象在全局环境中是不存在的）。作用域链中的下一个变量对象来自包含（外部）环境，而再下一个变量对象则来自下一个包含环境。这样，一直延续到全局执行环境；全局执行环境的变量对象始终都是作用域链中的最后一个对象。

标识符解析是沿着作用域链一级一级地搜索标识符的过程。搜索过程始终从作用域链的前端开始，然后逐级地向后回溯，直至找到标识符为止（如果找不到标识符，通常会导致错误发生）。


### JS面试题

下面是在博客园看到的几道经典的JS面试题，有助于理解JS基础知识。

```javascript
function Foo() {
    getName = function () { alert (1); };
    return this;
}
Foo.getName = function () { alert (2);};
Foo.prototype.getName = function () { alert (3);};
var getName = function () { alert (4);};
function getName() { alert (5);}

//请写出以下输出结果：
Foo.getName();
getName();
Foo().getName();
getName();
new Foo.getName();
new Foo().getName();
new new Foo().getName();
```

原文链接

[一道常被人轻视的前端JS面试题](http://www.cnblogs.com/xxcanghai/p/5189353.html)

```javascript
function fun(n,o) {
  console.log(o)
  return {
    fun:function(m){
      return fun(m,n);
    }
  };
}
var a = fun(0);  a.fun(1);  a.fun(2);  a.fun(3);//undefined,?,?,?
var b = fun(0).fun(1).fun(2).fun(3);//undefined,?,?,?
var c = fun(0).fun(1);  c.fun(2);  c.fun(3);//undefined,?,?,?
//问:三行a,b,c的输出分别是什么？
```

原文链接

[大部分人都会做错的经典JS闭包面试题](http://www.cnblogs.com/xxcanghai/p/4991870.html)

