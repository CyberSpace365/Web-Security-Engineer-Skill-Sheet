# 信息收集

信息收集对于渗透测试前期来说是非常重要的，因为只有我们掌握了目标网站或目标主机足够多的信息之后，我们才能更好地对其进行漏洞检测。正所谓，知己知彼百战百胜！

信息收集的方式可以分为两种：主动信息收集和被动信息收集。

* 主动信息收集：通过直接访问、扫描网站，这种流量将流经网站。
* 被动信息收集：利用第三方的服务对目标进行访问了解，比例：Google搜索、Shodan搜索等。

没有一种方式是最完美的，每个方式都有自己的优势。

* 主动方式，你能获取更多的信息，但是目标主机可能会记录你的操作记录。  
* 被动方式，你收集的信息会相对较少，但是你的行动并不会被目标主机发现。

一般在一个渗透项目下，你需要有多次的信息收集，同时也要运用不同的收集方式，才能保证信息收集的完整性。

## 一、信息收集该如何进行

信息收集是指通过各种方式获取所需要的信息，以便我们在后续的渗透过程更好地进行。最简单的比如说目标站点的IP、中间件、脚本语言、端口、邮箱等等。信息收集在我们渗透测试的过程当中，是最重要的一环，这一环节没做好，没收集到足够多的可利用的信息，我们很难进行下一步的操作。  
信息收集主要收集什么呢？该如何进行收集呢？

### 1. **信息收集需要收集哪些信息**

``` markdown
1. whois信息收集
2. 子域名收集
3. IP段的收集
4. 开放端口探测
5. 网站架构探测
6. 敏感文件和敏感目录探测
7. 目标域名邮箱收集
8. WAF探测
9. 旁站和C段
```

### 2. **信息收集的常用方法**

（1） **whois信息**

很多网站上都可以收集到whois信息，比如说：

> 国外的whois：<https://who.is/>  
> 站长之家：<http://whois.chinaz.com/>  
> 爱站：<https://whois.aizhan.com/>  
> 微步：<https://x.threatbook.cn/>  

这些网站都可以收集whois信息，而且还很全面，主要关注：注册商、注册人、邮件、DNS解析服务器、注册人联系电话。
有需要的还可以查企业的备案信息，主要有三种方式：

> 天眼查：<https://www.tianyancha.com/>  
> ICP备案查询网：<http://www.beianbeian.com/>  
> 国家企业信用信息公示系统：<http://www.gsxt.gov.cn/index.html>

*注意：国外的服务器一般来说是查不到的，因为他们不需要备案。国内的基本上都可以查到。*

*小技巧：如果在站长之家上隐藏了信息，可在who.is上再次查看。*

（2） **子域名**

子域名是在顶级域名下的域名，收集的子域名越多，我们测试的目标就越多，渗透的成功率也越大。往往在主站找不到突破口的时候，我们从子域名入手，有时候你会发现惊喜就到了。
简单收集了一些常用的工具：

1）Google Hacking语法

可以利用Google和Bing这样的搜索引擎进行搜索查询（site:www.xxx.com）
Google还支持额外的减号运算符，以排除我们对“网站:wikimedia.org -www -store ”不感兴趣的子域名。

2）有许多第三方服务聚合了大量的DNS数据集，并通过它们来检索给定域名的子域名。

* VirusTotal：<https://www.virustotal.com/#/home/search>
* DNSdumpster：<https://dnsdumpster.com/>

3）基于SSL证书查询
查找一个域名证书的最简单方法是使用搜索引擎来收集计算机的CT日志，并让任何搜索引擎搜索它们。前两种比较常用。

* crt.sh：<https://crt.sh/>
* Censys：<https://censys.io/certificates>
* Facebook ct：<https://developers.facebook.com/tools/ct/>
* Google ct：<https://google.com/transparencyreport/https/ct/>

4）简单的在线子域名收集（不推荐）  

