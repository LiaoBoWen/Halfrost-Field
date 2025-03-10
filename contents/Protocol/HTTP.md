<p align='center'>
<img src='../images/HTTP_logo.png'>
</p>


Web 使用一种名为 HTTP (HyperText Transfer Protocol，超文本传输协议) 的协议作为规范的。
>HTTP 更加严谨的译名应该是 超文本转移协议。


HTTP 于 1990 年问世。那时的 HTTP 并没有作为正式的标准，因为被称为 HTTP/0.9   
HTTP 正式作为标准被公布是 1996 年 5 月，版本命名为 HTTP/1.0，记载于 RFC1945   
HTTP 在 1997 年 1 月公布了当前最主流的版本，版本命名为 HTTP/1.1，记载于 RFC2616


## 一. HTTP 支持的方法

HTTP 是一种不保存状态，即 无状态（ stateless ）协议。HTTP 协议自身不对请求和响应之间的通信状态进行保存。也就是说在 HTTP 这个级别，协议对于发送过的请求或响应都不做持久化处理。这也是为了更快的处理大量事务，确保协议的可伸缩性。

HTTP/1.1 虽然是无状态协议，但是为了实现期望的保持状态的功能，特意引入了 Cookie 技术。

| 方法名 | 说明 | 支持的 HTTP 协议版本 | 详细说明|
| :---: | :---: | :---: |:---: |
| GET | 获取资源 | 1.0、1.1 | GET 方法用来请求访问已被 URI 识别的资源。指定的资源经服务器端解析后返回响应内容。（我想访问你的某个资源）|
| POST | 传输实体主体 | 1.0、1.1 | POST 方法用来传输实体的主体。虽然 GET 也可以传输实体的主体，但一般不用 GET 而用 POST，POST 的主要目的并不是获取响应的主体内容。（我想把这条信息告诉你）|
| PUT | 传输文件 | 1.0、1.1 | 要求在请求报文的主体中包含文件内容，然后保存到请求 URI 指定位置。（我想要把这份文件传给你）|
| HEAD | 获取报文首部 | 1.0、1.1 | HEAD 方法和 GET 方法一样，只是不返回报文主体部分。用于确认 URI 的有效性及资源更新的日期时间等等（我想要那个相关信息）|
| DELETE | 删除文件 | 1.0、1.1 | 与 PUT 相反的方法，DELETE 方法按请求 URI 删除指定资源（把这份文件删掉吧）|
| OPTIONS | 询问支持的方法 | 1.1 | OPTIONS 用来查询针对请求 URI 指定的资源支持的方法（你支持哪些方法？）|
| TRACE | 追踪路径 | 1.1 | TRACE 方法是让 Web 服务器将之前的请求通信返回给客户端的方法，TRACE 方法不常用，并且容易引发 XST ( Cross-Site-Tracing ，跨站追踪)攻击，所以通常更不会用到了|
| CONNECT | 要求用隧道协议连接代理 | 1.1 | CONNECT 方法要求在与代理服务器通信时建立隧道，实现用隧道协议进行 TCP 通信，主要使用 SSL （ Secure Sockets Layers ，安全套接层）和 TLS （ Transport Layer Security ，传输层安全）协议把通信内容加密后经网络隧道传输|
| PATCH | 更新部分文件内容| 1.1| **当资源存在的时候**，PATCH 用于资源的部分内容的更新，例如更新某一个字段。具体比如说只更新用户信息的电话号码字段，而 PUT 用于更新某个资源较完整的内容，比如说用户要重填完整表单更新所有信息，后台处理更新时可能只是保留内部记录 ID 不变。<br>**当资源不存在的时候**，PATCH 是修改原来的内容，也可能会产生一个新的版本。比如当资源不存在的时候，PATCH 可能会去创建一个新的资源，这个意义上像是 saveOrUpdate 操作。而 PUT 只对已有资源进行更新操作，所以是 update 操作|
| LINK | 建立和资源之间的联系 | 1.0 | ✖︎最新版中已经废弃✖︎|
| UNLINK | 断开连接关系 | 1.0 | ✖︎最新版中已经废弃✖︎|
|  |  |  | |
| PROPFIND | 获取属性 | 1.1 | WebDAV 获取属性|
| PROPPATCH | 修改属性 | 1.1 | WebDAV 修改属性|
| MKCOL | 创建属性 | 1.1 | WebDAV 创建属性|
| COPY | 复制资源及属性 | 1.1 | WebDAV 复制资源及属性|
| MOVE | 移动资源 | 1.1 | WebDAV 移动资源|
| LOCK | 资源加锁 | 1.1 | WebDAV 资源加锁|
| UNLOCK | 资源解锁 | 1.1 | WebDAV 资源解锁|


在HTTP/1.1规范中幂等性的定义是：

>Methods can also have the property of "idempotence" in that (aside from error or expiration issues) the side-effects of N > 0 identical requests is the same as for a single request.

从定义上看，HTTP 方法的幂等性是指一次和多次请求某一个资源应该具有同样的副作用。幂等性属于语义范畴，正如编译器只能帮助检查语法错误一样，HTTP 规范也没有办法通过消息格式等语法手段来定义它，这可能是它不太受到重视的原因之一。但实际上，幂等性是分布式系统设计中十分重要的概念，而 HTTP 的分布式本质也决定了它在 HTTP 中具有重要地位。


HTTP 方法的安全性指的是不会改变服务器状态，也就是说它只是可读的。所以只有 OPTIONS、GET、HEAD 是安全的，其他都是不安全的。

| HTTP 方法 | 幂等性 | 安全性 |
| :---: | :---: | :---: |
|OPTIONS|	yes	|yes|
|GET	|yes	|yes|
|HEAD	|yes	|yes|
|PUT	|yes	|no|
|DELETE	|yes	|no|
|POST	|no	|no|
|PATCH	|no	|no|

