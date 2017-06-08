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

  #1、减少http请求次数<br/>
     原因  据统计，10%~20%的最终用户响应时间花在接收所请求的html文档上，剩下的80%时间花在html文档所引用的所有<br/>
     组件（图片、脚本、样式表、Flash等）进行的http请求上。因此，改善响应时间的最简单途径是减少组件的数量，进而<br/>
     减少http请求的数量。

     处理方式   从页面移除组件的想法必将会导致产性能和产品设计的矛盾。所以，不但要减少http请求，又可以避免在性
     能和设计方面进行艰难的抉择，可以采用如下技术：
     1）图片地图
        图片地图允许你在一个图片上关联多个URL，并将超链接关联到图片上，目标URL的选择取决于用户点击了图片的哪个位置上。
     使用图片地图的技术可以将图片的http请求的次数降低甚至由多次变为一次。比如，有一个导航栏有五张图片，单击一个图片就
     可以导航到相应的页面或者信息。采用图片地图技术后，可以将五个图片的请求降为一个请求，
     参考http://stevesouders.com/hpws/imagemap.php。
     2）css Sprites
        通过合并图片降低http的请求，比图片地图更加灵活，而图片地图要求图片是必须连续的，但是css Sprites不需要。
     3）内联图片
        将通常熟悉的图片引进模式，比如http:模式的URL，改为data：URL。例如，<img src = "data:tt.gif"/>。不过该办法
        不被ie7以下支持，内联图片允许将小块数据内联为‘立即数’。
     4）合并样式表和脚本
