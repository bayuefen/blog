---
title: 【Web安全】点击劫持
date: 2019-06-06 11:26:51
top: false
reward: false
comments: false
tags:
    - Web安全
    - 日记
---

今天在测试前端开发项目，在生产环境的流程回归验证中，遇到了Web页面中使用`iframe`内嵌三方告知用户协议无法加载的问题。`Error`提示为
````
Refused to display 'https://www.xxx.com/' in a frame because it set 'X-Frame-Options' to 'sameorigin'.
````
这是一种经典的防范点击劫持(*Click Jacking*)的方式，通过设置HTTP请求头`X-Frame-Options`来禁止跨域`iframe`的引入。以下通过：
> * 什么是点击劫持
> * 点击劫持存在的Web安全隐患
> * 如何防范点击劫持

三方面进行基本的概述与总结。

<!-- more -->

#### 一、什么是点击劫持
点击劫持（ClickJacking）是一种通过视觉欺骗实现Web攻击的方式。主要的实现方式有两种：
> * **iframe引入攻击**：使用透明的iframe,覆盖在一个网页上，诱导性用户进行操作，在不知情的情况下点击透明的iframe页面；
> * **图片覆盖攻击**：使用图片覆盖网页，诱导用户点击；
> * **拖拽劫持与数据窃取**：诱导用户从隐藏不可见`iframe`中拖拽窃取数据；


##### iframe引入攻击
test.html
````
<!DOCTYPE HTML>
<html>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<head>
<title>bayuefen-demos-clickJacking</title>
<style>
     html,body,iframe{
         display: block;
          height: 100%;
          width: 100%;
          margin: 0;
          padding: 0;
          border:none;
     }
     iframe{
          opacity:0;
          filter:alpha(opacity=0);
          position:absolute;
          z-index:2;
     }
     button{
          position:absolute;
          top: 315px;
          left: 462px;
          z-index: 1;
          width: 72px;
          height: 26px;
     }
</style>
</head>
     <body>
          <button>Click-Jacking-Button</button>
          <iframe src="http://www.google.com"></iframe>
     </body>
</html>
````

当用户试图点击test.html里button时，实际上就会点击到iframe上的区块内容。
本质上，通过引入`iframe`进行页面覆盖，同时设置`position`为`absolute`，通过`opacity`设置页面透明度，并将`z-index`设置到较大的值用以覆盖。
从而实现完成一次点击劫持攻击。当然实际的攻击过程中，伪装的模式及方法更深。点击劫持本质上和`CSRF`攻击类似，都是通过诱导式，欺骗用户完成一系列操作行为，从而达到攻击劫持的目的。

##### 图片覆盖攻击
XSIO (Cross Site Image Overlaying)是一种通过调整图片style，将图片覆盖到指定的位置，从而诱导攻击的方式.
````
<a href="http://www.google.com">
  <img src="IMAGE_URL" style="position:absolute;top:90px;left:320px;" />
</a>
````
然后将图片伪装成正常的链接，构造一些文字吸引，欺骗用户点击，实现钓鱼的目的。

##### 拖拽劫持与数据窃取
拖拽劫持的方式是通过隐藏`iframe`中拖拽出攻击者希望得到的数据，然后放到攻击者能控制的另外一个页面中，从而窃取数据。
详细实现可参考国内安全研究员xisigr构造的[POC](https://book.2cto.com/201208/1996.html)

#### 二、点击劫持存在的Web安全隐患
点击劫持存在的主要Web安全隐患，相对于`XSS`和`CSRF`攻击来说，主要是通过诱导式与页面产生交互，通过视觉欺诈的模式实施攻击行为，主要利用在钓鱼、欺诈、广告作弊等方面。
而如何通过有效的手段去防范点击劫持，保证相对稳定及安全的访问环境，以及在测试过程中进行相关Web安全测试的探知是必要的先决要素。

#### 三、如何防范点击劫持
那么，如何通过技术手段，进行点击劫持的防范。主要有以下两种方式：
> * frame busting: 基于JavaScript的iframe禁用嵌套
> * X-Frame-Options: 基于HTTP头的拒绝iframe嵌套加载设置

##### frame busting
frame busting的条件判断语句：
````
if (top != self)
if (top.location != self.location)
if (top.location != location)
if (parent.frames.length > 0)
if (window != top)
if (window.top !== window.self)
if (window.self != window.top)
if (parent && parent != window)
if (parent && parent.frames && parent.frames.length>0)
if((self.parent&&!(self.parent===self))&&(self.parent.frames.length!=0))
````

frame busting 的纠正动作代码:
````
top.location = self.location
top.location.href = document.location.href
top.location.href = self.location.href
top.location.replace(self.location)
top.location.href = window.location.href
top.location.replace(document.location)
top.location.href = window.location.href
top.location.href = "URL"
document.write('')
top.location = location
top.location.replace(document.location)
top.location.replace('URL')
top.location.href = document.location
top.location.replace(window.location.href)
top.location.href = location.href
self.parent.location = document.location
parent.location.href = self.document.location
top.location.href = self.location
top.location = window.location
top.location.replace(window.location.pathname)
window.top.location = window.self.location
setTimeout(function(){document.body.innerHTML='';},1);
window.self.onload = function(evt){document.body.innerHTML='';}
var url = window.location.href; top.location.replace(url)
````

##### X-Frame-Options
X-Frame-Options 有三个值:
> * DENY: 表示该页面不允许在 frame 中展示，即便是在相同域名的页面中嵌套也不允许。
> * SAMEORIGIN: 表示该页面可以在相同域名页面的 frame 中展示。
> * ALLOW-FROM uri: 表示该页面可以在指定来源的 frame 中展示。

涉及`Apache`、`Nginx`、`IIS`对于相应头的配置，详情查看[MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/X-Frame-Options)

#### 参考文献
1.[X-Frame-Options 响应头](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/X-Frame-Options)
2.[Busting Frame Busting](https://www.cnblogs.com/LittleHann/p/3386055.html)
3.[Web应用安全之点击劫持（CLICKJACKING）与X-FRAME-OPTIONS HEADER](https://www.cnblogs.com/xuanhun/p/3610981.html)
4.[拖拽劫持与数据窃取](https://book.2cto.com/201208/1996.html)
5.[白帽子讲Web安全](https://book.douban.com/subject/10546925)