**POST 和 PATCH 这两个不是幂等性的**。  
两次相同的POST请求会在服务器端创建两份资源，它们具有不同的URI。  
对同一URI进行多次PUT的副作用和一次PUT是相同的。  


## 二. HTTP 状态码


服务器返回的  **响应报文**  中第一行为状态行，包含了状态码以及原因短语，用来告知客户端请求的结果。

| 状态码 | 类别 | 原因短语 |
| :---: | :---: | :---: |
| 1XX | Informational（信息性状态码） | 接收的请求正在处理 |
| 2XX | Success（成功状态码） | 请求正常处理完毕 |
| 3XX | Redirection（重定向状态码） | 需要进行附加操作以完成请求 |
| 4XX | Client Error（客户端错误状态码） | 服务器无法处理请求 |
| 5XX | Server Error（服务器错误状态码） | 服务器处理请求出错 |




### 1XX 信息

-  **100 Continue** ：表明到目前为止都很正常，客户端可以继续发送请求或者忽略这个响应。

### 2XX 成功

-  **200 OK** 

-  **204 No Content** ：请求已经成功处理，但是返回的响应报文不包含实体的主体部分。一般在只需要从客户端往服务器发送信息，而不需要返回数据时使用。

-  **206 Partial Content** ：表示客户端进行了范围请求。响应报文包含由 Content-Range 指定范围的实体内容。

### 3XX 重定向

-  **301 Moved Permanently** ：永久性重定向

-  **302 Found** ：临时性重定向

-  **303 See Other** ：和 302 有着相同的功能，但是 303 明确要求客户端应该采用 GET 方法获取资源。

- 注：虽然 HTTP 协议规定 301、302 状态下重定向时不允许把 POST 方法改成 GET 方法，但是大多数浏览器都会在 301、302 和 303 状态下的重定向把 POST 方法改成 GET 方法。

-  **304 Not Modified** ：如果请求报文首部包含一些条件，例如：If-Match，If-ModifiedSince，If-None-Match，If-Range，If-Unmodified-Since，如果不满足条件，则服务器会返回 304 状态码。

-  **307 Temporary Redirect** ：临时重定向，与 302 的含义类似，但是 307 要求浏览器不会把重定向请求的 POST 方法改成 GET 方法。

### 4XX 客户端错误

-  **400 Bad Request** ：请求报文中存在语法错误。

-  **401 Unauthorized** ：该状态码表示发送的请求需要有认证信息（BASIC 认证、DIGEST 认证）。如果之前已进行过一次请求，则表示用户认证失败。

-  **403 Forbidden** ：请求被拒绝，服务器端没有必要给出拒绝的详细理由。

-  **404 Not Found** 

### 5XX 服务器错误

-  **500 Internal Server Error** ：服务器正在执行请求时发生错误。

-  **503 Service Unavilable** ：服务器暂时处于超负载或正在进行停机维护，现在无法处理请求。

------------------------------------------------------------

## RFC 2616 状态码