* phpinfo：<https://phpinfo.me/domain/>
* links：<http://i.links.cn/subdomain/>

5）爆破枚举
这个就有很多工具可以用了，比较常见的是：

* layer子域名挖掘机
* subDomainsBrute
* K8
* orangescan
* DNSRecon

这里重点推荐 layaer 和 subDomainsBrute 工具，可以从子域名入侵到主站。

*小技巧：在<https://github.com/> 上也可以搜索子域名，运气好的话，会有意想不到的收获。*

（3）**IP段的收集**

一般IP的话，我们在收集子域名的时候，就大概知道目标网站的IP段了。
也可以通过whois命令获取。即通过中国互联网信息中心：<http://ipwhois.cnnic.net.cn/>进行查询。

（4）**开放端口探测**

很多时候，网站都会开启CDN加速，导致我们查询到的IP不是真实的IP，所以得先查询到真实的IP地址。方法一大把，下面介绍最准确的几种方法吧！

* 通过让服务器给你发邮件(看邮箱头源 ip )找真实ip（最可靠）
* 通过 zmpap 全网爆破查询真实ip（可靠）
* 通过查询域名历史ip，<http://toolbar.netcraft.com>（借鉴）
* 通过国外冷门的DNS的查询：nslookup xxx.com国外冷门DNS地址（借鉴）

收集到大量IP，那就要进行端口扫描了，看看有什么常见的漏洞。
最常用的就是神器Nmap了。

> 命令：nmap  -T4  -sT  -p-  -sV  ip

常见端口列表整理如下：

> 21,22,23,80\~90,161,389,443,445,873,1099,1433,1521,1900,2082,2083,2222,2601,2604,3128,3306,3311,3312,3389,4440,4848,5432,5560,5900,5901,5902,6082,6379,7001\~7010,7778,8080\~8090,8649,8888,9000,9200,10000,11211,27017,28017,50000,50030,50060

（5）**网站架构探测**

当我们探测目标站点网站架构时，比如说：操作系统，中间件，脚本语言，数据库，服务器，web容器等等，可以使用以下方法查询。

* wappalyzer插件——火狐插件
* 云悉：<http://www.yunsee.cn/info.html>
* 查看数据包响应头
* CMS指纹识别：<http://whatweb.bugscaner.com/look/>

CMS指纹识别又有很多方法，比如说御剑指纹识别、Webrobot工具、whatweb工具、还有在线查询的网站等等。

（6） 敏感文件和敏感目录探测

通常我们所说的敏感文件、敏感目录大概有以下几种：

* Git
* hg/Mercurial
* svn/Subversion
* bzr/Bazaar
* Cvs
* WEB-INF泄露
* 备份文件泄露、配置文件泄露

敏感文件、敏感目录挖掘一般都是靠工具、脚本来找，当然大佬手工也能找得到。

常用的工具有：

* 御剑（真的很万能）
* 爬虫（AWVS、Burpsuite等）
* 搜索引擎（Google、Github等）
* wwwscan
* BBscan（一位巨佬写的python脚本：<https://github.com/lijiejie/BBScan>）
* GSIL（也是一位巨佬写的python脚本：<https://github.com/FeeiCN/GSIL>）
* 社交平台（比如一些社交媒体的技术交流群、博客等）

（7）目标域名邮箱收集

一定要养成收集站点邮箱账号收集的习惯（因为好多官方后台都是用内部邮箱账号登录的，指不定哪天你就得到一个进后台的机会）。

* 通过说明文档以及网站页面收集，或者网站发表者以及留言板信息处收集账号
* 通过 `百度` 或者 `google` 语法收集
* 搜索相关 QQ 群收集相关企业员工的社交账号

*用途： 可用来进行爆破或者弱口令登录以及撞裤攻击。*

（8） WAF探测

