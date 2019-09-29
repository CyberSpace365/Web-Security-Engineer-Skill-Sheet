# 深入HTTP请求流程

随着Web2.0时代的到来，互联网从传统的C/S架构转变为更加方便快捷的B/S架构。B/S即浏览器/服务器结构，就像我们访问过的所有网站，客户机上只需要一个浏览器即可上网冲浪。  
当客户端与Web服务器进行交互时，就存在Web请求，这种请求都基于统一的应用层协议（HTTP协议）交互数据。  

## 1.HTTP协议解析

HTTP（HyperText Transfer Protocol）即超文本传输协议，是一种详细规定了浏览器和万维网服务器之间互相通信的规则，它是万维网交换信息的基础，它允许将HTML（超文本标记语言）文档从Web服务器传送到Web浏览器。  

### 1.1发起HTTP请求  

如何发起一个HTTP请求？这个问题似乎很简单，当在浏览器地址栏中输入一个URL，并按回车键后就发起了这个HTTP请求，很快就会看到这个请求的返回结果。  
URL（统一资源定位符）也被成为网页地址，是互联网标准的地址。  
URL的标准格式如下：`协议://服务器IP[:端口]/路径/[?查询]`  
 例如，`http://www.baidu.com/index.html` 就是一个标准的URL。  

### 1.2 HTTP协议版本介绍  

HTTP 协议是互联网的基础协议，也是网页开发的必备知识，最新版本 HTTP/2 更是让它成为技术热点。  

#### 1.2.1 HTTP/0.9  

HTTP 是基于 TCP/IP 协议的应用层协议。它不涉及数据包（packet）传输，主要规定了客户端和服务器之间的通信格式，默认使用80端口。  
最早版本是1991年发布的0.9版。该版本极其简单，只有一个命令GET。  