| 状态码 | 类别 | 原因短语 |含义||
| :---: | :---: | :---: |:---: |:---:|
| 100 | Informational（信息性状态码） | Continue（继续） |收到了请求的起始部分，客户端应该继续请求。|❤|
| 101 | Informational（信息性状态码） | Switching Protocols（切换协议）|服务器正根据客户端的指示将协议切换成 Update 首部列出的协议。|❤|
|||||
| 200 | Success（成功状态码）| OK |服务器已成功处理请求|❤|
| 201 | Success（成功状态码）|Created（已创建）| 对那些要服务器创建对象的请求来说，资源已创建完毕|
| 202 | Success（成功状态码）|Accepted（已接受）| 请求已接受，但服务器尚未处理|
| 203 | Success（成功状态码）|Non-Authoritative Information（非权威信息）| 服务器已将事务成功处理，只是实体首部包含的信息不是来自原始服务器，而是来自资源的副本|
| 204 | Success（成功状态码）|No Content（没有内容）| 响应报文包含一些首部和一个状态行，**但不包含实体的主体内容**，**一般在只需要从客户端往服务器发送信息，而对客户端不需要发送新信息内容的情况下使用**|❤|
| 205 | Success（成功状态码）|Reset Content（重置内容）| 另一个主要用于浏览器的代码。意思是浏览器应该重置当前页面上所有的 HTML 表单 |
| 206 | Success（成功状态码） |Partial Content（部分内容）| 成功执行了一个部分或者 Range (范围)请求，客户端可以通过一些特殊的首部来获取部分或某个范围内的文档<br>**响应报文中包含由 Content-Range、Date、以及 ETag 或者 Content-Location 指定范围的实体内容**|❤|
|||||
|300| Redirection（重定向状态码） |Multiple Choices（多项选择）| 客户端请求了实际指向多个资源的 URL。这个代码是和一个选项列表一起返回的，然后用户就可以选择他希望使用的选项了。服务器可以在 Location 首部包含首选 URL| 
|301| Redirection（重定向状态码） | Moved Permanently（永久移除）| **永久性重定向**，请求的 URL 已移走。响应中应该包含一个 Location URL，说明资源现在所处的位置|❤|
|302| Redirection（重定向状态码）| Found（已找到）| **临时性重定向**，与状态码 301 类似， 但这里的移除是临时的。客户端应该用 Location 首部给出的 URL 对资源进行临时定位|❤|
|303| Redirection（重定向状态码）| See Other（参见其他）| 告诉客户端应该用另一个 URL 获取资源。这个新的 URL 位于响应报文的 Location 首部。303 状态码 和 302 状态码有相同的功能，**但是 303 明确表示客户端应采用 GET 方法获取资源**。|❤|
||||当 301、302、303 响应状态码返回时，几乎所有的浏览器都会把 POST 改成 GET，并删除请求报文内的主体，之后请求会自动再次发送。<br>301、302 标准是禁止将 POST 方法改变成 GET 方法的，但实际使用时大家都会这么做||
|304| Redirection（重定向状态码）| Not Modified（未修改）| 该状态码表示客户端发送附带条件的请求时，服务器允许请求访问资源，但因发生请求未满足条件的情况后，直接返回 304 Not Modified（服务器端资源未改变，可直接使用客户端未过期的缓存）304 状态码返回时，不包含任何响应的主体部分。**304 虽然被划分在 3XX 类别中，但是和重定向一点关系也没有**|❤|
||||（附带条件的请求是指采用 GET 方法的请求报文中包含 If-Match，If-Modified-Since,If-None-Match，If-Range，If-Unmodified-Since 中任一首部）||
|305| Redirection（重定向状态码）| Use Proxy（使用代理）| 必须通过代理访问 资源，代理的位置是在 Location 首部中给出的|
|306|（未使用）||这个状态码当前并未使用|
|307| Redirection（重定向状态码）| Temporary Redirect（临时重定向）| 和状态码 302 类似。但客户端应该用 Location 首部给出的 URL 对资源进行临时定位。<br>307 会遵守浏览器标准，不会从 POST 变成 GET|❤|
|||||
|400|Client Error（客户端错误状态码）| Bad request（坏请求）| 告诉客户端它发送了一条异常请求|❤|
|401|Client Error（客户端错误状态码）| Unauthorized（未授权）| 与适当的首部一起返回，在客户端获得资源访问权之前，请它进行身份认证|❤|
|402|Client Error（客户端错误状态码）| Payment Required（要求付款）| 当前此状态码并未使用，是为未来使用预留的 |
|403|Client Error（客户端错误状态码）| Forbidden（禁止）| 服务器拒绝了请求|❤| 
|404|Client Error（客户端错误状态码）| Not Found（未找到）| 服务器无法找到 所请求的 URL|❤|
|405|Client Error（客户端错误状态码）| Method Not Allowed（不允许使用的方法）|请求中有一个所请求的 URI 不支持的方法。响应中应该包含一个 Allow 首部，以告知客户端所请求的资源支持使用哪些方法| 
|406|Client Error（客户端错误状态码）| Not Acceptable（无法接受）| 客户端可以指定一些参数来说明希望接受哪些类型的实体。服务器没有资源与客户端可接受的 URL 相匹配时可使用此代码| 
|407|Client Error（客户端错误状态码）| Proxy Authentication Required（要求进行代理认证）|和状态码 401 类似，但用于需要进行资源认证的代理服务器|
|408|Client Error（客户端错误状态码）| Request Timeout（请求超时）| 如果客户端完成其请求时花费的时间太长，服务器可以回送这个状态码并关闭连接 |
|409|Client Error（客户端错误状态码）| Conflict（ 冲突）| 发出的请求在资源上造成了一些冲突| 
|410|Client Error（客户端错误状态码）| Gone（消失了）| 除了服务器曾持有这些资源之外，与状态码 404 类似 |
|411|Client Error（客户端错误状态码）| Length Required（要求长度指示）| 服务器要求在请求报文中包含 Content- Length 首部时会使用这个代码。发起的请求中若没有 Content-Length 首部，服务器 是不会接受此资源请求的| 
|412|Client Error（客户端错误状态码）|Precondition Failed（先决条件失败）| 如果客户端发起了一个条件请求， 如果服务器无法满足其中的某个条件，就返回这个响应码| 
|413|Client Error（客户端错误状态码）| Request Entity Too Large（请求实体太大）| 客户端发送的实体主体部分比 服务器能够或者希望处理的要大|
|414|Client Error（客户端错误状态码）| Request URI Too Long（请求 URI 太长）| 客户端发送的请求所携带的请求 URL 超过了服务器能够或者希望处理的长度|
|415 |Client Error（客户端错误状态码）|Unsupported Media Type（不支持的媒体类型）| 服务器无法理解或不支持客户端所发送的实体的内容类型| 
|416 |Client Error（客户端错误状态码）|Requested Range Not Satisfiable（所请求的范围未得到满足）| 请求报文请求的是某范围内的指定资源，但那个范围无效，或者未得到满足 |
|417|Client Error（客户端错误状态码）| Expectation Failed（无法满足期望）| 请求的 Expect 首部包含了一个预期内容，但服务器无法满足||
|||||
|500|Server Error（服务器错误状态码）| Internal Server Error（内部服务器错误）| 服务器遇到了一个错误，使其无法为请求提供服务|❤|
|501 |Server Error（服务器错误状态码）|Not Implemented（未实现）| 服务器无法满足客户端请求的某个功能 |
|502 |Server Error（服务器错误状态码）|Bad Gateway（网关故障）| 作为代理或网关使用的服务器遇到了来自响应链中上游的无效响应 |
|503|Server Error（服务器错误状态码）| Service Unavailable（未提供此服务）| 服务器目前无法为请求提供服务，但过一段时间就可以恢复服务|❤|
|504|Server Error（服务器错误状态码） |Gateway Timeout（网关超时）| 与状态码 408 类似，但是响应来自网关或代理，此网关或代理在等待另一台服务器的响应时出现了超时 |
|505|Server Error（服务器错误状态码）| HTTP Version Not Supported（不支持的 HTTP 版本）| 服务器收到的请求是以它不支持或不愿支持的协议版本表示的|

