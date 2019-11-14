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

- HTTP的消息报头包括普通报头，请求报头，响应报头，实体报头。

- 每一个报头域都由 **名字+“:”+空格+值**组成，消息报头域名字大小写无关

#### 普通报头

既可以用于请求报头也可以出现在响应消息，但不用于被传输的实体，只用于传输的消息。

如：

- Cache-Control 用于指定缓存指令，缓存指令是单向的(响应中出现的缓存指令在请求中未必会出现)，且是独立
  的(一个消息的缓存指令不会影响另一个消息处理的缓存机制)
- Date普通报头域表示消息产生的日期和时间
- Connection普通报头域允许发送指定连接的选项。例如指定连接是连续，或者指定“close”选项，通知服务器，在
  响应完成后，关闭连接

#### 请求报头

请求报头允许客户端向服务器传递请求的附加信息以及客户端自身信息。

- Accept

  用于指定客户端接受哪些类型的信息, 如：Accept:image/gif表明客户端希望接受GIF图象格
  式的资源; Accept:text/html表明客户端希望接受html文本。

- Accept-Charset

  用于指定客户端接受的字符集。如：Accept-Charset:iso-8859-1,gb2312.如果在请求消 
  息中没有设置这个域，缺省是任何字符集都可以接受。

- Accept-Encoding

  用于指定可接受的内容编码.如：Accept-Encoding:gzip.deflate.如果请求消息中没有设置这个域服务器假定客户端对各种内容编码都可以接受。 

- Accept-Language

  用于指定一种自然语言。如：Accept-Language:zh-cn.如果请求消息中没有设置这个报头域，服务器假定客户端对各种语言都可以接受。

- Authorization

  用于证明客户端有权查看某个资源。当浏览器访问一个页面时，如果收到服务器的响应代码为401(未授权)，可以发送一个包含Authorization请求报头域的请求，要求服务器对其进行验证。 

- Host

  用于指定被请求资源的Internet主机和端口号，它通常从HTTP URL中提取出来的，发送请求时，该报头域是必需的

- User-Agent

  允许客户端将它的操作系统、浏览器和其它属性告诉服务器。这个报头域不是必需的

#### 响应报头

响应报头允许服务器传递不能放在状态行中的附加响应信息，以及关于服务器的信息和对Request-URI所标识的资
源进行下一步访问的信息。

- Location 

  用于重定向接受者到一个新的位置。Location响应报头域常用在更换域名的时候。 

- Server 

  包含了服务器用来处理请求的软件信息。与User-Agent请求报头域是相对应的。

- WWW-Authenticate 

  必须被包含在401(未授权的)响应消息中，客户端收到401响应消息时候，并发送Authorization报头域请求服务器对其进行验证时，服务端响应报头就包含该报头域。 

#### 实体报头

请求和响应消息都可以传送一个实体。一个实体由实体报头域和实体正文组成，但并不是说实体报头域和实体正文
要在一起发送，可以只发送实体报头域。实体报头定义了关于实体正文(eg:有无实体正文)和请求所标识的资源
的元信息。

- Content-Encoding 

  被用作媒体类型的修饰符，它的值指示了已经被应用到实体正文的附加内容的编 码，因而要获得Content-Type报头域中所引用的媒体类型，必须采用相应的解码机制。Content-Encoding这样用于记录文档的压缩方法，如：Content-Encoding:gzip 

- Content-Language 

  描述了资源所用的自然语言。没有设置该域则认为实体内容将提供给所有的语言阅读者。如：Content-Language:da

- Content-Length

  用于指明实体正文的长度，以字节方式存储的十进制数字来表示。 

- Content-Type

  用于指明发送给接收者的实体正文的媒体类型。如： Content-Type:text/html;charset=UTF-8
   Content-Type:application/json;charset=UTF-8

- Last-Modified
   用于指示资源的最后修改日期和时间。
   
- Expires
   
   给出响应过期的日期和时间。为了让代理服务器或浏览器在一段时间以后更新缓存中的页面，我们可以使用Expires实体报头域指定页面过期的时间。eg:Expires:Thu，15 Sep 2006 16:23:12 GMT 
   
   HTTP1.1的客户端和缓存必须将其他非法的日期格式(包括0)看作已经过期。如：为了让浏览器不要缓存页面， 我们也可以利用Expires实体报头域，设置为0，jsp中程序如下:response.setDateHeader("Expires","0"); 

