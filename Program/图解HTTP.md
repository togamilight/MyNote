# 第一章 了解 Web 及网络基础

## 1.3 网络基础 TCP/IP

通常使用的网络（包括互联网）是在 TCP/IP 协议族的基础上运作的，HTTP 是它的一个子集。

### TCP/IP 的分层管理

TCP/IP 协议族分为 4 层（从上到下）：
* **应用层**：决定了向用户提供应用服务时通信的活动；TCP/IP 协议族内预存了各类通用的应用服务，如 FTP（File Transfer Protocol，文件传输协议） 和 DNS（Domain Name System，域名系统） 服务。HTTP 属于这一层
* **传输层**：提供处于网络连接中的两台计算机之间的数据传输；传输层有两个性质不同的协议：TCP（Transmission Control Protocol，传输控制协议） 和 UDP（User Data Protocol，用户数据报协议）
* **网络层**：又名**网络互连层**，处理在网络上流动的数据包（数据包是网络传输最小单位），规定了通过怎样的传输路线到达对方计算机，并传送数据包，即在与对方机器间的众多计算机或网络设备中选择一条传输路线。IP（Internet Protocol，网际协议）属于这一层
* **链路层**：又名**数据链路层、网络接口层**，处理连接网络的硬件部分，包括控制操作系统、硬件的设备驱动、网卡及光纤等物理可见部分（还包括连接器等一切传输媒介）

在 HTTP 协议中，原始的 HTTP 数据，每经过一层，就会附加上该层对应的**首部**，称为**封装**，到达接收端再一层一层拆开，还原成 HTTP 数据：  
应用层生成 HTTP 数据 → 传输层附加 TCP 首部 → 网络层附加 IP 首部 → 链路层附加以太网首部

## 1.4 与 HTTP 关系密切的协议：IP、TCP 和 DNS

### 负责传输的 IP

IP（Internet Protocol，网际协议）位于**网络层**，负责把各种数据包传送给对方，需要 IP 地址 和 MAC 地址（Media Access Control Address）  
IP 地址是可变的，MAC 地址是网卡的固定地址，一般不变，它们之间可以进行配对，使用 ARP（Address Resolution Protocol，地址解析协议）根据 IP 地址反查 MAC 地址  
传输过程中，中转设备只能获悉很粗略的传输路线，通过 MAC 地址将数据传给下一个中转设备，这种机制称为**路由选择**

### 确保可靠性的 TCP

TCP 位于传输层，提供可靠的字节流服务  
* **字节流服务**：为方便传输，将大块数据**分割**成以报文段（Segment）为单位的数据包进行管理。  
* **可靠性**：数据包发送之后，TCP 会确认数据是否送达，通过**三次握手**策略，使用了 TCP 的标志 -- SYN(synchronize) 和 ACK(acknowledgement)
    * **三次握手**：
        1. 发送端先发送一个带 SYN 标志的数据包给对方
        2. 接收方收到后，回复一个带有 SYN/ACK 标志的数据包表示传达确认信息
        3. 发送端再回复一个带 ACK 标志的数据包，代表“握手”结束
        * 如果握手过程中某个阶段突然中断，TCP 会再次以相同顺序发送相同数据包
    * 除了三次握手，TCP 还有其它各种手段保证通信的可靠性

### 负责域名解析的 DNS 服务

DNS 服务位于应用层，提供域名到 IP 地址间的解析服务，从 DNS 服务器通过域名查找 IP 地址或从 IP 地址反查域名

## 1.7 URI 和 URL

URL(Uniform Resource Locator, 统一资源定位符) 是 URI(Uniform Resource Identifier, 统一资源标识符) 的一种形式，另一种形式是 URN(统一资源名)，不常用

### URI

* **Uniform**：统一的格式，方便处理多种不同类型的资源，加入新增的协议方案也方便(http/ftp)
* **Resource**：资源是指**可标识的任何东西**，可以是复数的集合体
* **Identifier**：标识符，表示可标识的对象

### 绝对 URI 格式

http://^1^user:pass^2^@www.example.com^3^:80^4^/dir/index.html^5^?uid=1^6^\#ch1^7^

