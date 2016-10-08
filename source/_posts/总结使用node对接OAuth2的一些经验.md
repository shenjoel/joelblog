---
title: 总结使用node对接OAuth2的一些经验
date: 2016-10-08 16:52:06
tags: [node, OAuth2]
---

### 1、在服务器端的客户端证书验证，DEPTH_ZERO_SELF_SIGNED_CERT错误
request模块中，rejectUnauthorized参数可设置忽略验证，开发调试方便，但生产环境下不可使用，正确方式是带上agentOptions: {ca: "xxx.pem", ...}
参考[stackoverflow](http://stackoverflow.com/questions/23601989/client-certificate-validation-on-server-side-depth-zero-self-signed-cert-error)

### 2、发掘了两个不错的模块
- [memory-cache](https://github.com/ptarjan/node-cache)
-- A simple in-memory cache. put(), get() and del()
-- 简单好用
- [shortid](https://github.com/dylang/shortid)
-- ShortId creates amazingly short non-sequential url-friendly unique ids. Perfect for url shorteners, MongoDB and Redis ids, and any other id users might see.
-- 功能强大