``` http 0.9  
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

``` http 1.0
GET /HTTP/1.0  
User-Agent:Mozilla/5.0(Macintosh;Inter MAc OS X 10_10_5)  
Accept:*/*  
```  

可以看到，这个格式与0.9版有很大变化。  
第一行是请求命令，必须在尾部添加协议版本（HTTP/1.0）。后面就是多行头信息，描述客户端的情况。  

##### 1.2.2.3 回应格式  

服务器的回应如下。  

``` http 1.0
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

关于字符的编码，1.0版规定，头信息必须是 ASCII 码，后面的数据可以是任何格式。因此，服务器回应的时候，必须告诉客户端，数据是什么格式，这就是Content-Type字段的作用。

下面是一些常见的Content-Type字段的值。

``` http 1.0
text/plain
text/html
text/css
image/jpeg
image/png
image/svg+xml
audio/mp4
video/mp4
application/javascript
application/pdf
application/zip
application/atom+xml
```

这些数据类型总称为MIME type，每个值包括一级类型和二级类型，之间用斜杠分隔。  
除了预定义的类型，厂商也可以自定义类型。

``` http 1.0
application/vnd.debian.binary-package
```

上面的类型表明，发送的是Debian系统的二进制数据包。  
MIME type还可以在尾部使用分号，添加参数。

``` http 1.0
Content-Type: text/html; charset=utf-8
```

上面的类型表明，发送的是网页，而且编码是UTF-8。  
客户端请求的时候，可以使用Accept字段声明自己可以接受哪些数据格式。

``` http 1.0
Accept: */*
```

上面代码中，客户端声明自己可以接受任何格式的数据。  
MIME type不仅用在HTTP协议，还可以用在其他地方，比如HTML网页。

``` html
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
<!-- 等同于 -->
<meta charset="utf-8" />
```

##### 1.2.2.5 Content-Encoding 字段

由于发送的数据可以是任何格式，因此可以把数据压缩后再发送。  
Content-Encoding字段说明数据的压缩方法。

``` http 1.0
Content-Encoding: gzip
Content-Encoding: compress
Content-Encoding: deflate
```

客户端在请求时，用Accept-Encoding字段说明自己可以接受哪些压缩方法。

``` http 1.0
Accept-Encoding: gzip, deflate
```

##### 1.2.2.6 缺点

HTTP/1.0 版的主要缺点是，每个TCP连接只能发送一个请求。发送数据完毕，连接就关闭，如果还要请求其他资源，就必须再新建一个连接。  
TCP连接的新建成本很高，因为需要客户端和服务器三次握手，并且开始时发送速率较慢（slow start）。所以，HTTP 1.0版本的性能比较差。随着网页加载的外部资源越来越多，这个问题就愈发突出了。  
为了解决这个问题，有些浏览器在请求时，用了一个非标准的Connection字段。

``` http 1.0
Connection: keep-alive
```

这个字段要求服务器不要关闭TCP连接，以便其他请求复用。服务器同样回应这个字段。

``` http 1.0
Connection: keep-alive
```

一个可以复用的TCP连接就建立了，直到客户端或服务器主动关闭连接。  
但是，这不是标准字段，不同实现的行为可能不一致，因此不是根本的解决办法。

#### 1.2.3 HTTP/1.1

1997年1月，HTTP/1.1 版本发布，只比 1.0 版本晚了半年。它进一步完善了 HTTP 协议，一直用到了20年后的今天，直到现在还是最流行的版本。  

##### 1.2.3.1 持久连接

1.1 版的最大变化，就是引入了持久连接（persistent connection），即TCP连接默认不关闭，可以被多个请求复用，不用声明Connection: keep-alive。  
客户端和服务器发现对方一段时间没有活动，就可以主动关闭连接。不过，规范的做法是，客户端在最后一个请求时，发送Connection: close，明确要求服务器关闭TCP连接。  
目前，对于同一个域名，大多数浏览器允许同时建立6个持久连接。

##### 1.2.3.2 管道机制

1.1 版还引入了管道机制（pipelining），即在同一个TCP连接里面，客户端可以同时发送多个请求。这样就进一步改进了HTTP协议的效率。  
举例来说，客户端需要请求两个资源。以前的做法是，在同一个TCP连接里面，先发送A请求，然后等待服务器做出回应，收到后再发出B请求。管道机制则是允许浏览器同时发出A请求和B请求，但是服务器还是按照顺序，先回应A请求，完成后再回应B请求。

##### 1.2.3.3 Centent-Length字段

一个TCP连接现在可以传送多个回应，势必就要有一种机制，区分数据包是属于哪一个回应的。这就是Content-length字段的作用，声明本次回应的数据长度。

``` http 1.1
Content-Length: 3495
```

上面代码告诉浏览器，本次回应的长度是3495个字节，后面的字节就属于下一个回应了。  
在1.0版中，Content-Length字段不是必需的，因为浏览器发现服务器关闭了TCP连接，就表明收到的数据包已经全了。

##### 1.2.3.4 分块传输编码

使用Content-Length字段的前提条件是，服务器发送回应之前，必须知道回应的数据长度。
对于一些很耗时的动态操作来说，这意味着，服务器要等到所有操作完成，才能发送数据，显然这样的效率不高。更好的处理方法是，产生一块数据，就发送一块，采用"流模式"（stream）取代"缓存模式"（buffer）。
因此，1.1版规定可以不使用Content-Length字段，而使用"分块传输编码"（chunked transfer encoding）。只要请求或回应的头信息有Transfer-Encoding字段，就表明回应将由数量未定的数据块组成。

``` http 1.1
Transfer-Encoding: chunked
```

每个非空的数据块之前，会有一个16进制的数值，表示这个块的长度。最后是一个大小为0的块，就表示本次回应的数据发送完了。下面是一个例子。

``` http 1.1
HTTP/1.1 200 OK
Content-Type: text/plain
Transfer-Encoding: chunked
25
This is the data in the first chunk
1C
and this is the second one
3
con
8
sequence
0
```

##### 1.2.3.5 其他功能

1.1版还新增了许多动词方法：`PUT`、`PATCH`、`HEAD`、 `OPTIONS`、`DELETE`。  
另外，客户端请求的头信息新增了`Host`字段，用来指定服务器的域名。  
`Host: www.example.com`  
有了`Host`字段，就可以将请求发往同一台服务器上的不同网站，为虚拟主机的兴起打下了基础。

##### 1.2.3.6 缺点

虽然1.1版允许复用TCP连接，但是同一个TCP连接里面，所有的数据通信是按次序进行的。服务器只有处理完一个回应，才会进行下一个回应。要是前面的回应特别慢，后面就会有许多请求排队等着。这称为"队头堵塞"（Head-of-line blocking）。  
为了避免这个问题，只有两种方法：一是减少请求数，二是同时多开持久连接。这导致了很多的网页优化技巧，比如合并脚本和样式表、将图片嵌入CSS代码、域名分片（domain sharding）等等。如果HTTP协议设计得更好一些，这些额外的工作是可以避免的。

#### 1.2.4 SPDY协议

2009年，谷歌公开了自行研发的`SPDY协议`，主要解决 HTTP/1.1 效率不高的问题。
这个协议在Chrome浏览器上证明可行以后，就被当作 HTTP/2 的基础，主要特性都在 HTTP/2 之中得到继承。

#### 1.2.5 HTTP/2

2015年，HTTP/2 发布。它不叫 HTTP/2.0，是因为标准委员会不打算再发布子版本了，下一个新版本将是 HTTP/3。

##### 1.2.5.1 二进制协议

HTTP/1.1 版的头信息肯定是文本（ASCII编码），数据体可以是文本，也可以是二进制。HTTP/2 则是一个彻底的二进制协议，头信息和数据体都是二进制，并且统称为"帧"（frame）：头信息帧和数据帧。  
二进制协议的一个好处是，可以定义额外的帧。HTTP/2 定义了近十种帧，为将来的高级应用打好了基础。如果使用文本实现这种功能，解析数据将会变得非常麻烦，二进制解析则方便得多。

##### 1.2.5.2 多工

HTTP/2 复用TCP连接，在一个连接里，客户端和浏览器都可以同时发送多个请求或回应，而且不用按照顺序一一对应，这样就避免了"队头堵塞"。
举例来说，在一个TCP连接里面，服务器同时收到了A请求和B请求，于是先回应A请求，结果发现处理过程非常耗时，于是就发送A请求已经处理好的部分， 接着回应B请求，完成后，再发送A请求剩下的部分。
这样双向的、实时的通信，就叫做多工（Multiplexing）。

##### 1.2.5.3 数据流

因为 HTTP/2 的数据包是不按顺序发送的，同一个连接里面连续的数据包，可能属于不同的回应。因此，必须要对数据包做标记，指出它属于哪个回应。  
HTTP/2 将每个请求或回应的所有数据包，称为一个数据流（stream）。每个数据流都有一个独一无二的编号。数据包发送的时候，都必须标记数据流ID，用来区分它属于哪个数据流。另外还规定，客户端发出的数据流，ID一律为奇数，服务器发出的，ID为偶数。  
数据流发送到一半的时候，客户端和服务器都可以发送信号（RST_STREAM帧），取消这个数据流。1.1版取消数据流的唯一方法，就是关闭TCP连接。这就是说，HTTP/2 可以取消某一次请求，同时保证TCP连接还打开着，可以被其他请求使用。  
客户端还可以指定数据流的优先级。优先级越高，服务器就会越早回应。

##### 1.2.5.4 头信息压缩

HTTP 协议不带有状态，每次请求都必须附上所有信息。所以，请求的很多字段都是重复的，比如Cookie和User Agent，一模一样的内容，每次请求都必须附带，这会浪费很多带宽，也影响速度。
HTTP/2 对这一点做了优化，引入了头信息压缩机制（header compression）。一方面，头信息使用gzip或compress压缩后再发送；另一方面，客户端和服务器同时维护一张头信息表，所有字段都会存入这个表，生成一个索引号，以后就不发送同样字段了，只发送索引号，这样就提高速度了。

##### 1.2.5.5 服务器推送

HTTP/2 允许服务器未经请求，主动向客户端发送资源，这叫做服务器推送（server push）。  
常见场景是客户端请求一个网页，这个网页里面包含很多静态资源。正常情况下，客户端必须收到网页后，解析HTML源码，发现有静态资源，再发出静态资源请求。其实，服务器可以预期到客户端请求网页后，很可能会再请求静态资源，所以就主动把这些静态资源随着网页一起发给客户端了。

### 1.3 HTTP协议详解

HTTP遵循请求（Request）/应答（Response）模型，Web浏览器向Web服务器发送请求时，Web服务器处理请求并返回适当的应答。

#### 1.3.1 HTTP请求与响应

（1）**HTTP请求**  
HTTP请求包括三部分，分别是请求行（请求方法）、请求头（消息报头）和请求正文。下面是HTTP请求的一个例子。

``` http
POST /login.php HTTP/1.1      //请求行
HOST:www.baidu.com           //请求头
User-Agent:Mozilla/5.0(Windows NT 6.1;rv:15.0 Gecko/20190101 Firefox/15.0)  
                              //空白行，代表请求头结束
