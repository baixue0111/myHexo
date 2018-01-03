---
title: undefined和null的区别
date: 2017-11-10
tags: [JavaScript]
categories: 
        - JavaScript
---
### 一、undefined和null地相似性：
在JavaScript中，将一个变量赋值undefined或null，几乎没什么区别。
``` JavaScript
var a = undefined;
var a = null;
```
上面的代码中，a变量分别被赋值为undefined和null，这两种写法几乎是等价。undefined和null在if语句中，都会被自动转为false，相等运算符甚至直接报告两者相等。
``` JavaScript
if (!undefined) {
    console.log('undefined is false');  // undefined is false
}

if (!null) {
    console.log('null is false');  // null is undefined
}

undefined == null;  // true
```
上面的代码说明，两者的行为是何等相似！
既然undefined和null的含义与用法都差不多，为什么要同时设置两个这样的值呢？
### 二、历史原因？
这与JavaScript的历史有关。1995年[JavaScript诞生](http://www.ruanyifeng.com/blog/2011/06/birth_of_JavaScript.html)时，最初像java一样，只设置了null作为表示“无”的值。根据C语言的传统，null被设计成可以自动转为0；
``` JavaScript
Number(null);  // 0
console.log(5 + null);   // 5
```
<!--more-->
但是JavaScript的设计者Brendan Eich，觉得这样做还不够，有两个原因。
**首先**，null像在java里一样，被当成一个对象，但是JavaScript的数据类型分成原始类型和合成类型两大类，Brendan Eich觉得表示“无”的值最好不是对象。
**其次**JavaScript的最初版本没有包括错误处理机制，发生数据类型不匹配时，往往是自动转换类型或者默认失败。Brendan Eich觉得，如果null自动转转为0，很不容易发生错误。因此又设计了一个undefined。
### 三、最初设计
JavaScript的最初版本是这样区分的：**null是一个表示“无”的对象，转为数值是0；undefined是一个表示“无”的原始值，转为数值为NaN。**
``` JavaScript
Number(undefined);  // NaN
console.log(5 + NaN);  // NaN
```
### 四、目前的用法
上面这样的区分，在实践中很快就被证明不行。目前，null和undefined基本都是同义的，只有一些细微的差别。**null表示“没有对象”，即该处不应该有值**。典型用法是：
* 作为函数的参数，表示该函数的参数不是对象。
* 作为对象原型链的终点。
``` JavaScript
Object.getPrototypeOf(Object.prototype);  // null
```
**undefined表示“缺少值”，就是此处应该有一个值，但是还没有定义。**典型用法是：
* 变量被声明了，但没有赋值，就等于undefined。
* 调用该函数时，应该提供的参数没有提供，该参数等于undefined。
* 对象没有赋值的属性，该属性的值为undefined。
* 函数没有返回值时，默认返回undefined。
``` JavaScript
var i;   // i  undefined
function f(x) {
    console.log(x);
}
f();  // undefined
var o = new Object();
o.p  // undefined
var x = f();
x;  // undefined
```
原文链接:
[http://www.ruanyifeng.com/blog/2014/03/undefined-vs-null.html](http://www.ruanyifeng.com/blog/2014/03/undefined-vs-null.html)

