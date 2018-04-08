---
title: cookie、localStorage和sessionStorage区别
date: 2018-03-13
tags: [JavaScript, ES6]
categories: 
        - JavaScript
---
## 什么是cookie？
cookie是用于访问者的计算机中的变量。每当同一台计算机通过浏览器请求某个页面时，就会发送这个cookie。可以通过JavaScript代码创建和取回cookie的值。
``` JavaScript
document.cookie = "_name = baixue";
document.cookie = 'str =' + escape(' i love JavaScript ');   // 当使用escape编码之后，取值之后需要使用unescape()进行解码才能得到原来的cookie值

```
如果要删除cookie就设置过期时间为一个过去的时间。
### cookie最典型的应用是？
1. 判断用户是否登录过网站，以便下次登录时能够直接登录。如果删除cookie，则每次登录必须从新填写登录信息。
2. 另一个重要的应用是“购物车”中的处理和设计。用户可能在一段时间内在同一家网站的不同页面选择不同商品，可以将这些信息都写入cookie，在最后付款时从cookie中提取这些信息，当然这里面有了安全和性能问题我们需要考虑了。

## session概念
session是存在服务器端的，类似session结构来存放用户数据，当浏览器第一次发出请求时，服务器自动生成一个session和sessionId用来唯一标识这个session，并将其响应发送到浏览器。当浏览器第二次发送请求，会将前一次服务器响应中的sessionId放在请求中一并发送到服务器上，服务器从请求中提取出sessionId，并和保存的所有sessionId进行对比，找到这个用户对应的session。
一般情况下，服务器会在一定时间内(默认30分钟)保存这个session，过了时间限制，就会销毁这个session。在销毁之前，程序员可以将用户的一些数据已key=value形式暂时存放在这个session中。当然也要使用数据库将这个session序列化后保存起来，这样的好处是没有了时间的限制，坏处是随着时间的增加，这个数据库会急速膨胀，特别是访问量增加的时候。一般还是采取前一种方式，以减轻服务器压力。
<!-- more -->
### cookie和session的区别
cookie是保存在客户端的，session是保存在服务端的。
简单的说，当你登录过一个网站的时候，如果web服务器端使用的是session,那么所有的数据保存在服务器上面，客户端每次请求服务器的时候会发送当前会话的sessionId,服务器会根据当前sessionId判断相应的用户数据标志，以确定用户是否登录，或具有某种权限。由于数据是存储在服务器上的，所以你不能伪造，但是如果你能够获取某个登录用户的sessionId，用特殊的浏览器伪造该用户的请求也是能成功的，
sessionId是服务器和客户端链接时随机分配的，一般来说是不会有重复，但如果有大量的并发请求，也不是没有重复的可能性。
如果浏览器使用的是 cookie，那么所有的数据都保存在浏览器端，比如你登录以后，服务器设置了 cookie用户名(username),那么，当你再次请求服务器的时候，浏览器会将username一块发送给服务器，这些变量有一定的特殊标记。服 务器会解释为 cookie变量。所以只要不关闭浏览器，那么 cookie变量便一直是有效的，所以能够保证长时间不掉线。如果你能够截获某个用户的 cookie变量，然后伪造一个数据包发送过去，那么服务器还是认为你是合法的。所以，使用 cookie被攻击的可能性比较大。如果设置了的有效时间，那么它会将 cookie保存在客户端的硬盘上，下次再访问该网站的时候，浏览器先检查有没有 cookie，如果有的话，就读取该 cookie，然后发送给服务器。如果你在机器上面保存了某个论坛 cookie，有效期是一年，如果有人入侵你的机器，将你的 cookie拷走，然后放在他的浏览器的目录下面，那么他登录该网站的时候就是用你的的身份登录的。所以 cookie是可以伪造的。当然，伪造的时候需要主意，直接copy cookie文件到 cookie目录，浏览器是不认的，他有一个index.dat文件，存储了 cookie文件的建立时间，以及是否有修改，所以你必须先要有该网站的 cookie文件，并且要从保证时间上骗过浏览器。

## session与cookie的区别
cookie是属于session的一种，但有不同，cookie不会占用服务器资源，是存在客服端内存或者一个cookie的文本文件中，而session则会占用服务器资源。所以，尽量不要使用session，而使用cookie。但是我们一般认为cookies是不可靠的。session是可靠的，但是目前很多著名的站点也都使用cookie,有时候为了解决禁用cookie后的页面处理，通常采用url重写技术，调用session中大量有用的方法从session中获取数据后置入页面。

## localStorage 和 sessionStorage的区别
localStorage 是长期存储，关闭浏览器时它依然存在
sessionStorage 是临时存储，关闭浏览器时就会自动清除

## localStorage、 sessionStorage和cookie的区别
localStorage 和 sessionStorage相对cookie来说，存储方式更直观，而且容量比cookie存储的容量大，cookie根据不同浏览器所存储的长度有所不同：
Firefox和Safari允许cookie多达4097个字节，包括名（name）、值（value）和等号。
Opera允许cookie多达4096个字节，包括：名（name）、值（value）和等号。
Internet Explorer允许cookie多达4095个字节，包括：名（name）、值（value）和等号。