WAF也叫Web应用防火墙，是通过执行一系列针对HTTP/HTTPS的安全策略来专门为Web应用提供保护的一款产品。WAF的探测很多人都会忽略掉，其实当我们遇到WAF，第一想法是溜了溜了、告辞告辞。但当我们知道是什么WAF时，又有种去绕过它的冲动，很多大牛都喜欢挑战WAF，于是网上就出现了很多绕过WAF的方法。毕竟成功绕过之后的那种自豪感真的真的很舒服。

常用的两种方式：

* 手工（提交恶意数据，简单粗暴）
* Kali工具（WAFW00F、Nmap）

Nmap探测WAF有两种脚本：

* 一种是http-waf-detect

>命令：nmap  -p80,443  --script=http-waf-detect  ip

* 一种是http-waf-fingerprint

> 命令：nmap  -p80,443  --script=http-waf-fingerprint  ip

WAFW00F探测WAF：
> 命令：wafw00f  -a  域名

（9） 旁站和C段

旁站：是和目标网站在同一台服务器上的其它的网站。
C端：是和目标服务器ip处在同一个C段的其它服务器。

旁站和C段的查询方式：

* 利用Bing，语法为：`http://cn.bing.com/search?q=ip:111.111.111.111`
* 站长之家：<http://s.tool.chinaz.com/same>
* Google Hacking，语法为：site:125.125.125.*
* 利用Nmap，语法：nmap  -p  80,8080  –open  ip/24
* K8工具、御剑、北极熊扫描器等
* webscan在线：<http://www.webscan.cc/>

**最后，附上几个常用的搜索引擎：**

* ZoomEy：<https://www.zoomeye.org/>
* FoFa：<https://fofa.so/>
* Dnsdb：<https://www.dnsdb.io/zh-cn/>
* Shodan：<https://www.shodan.io/>
* Censys：<https://censys.io/>
* 御剑全家桶：<http://www.moonsec.com/post-753.html>

## 二、信息收集常用搜索引擎的使用技巧

### 1. **Google Hacking**

Google Hacking 是利用强大的谷歌搜索，来在浩瀚的互联网中搜索到我们需要的信息。轻量级的搜索可以搜素出一些遗留后门，不想被发现的后台入口，中量级的搜索出一些用户信息泄露，源代码泄露，未授权访问等等，重量级的则可能是mdb文件下载，CMS 未被锁定install页面，网站配置密码，php远程文件包含漏洞等重要信息。

利用Google搜索我们想要的信息，需要配合谷歌搜索引擎的一些语法：  

* **基本搜索**  

``` markdown
逻辑与：and
逻辑或：or
逻辑非：-
完整匹配：“关键词”
通配符：* ?
```

* **高级搜索**
  
1. intext:

      寻找正文中含有关键字的网页，例如： `intext:后台登录`  将只返回正文中包含  `后台登录` 的网页。

2. intitle:

      寻找标题中含有关键字的网页，例如：  `intitle:后台登录`   将只返回标题中包含 `后台登录` 的网页，而  `intitle:后台登录  密码`  将返回标题中包含 `后台登录` 而正文中包含 `密码` 的网页。

3. allintitle:

      用法和intitle类似，只不过可以指定多个词，例如：  `alltitle:后台登录 管理员`   将返回标题中包含 `后台登录` 和 `管理员` 的网页。

4. inurl：

      将返回url中含有关键词的网页：例如： `inurl:Login`   将返回url中含有 `Login` 的网页。

5. allinurl:

      用法和inurl类似，只不过可以指定多个词，例如： `inurl:Login admin`  将返回url中含有 `Login` 和 `admin` 的网页。

6. site:

      指定访问的站点，例如： `site:baidu.com  inurl:Login`   将只在`baidu.com` 中查找url中含有 `Login` 的网页。

7. filetype:

      指定访问的文件类型，例如：`site:baidu.com filetype:pdf`  将只返回 `baidu.com` 站点上文件类型为 `pdf` 的网页。