Username=admin&password=admin //请求正文
```

HTTP请求行的第一行即为请求行，请求行由三部分组成，该行的第一部分说明了该请求是POST请求；该行的第二部分是一个斜杠（/login.php），用来说明请求的是该域名目录下的login.php；该行的最后一部分说明使用的是HTTP1.1版本（另一个可选项是1.0）。  
第二行至空白行为HTTP中的请求头（也被称为消息头）。其中，HOST代表请求的主机地址，User-Agent代表浏览器的标识。请求头由客户端自行设定。关于消息头的内容，在后面章节中将会详细介绍。  
HTTP请求的最后一行为请求正文，请求正文是可选的，它最常出现在POST请求方法中。

（2）**HTTP响应**  
与HTTP请求对应的是HTTP响应，HTTP响应也由三部分内容组成，分别是响应行、响应头（消息报头）和响应正文（消息主题）。下面是一个经典的HTTP响应。

``` http
HTTP/1.1 200 OK                         //响应行
Date:Thu,28 Feb 2019 08:36:36 GMT       //响应头
Server:BWS/1.0
Content-Type:text/html;charset=utf-8
Cache-Control:private
Expires:Thu, 28 Feb 2019 08:36:36 GMT
Content-Encoding：gzip
Set-Cookie:H_PS_PSSOD=2022_1438_1944_1788;path:/;domain=.baidu.com
Connection:Keep-Alive
                                        //空白行，代表响应头结束