1. **协议方案名**：获取访问资源时要指定协议类型，最后加一个冒号，也可使用 **data:** 或 **javascript:** 这类指定数据或脚本程序的方案名
2. **登录信息**：指定用户名和密码作为从服务器获取资源的身份认证，可选
3. **服务器地址**：可以是域名或者 ip，ipv6 用 **[ ]** 括起来
4. **服务器端口号**：指定服务器连接的网络端口，可选，默认为 80
5. **文件路径**
6. **查询字符串**：参数，可选
7. **片段标识符**：标记子资源，如文档内的某个位置，可选

# 第二章 简单的 HTTP 协议

## 2.2 通过请求和响应的交换达成通信

* 请求报文结构
  > GET /xx/x.html HTTP/1.1 （请求方法 URI 协议版本）
    Key: Value              （请求首部）
                            （空行）
    ...                     （内容实体）

* 响应报文结构
  > HTTP/1.1 200 OK （协议版本 状态码 状态码的原因短语）
    Key: Value      （响应首部）
                    （空行）
    ...             （主体）

## 2.3 HTTP 是不保存状态的协议

即**无状态**协议，HTTP 对于发送过的请求和响应都不做持久化处理,可以更快地处理大量事务，确保协议的伸缩性  
但是无状态也很不方便，所以引入了 Cookie 这一功能来保存状态

## 2.4 请求 URI 定位资源

URI 可以定位互联网上任意位置的资源  
请求 URI 的方式：  
* 使用绝对 URI：`GET http://xxx.com/xxx.html HTTP/1.1`
* 使用相对 URI 并在首部字段 Host 中写明域名或 IP
    > GET /xxx.html HTTP/1.1
      Host：xxx.com
* 当不访问特定资源而对服务器本身发起请求时，使用 `*` 作为 URI，如查询服务器支持的 HTTP 方法种类
    `OPTIONS * HTTP/1.1`

## 2.5 告知服务器意图的 HTTP 方法
HTTP/1.1：
* **GET**：获取资源（REST 中用来获取）
* **POST**：传输实体主体（REST 中用来添加）
* **PUT**：传输文件，在报文主体中包含文件内容，保存到请求 URI 指定的位置，但是由于不带验证机制，任何人都可上传，一般不使用。（REST 中用来修改）
* **DELETE**：删除文件，与 PUT 一样，很少使用。（REST 中用来删除）
* **HEAD**：与 GET 一样，但不返回报文主体部分，用于确认 URI 的有效性和资源的更新时间等
* **OPTIONS**：询问该 URI 指定的资源支持的方法
* **TRACE**：追踪路径，让服务器将之前的请求通信环回给客户端。发送请求时，在首部字段 Max-Forwards 中填入数值，每经过一个服务器端就减 1，当数值刚好减到 0 时，停止继续传输，最后接收到请求的服务器端返回状态码 200 OK 的响应。客户端通过 TRACE 方法查询发送的请求是怎样被加工修改、篡改的，中间经过的代理中转。但是不常用，而且容易引发 XST(Cross-Site Tracing，跨站追踪)，基本不用。
* **CONNECT**：要求用隧道协议连接代理，即与代理服务器通信时建立隧道，用隧道协议进行 TCP 通信。主要使用 SSL(Secure Socket Layer，安全套接层) 和 TLS(Transport Layer Security，传输层安全) 协议把通信内容加密后经网络隧道传输。格式如下：  
    `CONNECT 代理服务器名:端口号 HTTP版本`  
代理服务器成功响应后，进入网络隧道

## 2.7 持久连接节省通信量

在 HTTP 1.0 中，每进行完一次 HTTP 通信就要断开 TCP 连接，造成了无谓的开销。

为解决这个问题，提出了**持久连接**的方法，只要任意一端没有提出断开连接，则保持 TCP 连接状态。如此，在建立 1 次 TCP 连接后，可进行多次请求和响应的交互，减少了开销。在 HTTP/1.1 中，所有连接默认都是持久连接  

持久连接使请求可以以**管线化**方式发送，即并行发送多个请求，而不用等待上一个请求获得响应

## 2.8 使用 Cookie 的状态管理

服务器通过发送的 **Response** 报文的 **Header** 中的 **Set-Cookie** 字段通知客户端保存 **Cookie**，客户端每次请求时，会自动在 **Header** 带上 **Cookie** 字段

# 第三章 HTTP 报文内的 HTTP 信息

## 3.1 HTTP 报文

