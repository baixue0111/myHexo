---
title: JavaScript之bind
date: 2017-12-11
tags: [JavaScript]
categories:
        - JavaScript
---
## bind是什么？
bind()方法创建一个新的函数，当被调用时，将其this关键字设置为提供的值，在调用新函数时，在任何提供之前提供一个给定的参数序列。
``` JavaScript
var result = fun.bind(thisArg[, arg1[, arg2[, ...]]])
result(newArg1, newArg2...)
```
没看懂没事，请接着往下看
## bind到底做了什么？
从上面的介绍中可以看出三点：首先调用bind方法会返回一个新的函数（这个新的函数的函数体应该和fun是一样的）。同时bind中传递两个参数，第一个是this的指向，即传入了什么this就等于什么。如下代码所示：
``` JavaScript
this.value = 2;
var foo = {
    value: 1
};
var bar = function() {
    console.log(this.value);
};
var result = bar.bind(foo);
bar();  // 2, 即this === window
result();  // 1, 即this === foo
```
第二个参数为一个序列，你可以传递任意数量的参数到其中。并且会预置到新函数参数之前。
<!--more-->
``` JavaScript
this.value = 2;
var foo = {
    value: 1
};
var bar = function(name, age, school) {
    console.log(name);  // An
    console.log(age);   // 22
    console.log(school);  // 家里蹲大学
}
var result = bar.bind(foo, 'An');  // 预置了部分参数'An'
result(22, '家里蹲大学');  // 这个参数会和预置的参数合并到一起放入bar中
```
我们可以看出在最后调用result(22, '家里蹲大学')的时候，其内部已经包含了在调用bind的时候传入的'An'。一句话总结：调用bind，就会返回一个新的函数。这个函数里面的this就指向bind的第一个参数，同时this后面的参数会提前传给这个新的函数。调用该新的函数时，在传递的参数会放到预置的参数后，一起传递进新函数。
## 自己实现一个bind
### 实现一个bind需要实现以下两个功能：
* 返回一个函数，绑定this，传递预置参数
* bind返回的函数可以作为构造函数使用。故作为构造函数时应使得this失效，但是传入的参数依然有效
#### 返回一个函数，绑定this,传递预置参数
``` JavaScript
this.value = 2;
var foo = {
    value: 1
};
var bar = function(name, age, school) {
    console.log(name);  // An
    console.log(age);   // 22
    console.log(school);  // 家里蹲大学
    console.log(this.value);  // 1
};
Function.prototype.bind = function(newThis) {
    var aArgs = Array.prototype.slice.call(arguments, 1);  // 拿到除了newThis之外的预置参数序列
    var that = this;
    return function() {
        // 绑定this同时将调用时传递的序列和预置序列进行合并
        return that.apply(newThis, aArgs, concat(Array.prototype.slice.call(arguments)));
    }
}
var result = bar.bind(foo, 'An');
result(22, '家里蹲大学');
```
这里面有一个细节就是Array.prototype.slice.call(arguments, 1)这句话，我们知道arguments这个变量可以拿到函数调用时传递的参数，但不是一个数组，但是其具有一个length属性。为什么这样调用就可以将其转变为数组了呢？那么我们就需要回到V8的源码来进行分析。**这个版本的源码为早期的版本，内容相对少一些。**
``` javaScript
function ArraySlice(start, end) {
    var len = ToUint32(this.length);
    // 需要传递this指向对象，那么call(arguments)，
    // 便可将this绑定到arguments，拿到其length属性。
    var start_i = TO_INTEGER(start);
    var end_i = len;
    if(end !== void 0) end_i = TO_INTEGER(end);

    if(start_i < 0) {
        start_i += len;
        if(start_i < 0) start_i = 0;
    } else {
        if(start_i > len) start_i = len;
    }

    if(end_ < 0) {
        end_i += len;
        if(end_i < 0) end_i = 0;
    } else {
        if(end_i > len) end_i = len;
    }

    var result = [];
    if(end_ < start_i)
        return result;
    
    if(IS_ARRAY(this))
        SmartSlice(this, start_i, end_i - start_i, len, result);
    else 
        SimpleSlice(this, start_i, end_i - start_i, len, result);
    
    result.length = end_i - start_i;

    return result;
}
```
从源码中可以看到通过call将arguments下的length属性赋给slice后，便可通过start_i & end_i来获得最后的数组，所以不需要传递进slice时就是一个纯数组最后也可以得到一个数组变量。
#### bind返回的函数可以作为构造函数使用
被用作构造函数时，this应指向new出来的实例，同时有prototype属性，其指定实例的原型。
``` JavaScript
this.value = 2;
var foo = {
    value: 1
};
var bar = function(name, age, school) {
    ...
    console.log('this.value', this.value);
};
Function.prototype.bind = function(newThis) {
    var aArgs = Array.prototype.slice.call(arguments, 1);
    var that = this;  // that 始终指向bar
    var NoFunc = function() {}
    var resultFunc = function() {
        return that.apple(this instanceof that ? this : newThis, aArgs.concat(Array.prototype.slice.call(arguments)))
    }
    NoFunc.prototype = that.prototype;  // that指向bar
    resultFunc.prototype = new NoFunc()
    return resultFunc
}
var result = bar.bind(foo, 'An');
result.prototype.name = 'Lsc';  // 有prototype属性
var person = new result(22, '家里蹲大学');
console.log('person', person.name);  // 'Lsc'
```
上面这段模板代码做了两件重要的事：
1、给返回的函数模拟一个prototype属性。因为通过构造函数new出来的实例可以查询到原型上定义的属性和方法
``` JavaScript
var NoFunc = function() {}
...
NoFunc.prototype = that.prototype;  // that指向bar
resultFunc.prototype = new NoFunc();
return resultFunc;
```
通过上面代码可以看出，that始终指向bar。同时返回的函数已经继承了that.prototype即bar.prototype。为什么不直接让返回的函数的prototype属性resultFunc.prototype等于为bar(that).prototype呢？这是因为任何new出来的实例都可以访问原型链。如果直接赋值那么new出来的对象可以直接修改bar函数的原型链，这也就是原型链污染。所以我们采用继承的方式（将构造函数的原型链赋值为父级构造函数的实例），让new出来的对象的原型链与bar脱离关系。
2、判断当前被调用时，this是用于普通的bind还是用于构造函数从而更改this指向。如何判断当前的this指向哪里呢，通过这一点我们已经知道，通过bind方法返回的新函数已经有了原型链，剩下需要我们做的就是改变this的指向就可以模拟完成了。通过什么来判断当前被调用是以何种姿势呢，答案是**instanceof**。
``` JavaScript
instanceof // 运算符用来测试一个对象在其原型链中是否存在一个构造函数的prototype属性。
// 定义构造函数
function C() {}
function D() {}
var o = new C();
o instanceOf C;   // true, 因为Object.getPrototypeOf(o) === C.prototype;
o instanceOf D;  // fals，因为D.prototype不在o的原型链上
```
从上面可以看出，instanceof可以判断出是否是由这个函数new出来的，如果是new出来的，那么对这个对象的原型链应为该函数的prototype。所以我们来看这段关键的返回的函数结构。
``` JavaScript
var resultFunc = function() {
    return that.applt(this instanceof that ? this ：newThis, aArgs.concat(Array.prototype.slice.call(arguments)))
}
```
在这其中我们要先人清this instanceof that中的this是bind函数被调用后，返回的新函数中的this。所以这个this可能执行在普通的作用域环境，同时也可能被new一下从而改变自己的指向。再看that，that始终指向了bar，同时其原型链that.prototype是一直存在的。所以如果现在这个新函数就要做new操作，那么this指向了新函数，那么this instanceof that === true, 所以在apply中传入this为指向，即指向新函数。如果是普通调用，那么this不是被new出来的，即新函数不是作为构造函数，this instanceof that === false就很显而易见了。这个时候是正常的bind调用。将调用的第一个参数作为this的指向即可。
### 完整代码(MDN下的实现)
``` JavaScript
if (!Function.prototype.bind) {
  Function.prototype.bind = function(oThis) {
    if (typeof this !== 'function') {
      // closest thing possible to the ECMAScript 5
      // internal IsCallable function
      throw new TypeError('Function.prototype.bind - what is trying to be bound is not callable');
    }
 
    var aArgs   = Array.prototype.slice.call(arguments, 1),
        fToBind = this,
        fNOP    = function() {},
        fBound  = function() {
          return fToBind.apply(this instanceof fNOP
                 ? this
                 : oThis,
                 aArgs.concat(Array.prototype.slice.call(arguments)));
        };
 
    if (this.prototype) {
      // Function.prototype doesn't have a prototype property
      fNOP.prototype = this.prototype; 
    }
    fBound.prototype = new fNOP();
    return fBound;
  };
}
```
可以看到，其首先做了当前是否支持bind的绑定，不支持在实行兼容。同时判断调用这个方法的对象是否是个函数，如果不是则报错。同时这个模拟的方法也有一些缺陷，可关注MDN上的Polyfill部分。
## 小结
模拟bind实现最大的一个缺陷是，模拟出来的函数中会一直存在prototype属性，但是原生的bind作为构造函数是没有prototype的，这点打印一下即可。不过这样子new出来的实例没有原型链。
原文链接：[https://github.com/Aaaaaaaty/Blog/issues/1]