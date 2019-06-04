---
title: 【HTTP】http协议学习
date: 2019-04-09 22:08:32
tags:
    - HTTP
    - 日记
    - 学习Vlog
---

### HTTP协议
HTTP协议是客户端和 服务器端之间数据传输的格式规范，格式简称为“超文本传输协议”全称为“Hyper Text Transfer Protocol”。

<!-- more -->
### HTTP协议历史版本
- `HTTP 1.0`： 默认是基于短连接，即非持久连接，采用文本数据格式传输；单个tcp连接仅能维护传输单个Web对象。
    > 无host域

<br/>

- `HTTP 1.1`： 默认是基于长连接，即持久连接（可配置成非持久连接），采用文本数据格式传输；单个tcp连接可以维护多个Web对象的传输。
    > HTTP 1.1可获取host域这个参数。
    > 长连接可有效减少TCP三次握手四次挥手的网络连接性能开销。
    > HTTP 1.1支持只发送header信息，不带任何body信息。可以校验B/S架构模式下，Browser是否具有访问请求权限，有则返回`status: 100`，无则返回`status: 401`，从而再依返回结果将请求的body信息发送给服务器，节约网络带宽。
    > HTTP 1.1同时也支持资源加载的断点续传。
    > 同一时间对于同一域名，请求数量有限制，超过限制会造成网络阻塞请求。
    
<br/>

- `HTTP 2.0`： 采用二进制数据格式传输；实现多路复用；进行头部压缩优化。[HTTP1.1与HTTP2.0区别Demo](https://http2.akamai.com/demo)
    > ⭐️ HTTP 2.0采用*多路复用（Multiplexing）* 用以解决*线头阻塞* 的问题。核心基于Google公司开发的基于TCP的应用层协议[SPDY非标准协议](https://zh.wikipedia.org/wiki/SPDY)
    > ⭐️ 增加"二进制分帧层"实现底层tcp多路复用。将多个请求在同一个TCP连接上完成，承载任意数量的双向数据流。极大的提升了传输层通信性能。
    > HTTP 2.0采用首部压缩设计的[HPACK](http://http2.github.io/http2-spec/compression.html)算法
    > HTTP 2.0的Server Push以及缓存策略
    
<br/>

- `HTTP 3.0`:  核心基于UDP传输层协议的[QUIC协议](https://zhuanlan.zhihu.com/p/32553477);提供数据传输的高可靠性及0-RTT延迟；彻底解决*线头阻塞* 
    > 基于UDP传输层协议，实现高速及高可靠性的数据传输。
    > 彻底解决http1.1遗留的线头阻塞（HOL），实现不同流的数据传输相互独立传输，互不干扰。
    > 0-RTT，不必像TCP那样需要三次握手。
    > 参考：
    [QUIC协议浅析与HTTP/3.0](https://www.jianshu.com/p/bb3eeb36b479) 
    [如何看待 HTTP/3 ？](https://www.zhihu.com/question/302412059/answer/533223530)
### HTTP/2协议
1、二进制分帧层 (Binary Framing Layer)
2、在单个TCP连接里多路复用请求。
3、HTTP/2的Server Push，非常重要的一个特性。
4、HTTP Header的压缩，采用的是HPack算法。
5、应用层的重置连接
6、请求优先级设置
7、流量控制
8、HTTP/1 的几种优化可以弃用
参考：https://blog.wangriyu.wang/2018/05-HTTP2.html

### HTTP协议在OSI模型的位置
HTTP协议位于应用层
<p><image src="https://bayuefen.oss-cn-hangzhou.aliyuncs.com/blog/20190406164742398.png?x-oss-process=style/compress_high" width="600" align="center"></p>

### HTTP协议的method
GET：请求一个指定资源的表示形式. 使用GET的请求应该只被用于获取数据.

```
>>> r = requests.get('https://api.github.com/events')
```

HEAD：请求一个与GET请求的响应相同的响应，但没有响应体.

```
>>> r = requests.head('http://httpbin.org/get')
```

POST：用于将实体提交到指定的资源，通常导致状态或服务器上的副作用的更改. 
```
>>> r = requests.post('http://httpbin.org/post', data = {'key':'value'})
```

PUT：用请求有效载荷替换目标资源的所有当前表示。

```
>>> r = requests.put('http://httpbin.org/put', data = {'key':'value'})
```

DELETE：删除指定的资源。

```
>>> r = requests.delete('http://httpbin.org/delete')
```

CONNECT：建立一个到由目标资源标识的服务器的隧道。

OPTIONS：用于描述目标资源的通信选项。

```
>>> r = requests.options('http://httpbin.org/get')
```

TRACE：沿着到目标资源的路径执行一个消息环回测试。

PATCH：用于对资源应用部分修改。

GET和POST对比：
GET
1.方法用途
GET 方法的首要目的是 获取资源

2.方法特点
a) 参数可见
GET 方法的参数是明文可见的包含在 URL 当中，所以说敏感信息不建议使用 GET 方法
不过也正是因此，所以 GET 方法允许被保存书签

b) 数据类型只允许 ASCII
GET 方法的数据类型只允许是 ASCII 字符，所以说传递 二进制 文件就不可以用 GET 方法了哦

c) 可以保存书签
当我们访问某一个网站的频率特别高的时候，肯定添加到书签，那其实书签就是依靠 GET 方法来保存的

d) 可以被缓存
GET 方法支持缓存，当本次请求允许被缓存时，会将资源存值本地 cache ，在未过期的情况下直接取本地 cache；缓存过期后视情况而定

e) 参数会保留在浏览器历史记录
比较直观的感受就是，我们可以在浏览器的历史记录中查看到曾经搜索过的关键字信息

f) 请求长度会受限于所使用的浏览器与服务器
不同的浏览器对于 GET 请求长度的限制也是不同的，注意这是 浏览器 / 服务器（IE、Chrome、Apache、IIS等） 对于长度的限制，而不是 HTTP 协议

