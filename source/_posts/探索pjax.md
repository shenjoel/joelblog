---
title: 探索pjax
date: 2016-08-31 18:23:18
tags: [pjax]
---

### 什么是pjax?
- 它是一种浏览方式，当你点击一个站内的链接的时候， 不是做页面跳转， 而是站内页面刷新。 
- 它彻底改变标准网站的用户体验，多页面的呈现，单页面的操作

### 为什么要用pjax？
web研发模式演变的两种状态：
- 以后端为主的MVC开发模式
-- 前后端耦合程度高，后端控制MV层，沟通成本大，但利于SEO
- Ajax为主的SPA型开发模式
-- SPA - Single Page Application 指单页面应用，通过带"#"的链接，获取其hash值在同一个页面更新不同的场景，达到浏览器级别的前进与后退；而这种开发模式，前后端是通过ajax交换数据，所以爬虫爬下来的页面都是空白，虽然拥有良好的用户体验，但不利于SEO

有没有那么一种解决方案，即能做SEO，又能得到像SPA一样的用户体验呢？

是的，pjax是一个优秀的解决方案，你有足够多的理由来使用它：
- 可以在页面切换间平滑过渡，增加Loading动画。
- 可以在各个页面间传递数据，不依赖URL。
- 可以选择性的保留状态，如音乐网站，切换页面时不会停止播放歌曲。
- 所有的标签都可以用来跳转，不仅仅是a标签。
- 避免了公共JS的反复执行，如无需在各个页面打开时都判断是否登录过等等。
- 减少了请求体积，节省流量，加快页面响应速度。
- 平滑降级到低版本浏览器上，对SEO也不会有影响。

### pjax的原理
第一步：拦截a标签的默认跳转动作。
第二步：使用Ajax请求新页面。
第三步：将返回的Html替换到页面中。
第四步：使用HTML5的History API或者Url的Hash修改Url。

### HTML5 History API

**history.pushState(state, title, url)**
pushState方法会将当前的url添加到历史记录中，然后修改当前url为新url。请注意，这个方法只会修改地址栏的Url显示，但并不会发出任何请求。我们正是基于此特性来实现Pjax。它有3个参数：
- state: 用以存储关于你所要插入到历史记录的条目的相关信息。
- title: 顾名思义，就是document.title。不过这个参数目前并无作用，浏览器目前会选择忽略它。
- url: 新url，也就是你要显示在地址栏上的url。

**history.replaceState(state, title, url)**
replaceState方法与pushState大同小异，区别只在于pushState会将当前url添加到历史记录，之后再修改url，而replaceState只是修改url，不添加历史记录。

**window.onpopstate 事件**
一般来说，每当url变动时，popstate事件都会被触发。但若是调用pushState来修改url，该事件则不会触发，因此，我们可以把它用作浏览器的前进后退事件。该事件有一个参数，就是上文pushState方法的第一个参数state。

### 不支持HTML5 PushState的浏览器怎么办？
IE6到IE9是不支持pushState的，要修改Url，只能利用Url的Hash，也即是#号。

你可以随意找个网站试一下，在url后面加上#号和任意内容，页面并不会刷新。此时点击后退也只会回到上一条#号，同样不会刷新。

那么我们只需把pushState(新url)换成location.hash = 新url，把onpopstate事件换成onhashchange事件就可以兼容IE了。
QQ音乐，网易云音乐等就是使用这种方式。