8. link:

      指定链接的网页，例如：`link:www.baidu.com`  将返回所有包含指向 `www.baidu.com` 的网页。

9. related:

      相似类型的网页，例如： `related:www.llhc.edu.cn`  将返回与 `www.llhc.edu.cn`  相似的页面，相似指的是网页的布局相似。

10. cache:

      网页快照，谷歌将返回给你他存储下来的历史页面，如果你同时制定了其他查询词，将在搜索结果里以高亮显示，例如：`cache:www.hackingspirits.com  guest` ，将返回指定网站的缓存，并且正文中含有guest。

11. info：

      返回站点的指定信息，例如：`info:www.baidu.com` 将返回百度的一些信息。

12. define:

      返回某个词语的定义，例如：`define:Hacker` 将返回关于Hacker的定义。

13. phonebook:

      电话簿查询美国街道地址和电话号码信息。例如：`phonebook:Lisa+CA`  将返回名字里面包含 `Lisa并住在加州的人的所有名字`。

14. 查找网站后台：  

      * site:xx.com intext:管理  
      * site:xx.com inurl:login
      * site:xx.com intitle:后台

15. 查看服务器使用的程序：

      * site:xx.com filetype:asp
      * site:xx.com filetype:php
      * site:xx.com filetype:jsp
      * site:xx.com filetype:aspx

16. 查看上传漏洞：

      * site:xx.com inurl:file
      * site:xx.com inurl:load

17. Index of：

      利用  `Index of`  语法去发现允许目录浏览的web网站，就像在本地的普通目录一样。下面是一些有趣的查询：  
      * index of /admin  
      * index of /passwd  
      * index of /password  
      * index of /mail  
      * "index of /" +passwd  
      * "index of /" +password.txt  
      * "index of /" +.htaccess  
      * "index of /root"  
      * "index of /cgi-bin"  
      * "index of /logs"  
      * "index of /config"

18. inurl

      而上面这些命令中用的最多的就是 inurl: 了，利用这个命令，可以查到很多意想不到的东西。  

      * 查询 `allinurl:winnt/system32/` ：  列出的服务器上本来应该受限制的诸如“system32” 等目录，如果你运气足够好，你会发现“system32” 目录里的“cmd.exe” 文件，并能执行他，接下来就是提升权限并攻克了。  
      * 查询 `allinurl:wwwboard/passwd.txt` ： 将列出所有有“WWWBoard Password vulnerability”漏洞的服务器。  
      * 查询  `inurl:.bash_history` ： 将列出互联网上可以看见 “inurl:.bash_history” 文件的服务器。这是一个命令历史文件，这个文件包含了管理员执行的命令，有时会包含一些敏感信息比如管理员键入的密码。  
      * 查询 `inurl:config.txt`： 将看见网上暴露了“inurl:config.txt”文件的服务器，这个文件包含了经过哈希编码的管理员的密码和数据库存取的关键信息。  