HTTP 报文是由多行数据构成的字符串文本（用CRLF进行换行），分为**首部**和**主体**两部分，中间隔一空行（即两个CRLF回车换行符），**主体**不是必须的

* CRLF 即回车换行符，由回车符(0x0d)和换行符(0x0a)组成

## 3.2 报文首部结构

首部一般包含:
* **请求行**：包含用于请求的方法，请求 URI 和 HTTP 版本（请求专属）
* **状态行**：包含表明响应结果的 HTTP 版本、状态码和原因短语 （响应专属）
* **首部字段**：包含表示请求和响应的各种条件和熟悉的各类首部，一般有4种：**通用首部、请求首部、响应首部、实体首部**
* **其它**：可能包含 HTTP 的 RFC 里未定义的首部，如 Cookie 等

## 3.3 编码提高传输速率

HTTP 可以在传输过程中通过编码压缩来提高传输速率，当然，会消耗更多 CPU 等资源。

### 报文主体与实体主体

* **报文(message)**：HTTP 通信中的基本单位，由 8 位组字节流组成
* **实体(entity)**：作为请求或响应的有效载荷数据（补充项）被传输，由实体首部和实体主体组成
  
HTTP 报文的主体用于传输请求或响应的实体主体。通常，报文主体等于实体主体，只有当传输中进行编码操作时，实体的内容产生变化，才导致它和报文主体产生差异。

### 压缩传输的内容编码

HTTP 中内容编码的功能可进行压缩操作，指明应用在实体内容上的编码格式，保持实体信息原样压缩，压缩后的实体由客户端接收并负责解码。  
常用的内容编码：
* gzip (GNU zip)
* compress (UNIX 系统的标准压缩)
* deflate (zlib)
* identity (不进行编码)

### 分割发送的分块传输编码

在 HTTP 通信过程中，请求的资源未传输完成前，浏览器无法显示；在传输大容量数据时，把数据分割成多块，能让浏览器逐步显示页面，称为**分块传输编码**(Chunked Transfer Coding)。  
分块传输编码将实体主体分为多块，每块用**十六进制**来标记大小，最后一块会使用 "0(CR+LF)" 来标记；接收客户端负责解码，恢复成编码前的实体主体。
HTTP/1.1 有**传输编码**(Transfer Coding)机制，可以在通信时按某种编码方式传输，但只作用于分块传输编码中


## 3.4 发送多种数据的多部分对象集合

MIME(Multipurpose Internet Mail Extensions 多用途因特网邮件扩展) 机制，允许邮件处理文本、图片、视频等不同类型数据，其中有一种 **多部分对象集合** 的方法来容纳多份不同类型的数据，在 HTTP 中也有用到：
* **multipart/form-data**：用于表单上传文件
  ```HTTP
    Content-Type: multipart/form-data; boundary=AaB03x

    --AaB03x
    Content-Disposition: form-data; name="field1"
    Joe Blow
    --AaB03x
    Content-Disposition: form-data; name="pics"; filename="file1.txt"
    Content-Type: text/plain
    ...（file1.txt的数据）...
    --AaB03x--
  ```
* **multipart、byteranges**：状态码 "206 Partial Content"(部分内容)，响应报文包含了多个范围的内容时使用
  ```HTTP
    HTTP/1.1 206 Partial Content
    Date: Fri, 13 Jul 2012 02:45:26 GMT
    Last-Modified: Fri, 31 Aug 2007 02:02:20 GMT
    Content-Type: multipart/byteranges; boundary=THIS_STRING_SEPARATES

    --THIS_STRING_SEPARATES
    Content-Type: application/pdf
    Content-Range: bytes 500-999/8000
    ...（范围指定的数据）...
    --THIS_STRING_SEPARATES
    Content-Type: application/pdf
    Content-Range: bytes 7000-7999/8000
    ...（范围指定的数据）...
    --THIS_STRING_SEPARATES--
  ```

使用多部分对象集合时，需要在首部中加上 `Content-Type`；使用 boundary 字符串来划分各个实体，boundary 字符串前要有 "--"，集合结尾的 boundary 字符串末尾要有 "--"；每个实体中，都可以有首部字段，可以在某部分嵌套使用多部分对象集合

## 3.5 获取部分内容的范围请求

