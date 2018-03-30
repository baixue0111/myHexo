---
title: 网页飘窗-demo
date: 2018-02-13
tags: [JavaScript]
categories: 
        - JavaScript
---
## 网页飘窗
html代码如下：
``` html
<a id="float" href="http://www.baidu.com" target="_blank" style="width: 160px;height: 160px;background: #acf;position: fixed;z-index: 999; text-align: center;line-height: 160px;">飘窗测试</a></div>
```
js代码如下：
``` JavaSCript
!function () {
    var winWidth = window.innerWidth;                   
    var winHeight = window.innerHeight;
    var t;
    window.onresize = function(){
        winWidth = window.innerWidth;                   
        winHeight = window.innerHeight;
    }
    var $left = 0,$top = 0,a = 1,b = 1,speed = 2;
    function goFun(posit,speed){
        return posit + speed;
    }
    var t = setInterval(function(){
        $left = goFun($left,speed*a)
        if( $left >= (winWidth-$dom.offsetWidth)){
            a = -1;
        }else if($left <= 0){
            a = 1;
        }
        $top = goFun($top,speed*b)
        if( $top >= (winHeight-$dom.offsetHeight)){
            b = -1;
        }else if($top <= 0){
            b = 1;
        }
        $dom.style.cssText += "left:"+$left+"px;top:"+$top+"px";
    },20)

    function $(id) {
        return document.getElementById(id);
    }
    
    // 鼠标移进飘窗停止
    $("float").onmouseover = function () {
        clearInterval(t);
    }
    // 鼠标移出飘窗开始
    $("float").onmouseout = function () {
        t = setInterval(function(){
            $left = goFun($left,speed*a)
            if( $left >= (winWidth-$dom.offsetWidth)){
                a = -1;
            }else if($left <= 0){
                a = 1;
            }
            $top = goFun($top,speed*b)
            if( $top >= (winHeight-$dom.offsetHeight)){
                b = -1;
            }else if($top <= 0){
                b = 1;
            }
            $dom.style.cssText += "left:"+$left+"px;top:"+$top+"px";
        },20)
    }
}(document.getElementById('float'));    // 飘窗id

```