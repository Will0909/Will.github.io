---
title: HTTP
tags: node
categories:
- node
- 网络编程
---

### 什么是HTTP？有什么特点？

HTTP是一个应用层的面向对象的协议，通常运行在TCP之上，它主要有以下特点：

1. **支持客户端和服务端模式**。
2. **简单快速**：向服务端请求服务时，只需要传送请求方法和路径。由于HTTP协议简单，使得HTTP服务器的程序规模小，所以通信速度很快。
3. **灵活**：HTTP运行传输任意类型的数据对象。通过Content-Type。
4. **无连接**：限制每次连接只处理一个请求，服务器处理完客户的请求并收到客户应答后就断开连接，这样可以节省传输时间。
5. **无状态**：协议对应事务处理没有记忆能力。

### URL

#### URI、URL和URN

URI = Universal Resource Identifier 统一资源标志符，用来标识抽象或物理资源的一个紧凑字符串。
URL = Universal Resource Locator 统一资源定位符，一种定位资源的主要访问机制的字符串，一个标准的URL必须包括：protocol、host、port、path、parameter、anchor。

URN = Universal Resource Name 统一资源名称，通过特定命名空间中的唯一名称或ID来标识资源。

**URI包括URL和URN两个类别，URL是URI的子集，所以URL一定是URI，而URI不一定是URL**

#### URL格式 

 http://host:port/abs_path

- http表示要通过HTTP协议来定位网络资源；
- host表示合法的Internet主机域名或者IP地址；
- port指定一个端口号，为空则使用缺省端口80；
- abs_path指定请求资源的路径

### Request

HTTP请求由三部分组成：请求行，消息报头，请求正文。

**请求行**：

格式如下：

```
Method Request-URI HTTP-Version CRLF
```
****

- **Method**表示请求方法，主要有以下方法：
  - GET 请求获取Request-URI所标识的资源
  - POST 在Request-URI所标识的资源后附加新的数据
  - HEAD 请求获取由Request-URI所标识的资源的响应消息报头
  - PUT 请求服务器存储一个资源，并用Request-URI作为其标识
  - DELETE 请求服务器删除Request-URI所标识的资源
  - TRACE 请求服务器回送收到的请求信息，主要用于测试或诊断
  - CONNECT 保留将来使用
  - OPTIONS 请求查询服务器的性能，或者查询与资源相关的选项和需求
- **Request-URI**是一个统一资源标识符
- **HTTP-Version**表示请求的HTTP协议版本
- **CRLF**表示回车和换行（除了作为结尾的CRLF外，不允许出现单独的CR或LF字符）

应用举例：

GET方法：在浏览器的地址栏中输入网址的方式访问网页时，浏览器采用GET方法向服务器获取资源

```
GET /form.html HTTP/1.1
```

POST方法：要求被请求服务器接受附在请求后面的数据，常用于提交表单

```
POST /reg HTTP/ (CRLF)
Accept:image/gif,image/x-xbit,... (CRLF)
HOST:www.guet.edu.cn (CRLF)
Content-Length:22 (CRLF)
Connection:Keep-Alive (CRLF)
Cache-Control:no-cache (CRLF)
(CRLF) //该CRLF表示消息报头已经结束，在此之前为消息报头
user=jeffrey&pwd=1234 //此行以下为提交的数据
```

### Response

HTTP响应由三部分组成，状态行、消息报头、响应正文

**状态行**：

格式如下：

```
HTTP-Version Status-Code Reason-Phrase CRLF
```

- **HTTP-Version**表示服务器HTTP协议的版本

- **Status-Code**表示服务器发回的响应状态代码，常见的状态码有如下类别：

  - 1xx：指示信息--表示请求已接收，继续处理
  - 2xx：成功--表示请求已被成功接收、理解、接受
  - 3xx：重定向--要完成请求必须进行更进一步的操作
  - 4xx：客户端错误--请求有语法错误或请求无法实现
  - 5xx：服务器端错误--服务器未能实现合法的请求

  常见的状态码如下：

  - 200 OK //客户端请求成功
  - 304 Not Modified 自从上次请求后，请求的网页未修改过。 服务器返回此响应时，不会返回网页内容。
  - 400 Bad Request //客户端请求有语法错误，不能被服务器所理解
  - 401 Unauthorized //请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用
  - 403 Forbidden //服务器收到请求，但是拒绝提供服务
  - 404 Not Found //请求资源不存在，eg：输入了错误的URL
  - 500 Internal Server Error //服务器发生不可预期的错误
  - 503 Server Unavailable //服务器当前不能处理客户端的请求，一段时间后可能恢复正常

- **Reason-Phrase**表示状态代码的文本描述

- **CRLF**表示回车和换行（除了作为结尾的CRLF外，不允许出现单独的CR或LF字符）

### 消息报头