使用范围请求(Range Request)可以指定下载的实体范围，可用来断点续传；使用首部字段 Range 来指定资源的 byte 范围
* `Range:bytes=5001-10000`，5001-10000 字节
* `Range:bytes=5001-`，从 5001 字节之后的全部
* `Range:bytes=-3000,5000-7000`，从开头到 3000 字节和 5000-7000 字节

针对范围请求，响应会返回状态码为 `206 Partial Content` 以及 `Content-Type：multipart/byteranges` 的响应报文；如果服务器无法响应范围请求，则返回状态码 `200 OK` 和完整的实体内容

## 3.6 内容协商

客户端与服务器就响应的资源内容进行交涉，提供给客户端最合适的资源(比如根据浏览器的默认语言返回不同语言的页面)，以响应资源的语言、字符集、编码方式等作为判断的基准，使用以下首部字段：  
`Accept, Accept-Charset, Accept-Encoding, Accept-Language, Content-Language`

内容协商技术有 3 种类型：
* **服务器驱动协商**：以请求的首部字段为参考，服务器自动处理；但以浏览器发送的信息为依据，不一定能筛选出最优内容
* **客户端驱动协商**：用户从浏览器显示的可选列表中手动选择，或者用 JS 脚本自动选择，比如按 OS 或浏览器的类型，自行切换 PC 版和手机版的页面
* **透明协商**：以上两者结合


# 第四章 返回结果的 HTTP 状态码

* **1XX**：Inforamtional，信息性状态码，接收的请求正在处理
* **2XX**：Success，成功，请求正常处理完毕
* **3XX**：Redirection，重定向，需要进行附加操作以完成请求
* **4XX**：Client Error，客户端错误，服务器无法处理请求
* **5XX**：Server Error，服务器错误，服务器处理请求出错

## 4.2 2XX 成功

* **200 OK**：表示请求被正常处理了
* **204 No Content**：表示请求被正常处理，但响应报文中不含实体主体，也不允许返回实体主体；此时，浏览器的页面不会更新。一般在只需要客户端往服务器发送信息，而服务器不需要返回信息时使用
* **206 Partial Content**：范围请求被正常处理，响应报文中包含由 `Content-Range` 指定范围的实体内容

## 4.3 3XX 重定向

* **301 Moved Permanently**：永久性重定向，表示请求的资源已被分配了新的 URI，以后应使用 `Location` 首部字段提示的 URI 访问。会更新书签 ？
* **302 Found**：临时性重定向，表示本次应该用新的 URI 访问资源。不会更新书签 ？
* **303 See Other**：表示请求的资源存在另一个 URI，应使用 GET 方法获取。和 302 的区别在于，需要以 GET 方法请求新的 URI
  * 当 301、302、303 状态码返回时，几乎所有浏览器都会把 POST 改为 GET，并删除请求报文内的主体，之后自动再次发送请求。301、302 标准是禁止将 POST 改为 GET 的，但实际使用时大家都这么做 ？
* **304 Not Modified**：表示客户端发送附带条件的请求时，服务器允许访问资源，但未满足条件。此时，不包含任何响应主体部分；304 其实和重定向没什么关系
  * 附带条件的请求是指 GET 请求包含 `If-Match, If-Modified-Since, If-None-Match, If-Range, If-Unmodified-Since` 中任一首部
* **307 Temporary Redirect**：临时重定向，与 302 相同，但严格遵照标准，不会从 POST 变为 GET

## 4.4 4XX 客户端错误

* **400 Bad Request**：表示请求报文有语法错误，需要修改请求内容再次发送；浏览器会像 `200 OK` 一样对待该状态码 ？
* **401 Unauthorized**：表示发送的请求需要有通过 HTTP 认证（BASIC 认证、DIGEST 认证）的认证信息，如果之前已有一次请求，则表示用户认证失败
* **403 Forbidden**：表示拒绝访问该资源，不需要给出拒绝理由
* **404 Not Found**：表示无法找到该资源

## 4.5 5XX 服务器错误

* **500 Internal Server Error**：表示服务器在执行请求时发生了错误
* **503 Service Unavailable**：表示服务器暂时处于超负载或停机维护，无法处理请求；若事先得知恢复需要的时间，最好写入 `RetryAfter` 首部字段再返回


# 第五章 与 HTTP 协作的 WEB 服务器

## 5.1 虚拟主机

