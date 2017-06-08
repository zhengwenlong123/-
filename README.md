网站性能优化总结
=========
* 减少http请求次数
  * 原因
  * 处理方式
* 使用CDN
* 添加expires头
* 压缩组件
  * 压缩是如何工作的
  * 压缩的内容
  * 压缩的配置
  * 代理缓存
  * 边缘情形
* 将样式表放在顶部
* 将脚本放在底部
* 避免css表达式
* 使用外部JavaScript和css
* 减少DNS查询
  * 原因
  * 策略
* 精简JavaScript
* 避免重定向
  * 重定向的定义
  * 重定向的类型
  * 重定向损坏性能分析
* 移除重复脚本
  * 出现重复脚本的原因
  * 重复脚本如何影响性能
  * 如何避免重复脚本
* 配置ETag
  * ETag是什么？
  * 引入ETag的原因
  * ETag带来的问题
  * 应对ETag问题策略
* 使ajax可缓存
  * ajax使用场景
  * 要使ajax可缓存的原因
  * 啥样的ajax可缓存
  * 使用HTML5的新特性来减少不必要的AJAX调用
  
  
==========<br/>
## 1、减少http请求次数 
### 原因 
   据统计，10%~20%的最终用户响应时间花在接收所请求的html文档上，剩下的80%时间花在html文档所引用的所有<br/>
   组件（图片、脚本、样式表、Flash等）进行的http请求上。因此，改善响应时间的最简单途径是减少组件的数量，进而<br/>
   减少http请求的数量。
### 处理方式 
   从页面移除组件的想法必将会导致产性能和产品设计的矛盾。所以，不但要减少http请求，又可以避免在性
   能和设计方面进行艰难的抉择，可以采用如下技术：<br/>
     1）图片地图<br/>
        图片地图允许你在一个图片上关联多个URL，并将超链接关联到图片上，目标URL的选择取决于用户点击了图片的哪个位置上。<br/>
     使用图片地图的技术可以将图片的http请求的次数降低甚至由多次变为一次。比如，有一个导航栏有五张图片，单击一个图片就<br/>
     可以导航到相应的页面或者信息。采用图片地图技术后，可以将五个图片的请求降为一个请求，<br/>
     参考http://stevesouders.com/hpws/imagemap.php。<br/>
     2）css Sprites<br/>
        通过合并图片降低http的请求，比图片地图更加灵活，而图片地图要求图片是必须连续的，但是css Sprites不需要。<br/>
     3）内联图片<br/>
        将通常熟悉的图片引进模式，比如http:模式的URL，改为data：URL。例如，<img src = "data:tt.gif"/>。不过该办法<br/>
        不被ie7以下支持，内联图片允许将小块数据内联为‘立即数’。<br/>
     4）合并样式表和脚本
## 2、使用CDN
      CDN的全称是Content Delivery Network，即内容分发网络。其目的是通过在现有的Internet中增加一层新的网络架构，将<br/>
   网站的内容发布到最接近用户的网络"边缘"，使用户可以就近取得所需的内容，解决Internet网络拥挤的状况，提高用户访问<br/>
   网站的响应速度。
## 3、添加expires头
   浏览器使用缓存来减少HTTP请求次数和大小，使页面加速装载。html5缓存。Web sever通过Expires Header 告诉客户端一<br/>
   个元素可以缓存的时间长度。
## 4、压缩组件
   减小页面大小以减少网络响应时间的最简单影响最大的技术就是是使用gzip编码来压缩HTTP响应包。

### 压缩是如何工作的
   Web客户端可以通过HTTP请求中的Accept-Encoding头来表示对压缩的支持。<br/>
   Web服务器通过响应中的Content-Encoding头来通知Web客户端。<br/>
   gzip是目前最流行和最有效的压缩方法。<br/>

