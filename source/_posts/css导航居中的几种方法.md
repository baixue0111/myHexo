---
title: css导航菜单水平居中的多种方法
date: 2017-12-23
tags: [Css]
categories:
    - Css
---
在网页设计中，水平导航菜单使用十分广泛，在Css样式中，我们一般会用**display: inline-block**来解决。但是如何碰到未知宽度的元素居中呢？下列会列出几种方法来解决水平居中问题，当然这些方法不一定只用解决导航居中的，其他类似情况也可以使用。
![](/images/cssCenter.jpg)
<!-- more -->
### Css导航水平居中的多种方法：
#### 方法一：display: inline-block
这个属性主要针对其父级容器，给最外层父级容器设置**display:inline-block**行内块元素，然后给最近父元素设置**text-align:center**使其达到水平居中的效果。   
``` html
<div class="box">
    <ul class="parent">
        <li><span>导航1</span></li>
        <li><span>导航2</span></li>
        <li><span>导航3</span></li>
        ...
    </ul>
</div>
```
Css代码：
``` Css
.box{
    display: inline-block;
}
.parent{
    text-align: center;
}
.parent li{
    float: left;
    margin: 10px 20px;
}
```
#### 方法二：position: relative 
使用定位方法是元素水平居中，这个方法不推荐使用，因为外层还得多包涵一层div。
``` html
<div class="navbar">
    <div>
        <ul>
            <li><span>导航1</span></li>
            <li><span>导航2</span></li>
            <li><span>导航3</span></li>
            ...
        </ul>
    </div>
</div>

```
Css代码：
``` Css
.navbar {
    overflow:hidden;
}
.navbar > div {
    position:relative;
    left:50%;
    float:left;
}
.navbar ul {
    position:relative;
    left:-50%;
    float:left;
}
.navbar li {
    float:left;
}
.navbar li + li {
    margin-left:20px;
}
```
#### 方法三： display: table
``` html
<ul class="navbar">
    <li><a href="/">Home</a></li>
    …
</ul>
```
Css代码：
``` Css
.navbar {
    display:table;
    margin:0 auto;
}
.navbar li {
    display:table-cell;
}
.navbar li + li {
    padding-left:20px;
}
```
#### 方法四：display:inline-flex
``` html
<div class="navbar">
    <ul>
        <li><a href="/">Home</a></li>
        …
    </ul>
</div>
```
Css代码：
``` Css
.navbar {
    text-align:center;
}
.navbar > ul {
    display:-webkit-inline-box;
    display:-moz-inline-box;
    display:-ms-inline-flexbox;
    display:-webkit-inline-flex;
    display:inline-flex;
}
.navbar li + li {
    margin-left:20px;
}
```
浏览器兼容： 不支持IE7及以下版本的浏览器。
#### 方法五： width:fit-content
``` html
<div class="navbar">
    <ul>
        <li><a href="/">首页</a></li>
        …
    </ul>
</div>
```
Css代码：
``` Css
.navbar {
    text-align:center;
}
.navbar > ul {
    display:-webkit-inline-box;
    display:-moz-inline-box;
    display:-ms-inline-flexbox;
    display:-webkit-inline-flex;
    display:inline-flex;
}
.navbar li + li {
    margin-left:20px;
}
```
浏览器兼容：这个兼容比较低，只支持Firefox或chrome、Opera 12这些较新的浏览器。