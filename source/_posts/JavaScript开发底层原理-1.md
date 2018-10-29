---
title: JavaScript开发底层原理-Call Stack(1)
date: 2018-10-27 11:27:20
tags:
- node, javaScript底层
categories: [javascript, node.js]
---

## Call Stack

#### 调用栈是解析器(如浏览器中的的javascript解析器)的一种机制，可以在脚本调用多个函数时，跟踪每个函数在完成执行时应该返回控制的点。（如什么函数正在执行，什么函数被这个函数调用，下一个调用的函数是谁）

> * 当脚本要调用一个函数时，解析器把该函数添加到栈中并且执行这个函数
> * 任何被这个函数调用的函数会进一步添加到调用栈中，并且运行到它们被上个程序调用的位置。
> * 当函数运行结束后，解释器将它从堆栈中取出，并在主代码列表中继续执行代码。
> * 如果栈占用的空间比分配给它的空间还大，那么则会导致“栈溢出”错误。

```javascript
    function greeting() {
        sayHi();
    }

    function sayHi() {
        return "Hi!";
    }

    // 调用 `greeting` 方法
    greeting();

```

#### 执行过程

* 1.忽略所有的方法, 直到到达 greeting()方法.
* 2.调用 greeting() 方法.
* 3.把 `greeting` 方法加入调用栈列表。

```javascript
    调用栈列表:
    - greeting
```


* 4.执行 `greeting` 方法中的所有代码行。
* 5.到达 sayHi() 方法。
* 6.把 sayHi() 方法加入调用栈列表。

```javascript
    调用栈列表:
    - greeting
    - sayHi
```

* 7.执行 sayHi() 函数中的所有代码行，直到结束。
* 8.将执行返回到调用 sayHi() 的行，并继续执行 greeting() 函数的其余部分。
* 9.把 sayHi() 方法从调用栈列表中删除。

```javascript
    调用栈列表:
    - greeting
```

* 10.当 greeting() 函数中的所有内容都执行完之后，返回到它的调用行继续执行其余的JS代码。
* 11.把 greeting() 方法从调用栈列表中删除。

```javascript
    调用栈列表:
    空
```
     
###### 我们从一个空的调用栈开始，当我们调用一个函数时，它会自动添加到调用栈中，在执行完所有代码之后，它会自动从调用栈中删除。最后，我们也得到了一个空栈。