### 压缩的内容
   压缩的内容包括：HTML文档，脚本和样式表（最常见）以及包括XML和JSON在内的任何文本响应。图片和PDF不包括在内（因为它们以及被压缩了）。<br/>
   压缩的成本包括：服务器端花费额外的CPU周期来完成压缩。客户端要对压缩文件进行解压缩。<br/>
   mod_gzip_minimum_file_size指令控制着希望压缩的文件的最小值，默认值是500B。<br/>

### 配置
   配置gzip时使用的模块取决于Apache的版本：<br/>
      Apache 1.3的gzip压缩由mod_gzip模块提供。<br/>
      Apache 2.x的压缩通过mod_deflate模块完成。<br/>
### 代理缓存：
   当浏览器直接与服务器直接通信时，前面讲到的压缩的配置是可以很好的工作的。但是，当浏览器是通过代理进行与服务器通信的，通过代理<br/>
   进行发送请求的，就会出现配置问题，比如浏览器不支持gzip，代理会直接把请求发送给服务器，服务器响应的是未经过压缩的，后面会出现<br/>
   一系列问题。<br/>
   
   解决代理缓存的方法是在Web服务器的响应中添加Vary头。<br/>
   代理会缓存每个响应的两个版本：Accept-Encoding为gzip时的压缩内容和没有指定Accept-Encoding时的非压缩内容。<br/>
   默认情况下，mod_gzip会向所有响应添加Vary：Accept Encoding头，以驱使代理执行正确的操作。<br/>
### 边缘情形：
   用于设置浏览器白名单的指令过于复杂，无法使用HTTP头进行编码。最佳做法是将User-Agent作为代理的另外一种评判标准添加到Vary头中。<br/>
   如何平衡压缩和代理支持的决定是很复杂的，需要在加快响应时间，减少带宽开销和边缘情形浏览器缺陷之间进行权衡。<br/>
## 5、将样式表放到顶部
### 原因
   浏览器解析html等文件时，从上到下的顺序解析的。<br/>
### 分析
   发现把样式表提到HEAD部分里，由于符合浏览器解析的顺序，加快了页面渲染速度。另外，css放在头部，在浏览器解析结构时，就可以<br/>
   渲染出结果，不会出现白屏的情况。一般采用link的方式引入css放在header头部里。<br/>

## 6、将脚本放到底部
   页面下载脚本时会阻止其他内容下载与呈现，以防止浏览器的重绘重排。所以把脚本放在页面的底部不会阻止页面内容的呈现，而且页面<br/>
   一些可视化组件可以尽早下载而不被阻塞。<br/>

## 7、避免css表达式
   css表达式是功能强大的（同时也是危险的）用于用户设置css属性的方式，IE从版本5开始支持css表达式，如 background-color：<br/>
   expression（（new Date（））.getHours（）%2？“#B8D4FF”：“#F08A00”）即背景每小时执行一次。有可能被执行上千次，从而降<br/>
   低网站性能。<br/>
   为了避免css表达式，可以有俩种处理方式，一种是可以采用一次性表达式，即第一次执行就以明确的值代替表达式，如果需要动态设置值，<br/>
   可以采用另外一种方式，采用事件处理函数代替。<br/>

## 8、使用外部JavaScript和css
纯粹而言，内联的JavaScript和csss是比外联的速度更快，是因为尽管俩者下载的数据量相同的，可是外联将会增加http的请求数量，故而相对于内联来说速度会较慢。但是现实中，我们使用的外联JavaScript和css是比内联会产生较快的页面，外部JavaScript和css可以带来一个比较显著的收益，就是JavaScript和css文件有机会被浏览器缓存起来，而内部HTML文档内的文件通常不会被配置为可以进行缓存，在这种情况下每次请求html文档都要下载内联的JavaScript和css，并且内联相对而言会增大html文档的大小。
将外联和内联进行对比分析，其实关键点在与html文档请求数量相关、外部JavaScript和css组件被缓存的频率。
如果是用户不经常打开的网页，可能会很久才会打开一次，这个时候内联的文件就相对优势，不需要缓存，因为即使缓存了，但是由于用户太久没打开，即使可以缓存了JavaScript和css文件，但是用户太久没打开已经过了有效期，这样采用内联方式相对合适。相反采用外部JavaScript和css文件会比较好。