<html>                                  //响应正文或者叫消息主题
      <head>
            <title>
                  Index.htnl
            </title>
      </head>
......
</html>
```

HTTP响应的第一行为响应行，其中有HTTP版本（HTTP1.1）、状态码（200）以及消息"OK"。  
第二行至末尾的空白行为响应头，由服务器向客户端发送。  
消息报头之后是响应正文，是服务器向客户端发送的HTML数据。

#### 1.3.2 HTTP请求方法

HTTP请求的方法非常多，其中GET、POST最常见。下面是HTTP请求方法的详细介绍。  
（1）**GET**  
GET方法用于获取请求页面的指定信息（以实体的格式）。如果请求资源为动态脚本（非HTML），那么返回文本是Web容器解析后的HTML源代码，而不是源文件。例如请求index.jsp，返回的不是index.jsp的源文件，而是经过解析后的HTML代码。
如下HTTP请求：

``` http
GET /index.php?id=1 HTTP/1.1
HOST:www.baidu.com
```

使用GET请求index.php，并且id参数为1，在服务器端脚本语言中可以选择性地接收这些参数，比如id=1&name=admin，一般都是由开发者内定好的参数项目才会接收，比如开发者只接收id参数项目，若加了其他参数项，如：

``` http
Index.php?id=1&username=admin
```

服务器端脚本不会理会你加入的内容，依然只会接收id参数，并且去查询数据，最终向服务器端发送解析过的HTML数据，不会因为你的干扰而乱套。  

（2）**HEAD**  
HEAD方法除了服务器不能再响应里返回消息主体外，其他都与GET方法相同。此方法经常被用来测试超文本链接的有效性、可访问性和最近的改变。攻击者编写扫描工具时，就常用此方法，因为只测试资源是否存在，而不用返回消息主题，所以速度一定是最快的。一个经典的HTTP HEAD请求如下：

``` http
HEAD /index.php HTTP/1.1
HOST:www.baidu.com
```

（3）**POST**  
POST方法也与GET方法相似，但最大的区别在于，GET方法没有请求内容，而POST是有请求内容的。POST请求最多用于向服务器发送大量的数据。GET虽然也能发送数据，但是有大小（长度）的限制，并且GET请求会将发送的数据显示在浏览器端，而POST则不会，所以安全性相对来说高一点。  
例如，上传文件、提交留言等，只要是向服务器传输大量的数据，通常都会使用POST请求。一个经典的HTTP POST请求如下：

``` http
POST /login.php HTTP/1.1
HOST:www.baidu.com
Content-Length:26
Accept:text/html,application/xhtml+xml,application/xml;
Origin:http://home.cto.com
User-Agent:Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.17 Chrome/24.0Safari/537.17 SE 2.X MetaSr 1.0
Content-Type:application/x-www-form-urlencoded
Accept-Language:zh-CN,zh;q=0.8
Accept-Charset:GBK,utf-8;q=0.7,*,q=0.3

