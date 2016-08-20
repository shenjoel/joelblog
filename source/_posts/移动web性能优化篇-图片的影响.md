---
title: 移动web性能优化篇-图片的影响
date: 2016-08-20 16:27:25
tags: [移动WEB, 优化, 图片]
---

本文主要讨论在移动 WEB 中，图片的加载给页面整体性能带来的影响以及优化策略。

我们知道，浏览器的 DOMContentLoaded 事件会在主页面加载并解析完成之后触发，不会等页面样式、图片、iframe 等子资源加载完。以下是 [MDN](https://developer.mozilla.org/en/docs/Web/Events/DOMContentLoaded) 对它的描述：

>The DOMContentLoaded event is fired when the initial HTML document has been completely loaded and parsed, without waiting for stylesheets, images, and subframes to finish loading.

但在实际测试中，移动端完全相同的页面，加载与不加载图片对 DOMContentLoaded 触发时机的影响却很大。以下是我们在某个移动产品中，将图片延迟加载后的 DOMContentLoaded 时间对比，可以看出明显变化：
![](//st.imququ.com/i/webp/static/uploads/2015/10/dom-ready-time.png)

我们只是将页面所有图片（大约十几张）进行延迟加载，就让 DOMContentLoaded 事件提前 250 毫秒触发。这是我之前没有意料到的，移动设备在网络、CPU、内存等方面的性能与 PC 相比差距很大，很多 PC 上可以忽略的问题，在移动端必须重视起来。

移动 WEB 要做好图片优化，无外乎两点：
- [控制图片大小](/2016/08/20/移动web性能优化篇-图片的大小/)
- [控制图片加载](/2016/08/20/移动web性能优化篇-图片的加载/)