## 9、减少DNS查询
### 原因
DNS用于映射主机名和IP地址。当在浏览器中键入www.xxx.com，浏览器发启的DNS解析请求会返回服务器的IP地址。DNS解析有代价，一般一次解析需要20～120毫秒。浏览器在DNS查询完成前不会下载任何东西。这就是DNS次数影响网站性能的原因，查询次数所需的时间越多，打开页面的时间就越长。

### 策略
浏览器对DNS的查找结果进行缓存。而除了浏览器的缓存之外，操作系统（例如Windows)也会对DNS查询的结果做缓存。
当客户端的DNS缓存为空时（浏览器与操作系统），DNS查询的次数等同于网页中各域名的个数。包括该网页URL、图片、脚本文件、样式表、FLASH对象等使用的域名。所以减少域名数量可以减少DNS查询次数，可以减少响应时间。
可是，减少域名主机可能造成并行下载数的减少，从而可能增加响应时间，影响网站性能。
因此，最好的建议是在一个网站里面，将所有的内容资源存放在>=2个域但<=4个域内。

## 10、精简JavaScript
JavaScript作为一门解析型语言，是构建web页面的首选。由于没有了编译步骤，在最终部署前进行优化。精简JavaScript，可以减少代码的大小，就有利于提高网站性能。
精简 ，就是从代码中移除不必要的字符以减小代码的大小，进而改善代码脚本加载的时间，减少响应时间。在代码被精简后，所有的注释以及不要的空白字符（空格、换行、制表符）都将被移除。

## 11、避免重定向
### 重定向的定义
重定向用于将用户从一个URL重新路由到另一个URL。

### 重定向的类型
当web服务器向浏览器返回一个重定向时，响应中就会拥有一个范围在3xx状态码。以下列举常用的几种类型：
301：永久重定向，主要用于当网站的域名发生变更之后，告诉搜索引擎域名已经变更了，应该把旧域名的的数据和链接数转移到新域名下，从而不会让网站的排名因域名变更而受到影响。
302：临时重定向，主要实现post请求后告知浏览器转移到新的URL。
304：Not Modified，主要用于当浏览器在其缓存中保留了组件的一个副本，同时组件已经过期了，这是浏览器就会生成一个条件GET请求，如果服务器的组件并没有修改过，则会返回304状态码，同时不携带主体，告知浏览器可以重用这个副本，减少响应大小。

### 重定向损坏性能分析
当页面发生了重定向，就会延迟整个HTML文档的传输。在HTML文档到达之前，页面中不会呈现任何东西，也没有任何组件会被下载。
所以避免重定向，能够提升性能。比如URL缺少结尾的斜线，当URL的结尾必须要出现（/）的时候而没有出现，这将导致了一个301的响应，发生一个URL的重定向。如果这个时候能够加上（/）就可以避免产生这次重定向。

## 12、移除重复脚本
在一个页面中俩次包含同一个javascript文件会损坏性能。

### 出现重复脚本的原因
导致一个脚本的重复又两个主要因素：团队大小和脚本数量。开发一个网站需要极大数量的资源，不同的团队需要构建一个大型web的不同部分，当团队整合和沟通工作没有做足，则容易出现重复脚本的情况。当然脚本数量也是重要的一环，脚本数量越多越容易出现重复脚本的情况。

### 重复脚本怎么影响性能
在没有缓存的情况下，如果在html中重复链接了相同的脚本，IE7以下(包括IE7)将会产生两次HTTP请求，IE8以上则不会。
除了产生不必要的HTTP请求外，对脚本进行重复执行也会浪费时间，脚本的重复执行在浏览器中都存在。