POST
1.方法用途
POST 方法的首要目的是 提交，POST 方法一般用于添加资源

2.方法特点
a) 参数不可见，也不会被保存
所以说 POST 方法是不可以被保存书签的

b) 不能收藏为书签
理由如上

c) 不可以被缓存
我要提交的数据被缓存在本地 cache 中想想其实也是没道理的

d) 不会被保存在浏览器历史中
同样是因为参数不可见

e) 不限制请求长度
对于 POST 方法这种以 提交 为首要目的的方法，肯定是不可以限制请求长度的

f) 数据类型
不限，所以说 POST 是可以 提交文件 到服务器的

g) 请求方式
POST 请求与 GET 请求不同，他会首先提交 HEAD 信息，待得到 100 响应后，才会再次将 DATA 提交

### HTTP协议的组成
请求报文包含三部分：
**·**请求行(Request line)：包含请求方法、URI、HTTP版本信息
**·**请求首部字段(Request header)
**·**请求内容实体
响应报文包含三部分：（以豆瓣电影TOP250为例）
>Request Headers

> GET /top250 HTTP/1.1              
\#GET表示一个读取请求，将从服务器获得网页数据，/表示URL的路径，URL总是以/开头，/就表示首页，最后的HTTP/1.1指示采用的HTTP协议版本是1.1。
Host: movie.douban.com
\#表示请求的域名是movie.douban.com
Connection: keep-alive
\#表示支持长连接
Cache-Control: max-age=0
\#指定请求和响应遵循的缓存机制
Upgrade-Insecure-Requests: 1
\#浏览器可以处理https协议
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.86 Safari/537.36
\#发出请求的用户信息
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3
\#客户端希望接受的数据类型
Accept-Encoding: gzip, deflate, br
\#浏览器发给服务器,声明浏览器支持的编码类型
Accept-Language: zh-CN,zh;q=0.9
\#浏览器支持的语言分别是简体中文和中文，优先支持简体中文。
Cookie: bid=ZbyUzrJdS2w; __utmc=30149280; __utmc=223695111; __yadk_uid=nTiBvU6fTOaXD90dB6edYhp8urhJwCjc; viewed="27599884"; gr_user_id=4aa3ad30-748f-488c-b5b3-bcff3f0456d4; douban-fav-remind=1; ll="118172"; _vwo_uuid_v2=DE51C937E99B505F3A54AE46B8619B83A|80d039133d473653fabc07636080bbb8; push_noty_num=0; push_doumail_num=0; __utmv=30149280.19454; ap_v=0,6.0; _pk_ref.100001.4cf6=%5B%22%22%2C%22%22%2C1554617137%2C%22https%3A%2F%2Fwww.
baidu.com%2Flink%3Furl%3Dt8BO_5f8zl_78xcE_dnmyhd_1xbKVsXtXZv_yHxp72UVO88nbOSLQSA1xMPpsfX2%26wd%3
D%26eqid%3Da8c4f714000c0677000000035ca9932e%22%5D;
 _pk_id.100001.4cf6=e94317b751ae8229.1554384156.13.1554617137.1554583408.; _pk_ses.100001.4cf6=*; __utma=30149280.106710662.1554384144.1554583409.1554617137.12; __utmb=30149280.0.10.1554617137; __utmz=30149280.1554617137.12.6.utmcsr=baidu|utmccn=(organic)|utmcmd=organic; __utma=223695111.1166551075.1554384156.1554583409.1554617137.13; __utmb=223695111.0.10.1554617137; __utmz=223695111.1554617137.13.6.utmcsr=baidu|utmccn=(organic)|utmcmd=organic