HTTP/1.1 允许一台服务器搭建多个 WEB 站点，这利用了虚拟主机(Virtual Host)的功能；通过不同的主机名和域名访问，DNS 会将它们解析为同一个 IP 地址，在发送请求时，必须在 Host 首部完整指定主机名或域名的 URI

## 5.2 通信数据转发程序

### 代理

代理是一种有转发功能的应用程序，接收由客户端发送的请求并转发给服务器，同时也接收服务器返回的响应并转发给客户端。  
代理不会改变 URI，只会在首部字段 `Via` 追加经过的代理服务器信息。

作用：利用缓存技术减少网络带宽流量；组织内部针对特定网站的访问控制；获取访问日志等等。

* **缓存代理**：在转发响应时，将资源的副本缓存到代理服务器上，再次请求该资源时，直接将缓存的资源返回
* **透明代理**：不对报文进行任何加工的代理。相反的，成为不透明代理

### 网关

转发其他服务器通信数据的服务器，可以将 HTTP 请求转化为其他协议通信，与非 HTTP 服务器通信，比如数据库等。

### 隧道

隧道可按要求建立起一条与其他服务器的通信线路，届时使用 SSL 等加密手段进行通信，确保安全通信  
隧道不会解析 HTTP 请求，会在断开连接时结束

## 5.3 资源的缓存

缓存指代理服务器或客户端本地磁盘保存的资源附表，可减少对源服务器的访问，节省通信流量和时间。
缓存一般会设置有效期，也会根据客户端的要求而更新

# 第六章 HTTP 首部

## 6.2 首部字段

首部字段结构为：`Name:Value`，可能有多个值，用 "," 分隔。
* 首部字段名重复，规范内未明确规定，不同浏览器会进行不同处理

### 4 种首部字段类型
以下名称省略了 "首部字段"(Header Fields)
* **通用**：General，请求和响应报文都会使用
* **请求**：Request，请求报文使用，补充了请求的附加内容、客户端信息、响应内容相关优先级等信息
* **响应**：Response，响应报文使用，补充了响应的附加内容，也会要求客户端附加额外的内容信息
* **实体**：Entity，针对报文的实体部分使用的首部，补充了资源内容更新时间等与实体有关的信息

### HTTP/1.1 首部字段一览

HTTP/1.1 规范定义了 47 中首部字段

#### 通用
| 首部字段名        | 说明                       |
| ----------------- | -------------------------- |
| Cache-Controle    | 控制缓存的行为             |
| Connection        | 逐跳首部、连接的管理 ?     |
| Date              | 创建报文的日期时间         |
| Pragma            | 报文指令                   |
| Trailer           | 报文末端的首部一览         |
| Transfer-Encoding | 指定报文主体的传输编码方式 |
| Upgrade           | 升级为其它协议             |
| Via               | 代理服务器的相关信息       |
| Warning           | 错误通知                   |

#### 请求
| 首部字段名          | 说明                                            |
| ------------------- | ----------------------------------------------- |
| Accept              | 用户代理可处理的媒体类型                        |
| Accept-Charset      | 优先的字符集                                    |
| Accept-Encoding     | 优先的内容编码                                  |
| Accept-Language     | 优先的自然语言                                  |
| Authorization       | Web 认证信息                                    |
| Expect              | 期待服务器的特定行为                            |
| From                | 用户的电子邮箱地址                              |
| Host                | 请求资源所在服务器                              |
| If-Match            | 比较实体标记(ETag)                              |
| If-Modified-Since   | 比较资源的更新时间                              |
| If-None-Match       | 比较实体标记（与 If-Match 相反）                |
| If-Range            | 资源未更新时发送实体 Byte 的范围请求            |
| If-Unmodified-Since | 比较资源的更新时间（与 If-Modified-Since 相反） |
| Max-Forwards        | 最大传输逐跳数                                  |
| Proxy-Authorization | 代理服务器要求客户端的认证信息                  |
| Range               | 实体的字节范围请求                              |
| Referer             | 对请求中 URI 的原始获取方                       |
| TE                  | 传输编码的优先级                                |
| User-Agent          | HTTP 客户端程序的信息                           |