19. 还有一些其他一些使用“inurl:”和“allinurl:”查询组合的例子：

      * inurl:admin filetype:txt  
      * inurl:admin filetype:db  
      * inurl:admin filetype:cfg  
      * inurl:mysql filetype:cfg  
      * inurl:passwd filetype:txt  
      * inurl:”wwwroot/*.”  
      * inurl:adpassword.txt  
      * inurl:webeditor.php  
      * inurl:file_upload.php  
      * inurl:gov filetype:xls “restricted”  
      * index of ftp +.mdb allinurl:/cgi-bin/ +mailto

### 2. **Shodan**

Shodan 是一个搜索引擎，但它与 Google 这种搜索网址的搜索引擎不同，Shodan 是用来搜索网络空间中在线设备的，你可以通过 Shodan 搜索指定的设备，或者搜索特定类型的设备，其中 Shodan 上最受欢迎的搜索内容是：webcam，linksys，cisco，netgear，SCADA等等。  

在windows系统上访问Shodan只需要访问链接：<https://www.shodan.io>

***Shodan的工作原理***

Shodan 通过扫描全网设备并抓取解析各个设备返回的 `banner` 信息，通过了解这些信息 Shodan 就能得知网络中哪一种 Web 服务器是最受欢迎的，或是网络中到底存在多少可匿名登录的 FTP 服务器，或者哪个ip对应的主机是哪种设备。

***Shodan的用法***

1. 基本搜索  

      这里就像是用 Google 一样，在主页的搜索框中输入想要搜索的内容即可，例如搜索 “8443”。  
      搜索结果包含两个部分，左侧是大量的汇总数据包括：  

      > * Results map – 搜索结果展示地图  
      > * Top services (Ports) – 使用最多的服务/端口  
      > * Top organizations (ISPs) – 使用最多的组织/ISP  
      > * Top operating systems – 使用最多的操作系统  
      > * Top products (Software name) – 使用最多的产品/软件名称  

      随后，在中间的主页面我们可以看到包含如下的搜索结果：

      > * IP 地址  
      > * 主机名  
      > * ISP  
      > * 该条目的收录收录时间  
      > * 该主机位于的国家  
      > * Banner 信息  

      想要了解每个条目的具体信息，只需要点击每个条目对应的链接即可。此时，URL 会变成这种格式 `https://www.shodan.io/host/[IP]`，所以我们也可以通过直接访问指定的 IP 来查看详细信息。

2. 过滤搜索

      如果像前面单纯只使用关键字直接进行搜索，搜索结果可能不尽人意，那么此时我们就需要使用一些特定的命令对搜索结果进行过滤，常见用的过滤命令如下所示：
      > * hostname：搜索指定的主机或域名，例如 hostname:"google"。  
      > * port：搜索指定的端口或服务，例如 port:"21"。  
      > * country：搜索指定的国家，例如 country:"CN"。  
      > * city：搜索指定的城市，例如 city:"Hefei"。  
      > * org：搜索指定的组织或公司，例如 org:"google"。  
      > * isp：搜索指定的ISP供应商，例如 isp:"China Telecom"。  
      > * product：搜索指定的操作系统/软件/平台，例如 product:"Apache httpd"。  
      > * version：搜索指定的软件版本，例如 version:"1.6.2"。  
      > * geo：搜索指定的地理位置，参数为经纬度，例如 geo:"31.8639, 117.2808"。  
      > * before/after：搜索指定收录时间前后的数据，格式为dd-mm-yy，例如 before:"11-11-15"。  
      > * net：搜索指定的IP地址或子网，例如 net:"210.45.240.0/24"。  

3. 搜索实例

      查找位于合肥的 Apache 服务器：
      > apache city:"Hefei"

      查找位于国内的 Nginx 服务器：
      > nginx country:"CN"

      查找 GWS(Google Web Server) 服务器：
      > "Server: gws" hostname:"google"

      查找指定网段的华为设备：
      > huawei net:"61.191.146.0/24"

      如上通过在基本关键字后增加指定的过滤关键字，能快速的帮助发现我们感兴趣的内容。当然，还有更快速更有意思的方法，那就是点击 Shodan 搜索栏右侧的 “Explore” 按钮，就会得到很多别人分享的搜索语法。

4. 其他功能

      Shodan 不仅可以查找网络设备，它还具有其他相当不错的功能。
      * Exploits：每次查询完后，点击页面上的 “Exploits” 按钮，Shodan 就会帮我们查找针对不同平台、不同类型可利用的 exploits。当然也可以通过直接访问网址来自行搜索：<https://exploits.shodan.io/welcome>
      * 地图：每次查询完后，点击页面上的 “Maps” 按钮，Shodan 会将查询结果可视化的展示在地图当中；
      * 报表：每次查询完后，点击页面上的 “Create Report” 按钮，Shodan 就会帮我们生成一份精美的报表。
