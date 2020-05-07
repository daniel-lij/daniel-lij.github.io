---
title: call, apply和bind的实现
date: 2018-11-29 15:40:16
tags:
- node, javaScript底层
categories: [javascript, node.js]
---


## call与apply

#### call与apply方法都是使用一个指定的this值和对应的参数前提下调用某个函数和方法。区别在于call是通过传多个参数的方式,而apply则是传入一个数组。

```javascript

var obj = {
  name: 'linxin'
}

function func(age, sex) {
  console.log(this.name,age,sex);
}

func.call(obj,12,'女');         // linxin 12 女
func.apply(obj, [18, '女']);        //linxin 18 女

```


### 模拟实现

#### (简单实现)思路: 在javascript中的this指向说到了:函数还可以作为某个对象的方法调用,这时this就指这个上级对象.也就是我们平时说的，谁调用，this指向谁。所以实现的方法就是在传入的对象中添加这么一个方法，然后再去执行这个方法。为了保持对象一致，在执行完之后再把这个对象删除。

```javascript

Function.prototype.newCall = function(context) {
  context.fn = this;  // 通过this获取call的函数
  context.fn();
  delete context.fn;
}
let foo = {
  value: 1
}
function bar() {
  console.log(this.value);
}
bar.newCall (foo);

```

#### (eval版本,传入参数)思路: 我们可以进行优化一下, 因为传入的参数数量是不确定的，我们可以从Arguments对象去获取，这个比较简单。问题是参数是不确定的，我们可以通过eval拼接或者es6


##### eval版本
```javascript

Function.prototype.newCall = function(context) {
  context.fn = this;  // 通过this获取call的函数
  var args = []
  for(var i = 1, len = arguments.length; i < len; i++){
      args.push('arguments[' + i + ']')
  }
  eval('context.fn(' + args + ')')
  delete context.fn;
}
let person = {
  name: 'Abiel'
}
function sayHi(age,sex) {
  console.log(this.name, age, sex);
}
sayHi.newCall (person, 25, '男'); // Abiel 25 男

```

##### es6版本

```javascript

Function.prototype.newCall = function(context, ...parameter) {
  context.fn = this; 
  context.fn(...parameter)
  delete context.fn;
}
let person = {
  name: 'Abiel'
}
function sayHi(age,sex) {
  console.log(this.name, age, sex);
}
sayHi.newCall (person, 25, '男'); // Abiel 25 男

```

##### 改进版本

```javascript

Function.prototype.newCall = function(context, ...parameter) {
    if(typeof context === 'object'){
        context = context || window
    }else{
        context = Object.create(null)
    }
    let fn = Symbol()
    context[fn] = this
    context[fn](...parameter)
    delete context.[fn]
}
let person = {
  name: 'Abiel'
}
function sayHi(age,sex) {
  console.log(this.name, age, sex);
}
sayHi.newCall (person, 25, '男'); // Abiel 25 男

```

## bind的实现

#### bind也是函数的方法,作用也是改变this的执行, 同时也是能传多个参数, 与call和apply不同的是bind方法不会立即执行，而是会返回一个改变上下文this指向后的函数，原函数并没有被改变。并且如果函数本身是一个绑定了this对象的函数，那么apply和call不会立即执行。

```javascript

    Function.prototype.bind = function (context,...innerArgs) {
        var me = this
        return function (...finnalyArgs) {
            return me.call(context,...innerArgs,...finnalyArgs)
        }
    }
    let person = {
        name: 'Abiel'
    }
    function sayHi(age,sex) {
        console.log(this.name, age, sex)
    }
    let personSayHi = sayHi.bind(person, 25)
    personSayHi('男')

```