### 如何避免重复脚本
1. 形成良好的脚本组织。重复脚本有可能出现在不同的脚本包含同一段脚本的情况，有些是必要的，但有些却不是必要的，所以需要对脚本进行一个良好的组织。
2. 实现脚本管理器函数。


## 13、配置ETag
http://www.cnblogs.com/chenxizhang/archive/2013/05/11/3072898.html
http://blog.csdn.net/liaozhongping/article/details/51114264

### ETag是什么？
实体标签(EntityTag)是唯一标识了一个组件的一个特定版本的字符串，是web服务器用于确认缓存组件的有效性的一种机制，通常可以使用组件的某些属性来构造它。（总结：实体标签是web服务器和浏览器用于确认缓存组件的有效性的一种机制。）
浏览器下载组件的时候，会将它们存储到浏览器缓存中。如果需要再次获取相同的组件，浏览器将检查组件的缓存时间，假如已经过期，那么浏览器将发送一个条件GET请求到服务器，服务器判断缓存还有效，则发送一个304响应，告诉浏览器可以重用缓存组件。服务器判断缓存是否的方式有俩种：一种是ETag，另外一种是根据最新修改的时间。（详细介绍：http://blog.csdn.net/liaozhongping/article/details/51114264）

### 引入ETag的原因
ETag主要是为了解决Last-Modified无法解决的一些问题：
1. 一些文件也许会周期性的更改，但是他的内容并不改变(仅仅改变的修改时间)，这个时候我们并不希望客户端认为这个文件被修改了，而重新GET;
2. 某些文件修改非常频繁，比如在秒以下的时间内进行修改，(比方说1s内修改了N次)，If-Modified-Since能检查到的粒度是s级的，这种修改无法判断(或者说UNIX记录MTIME只能精确到秒);
3. 某些服务器不能精确的得到文件的最后修改时间。

### ETag带来的问题
ETag的问题在于通常使用某些属性来构造它，有些属性对于特定的部署了网站的服务器来说是唯一的。当使用集群服务器的时候，浏览器从一台服务器上获取了原始组件，之后又向另外一台不同的服务器发起条件GET请求，ETag就会出现不匹配的状况。

### 应对ETag策略：
1. 如果使用Last-Modified不会出现任何问题，可以直接移除ETag，google的搜索首页则没有使用ETag。
2. 确定要使用ETag，在配置ETag的值的时候，移除可能影响到组件集群服务器验证的属性，例如只包含组件大小和时间戳。

## 14、使ajax可缓存
### ajax的使用场景
  1. 异步加载，使得页面的内容可以分批加载。
  2. 局部更新，使得页面的局部更新不会导致页面的刷新。

### 使ajax可缓存原因
当用户发起主动的ajax请求时，用户请求过程仍然需等待，异步不代表是“即时”，也是需要一个响应的过程，在这段时间内请求未能显示。

### 啥样的ajax可缓存
对于AJAX而言，有一些特殊性，并不是所有的AJAX请求都是可以缓存的。这是由于AJAX的请求通常有两种不同的方法：POST和GET。他们在进行请求的时候，就会略有不同。
  1. POST的请求，是不可以在客户端缓存的，每次请求都需要发送给服务器进行处理，每次都会返回状态码200。（这里可以优化的是，服务器端对数据进行缓存，以便提高处理速度）
  2. GET的请求，是可以（而且默认）在客户端进行缓存的，除非指定了不同的地址，否则同一个地址的AJAX请求，不会重复在服务器执行，而是返回304。
可参考地址：http://www.cnblogs.com/chenxizhang/archive/2013/05/12/3073690.html

### 使用HTML5的新特性来减少不必要的AJAX调用
将一部分数据缓存在客户端中，而且最好不要在临时文件夹中，以便下次启动时还能使用到这些数据。HTML 5中提供了一个新的特性：local storage。
