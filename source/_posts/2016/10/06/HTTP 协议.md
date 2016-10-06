---
title: HTTP 协议
date: 2016-10-06
---

HTTP 协议对应 Web 开发者来说都必须要了解的，无论技术背景或首选编程语言是什么，"请求－响应" 对话是驱动 Web 上通信的基础。

### HTTP 概述

HTTP 协议是 Hyper Text Transfer Protocol（超文本传输协议）的缩写。通常，由 HTTP 客户端发起一个请求，创建一个到服务器指定端口（默认是 80 端口）的 TCP 连接。HTTP 服务器则在那个端口监听客户端的请求。一旦收到请求，服务器会向客户端返回一个状态，比如 "HTTP/1.1 200 OK" ，以及返回的内容，如请求的文件、错误消息、或者其它信息。

平时我们在浏览器上输入网址，按回车就能浏览网页内容。这过程中主要分为两个部分 “请求” （浏览器向服务器发送的消息） 和 “响应” （服务器将处理结果返回给浏览器）。

### HTTP 请求

HTTP 请求分为四格部分：请求行, 请求头, 空行, 请求主体，下面直接给出一个示例：

```
GET /hello.txt HTTP/1.1
Host: www.example.com
```

上示例中第一行为请求行，第二行为请求头，末尾有一个空行（即空行，必须要有）。

- 请求行包含：请求方法, 请求资源, 协议版本  
常见的请求方法有 `GET` `POST` `HEAD` 等  
协议版本有 0.9,  HTTP/1.0, HTTP/1.1

- 在 HTTP/1.1协议中，所有的请求头，除 Host 外，都是可选的。点击 [HTTP头字段](https://zh.wikipedia.org/wiki/HTTP%E5%A4%B4%E5%AD%97%E6%AE%B5%E5%88%97%E8%A1%A8) 了解更多头字段。下面给出常用的请求头：

```
host：请求的域名，必须要有
accept-encoding：可以接受的编码类型（不是字符编码，指的是文件格式）
connection：连接方式，1.0版本close，1.1版本keep-alive
accept-language：可以接收的语言类型
cookie：之前由服务器通过 Set-Cookie 发送的一个超文本传输协议 Cookie
user-agent：客户端信息
content-length：表示的请求体的长度，单位是字节
```

### HTTP 响应

HTTP 响应分为四个部分：状态行, 响应头, 空行, 消息体（响应内容） 示例：

```
HTTP/1.1 200 OK
Date: Mon, 27 Jul 2009 12:28:53 GMT
Server: Apache
Last-Modified: Wed, 22 Jul 2009 19:15:56 GMT
ETag: "34aa387-d-1568eb00"
Accept-Ranges: bytes
Content-Length: 51
Vary: Accept-Encoding
Content-Type: text/plain

Hello World! My payload includes a trailing CRLF.
```

上示例中第一行为状态行，第二行至第九行处为响应头，第十行为空行，最后一行为消息体。

- 状态行包含：协议版本, 状态码, 状态描述。其中有关状态码更多的介绍可移步阅读 [HTTP状态码](https://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81)

- 响应头更多信息可点击上文中的 “HTTP头字段” 链接查看。下面给出常见的响应头：

```
server：服务器信息
date：服务器响应的时间
last-modified：当前请求文件最后被修改的时间
content-length：响应体的数据长度，单位是字节
content-type：响应体的类型
location：重定向
cache-control：缓存控制
```

其中一个重要的 HTTP 响应头 Content-Type 服务器上的每个资源都可以以不同的格式返回给客户端，如 HTML、XML 或 JSON 等；通过在 Content-Type 里设置如 text/html 这样的互联网媒体类型，可以告知客户端，服务器给出的响应格式是什么。常用的媒体类型可移步阅读 [互联网媒体类型](https://zh.wikipedia.org/wiki/%E4%BA%92%E8%81%94%E7%BD%91%E5%AA%92%E4%BD%93%E7%B1%BB%E5%9E%8B)


### 参考资料
<https://zh.wikipedia.org/wiki/%E8%B6%85%E6%96%87%E6%9C%AC%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE>  
<https://zh.wikipedia.org/wiki/WebSocket>  
<https://zh.wikipedia.org/wiki/Comet_%28web%E6%8A%80%E6%9C%AF%29>  
<https://zh.wikipedia.org/wiki/%E6%8E%A8%E9%80%81%E6%8A%80%E6%9C%AF>  
<http://www.runoob.com/http/http-tutorial.html>  
<http://blog.csdn.net/u012545279/article/details/17579155>  
<http://blog.csdn.net/zfrong/article/details/6070608>  
<https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=zh-cn>  
<http://www.alloyteam.com/2012/03/web-cache-1-web-cache-overview/>