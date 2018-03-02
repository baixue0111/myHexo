---
title: JS 对象转换赋值
date: 2018-03-02
tags: [JavaScript]
categories: 
        - JavaScript
---

如果复制了一个对象，并修改对象里某个值且保持原始对象的值不变时，可以使用对象转换赋值方法进行操作。如：
``` JavaScript

var _obj = new Object();

_obj = {
    "name": "baixue",
    "id": 12,
    "website": "http://baidu.com"
}

var newObj = _obj;

console.log(_obj); //{"name": "baixue","id": 12,"website": "http://baidu.com"}
console.log(newObj); //{"name": "baixue","id": 12,"website": "http://baidu.com"}

newObj.id = 22;

console.log(_obj); //{"name": "baixue","id": 22,"website": "http://baidu.com"}
console.log(newObj); //{"name": "baixue","id": 22,"website": "http://baidu.com"}
```
<!-- more -->
**我只修改了newObj的值，而_obj的值也变了，这是因为newObj复制了_obj的值此时_obj和newObj同时指向了一个地址，所以改变其中任意一个对象的值另一个都会变，那该如何处理可以保持原始对象值不变呢，请继续看下方示例。**

还是上面的例子，我们修改一下：
``` JavaScript
var _obj = new Object();

_obj = {
    "name": "baixue",
    "id": 12,
    "website": "http://baidu.com"
}

_obj = JSON.stringify(_obj);
var newObj = JSON.parse(_obj);

// console.log(_obj);   // string 类型 {"name":"baixue","id":12,"website":"http://baidu.com"}  
// console.log(newObj);  // Object 类型{"name":"baixue","id":12,"website":"http://baidu.com"}

newObj.id = 35;

console.log(_obj);   // string 类型 {"name":"baixue","id":12,"website":"http://baidu.com"}  
console.log(newObj);  // Object 类型{"name":"baixue","id":35,"website":"http://baidu.com"}
```