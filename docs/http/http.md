# 深入HTTP请求流程

随着Web2.0时代的到来，互联网从传统的C/S架构转变为更加方便快捷的B/S架构。B/S即浏览器/服务器结构，就像我们访问过的所有网站，客户机上只需要一个浏览器即可上网冲浪。  
当客户端与Web服务器进行交互时，就存在Web请求，这种请求都基于统一的应用层协议（HTTP协议）交互数据。  

## 1.HTTP协议解析

HTTP（HyperText Transfer Protocol）即超文本传输协议，是一种详细规定了浏览器和万维网服务器之间互相通信的规则，它是万维网交换信息的基础，它允许将HTML（超文本标记语言）文档从Web服务器传送到Web浏览器。  

### 1.1发起HTTP请求  

如何发起一个HTTP请求？这个问题似乎很简单，当在浏览器地址栏中输入一个URL，并按回车键后就发起了这个HTTP请求，很快就会看到这个请求的返回结果。  
URL（统一资源定位符）也被成为网页地址，是互联网标准的地址。  
URL的标准格式如下：  
`协议://服务器IP[:端口]/路径/[?查询]`  
  >例如，`http://www.baidu.com/index.html` 就是一个标准的URL。  

### 1.2 HTTP协议详解  

HTTP 协议是互联网的基础协议，也是网页开发的必备知识，最新版本 HTTP/2 更是让它成为技术热点。  

#### 1.2.1 HTTP/0.9  

HTTP 是基于 TCP/IP 协议的应用层协议。它不涉及数据包（packet）传输，主要规定了客户端和服务器之间的通信格式，默认使用80端口。  
最早版本是1991年发布的0.9版。该版本极其简单，只有一个命令GET。  

``` html  
GET /index.html  
```  

上面命令表示，TCP连接（connection）建立后，客户端向服务器请求（request）网页index.html。  
协议规定，服务器只能回应HTML格式的字符串，不能回应别的格式。  

``` html
<html>
    <body>Hello World</body>
</html>
```  

服务器发送完毕，就关闭TCP连接。  

#### 1.2.2 HTTP/1.0

##### 1.2.2.1 简介  

1996年5月，HTTP/1.0 版本发布，内容大大增加。  
首先，任何格式的内容都可以发送。这使得互联网不仅可以传输文字，还能传输图像、视频、二进制文件。这为互联网的大发展奠定了基础。  
其次，除了GET命令，还引入了POST命令和HEAD命令，丰富了浏览器与服务器的互动手段。  
再次，HTTP请求和回应的格式也变了。除了数据部分，每次通信都必须包括头信息（HTTP header），用来描述一些元数据。  
其他的新增功能还包括状态码（status code）、多字符集支持、多部分发送（multi-part type）、权限（authorization）、缓存（cache）、内容编码（content encoding）等。  

##### 1.2.2.2 请求格式  

下面是一个1.0版的HTTP请求的格式。  

``` html
GET /HTTP/1.0  
User-Agent:Mozilla/5.0(Macintosh;Inter MAc OS X 10_10_5)  
Accept:*/*  
```  

可以看到，这个格式与0.9版有很大变化。  
第一行是请求命令，必须在尾部添加协议版本（HTTP/1.0）。后面就是多行头信息，描述客户端的情况。  

##### 1.2.2.3 回应格式  

服务器的回应如下。  

``` html
HTTP/1.0 200 OK  
Content-Type:text/plain  
Content-Length:1337582  
Expires:Thu,05 Dec 1997 16:00:00 GMT
Last-Modified:Wed,5 August 1996 15:55:28 GMT  
Server:Apache 0.84  
  
<html>
    <body>Hello World</body>
<html>
```  

回应的格式是“头信息+一个空行（\r\n）+数据”。其中，第一行是“协议版本+状态码（status code）+状态描述”。  

##### 1.2.2.4 Content-Type 字段  