user=admin&password=admin
```

用POST方法向服务器请求login.php，并且传递参数user=admin&password=amdin。

（4）**PUT**  
PUT方法用于请求服务器把请求中的实体存储在请求资源下，如果请求资源已经在服务器中存在，那么将会用此请求汇总的数据替换原先的数据，作为指定资源的最新修改版。如果请求指定的资源不存在，将会创建这个资源，且数据为请求正文，请求如下：

``` http
PUT /input.txt
HOST:www.baidu.com
Content-Length:6
123456
```

这段HTTP PUT请求将会在主机根目录下创建input.txt，内容为123456。通常情况下，服务器都会关闭PUT方法，因为它会为服务器建立文件，属于危险的方法之一。

（5）**DELETE**  
DELETE方法用于请求源服务器删除请求的指定资源。服务器一般都会关闭此方法，因为客户端可以进行删除文件操作，属于危险方法之一。  
（6）**TRACE**  
TRACE方法被用于激发一个远程的应用层的请求消息回路，也就是说，回显服务器收到的请求。TRACE方法允许客户端去了解数据被请求链的另一端接收的情况，并且利用那些数据信息去测试或诊断。但此方法非常少见。  
（7）**CONNECT**  
HTTP1.1协议规范保留了CONNECT方法，此方法是为了用于能动态切换到隧道的代理。  
（8）**OPTIONS**  
OPTIONS方法是用于请求获得由URI标识的资源在请求/响应的通信过程中可以使用的功能选项。通过这个方法，客户端可以在采取具体资源请求之前，决定对该资源采取何种必要措施，或者了解服务器的性能。  
HTTP OPTIONS请求如下：

``` http
OPTIONS / HTTP/1.1
HOST:www.baidu.com
```

以上为HTTP1.1标准请求方法，详情请参照`“https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html”`，但HTTP中的请求方法还不止这些，例如WebDAV。WebDAV（Web-based Distributed Authoring and Versioning）是一种基于HTTP/1.1协议的通信协议，它扩展了HTTP 1.1，在GET、POST、HEAD等几个HTTP标准方法以外添加了一些新的方法，使应用程序可直接对Web Server进行读写，并执行写文件锁定（Locking）和解锁（Unlock）、文件复制（Copy）、文件移动（Move）。另外，还可以支持文件的版本控制。  

#### 1.3.3 HTTP状态码

当客户端发出HTTP请求，服务器端接收后，会向客户端发送响应信息，其中，HTTP响应中的第一行中，最重要的一点就是HTTP的状态码，内容如下：  

``` http
HTTP/1.1 200 OK
```

此响应的状态码为200，在HTTP协议中表示请求成功。HTTP协议中的状态码有三位数字组成，第一位数字定义了响应的类别，且只有以下5种。

* 1xx：信息提示，表示请求已被成功接收，继续处理。其范围为100~101。
* 2xx：成功，服务器成功地处理了请求。其范围为200~206。
* 3xx：重定向，重定向状态码用于告诉浏览器客户端，它们访问的资源已被移动，并告诉客户端新的资源地址位置。这时，浏览器将重新对新资源发起请求。其范围为300~305。
* 4xx：客户端错误状态码，有时客户端会发送一些服务器无法处理的东西，比如格式错误的请求，或者最常见的是，请求一个不存在的URL。其范围为400~415。
* 5xx：有时候客户端发送了一条有效请求，但Web服务器自身却出错了，可能是Web服务器运行出错了，或者网站都挂了。5xx就是用来描述服务器内部错误的，其范围为500~505。

常见的状态码描述如下：

``` html
200：客户端请求成功，是最常见的状态。
302：重定向。
404：请求资源不存在，是最常见的状态。
400：客户端请求有语法错误，不能被服务器所理解。
401：请求未经授权。
403：服务器收到请求，但是拒绝提供服务。
500：服务器内部错误，是最常见的错误。
503：服务器当前不能处理客户端的请求，一段时间后可能恢复正常。
```

#### 1.3.4 HTTP消息

HTTP消息又称为HTTP头（HTTP header），由四部分组成，分别是请求头、响应头、普通头和实体头。从名称上看，我们就可以知道它们所处的位置。  

（1）**请求头**  
请求头只出现在HTTP请求中，请求报头允许客户端向服务器端传递请求的附加信息和客户端自身的信息。常用的HTTP请求头如下。

* Host  
Host请求报头域主要用于指定被请求资源的Internet主机架和端口号，例如：HOST:www.baidu.com:80。  

* User-Agent  
User-Agent请求报头域允许客户端将它的操作系统、浏览器和其他属性告诉服务器。登录一些网站时，很多时候都可以见到显示我们的浏览器、系统信息，这些都是此头的作用，如：User-Agent:My privacy。  

* Referer  
Referer包含一个URL，代表当前访问URL的上一个URL，也就是说，用户是从什么地方来到本页面。如：Referer:www.baidu.com/login.php，代表用户从login.php来到当前页面。  

* Cookie  
Cookie是非常重要的请求头，它是一段文本，常用来表示请求者身份等。  

* Range  
Range可以请求实体的部分内容，多线程下载一定会用到此请求头。例如：  
表示头500字节：bytes=0~499  
表示第二个500字节：bytes=500~999  
表示最后500字节：bytes=-500  
表示500字节以后的范围：bytes=500-  

* x-forward-for  
x-forward-for即XXF头，它代表请求端的IP，可以有多个，中间以逗号隔开。  

* Accept  
Accept请求报头域用于指定客户端接收哪些MIME类型的信息，如Accept：text/html，表明客户端希望接收HTML文本。  

* Accept-Charset  
Accept-Charset请求报头域用于指定客户端接收的字符集。例如：Accept-Charset:iso-8859-1，gb2312。如果在请求消息中没有设置这个域，默认是任何字符集都可以接收。  

（2）**响应头**  
响应头是服务器根据请求向客户端发送的HTTP头。常见的HTTP响应头如下。  

* Server  
服务器所使用的Web服务器名称，如Server:Apache/1.3.6(Unix)，攻击者通过查看此头，可以探测Web服务器名称。所以，建议在服务器端进行修改此头的信息。  

* Set-Cookie  
向客户端设置Cookie，通过查看此头，可以清楚地看到服务器向客户端发送的Cookie信息。  

* Last-Modified  
服务器通过这个头告诉浏览器，资源的最后修改时间。  

* Location  
服务器通过这个头告诉浏览器去访问哪个页面，浏览器接收到这个请求之后，通常会立刻访问Location头所指向的页面。这个头通常配合302状态码使用。  

* Refresh  
服务器通过Refresh头告诉浏览器定时刷新浏览器。  

（3）**普通头**  
在普通报头中，有少数报头域用于所有的请求和响应消息，但并不用于被传输的实体，只用于传输的消息。例如：Date，表示消息产生的日期和时间。Connection，允许发送指定连接的选项。例如，指定连接是连续的，或者指定“close”选项，通知服务器，在响应完成后，关闭连接。Cache-Control，用于指定缓存指令，缓存指令是单向的，且是独立的。  

（4）**实体头**  
请求和响应消息都可以传送一个实体头。实体头定义了关于实体正文和请求所标识的资源的元信息。元信息也就是实体内容的属性，包括实体信息类型、长度、压缩方法、最后一次修改时间等。常见的实体头如下。  

* Content-Type
Content-Type实体头用于向接收方指示实体的介质类型。  
* Content-Encoding  
Content-Encoding头被用作媒体类型的修饰符，它的值指示了已经被应用到实体正文的附加内容的编码，因而要获得Content-Type报头域中所引用的媒体类型，必须采用相应的解码机制。  

* Content-Length  
Content-Length实体报头用于指明实体正文的长度，以字节方式存储的十进制数字来表示。  

* Last-Modified  
Last-Modified实体报头用于指示资源的最后修改日期和时间。