#### 响应
| 首部字段名         | 说明                         |
| ------------------ | ---------------------------- |
| Accept-Ranges      | 是否接受字节范围请求         |
| Age                | 推算资源创建经过的时间       |
| ETag               | 资源的匹配信息               |
| Location           | 令客户端重定向至指定 URI     |
| Proxy-Authenticate | 代理服务器对客户端的认证信息 |
| Retry-After        | 对再次发起请求的时机要求     |
| Server             | 服务器的安装信息             |
| Vary               | 代理服务器缓存的管理信息     |
| WWW-Authenticate   | 服务器对客户端的认证信息     |

#### 实体
| 首部字段名       | 说明                   |
| ---------------- | ---------------------- |
| Allow            | 资源可支持的 HTTP 方法 |
| Content-Encoding | 实体主体使用的编码方式 |
| Content-Language | 实体主体的自然语言     |
| Content-Length   | 实体主体的大小（字节） |
| Content-Location | 替代对应资源的 URI     |
| Content-MD5      | 实体主体的报文摘要     |
| Content-Range    | 实体主体的位置范围     |
| Content-Type     | 实体主体的媒体类型     |
| Expires          | 实体主体过期的日期时间 |
| Last-Modified    | 资源的最后修改日期时间 |

### End-to-end / Hop-by-hop

根据定义成**缓存代理和非缓存代理**的行为，将 HTTP 首部分为两个类别：
* 端对端首部(End-to-end)：此类别中的首部会转发给请求/响应对应的最终接收目标，且必须被转发，且必须保存在由缓存生成的响应中
* 逐跳首部(Hop-by-hop)：此类别中的首部只对单次转发有效，会因通过缓存或代理而不再转发，HTTP/1.1 之后，如果使用逐跳首部，需提供 Connection 首部字段

仅有 8 种逐跳首部，其余都是端对端首部：
**Connection, Keep-Alive, Proxy-Authenticate, Proxy-Authorization, Trailer, TE, Transfer-Encoding, Upgrade**

## 6.3 通用首部

通用首部指请求和响应报文都会使用的首部

### Cache-Control

设置缓存的工作机制，指令的参数可选，多个指令用 "," 分隔：
`Cache-Control: private, max-age=0, no-cache`

缓存请求指令：
| 指令            | 参数 | 说明                         |
| --------------- | ---- | ---------------------------- |
| no-cache        | 无   | 强制向源服务器再次验证       |
| no-store        | 无   | 不缓存请求或响应的任何内容   |
| max-age=[秒]    | 必需 | 响应的最大 Age 值            |
| max-stale=[秒]  | 可选 | 接收已过期的响应             |
| min-fresh=[秒]  | 必需 | 期望在指定时间内的响应仍有效 |
| no-transform    | 无   | 代理不可更改媒体类型         |
| only-if-cached  | 无   | 从缓存获取资源               |
| cache-extension | -    | 新指令标记(token)            |

缓存响应指令：
| 指令             | 参数 | 说明                                       |
| ---------------- | ---- | ------------------------------------------ |
| public           | 无   | 可向任意方提供响应的缓存                   |
| private          | 可选 | 仅向特定用户返回缓存                       |
| no-cache         | 可选 | 缓存前必需先确认其有效性                   |
| no-store         | 无   | 不缓存请求或响应的任何内容                 |
| no-transform     | 无   | 代理不可更改媒体类型                       |
| must-revalidate  | 无   | 可缓存但必须再向源服务器进行确认           |
| proxy-revalidate | 无   | 要求缓存服务器对缓存的响应有效性再进行确认 |
| max-age=[秒]     | 必需 | 响应的最大 Age 值                          |
| s-maxage=[秒]    | 必需 | 公共缓存服务器响应的最大 Age 值            |
| cache-extension  | -    | 新指令标记(token)                          |