\#http是无状态的，所以引入了cookie来管理服务器与客户端之间的状态

响应报文包含三部分：
**·**状态行：包含HTTP版本、状态码、状态码的原因短语
**·**响应首部字段
**·**响应内容实体
> Response header

> HTTP/1.1 200 OK
\#响应状态
Date: Sun, 07 Apr 2019 06:05:45 GMT
\#生成消息的具体时间和日期
Content-Type: text/html; charset=utf-8
\#服务器发送 html 文档，字符集为 UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
Keep-Alive: timeout=30
Vary: Accept-Encoding
\#服务器响应时根据请求头中的的值返回不同的内容
X-Xss-Protection: 1; mode=block
设置浏览器的XSS防护机制，浏览器如果检测到恶意代码，则不渲染恶意代码
X-Douban-Mobileapp: 0
Expires: Sun, 1 Jan 2006 01:00:00 GMT
浏览器会在指定过期时间内使用本地缓存
Pragma: no-cache
Cache-Control: must-revalidate, no-cache, private
X-DAE-Node: brand4
X-DAE-App: movie
Server: dae
X-Content-Type-Options: nosniff
Content-Encoding: gzip
文档使用的 MIME 类型是 text/html，并且对内容进行了 gzip 压缩

### HTTP协议的状态码
1xx（临时响应）表示临时响应并需要请求者继续执行操作的状态代码。
2xx （成功） 表示成功处理了请求的状态代码。
3xx （重定向） 表示要完成请求，需要进一步操作。 通常，这些状态代码用来重定向。
4xx（请求错误） 这些状态代码表示请求可能出错，妨碍了服务器的处理。
5xx（服务器错误） 这些状态代码表示服务器在尝试处理请求时发生内部错误。 这些错误可能是服务器本身的错误，而不是请求出错。
常见的：200 – 服务器成功返回网页 |404 – 请求的网页不存在 |503 – 服务不可用 

