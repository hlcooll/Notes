## TCP/IP协议族(二) HTTP报文头解析

[原文地址链接](https://mp.weixin.qq.com/s?__biz=MzI3MjUxNzkxMw==&mid=2247483925&idx=1&sn=e201adb363a7f4b357981d8d1faef4f4&chksm=eb301c63dc479575308b1c7685ea91ffa7a99cc0fafff451d1c6fc704e568c8c2d5e0beb1155&mpshare=1&scene=1&srcid=&sharer_sharetime=1565777807878&sharer_shareid=9c5a3d8a04ee886a1949cf0328e23b60&key=830c786f292784e55aff830bb82c07c0e1e0673e64d69465fafea077f87d5fd81681f07f09a38586fd1192303de1281c786c29707edcd9d456d072054124095622733c09740195908611ec90c76a654a&ascene=1&uin=MjY5MDU3MTgyOA%253D%253D&devicetype=Windows+10&version=62060834&lang=zh_CN&pass_ticket=yWUZwjuyKyRCTSu0v5m9CV4yaG7Md2j9JL4wzHKMDoKbdNaNaQf9plFJlk%252Blff3I)

本篇博客我们就来详细的聊一下HTTP协议的常用头部字段，当然我们将其分为请求头和响应头进行阐述。下方是报文头每个字段的格式，首先是头部字段的名称，如Accept，冒号后方紧跟的是该字段名所对应的值，每个值之间有逗号分隔。如果该值需要优先级，那么在值的后方跟上优先级q=0.8(q的值由0~1，优先级从低到高)。值与优先级中间由分号相隔。

> 头部字段名：值1, 值2;q=0.8

下方就是截取的网络请求中Request Headers的部分内容。红框中的Accept-Language就是头部字段名，冒号后边就是该字段相应的值了。如下所示： 

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQne2EUDLib2SIbt1K8h6iatpUa7ULSf0LgyVsZiaBlnnBhTYZL7B2c3gRDQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

HTTP头部字段可以分为通用头部字段，请求头部字段，响应头部字段以及实体头部字段，下方会给出详细的介绍。

**一.通用头部字段 （General Header Fields）**

该字段在请求头和响应头都会使用到，下方是常用的通用头部字段：

**1、Cache-Control**

用来操作缓存的工作机制，下方截图响应头中的的Cache-Control的参数为private和max-age=10。private缓存是私有的，仅像特定用户提供相应的缓存信息。如果是public，那么就意味着可向任意方提供相应的缓存信息。max-age = 10表示缓存有效期为10秒。从下方的Expires(过期时间)和Last-Modified(最后修改时间)就可以看出，这两者之间的差值正好是10秒。

该字段还可以对应其他的参数：

  no-cache：如果是客户端的话，说明客户端不会接收缓存过的响应，要请求最新的内容。而服务器端则表示缓存服务器不能对相应的资源进行缓存。
     
no-store：表示缓存不能在本地存储。
   
max-age：该参数后方会被赋值上相应的秒数，在请求头中表示如果缓存时间没有超过这个值就返回给我。而在响应头中时，则表示资源在缓存服务器中缓存的最大时间。

 only-if-cached：表示客户端仅仅请求缓存服务器上的内容，如果缓存服务器上没有请求的内容，那么返回504 Gateway Timeout。
  
 must-revalidata：表示缓存服务器在返回资源是，必须向资源服务器确认其缓存的有效性。
  
no-transform：无论请求还是响应，都不能在传输的过程中改变报文体的媒体类型。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnMyL9yjqUEobjsqhRFm2YbWkuh8zZibyTHH4DW4OGmVg3v7HCxK3LLTQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**2、Connection**

该字段可以控制不转发给代理服务器的首部字段以及管理持久连接，下方这个响应报文头中的Connection就是用来管理持久连接的，其参数为keep-alive，就是保持持久连接的意思。可以使用close参数将其关闭。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnsaQ1Aa3jORib6Sqgibq7OrmkOXM5GONhZxlQCeZVRRF8jZsiavqYYm1ag/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**3、Transfer-Encoding**

该字段表示报文在传输过程中采用的编码方式，在HTTP/1.1的报文传输过程中仅对分块编码有效。下方这个截图就是Transfer-Encoding在Response Header中的使用，后边根的chunked(分块)的参数，说明报文是分块进行传输的。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnnWZDLzZOzyOU8WB5bIQXzMDI75oiajFf6cpCeNgcmYiaxA0ylBk74guQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**4、Via**

该字段是为了追踪请求和响应报文测传输路径，报文经过代理或者网关是会在Via字段添加该服务器的信息，然后再进行转发。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQn3j1LGzp6vTgKpEicLJ3icNjXZWmQA7edCleMserQBdibh7RicgvNIcdfiag/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**二.请求头部字段 （Request Header Fields）**

顾名思义，请求头部字段当然是在请求头中才使用的字段。该字段用于补充请求的附加信息，客户端信息等。接下来将给出常用而且比较重要的几个请求头部字段。

**1  Accept**

该字段可通知服务器用户代理能够处理的媒体类型以及该媒体类型对应的优先级。媒体类型可使用“type/subtype”这种形式来指定，分号后边紧跟着的是该类型的优先级。如下所示。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnkL24KfpujSEJbyHBbMWrBdzkql8BnsduQCzKlzJIUuBibln8XT8ZmRA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**2  Accept-Encoding**

该字段用来告知服务器，客户端这边可支持的内容编码以及相应内容编码的优先级, 下方就是Accept-Encoding的用法。gzip表示由文件压缩程序gzip(GNU zip)生成的编码格式。compress表示UNIX文件压缩程序compress生成的编码格式。deflate表示组合使用zlib格式以及有deflate压缩算法生成的编码格式。identity表示不执行压缩或者使用一致的默认编码格式。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQn4xTLJyAzPxW0gBJeLaRs199Y4Hz6omjQnrpj8yGg4VYsLmuG6m1lUg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**3 Accept-Language**

该字段用来告知服务器，客户端可处理的自然语言集，以及对应语言集的优先级。以下方的截图为例，Accept-Language后方跟了三个属性，分别是“zh-CN”, "zh;q=0.8"，“en;q=0.6”。也就是说客户端可处理三种自然预言集，zh-CN，其优先级是1（最高）。第二种是zh ，其优先级是0.8，次之。第三个是en，优先级为0.6，优先级在三者之间最低。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnbnh1TxPsUQyvDxBs4oZvJxoznia6Sqt7cib0THrvTCjFricvhpNBeX5kQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**4 Authorization**

用来告知服务器用户端的认证信息，下方就是连接公司内部SVN系统时需要认证时的请求头部信息。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnVw3nLfbDcGIOibz1nIaPFWHdgbuI1picPpuwQ45EiavFhtf8FuBWGlDMg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

如果你没有填写认证信息的话，那么就会返回**401 Unauthorized。如下所示：**

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnBATIzricPyG9CsSDaIfGciaHj2EnvQjPzCHMibgdTxgzRAdiczb26jn0VQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**5 If-Match 与If-None-Match**

上面这两个请求头部字段都是带有逻辑判断的，从上面的英文我们不难看出两者恰好相反。两者后方都跟着串字符串，如If-Match "xcsldjh49773hce", 后边这个字符的匹配对象是ETag(稍后会介绍)。If-Match的请求是如果后方的字符串与ETag相等则服务器端进行请求，否则不进行处理。If-None-Match是If-Match的非操作，同样是匹配ETag, 如果Etag没有匹配成功就处理请求，否则不处理。

**6 If-Modified-Since与If-Unmodified-Since**

If-Modified-Since也是带有逻辑判断的请求头部字段，该字段后方跟的是一个日期，意思是在该日期后发生了资源更新，那么服务器就会处理该请求。If-Unmodified-Since就是 If-Modified-Since的非操作。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnR7tic5pvZ5UC1qMomUYSlMAzDe6xt4TINOu6dQAs3WhUrtqRM9tBFUA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**7 If-Range**

if-Range字段后方也是跟的Etag, 该字段要结合着Range字段进行使用。其所代表的意思就是如果Etag匹配成功，请求的内容就按照Range字段所规定的范围进行返回，否则返回全部的内容。用法如下所示：

> If-Range: "etag_code" 
> 
> Range: bytes=1000-5000

**8  Referer **

 其实**Referer**是一个错误的拼写，但是一直在使用。正确的英文单词应该是Referrer(此处可翻译为:来历、来路)。Referer字段后方跟的是一个URI, 该URI就是发起请求的URI，具体如下所示：

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnHnMMve6TLdKptDzARxdpZb2vHyflpupeZxETD6ONQKOvdqncYWncFw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**9  User-Agent**

该字段会将请求方的浏览器和用户代理名称等信息传达给服务器。下方就是从我当前笔记本的Chrome浏览器请求网络时的User-Agent信息。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnxVJY8YrSs9fJTNb0nh4m5NKcCuhbiaicVEWpVSLfpqxia3DBay2ebRJBg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**三.响应头部字段 （Request Header Fields）**

聊完请求报文头部字段后，我们接下来来聊一下响应报文头部字段。响应头是由Server向Client返回响应报文中使用的头部信息。用户补充响应的附加信息、服务信息等。下方是几个常见响应头部字段。

**1  Accept-Ranges**

该字段用来告知客户端服务器那边是否支持范围请求（请求部分内容，请求头中使用Range字段）。Accept-Ranges的值为bytes时，就说明服务器支持范围请求，为none时，说明服务器不支持客户端的范围请求。下方是博客园的页面的加载，从下方可以看出是支持范围请求的，如下所示：

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnbqExp70X4EkULnAFrcOF3RGTcvhQFZX5l8DOS3yYRKymiaYxDr4EdDQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**2 Age**

该字段告知客户端，源服务器在多久前创建了该响应。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQn9ClQxUZuBx5hzWXggd2CFmFLO7ePMFqvTkGHdwkJMdZTt1KiaicnZvsQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**3 Etag**

Etag是服务器当前请求的服务器资源（图片，HTML页面等）所对应的一个独有的字符串。不同资源间的Etag是不同的，当资源更新时Etag也会进行更新。

所以结合着请求头中的If-Match等逻辑请求头，可以判断当前Client端已经加载的资源在服务器端是否已经更新了。当初次请求一个资源，如图片时，我们可以将其Etag进行保存，在此请求时，可放在If-None-Match后方，进行资源更新。如果服务器资源并未修改，就不对该请求做出响应。下方就是网页中某张图片对应着的Etag，如下所示。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnzJWqDMs3SF5NHY6Jba7DRqQmIaRT1WdPRmLfdhThlFgDFTnQP8eAsA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**4 Location**

Location字段一般与重定向结合着使用。下方是我访问“www.baidu.com/hello”这个连接的响应报文。因为服务器上并没有/hello这个资源路径，所以给我重定向了error.html页面，这个重定向的URL就存储在Location字段中，如下所示：

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQncBesvkkYujB8W1SAeqy3a42oZcibeoLwZm8XcNOwcynAh2wCdd71tIA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**5 Server**

该响应字段表明了服务器端使用的服务器型号，下方是博客园某张图片的响应头，使用的Web服务器是Tengine, Tengin是淘宝发起的Web服务器项目，是基于Nginx的，关于Tengin的相关内容，请自行Google吧。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQncgwm0vPPibHiaMF9IK5DibLPwydiakaG8iadwn96HrFt4GsxgE2gXcqnOIA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**6 Vary** 

Vary可对缓存进行控制，通过该字段，源服务器会向代理服务器传达关于本地缓存使用方法的命令。下方就是Vary的使用，Vary后方的参数是Accept-Encoding。其意思是返回的缓存要以Accept-Encoding为准。当请求的Accept-Encoding的参数与缓存内容的Accept-Encoding参数一致时就返回缓存内容，否则就请求源服务器。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnSXBic6xOvQvM3ohxerGG9L0Bnh0kGCibarI3NMRHggwHwuP3rlPEWryg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**7 WWW-Authenticate**

该字段用于HTTP的访问认证，在状态码**401 Unauthorized**中肯定带有此字段，该字段用来指定客户端的认证方案（Basic或者Digest）。参数realm的字符串是为了辨别请求URL指定资源所受到的保护策略。如下所示：

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnBATIzricPyG9CsSDaIfGciaHj2EnvQjPzCHMibgdTxgzRAdiczb26jn0VQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**四、实体头部字段（Content Header Fields）**

 接下来我们就来聊聊常见的实体头部字段，实体头部字段是报文实体所使用的头部，用来补充与报文实体相关的信息。

**1  Allow**

该字段用于服务器通知客户端服务器这边所支持的所有请求方法（GET、POST等）。如果服务器找不到客户端请求中所提到的方法的话，就会返回405 Method Not Allowed，于此同时还会把所有能支持的HTTP方法写入到首部字段Allow后返回。

> Allow : GET, POST, HEAD, PUT, DELETE

**2 Content-Encoding**

该字段用来说明报文实体的编码方式，下方这段报文头中的Content-Encoding的参数为gzip，说明是使用gzip对报文实体进行压缩的。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnVRehdLiaK7zTDjUnNcokFKggGekRnric0SvDIgcWHZ48w2DichI637NEg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**3 Content-Language**

该字段表示报文实体使用的自然语言，使用方式如下所示：

> Content-Language: zh-CN

**5 Content-MD5**

该字段中存储的是报文实体进行MD5加密然后再使用Base64进行编码的字符串。客户端收到响应报文后，可以对报文实体进行MD5加密，然后再对其进行Base64编码，然后与Content-MD5中的字符串进行比较来确定报文是否进行修改，可以说这是一个简单的验签功能。但是此方法并不能确定报文是否被修改了，因为Content-MD5这个值也有可能被篡改。

**五、Cookie相关的头部字段**

因为HTTP协议本身是无状态的，在Web站点中使用Cookie来管理服务器与客户端之间的状态。解析来我就来介绍一下Cookie相关的头部字段。

**1、Set-Cookie**

响应报文中会使用到该字段。当服务器准备开始管理客户端的状态时，会事先告知其各种信息。下方字段是登录知乎时所返回的所要设置的Cookie信息。接下来我们就要对这串Cookie信息进行解析。

> *   键值对：在Set-Cookie字段中，“z_co=Mi4……”这就是要存入Cookie中的信息，当然可以是多个键值对，中间使用逗号进行分割即可。
>     
>     
> *   Domain：然后是Domain属性，由下方不难看出，Domain中存储的就是Cookie适用对象的域名，若不指定Domain的值，那么默认就是创建Cookie的服务器的域名。
>     
>     
> *   expire：该字段属性的值是一个时间，也就是Cookie的有效期，若不指定该属性的值，默认就是当前会话有效，关闭浏览器Cookie即失效。
>     
>     
> *   httponly：设置该属性的目的是让JavaScript脚本无法获取Cookie，其主要目的是防止跨站脚本攻击对Cookie信息的窃取。
>     
>     
> *   path：用于限制指定Cookie的发送范围的文件目录。
>     
>     
> *   Secure：仅在HTTPS安全通信时才会发送Cookie。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQnoanll6VyedjiaqdTibNuKyHZj0nt1H1IlDPAJMtvy7VV0eicZSiadkaicibw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**2.Cookie**

请求报文头中会使用该字段，用于将本地存储的Cookie信息发送给服务端。下方就是知乎上每次请求文章所带有的Cookie信息，当然下方只是部分信息，但是我们还是从中可以找到之前我们存储的“z_co=Mi4……”这个键值对的。

![](https://mmbiz.qpic.cn/mmbiz_png/ECU32j5ZyNP9MZN8t7NjJibS1wpIusLQn2mZUyfiajic299t2AjPtRzLc6wvZFELxuGooANk4OiasUPk3ycga5yDnQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

其他比较常见而且比较简单的头部字段就不做过多赘述了，今天博客就先到这儿吧。