* **no-cache 和 no-store**：**no-cache** 表示不缓存过期的资源，缓存前会向源服务器进行有效期确认(读取缓存前应该也会?)；**no-store** 则是完全不缓存。
* **no-cache** 还有许多不明了之处
* **max-age**：请求中的 **max-age** 用于获取缓存时间小于该秒数的缓存资源，超过该时间的资源从服务器重新获取；响应中的 **max-age** 用于设置资源在缓存服务器中保存的最长时间，在此时间内，缓存服务器将不对资源的有效性再做确认。在 HTTP/1.1 中，**max-age** 指令会覆盖 **Expires** 首部字段（在 1.0 中则相反）
* **s-maxage**：与 **max-age** 基本相同，但只适用于供多个用户使用的公共缓存服务器（代理），会覆盖 **max-age** 指令和 **Expires** 首部字段
* **min-fresh**：仅获取该秒数后仍有效的缓存资源
* **max-stale**：过期的缓存资源也照样获取，如果有参数，则获取过期不超过该秒数的缓存资源
* **only-if-cached**：仅获取已缓存的资源，要求缓存服务器不重新加载响应，也不再次确认资源有效性。若无，则返回 `504 Gateway Timeout`
* **must-revalidate**：必须向源服务器验证即将返回的响应缓存是否有效，若无法连通源服务器获取有效资源，则返回 `504 Gateway Timeout`；会忽略 **max-stale**
* **no-transform**：缓存时不能更改实体主体的媒体类型，防止压缩图片等操作
* **cache-extension token**：好像就是可以加入自定义的指令，若缓存服务器无法理解，则直接忽略

### Connection

Connection 首部具有两个作用：
* **控制不再转发给代理的首部字段**：value 为**不再转发的首部字段名**，发送一次后即删除，不转发；好像要用逐跳首部都必须写在这里？
* **管理持久连接**：value 为 **Close 或 Keep-Alive**
  * HTTP/1.1 版本默认是持久连接，客户端在持久连接上连续发送请求，**Close** 表示服务器想断开持久连接；
  * 在旧版本则是非持久连接，客户端发送 **Keep-Alive** 可维持连接，服务器接收后返回同样的 Connection 首部以及额外的 Keep-Alive 首部来指定维持连接的时长，如下
    ```HTTP
    Keep-Alive: timeout=10, max=500
    Connection: Keep-Alive
    ```

### Date

表示创建 HTTP 报文的日期时间  
* HTTP/1.1版本使用使用 RFC1123 中规定的格式：
    `Date: Tue, 03 Jul 2012 04:40:59 GMT`
* 旧版本使用 RFC850 中规定的格式：
    `Date: Tue, 03-Jul-12 04:40:59 GMT`
* 还有一种为 C 标准库的 asctime() 的输出格式
    `Date: Tue Jul 03 04:40:59 2012`

### Pragma

旧版本遗留字段，仅为兼容而定义  
`Pragma: no-cache` 仅用于客户端请求中，要求所有中间服务器不返回缓存资源，一般与 `Cache-Control: no-cache` 共用确保兼容

### Trailer

事先说明在报文主体后记录了哪些首部字段，可应用于 HTTP/1.1 的分块传输编码时

### Transfer-Encoding

规定传输报文主体时采用的编码方式；  
在 HTTP/1.1 中只有一种方式：**chunked**(分块传输)

### Upgrade

检测 HTTP 协议 及其他协议是否可使用更高的版本进行通信。  
该首部产生作用的 Upgrade 对象仅限于客户端和邻接服务器之间（逐跳首部），所以还需要 `Connection: Upgrade`;  
对于含有 Upgrade 首部的请求，服务器可用 `101 Switching Protocols` 状态码响应

```HTTP
GET /index.html HTTP/1.1
Upgrade: TLS/1.0
Connection: Upgrade

HTTP/1.1 101 Switching Protocols
Upgrade: TLS/1.0, HTTP/1.1
Connection: Upgrade
```

### Via

追踪客户端与服务器之间的请求响应报文的传输路径，报文经过代理和网关时，在 Via 首部中附加该服务器的信息；  
除了追踪转发外，还可避免**请求回环**的发生  
经过多个服务器时，可在同一个 Via 中附加服务器信息，也可增加新的 Via 首部  
为了追踪传输路径，通常与 TRACE 方法一起使用

### Warning

告知用户一些与缓存相关的问题的警告  
`Warning: [警告码] [警告的主机:端口号] "[警告内容]" ([日期时间])`  

HTTP/1.1 中定义了 7 种警告，警告码对应的内容仅推荐参考；今后可能新增警告码
| 警告码 | 警告内容                                        | 说明                                                           |
| ------ | ----------------------------------------------- | -------------------------------------------------------------- |
| 110    | Response is stale (响应已过期)                  | 代理返回已过期的资源                                           |
| 111    | Revalidation failed (再验证失败)                | 代理再验证资源有效性时失败（服务器无法到达等原因）             |
| 112    | Disconnection operation (断开连接操作)          | 代理与互联网连接被故意切断                                     |
| 113    | Heuristic expiration (试探性过期)               | 响应的使用期超过24小时（有效缓存的设定时间大于24小时的情况下） |
| 199    | Miscellaneous warning (杂项警告)                | 任意的警告内容                                                 |
| 214    | Transformation applied (使用了转换)             | 代理对内容编码或媒体类型等执行了某些处理时                     |
| 299    | Miscellaneous persistent warning (持久杂项警告) | 任意的警告内容                                                 |