>在 RFC2616 中定义了 40 种 HTTP 状态码，webDAV ( Web-based Distributed Authoring and Versioning，基于万维网的分布式创作和版本控制)在 RFC4918 和 RFC5842 中，定义了一些特殊的状态码，在 RFC2518、RFC2817、RFC2295、RFC2774、RFC6585 中还额外定义了一些附加的 HTTP 状态码。总共有 60+ 种。具体链接可以见 [HTTP状态码 (wikipedia)](https://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81)


webDAV 新增状态码

| 状态码 | 类别 | 原因短语 |含义||
| :---: | :---: | :---: |:---: |:---:|
| 102 | Informational（信息性状态码） | Processing（处理中）|可正常处理请求，但目前是处理中状态。WebDAV请求可能包含许多涉及文件操作的子请求，需要很长时间才能完成请求。该代码表示​​服务器已经收到并正在处理请求，但无响应可用。这样可以防止客户端超时，并假设请求丢失。||
| 207 | Success（成功状态码） | Multi-Status（多种状态）| 存在多种状态。代表之后的消息体将是一个 XML 消息，并且可能依照之前子请求数量的不同，包含一系列独立的响应代码。||
| 208 | Success（成功状态码） | Already Reported（已经响应）| DAV绑定的成员已经在（多状态）响应之前的部分被列举，且未被再次包含。||
|422|Client Error（客户端错误状态码）| Unprocessable Entity（不可处理的实体）| 格式正确，内容有误，无法处理响应||
|423|Client Error（客户端错误状态码）| Locked（被锁定）| 资源已被加锁||
|424|Client Error（客户端错误状态码）| Failed Dependency（失败的依赖）| 处理与某请求关联的请求失败，因为不再维持依赖关系。||
|507|Server Error（服务器错误状态码）| Insufficient Storage（存储空间不足）| 服务器无法存储完成请求所必须的内容。这个状况被认为是临时的。||
|508|Server Error（服务器错误状态码）| Loop Detected（检测到环）| 服务器在处理请求时陷入死循环。|

## 三. MIME 媒体内容


HTTP 仔细地给每种要通过 Web 传输的对象都打上了名为 MIME 类型（MIME type）的数据格式标签。最初设计 MIME（Multipurpose Internet Mail Extension，多用途因特网邮件扩展）是为了解决在不同的电子邮件系统之间搬移报文时存在的问题。MIME 在电子邮件系统中工作得非常好，因此 HTTP 也采纳了它，用它来描述并标记多媒体内容。


RFC2045，“ MIME: Format of Internet Message Bodies”（“ MIME：因特网报文主体的格式”）


常见的主 MIME 类型

| 类型| 描述 | 
| :---: | :---: |
|application |应用程序特有的内容格式（离散类型）|
|audio| 音频格式（离散类型） |
|chemical |化学数据集（离散 IETF 扩展类型）| 
|image| 图片格式（离散类型） |
|message |报文格式（复合类型）| 
|model| 三维模型格式（离散 IETF 扩展类型）| 
|multipart |多部分对象集合（复合类型）|
| text |文本格式（离散类型）| 
|video |视频电影格式（离散类型）|




## 四. HTTP 报文结构

<p align='center'>
<img src='../images/HTTP 报文结构.png'>
</p>


<p align='center'>
<img src='../images/请求报文.png'>
</p>


<p align='center'>
<img src='../images/响应报文.png'>
</p>


<p align='center'>
<img src='../images/报文实例.png'>
</p>


举个例子：


```

General:

Request URL: https://github.com/halfrost
Request Method: GET
Status Code: 200 OK
Remote Address: 127.0.0.1:6152
Referrer Policy: no-referrer-when-downgrade


```


Response Headers:

<p align='center'>
<img src='../images/HTTP_ResponseMessageExample.png'>
</p>


```  

HTTP/1.1 200 OK
Date: Sun, 22 Apr 2018 15:47:27 GMT
Content-Type: text/html; charset=utf-8
Transfer-Encoding: chunked
Server: GitHub.com
Status: 200 OK
Cache-Control: no-cache
Vary: X-Requested-With
Set-Cookie: user_session=GYkmjrs9T6H9r16Gx85; path=/; expires=Sun, 06 May 2018 15:47:27 -0000; secure; HttpOnly
Set-Cookie: __Host-user_session_same_site=GYkmjre6H9r16Gx85; path=/; expires=Sun, 06 May 2018 15:47:27 -0000; secure; HttpOnly; SameSite=Strict
Set-Cookie: _gh_sess=OHppNS84T05ubXZFS2swUm9SUlBqdXNpWlA2bHZZ3alUyUGNLZ0pqMD0tLTNLWDI0K1pTUUFlaWJUVU5XUTJaNFE9PQ%3D%3D--74346822d2bf179f6ff73ce52c8b8606c8f78755; path=/; secure; HttpOnly
X-Request-Id: 855feee9-5be2-482f-911a-b0eb22d55088
X-Runtime: 0.170448
Strict-Transport-Security: max-age=31536000; includeSubdomains; preload
X-Frame-Options: deny
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Referrer-Policy: origin-when-cross-origin, strict-origin-when-cross-origin
Expect-CT: max-age=2592000, report-uri="https://api.github.com/_private/browser/errors"
Content-Security-Policy: default-src 'none'; base-uri 'self'; block-all-mixed-content; child-src render.githubusercontent.com; connect-src 'self' uploads.github.com status.github.com collector.githubapp.com api.github.com www.google-analytics.com github-cloud.s3.amazonaws.com github-production-repository-file-5c1aeb.s3.amazonaws.com github-production-upload-manifest-file-7fdce7.s3.amazonaws.com github-production-user-asset-6210df.s3.amazonaws.com wss://live.github.com; font-src assets-cdn.github.com; form-action 'self' github.com gist.github.com; frame-ancestors 'none'; img-src 'self' data: assets-cdn.github.com identicons.github.com collector.githubapp.com github-cloud.s3.amazonaws.com *.githubusercontent.com; manifest-src 'self'; media-src 'none'; script-src assets-cdn.github.com; style-src 'unsafe-inline' assets-cdn.github.com
X-Runtime-rack: 0.175479
Content-Encoding: gzip
Vary: Accept-Encoding
X-GitHub-Request-Id: B706:3019:355B8D9:52B9B00:5ADCAE85


```


Request Headers:


<p align='center'>
<img src='../images/HTTP_RequestMessageExample.png'>
</p>


```

GET /halfrost HTTP/1.1
Host: github.com
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/66.0.3359.117 Mobile Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Referer: https://github.com/halfrost/Halfrost-Field/blob/master/contents/Protocol/HTTP.md
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8
Cookie: _octo=GH1.1.101205900.1486965233; logged_in=yes; dotcom_user=halfrost; _ga=GA1.2.183217117.1486965233; user_session=GYkmjrs9Ts80x85; __Host-user_session_same_site=GYkmjrs9THGx85; tz=Asia%2FShanghai; _gat=1; _gh_sess=S1JyM0tEbTVEcU50OXRERmUwOVlqRVZiQWp5SDlBeWt3RitrbEczRkxjaWVLWWNVc2k4YjhBTDVQT3BZajEwSGRJOEE2bz0tLVNLRHhiTlVDN2xEUXJ1OFM1ME1VeVE9PQ%3D%3D--59dc56a889d38d30125fbee36df9dab97e7a46c0


```


请求报文是由请求方法，请求 URI，协议版本，可选请求首部字段和内容实体构成的。

响应报文基本上由协议版本，状态码（表示请求成功与失败的数字代码），用以解释状态码的原因短语，可选的响应首部字段以及实体主体构成。


### 1. 通用首部

| 首部 | 描述 | 
| :---: | :---: |
|Cache-Control|控制缓存的行为，用于随报文传送缓存的指示|
|Connection| 允许客户端和服务器指定与请求/响应连接有关的选项| 
|Date| 提供日期和时间标志，说明报文是什么时间创建的 |
|Pragma|报文指令，另一种随报文传送指示的方式，但并不专用于缓存。Pragma 是 HTTP/1.1 之前版本的历史遗留字段，仅作为与 HTTP/1.0 的向后兼容而定义。如果想要所有的服务器保持相同的行为，可以考虑发送 Pragma 指令。例如：Pragma: no-cache Cache-Control: no-cache|
|MIME-Version |给出了发送端使用的 MIME 版本 |
|Trailer| 如果报文采用了分块传输编码（chunked transfer encoding）方式，就可以用这个首部列出位于报文拖挂（trailer）部分的首部集合 |
|Transfer- Encoding |告知接收端为了保证报文的可靠传输，对报文采用了什么编码方式 |
|Update| 给出了发送端可能想要 “升级” 使用的新版本或协议|
|Via |显示了报文经过的中间节点（代理、网关）|
|Warning| 错误通知|



Cache-Control 首部功能很强大。服务器和客户端都可以用它来说明新鲜度，并且除了使用期或过期时间之外，还有很多指令可用。 


| 指令 | 参数 | 报文类型 | 说明 | 
| :---: | :---: | :---: | :---: |
|no-cache ||请求| 在重新向服务器验证之前，不要返回文档的缓存副本 |
|no-store|| 请求| 不要返回文档的缓存副本。不要保存服务器的响应 |
|max-age = [秒]|必须| 请求 |缓存中的文档不能超过指定的使用期 |
|max-stale ( = [秒]) |可省略|请求| 文档允许过期（根据服务器提供的过期信息计算），但不能超过指令中指定的过期值 |
|min-fresh = [秒]|必须|请求| 文档的使用期不能小于这个指定的时间与它的当前存活时间之和。换句话说，响应必须至少在指定的这段时间之内保持新鲜 |
|no-transform|| 请求| 文档在发送之前不允许被转换 |
|only-if-cached ||请求| 只有当文档在缓存中才发送，不要联系原始服务器 |
|cache-extension||请求|新指令标记（token）|
|||||
|public ||响应| 响应可以被任何服务器缓存 |
|private |可省略|响应 |响应可以被缓存，但只能被单个客户端访问 |
|no-cache |可省略|响应 |如果该指令伴随一个首部列表的话，那么内容可以被缓存并提供给客户端，但必须先删除所列出的首部。如果没有指定首部，缓存中的副本在没有重新向服务器验证之前不能提供给客户端 |
|no-store ||响应 |响应不允许被缓存 |
|no-transform ||响应| 响应在提供给客户端之前不能做任何形式的修改 |
|must-revalidate ||响应| 响应在提供给客户端之前必须重新向服务器验证 |
|proxy-revalidate ||响应 |共享的缓存在提供给客户端之前必须重新向原始服务器验证。私有的缓存可以忽略这条指令|
| max-age = [秒]|必须|响应| 指定文档可以被缓存的时间以及新鲜度的最长时间|
|s-max-age = [秒] |必须|响应 |指定文档作为共享缓存时的最长使用时间（如果有 max-age 指令的话，以本指令为准）。私有的缓存可以忽略本指令|
|cache-extension||响应|新指令标记（token）|



> no-cache 和 no-store 的区别：no-cache 代表不缓存过期的资源，缓存会向源服务器进行有效期确认后再处理资源。no-store 才是真正的不缓存。


no-cache 并不代表完全的禁用缓存，而是代表会每次去核对服务端的 Etag，如果相同，那么就不会去服务端下载完整的资源，返回一个 304 Not Modified。（最长缓存 3 年）

no-store 才是真正的禁用缓存，它表示每次服务端都会去下载最新的资源。（当然，通常似乎都用不上）。

public 和 private 的差别主要在于如果是有用户认证环节的页面，设置为private 就只有终端浏览器会缓存，中间 CDN 并不会缓存，而设置为 public，则会在每一个环节缓存。默认不需要设置 public，因为 max-age 已经表明可以由各个环节缓存了（单位为秒）。此刻如果命中缓存，则不会再去请求服务器核对 Etag，而是直接返回 200(from disk)。

当然，由于 public 会在每一个环节缓存，如果对修改更新预览又强需求的网页，那么最好不要使用这一缓存策略，否则还需要刷新 CDN 源，很麻烦。

如果挑选缓存策略，可以见下图：

<p align='center'>
<img src='../images/cache-control.png'>
</p>

------------------------------------------------------------------


Warning 首部是从 HTTP/1.0 的响应首部（Retry-After）演变过来的。该首部通常会告知用户一些与缓存相关的问题的警告。

Warning 首部的格式如下：

```
Warning: [警告码][警告的主机：端口号]"[警告内容]"([日期内容])

```

HTTP/1.1 中定义了 7 种警告，警告码具备扩展性，今后可以能追加新的警告码。

| 警告码 | 警告内容 | 说明 |
| :---: | :---: | :---: |
|110|Response is stale（响应已过期）| 代理返回已过期的资源|
|111|Revalidation failed（再验证失败）|代理再验证资源有效性时失败（服务器无法到达等原因）|
|112|Disconnection operation（断开连接操作）| 代理与互联网连接被故意切断|
|113|Heuristic expiration（试探性过期）|响应的使用期超过 24 小时（有效缓存的设定时间大于 24 小时的情况下）|
|199|Miscellaneous warning（杂项警告）|任意的警告内容|
|214|Transformation applied（使用了转换）|代理对内容编码或媒体类型等执行了某些处理时|
|299|Miscellaneous persistent warning（持久杂项警告）| 任意的警告内容|


### 2. 请求首部

### 请求信息性首部

| 首部 | 描述 | 
| :---: | :---: |
|Client-IP4| 提供了运行客户端的机器的 IP 地址|
|From| 提供了客户端用户的 E-mail 地址 |
|Host |给出了接收请求的服务器的主机名和端口号 |
|Referer |提供了包含当前请求 URI 的文档的 URL（正确的拼写其实应该是Referrer ，大家一致沿用错误至今） |
|UA-Color |提供了与客户端显示器的显示颜色有关的信息 |
|UA-CPU |给出了客户端 CPU 的类型或制造商 |
|UA-Disp |提供了与客户端显示器（屏幕）能力有关的信息| 
|UA-OS |给出了运行在客户端机器上的操作系统名称及版本 |
|UA-Pixels |提供了客户端显示器的像素信息 |
|User-Agent |将发起请求的应用程序名称告知服务器|

### Accept 首部

| 首部 | 描述 | 
| :---: | :---: |
|Accept |告诉服务器能够发送哪些媒体类型 |
|Accept- Charset |告诉服务器能够发送哪些字符集 |
|Accept- Encoding |告诉服务器能够发送哪些编码方式 |
|Accept- Language |告诉服务器能够发送哪些语言 |
|TE | 告诉服务器可以使用哪些扩展传输的编码|


常见内容编码

常用的内容编码有以下几种：

- gzip（GNU zip）  
  由文件压缩程序 gzip（GNU zip）生成的编码格式（RFC1952），采用 Lempel-Ziv 算法（LZ77）及 32 位循环冗余校验（Cyclic Redundancy Check，统称 CRC）
- compress（UNIX 系统的标准压缩）  
  由 UNIX 文件压缩程序 compress 生成的编码格式，采用 Lempel-Ziv-Welch 算法 （LZW）
- deflate（zlib）  
  组合使用 zlib 格式（RFC1950）及由 deflate 压缩算法（RFC1951）生成的编码格式
- identity（不进行编码）  
  不执行压缩或不会变化的默认编码格式


### 条件请求首部

| 首部 | 描述 | 
| :---: | :---: |
|Expect |允许客户端列出某请求所要求的服务器行为 |
|If-Match| 如果实体标记与文档当前的实体标记相匹配，就获取这份文档 |
|If-Modified-Since |除非在某个指定的日期之后资源被修改过，否则就限制这个请求 |
|If-None-Match |如果提供的实体标记与当前文档的实体标记不相符，就获取文档 |
|If-Range |允许对文档的某个范围进行条件请求 |
|If-Unmodified-Since| 除非在某个指定日期之后资源没有被修改过，否则就限制这个请求 |
|Range |如果服务器支持范围请求，就请求资源的指定范围|

### 安全请求首部

| 首部 | 描述 | 
| :---: | :---: |
|Authorization |包含了客户端提供给服务器，以便对其自身进行认证的数据 |
|Cookie |客户端用它向服务器传送一个令牌 —— 它并不是真正的安全首部，但确实隐含了安全功能 |
| Cookie | 用来说明请求端支持的 cookie 版本|

 
### 代理请求首部

| 首部 | 描述 | 
| :---: | :---: |
|Max-Forward |在通往源端服务器的路径上，将请求转发给其他代理或网关的最大次数 —— 与 TRACE 方法一同使用 |
|Proxy-Authorization |与 Authorization 首部相同， 但这个首部是在与代理进行认证时使用的 |
|Proxy-Connection |与 Connection 首部相同， 但这个首部是在与代理建立连接时使用的|


### 3. 响应首部

### 响应信息性首部

| 首部 | 描述 | 
| :---: | :---: |
|Age |（从最初创建开始）响应持续时间 |
|Public | 服务器为其资源支持的请求方法列表 |
|Retry-After |如果资源不可用的话，在此日期或时间重试 Server 服务器应用程序软件的名称和版本 |
|Title | 对 HTML 文档来说，就是 HTML 文档 的源端给出的标题 |
|Warning| 比原因短语中更详细一些的警告报文|


### 协商首部

| 首部 | 描述 | 
| :---: | :---: |
|Accept-Ranges |对此资源来说，服务器可接受的范围类型 |
|Vary |服务器查看的其他首部的列表，可能会使响应发生变化；也就是说，这是一个首部列表，服务器会根据这些首部的内容挑选出最适合的资源版本发送给客户端。首部字段 Vary 可对缓存进行控制。源服务器会向代理服务器传达关于本地缓存使用方法的命令。从代理服务器接收到源服务器返回包含 Vary 指定项的响应之后，若再进行缓存，仅对请求中含有相同 Vary 指定首部字段的请求返回缓存。即使对相同资源发起请求，但由于 Vary 指定的首部字段不相同，因此必须要从源服务器重新获取资源。|

### 安全响应首部

| 首部 | 描述 | 
| :---: | :---: |
|Proxy-Authenticate| 来自代理的对客户端的质询列表 |
|Set-Cookie |不是真正的安全首部，但隐含有安全功能；可以在客户端设置一个令牌，以便服务器对客户端进行标识 |
|Set-Cookie2 |与 Set-Cookie 类似，RFC 2965 Cookie 定义； |
|WWW-Authenticate| 来自服务器的对客户端的质询列表。它会告知客户端适用于访问请求 URI 所指定资源的认证方案（Basic 或是 Digest）和带参数提示的质询（challenge）|


Cookie 的 HttpOnly 属性是 Cookie 的扩展功能，它使 JavaScript 脚本无法获得 Cookie。其主要目的为了防止跨站脚本攻击（Cross-site scripting，XSS）对 Cookie 的信息窃取。

```
Set-Cookie: name-value;HttpOnly

```

顺带一提，该扩展并非是为了防止 XSS 而开发的。

### 4. 实体首部


### 实体信息性首部

| 首部 | 描述 | 
| :---: | :---: |
|Allow |列出了可以对此实体执行的请求方法 |
|Location |告知客户端实体实际上位于何处；用于将接收端定向到资源的（可能是新的）位置（URL）上去|


### 内容首部

| 首部 | 描述 | 
| :---: | :---: |
|Content-Base16 |解析主体中的相对 URL 时使用的基础 URL |
|Content-Encoding |对主体执行的任意编码方式 |
|Content-Language| 理解主体时最适宜使用的自然语言 |
|Content-Length |主体的长度或尺寸|
| Content-Location |资源实际所处的位置 |
|Content-MD5 |主体的 MD5 校验和|
| Content-Range |在整个资源中此实体表示的字节范围 |
|Content-Type |这个主体的对象类型|


由于 HTTP 首部无法记录二进制值，所以要通过 Base-64 编码处理。采用 Content-MD5 这种方法，对内容上的偶发性改变是无从查证的，也无法检测出恶意篡改。原因在于，内容如果被篡改了，那么同时意味着 Content-MD5 也可以被重新计算后更新，被篡改。所以处在接收阶段的客户端是无法意识到报文主体以及首部字段 Content-MD5 是已经被篡改过的。

### 实体缓存首部

| 首部 | 描述 | 
| :---: | :---: |
|ETag |与此实体相关的实体标记 |
|Expires |实体不再有效，要从原始的源端再次获取此实体的日期和时间 |
|Last-Modified |这个实体最后一次被修改的日期和时间|


如果两者的 URI 是相同，所以仅凭 URI 指定缓存的资源是很困难的。若下载过程中出现连续中断、再连接的情况，都会依据 ETag 值指定资源。

ETag 也分为强 ETag 值和弱 ETag 值：

强 ETag 值：

强 ETag 值，不论实体发生多少细微的变化都会改变其值。

```  
ETag: "usagi-1234"

```

弱 ETag 值：

弱 ETag 值只用于提示资源是否相同。只有资源发生了根本改变，产生差异时才会改变 ETag 值。这时，会在字段值最开始处附加 W/

```  
ETag: W/"usagi-1234"

```




### 5. 扩展首部


### （1）X-Frame-Options

首部字段 X-Frame-Options 属于 HTTP 响应首部，用于控制网站内容在其他 Web 网站的 Frame 标签内的显示问题。其主要目的是为了防止点击劫持（clickjacking）攻击。

### （2）X-XSS-Protection

首部字段 X-XSS-Protection 属于 HTTP 响应首部，它是针对跨站脚本攻击（XSS）的一种对策，用于控制浏览器 XSS 防护机制的开关。0：将 XSS 过滤设置成无效状态，1：将 XSS 过滤设置成有效状态。

### （3）DNT

首部字段 DNT 属于 HTTP 请求首部，其中 DNT 是 Do Not Track 的简称，意为拒绝个人信息被收集，是表示拒绝被精准广告追踪的一种方法。0：同意被追踪，1：拒绝被追踪。

### （4）P3P

首部字段 P3P 属于 HTTP 响应首部，通过利用 P3P（The Platform for Privacy Preferences，在线隐私偏好平台）技术，可以让 Web 网站上的个人隐私变成一种仅供程序可理解的形式，以达到保护用户隐私的目的。

>在 HTTP 等多种协议中，通过给非标准参数加上前缀 X- ，来区别于标准参数，并使那些非标准的参数作为扩展变成可能。但是这种简单粗暴的做法有百害而无一益，因此在 “RFC6648 - Deprecating the "X-" Prefix and Similar Constructs in Application Protocols ”中提议停止该做法。然而，对已经在使用中的 X- 前缀来说，不应该要求其变更。



HTTP 首部字段将定义成缓存代理和非缓存代理的行为，分为 端到端首部（End-to-end Header）、逐跳首部（Hop-by-hop Header）

- 端到端首部：分在此类别中的首部会转发给请求 / 响应对应的最终接收目标，且必须保存在由缓存生成的相应中，另外规定它必须被转发。
- 逐跳首部：分在此类别中的首部只对单次转发有效，会因通过缓存或代理而不再转发。HTTP/1.1 和之后版本中，如果要使用 hop-by-hop 首部，需提供 Connection 首部字段。（Connection、Keep-Alive、Proxy-Authenticate、Proxy-Authorization、Trailer、TE、Transfer-Encoding、Upgrade 这 8 个首部字段属于逐跳首部，除此以外的字段都属于端到端首部）


## 五. 提高 HTTP 性能

### 1. 并行连接

通过多条 TCP 连接发起并发的 HTTP 请求。

### 2. 持久连接 

重用 TCP 连接，以消除连接及关闭的时延。 持久连接（HTTP Persistent Connections），也称为 HTTP keep-alive 或者 HTTP connection reuse 。

在 HTTP/1.1 中，所有的连接默认都是持久连接。但是服务器端不一定都能够支持持久连接，所以除了服务端，客户端也需要支持持久连接。


### 3. 管道化连接 

通过共享的 TCP 连接发起并发的 HTTP 请求。

持久连接使得多数请求以管线化（pipelining）方式发送成为可能。以前发送请求后需要等待并收到响应，才能发送下一个请求。管线化技术出现后，不用等待响应，直接发送下一个请求。

比如当请求一个包含 10 张图片的 HTML Web 页面，与挨个连接相比，用持久连接可以让请求更快结束。而管线化技术则比持久连接还要快。请求数越多，时间差就越明显。


### 4. 复用的连接

交替传送请求和响应报文（实验阶段）。




## 六. GET 和 POST 的区别

## 参数

GET 和 POST 的请求都能使用额外的参数，但是 GET 的参数是以查询字符串出现在 URL 中，而 POST 的参数存储在内容实体中。

GET 的传参方式相比于 POST 安全性较差，因为 GET 传的参数在 URL 中是可见的，可能会泄露私密信息。并且 GET 只支持 ASCII 字符，如果参数为中文则可能会出现乱码，而 POST 支持标准字符集。

```
GET /test/demo_form.asp?name1=value1&name2=value2 HTTP/1.1
```

```
POST /test/demo_form.asp HTTP/1.1
Host: w3schools.com
name1=value1&name2=value2
```

## 安全

安全的 HTTP 方法不会改变服务器状态，也就是说它只是可读的。

GET 方法是安全的，而 POST 却不是，因为 POST 的目的是传送实体主体内容，这个内容可能是用户上传的表单数据，上传成功之后，服务器可能把这个数据存储到数据库中，因此状态也就发生了改变。

安全的方法除了 GET 之外还有：HEAD、OPTIONS。

不安全的方法除了 POST 之外还有 PUT、DELETE。

## 幂等性

幂等的 HTTP 方法，同样的请求被执行一次与连续执行多次的效果是一样的，服务器的状态也是一样的。换句话说就是，幂等方法不应该具有副作用（统计用途除外）。在正确实现的条件下，GET，HEAD，PUT 和 DELETE 等方法都是幂等的，而 POST 方法不是。所有的安全方法也都是幂等的。

GET /pageX HTTP/1.1 是幂等的。连续调用多次，客户端接收到的结果都是一样的：

```
GET /pageX HTTP/1.1
GET /pageX HTTP/1.1
GET /pageX HTTP/1.1
GET /pageX HTTP/1.1
```

POST /add_row HTTP/1.1 不是幂等的。如果调用多次，就会增加多行记录：

```
POST /add_row HTTP/1.1
POST /add_row HTTP/1.1   -> Adds a 2nd row
POST /add_row HTTP/1.1   -> Adds a 3rd row
```

DELETE /idX/delete HTTP/1.1 是幂等的，即便是不同请求之间接收到的状态码不一样：

```
DELETE /idX/delete HTTP/1.1   -> Returns 200 if idX exists
DELETE /idX/delete HTTP/1.1   -> Returns 404 as it just got deleted
DELETE /idX/delete HTTP/1.1   -> Returns 404
```

## 可缓存

如果要对响应进行缓存，需要满足以下条件：

1. 请求报文的 HTTP 方法本身是可缓存的，包括 GET 和 HEAD，但是 PUT 和 DELETE 不可缓存，POST 在多数情况下不可缓存的。
2. 响应报文的状态码是可缓存的，包括：200, 203, 204, 206, 300, 301, 404, 405, 410, 414, and 501。
3. 响应报文的 Cache-Control 首部字段没有指定不进行缓存。

## XMLHttpRequest

为了阐述 POST 和 GET 的另一个区别，需要先了解 XMLHttpRequest：

> XMLHttpRequest 是一个 API，它为客户端提供了在客户端和服务器之间传输数据的功能。它提供了一个通过 URL 来获取数据的简单方式，并且不会使整个页面刷新。这使得网页只更新一部分页面而不会打扰到用户。XMLHttpRequest 在 AJAX 中被大量使用。

在使用 XMLHttpRequest 的 POST 方法时，浏览器会先发送 Header 再发送 Data。但并不是所有浏览器会这么做，例如火狐就不会。





------------------------------------------------------

Reference：  
《图解 HTTP》  
《HTTP 权威指南》  
RFC2616

> GitHub Repo：[Halfrost-Field](https://github.com/halfrost/Halfrost-Field)
> 
> Follow: [halfrost · GitHub](https://github.com/halfrost)
>
> Source: []()