状态码                     |中文描述 
:--------------------:|:--------------------:
100|（继续） 请求者应当继续提出请求。 服务器返回此代码表示已收到请求的第一部分，正在等待其余部分。  
101|（切换协议） 请求者已要求服务器切换协议，服务器已确认并准备切换。
102|（已接受）已经接受请求，但未处理完成
200| （成功）  服务器已成功处理了请求。 通常，这表示服务器提供了请求的网页。
201|（已创建）  请求成功并且服务器创建了新的资源。
202|（已接受）  服务器已接受请求，但尚未处理。
203|（非授权信息）  服务器已成功处理了请求，但返回的信息可能来自另一来源。
204|（无内容）  服务器成功处理了请求，但没有返回任何内容。
205|（重置内容） 服务器成功处理了请求，但没有返回任何内容。 
206|（部分内容）  服务器成功处理了部分 GET 请求。
300|（多种选择）  针对请求，服务器可执行多种操作。 服务器可根据请求者 (user agent) 选择一项操作，或提供操作列表供请求者选择。 
301|（永久移动）  请求的网页已永久移动到新位置。 服务器返回此响应（对 GET 或 HEAD 请求的响应）时，会自动将请求者转到新位置。
302|（临时移动）  服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求。
303|（查看其他位置） 请求者应当对不同的位置使用单独的 GET 请求来检索响应时，服务器返回此代码。
304|（未修改） 自从上次请求后，请求的网页未修改过。 服务器返回此响应时，不会返回网页内容。 
305|（使用代理） 请求者只能使用代理访问请求的网页。 如果服务器返回此响应，还表示请求者应使用代理。
307|（临时重定向）  服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求。
400|（错误请求） 服务器不理解请求的语法。
401|未授权） 请求要求身份验证。 对于需要登录的网页，服务器可能返回此响应。 
403|（禁止） 服务器拒绝请求。 
404|（未找到） 服务器找不到请求的网页。 
405|（方法禁用） 禁用请求中指定的方法。
406|（不接受） 无法使用请求的内容特性响应请求的网页。 
407|（需要代理授权） 此状态代码与 401（未授权）类似，但指定请求者应当授权使用代理。
408|（请求超时）  服务器等候请求时发生超时。 
409|（冲突）  服务器在完成请求时发生冲突。 服务器必须在响应中包含有关冲突的信息。 
410|（已删除）  如果请求的资源已永久删除，服务器就会返回此响应。 
411|（需要有效长度） 服务器不接受不含有效内容长度标头字段的请求。 
412|（未满足前提条件） 服务器未满足请求者在请求中设置的其中一个前提条件。 
413|（请求实体过大） 服务器无法处理请求，因为请求实体过大，超出服务器的处理能力。 
414|（请求的 URI 过长） 请求的 URI（通常为网址）过长，服务器无法处理。 
415|（不支持的媒体类型） 请求的格式不受请求页面的支持。 
416|（请求范围不符合要求） 如果页面无法提供请求的范围，则服务器会返回此状态代码。 
417|（未满足期望值） 服务器未满足”期望”请求标头字段的要求。
500|（服务器内部错误）  服务器遇到错误，无法完成请求。 
501|（尚未实施） 服务器不具备完成请求的功能。 例如，服务器无法识别请求方法时可能会返回此代码。
502|（错误网关） 服务器作为网关或代理，从上游服务器收到无效响应。
503|（服务不可用） 服务器目前无法使用（由于超载或停机维护）。 通常，这只是暂时状态。 
504|（网关超时）  服务器作为网关或代理，但是没有及时从上游服务器收到请求。 
505|（HTTP 版本不受支持） 服务器不支持请求中所用的 HTTP 协议版本。
  
  
### cookies和会话
因为HTTP是无状态的，对事物处理没有记忆能力。cookies和会话出现用于保持HTTP连接状态，
会话在服务端，用来保存用户的会话信息；cookies在客户端，浏览器下次访问网页时会自动附带上它发送给服务器，
服务器通过识别cookies找到对应的会话判断用户状态。
PS:关闭浏览器不会导致会话被删除，反而没有存储到硬盘上的cookie会消失，因此为了节省存储空间需要为会话设置一个失效时间。