## 6.4 请求首部

用于补充请求的附加信息、客户端信息、对响应内容相关的优先级等

### Accept

声明用户代理能处理的媒体类型及其优先级，使用 **type/subtype** 的形式，一次可指定多种媒体类型  
若想为某个类型指定优先级，需要用 `;` 与其它类型分隔(若没有优先级，则类型间用 `,` 分隔)，用 `q=` 来设置优先级，q 的范围为 0~1(可精确到小数点后 3 位)，默认为 1，越大越优先  
可用星号作通配符，指定任意类型

`Accpet: text/html, application/xml; q=0.9, */*`

### Accept-Charset

声明用户代理支持的字符集及其优先级，用法与 Accept 类似  
应用于内容协商机制的服务器驱动协商

### Accept-Encoding

声明用户代理支持的内容编码及其优先级，用法与 Accept 类似  
内容编码类型：
* **gzip**：由文件压缩程序 GNU zip 生成的编码格式(RFC1952)，采用 Lempel-Ziv 算法(LZ77) 及 32 位循环冗余校验(Cyclic Redundancy Check，通称 CRC)
* **compress**：由 UNIX 文件压缩程序 compress 生成的编码格式，采用 LempelZiv-Welch 算法(LZW)
* **deflate**：组合使用 zlib 格式(RFC1950) 及由 deflate 压缩算法(RFC1951)生成的编码格式
* **identity**：不执行压缩或不会变化的默认编码格式

### Accept-Language

声明用户代理能处理的自然语言集及其优先级，用法与 Accept 类似  

### Authorization

用户代理的认证信息（证书值）  
通常，若请求的资源需要 HTTP 认证，服务器会返回 **401 Unauthorized** 状态码，用户代理接收后会把 Authorization 首部加入到请求中（具体可参阅 RFC2616）

### Except

告知服务器，期望出现的某种特定行为，若服务器无法理解，则返回 **417 Expectation Failed**  
HTTP/1.1 值定义了 100-continue（期望返回状态码 100 Continue）

### From

使用用户代理的用户的电子邮件地址，其目的是为了显示搜索引擎等用户代理的负责人的电子邮件联系方式

### Host

指定请求资源所处的互联网主机名和端口号；  
当请求发送到服务器时，请求中的主机名会被 IP 地址直接替换，若该 IP 地址下部署多个域名（虚拟主机），则服务器无法分辨该请求对应哪个域名，所以需要 Host 首部来指定；若服务器未设定主机名，则直接发送一个空值  
是 HTTP/1.1 唯一必须包含在请求中的首部字段  

### If-Match

形如 If-xxx 的首部成为条件请求，服务器接到附带条件的请求后，判断指定条件为真时，才会执行请求。  

If-Match 指定匹配资源所用的实体标记（ETag）值，这时服务器无法使用弱 ETag 值，当ETag 不匹配时，返回状态码 **412 Precondition Failed**；指定值为 '*' 时，匹配任意 ETag

### If-Modified-Since

指定一个时间，当资源在这个时间之后有过更新时，才处理请求，否自返回状态码 **304 Not Modified**

### If-None-Match

与 If-Match 相反。在 GET 或 HEAD 方法中使用，可获取最新修改的资源，与 If-Modified-Since 有些类似

### If-Range

指定 ETag 值或时间，与 Range 搭配使用，如：
```HTTP
If-Range: "123456"
Range: bytes=5001-10000
```
若资源的 ETag 值或修改时间匹配，则请求作为范围请求处理，否则返回完整资源

### If-Unmodified-Since

与 If-Modified-Since 相反，若资源在指定时间后更新过，返回状态码 **412 Precondition Failed**

### Max-Forwards

值为十进制整数；在 TRACE 或 OPTIONS 方法中，指定可经过的服务器的最大数目，每次转发前减一，当值为 0 时返回响应


