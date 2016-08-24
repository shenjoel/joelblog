---
title: 在hexo中加入多说评论模块
date: 2016-08-19 15:51:58
tags: [hexo, 多说, 评论]
---
首先在根目录下_config.yml中增加duoshuo_shortname: 你站点的short_name，这里的short_name也就是你的二级域名, 例如(http://shenjoel.github.io/)。
如果使用的是默认的landscape主题只需要修改themes\landscape\layout\_partial\article.ejs中的

```sh
<% if (!index && post.comments && config.disqus_shortname){ %>
<section id="comments">
	<div id="disqus_thread">
	 <noscript>Please enable JavaScript to view the <a href="//disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
	</div>
</section>
<% } %>
```
改为

```sh
<% if (!index && page.comments && config.duoshuo_shortname){ %>
<section id="comments">
	<!-- 多说评论框 start -->
	<div id="ds-thread" class="ds-thread" data-thread-key="<%= page.path %>" data-title="<%= page.title %>" data-url="<%= page.permalink %>"></div>
	<!-- 多说评论框 end -->
	<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
	<script type="text/javascript">
		var duoshuoQuery = {short_name:"datoublog"};
		(function() {
		var ds = document.createElement('script');
		ds.type = 'text/javascript';ds.async = true;
		ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
		ds.charset = 'UTF-8';
		(document.getElementsByTagName('head')[0] 
		 || document.getElementsByTagName('body')[0]).appendChild(ds);
		})();
	</script>
	<!-- 多说公共JS代码 end -->
</section>
<% } %>
```