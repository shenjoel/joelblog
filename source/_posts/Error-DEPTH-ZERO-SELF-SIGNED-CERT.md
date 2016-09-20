---
title: 'Error: DEPTH_ZERO_SELF_SIGNED_CERT'
date: 2016-09-20 11:16:31
tags: [node]
---



#### 在实现第三方系统对接时，提示了一个错误：

	Error: DEPTH_ZERO_SELF_SIGNED_CERT

#### 而在开发过程中跳过这一问题的方法是添加下面这句：

	process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";

#### 下文是一个中肯的解释：

Node is complaining because the TLS (SSL) certificate it's been given is self-signed (i.e. it has no parent - a depth of 0). It expects to find a certificate signed by another certificate that is installed in your OS as a trusted root.

Your "fix" is to disable Node from rejecting self-signed certificates by allowing ANY unauthorised certificate.

Your fix is insecure and shouldn't really be done at all, but is often done in development (it should never be done in production).

The proper solution should be to put the self-signed certificate in your trusted root store OR to get a proper certificate signed by an existing Certificate Authority (which is already trusted by your server).

#### 大概意思是：

Node被赋予了TLS（SSL）证书是自签名的，它希望找到由安装在您的操作系统作为一个受信任的根证书，另一个签名的证书。

你的“修复”是允许任何未经授权的证书，取消其拒绝自签名证书节点。这里指 **process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";**

你的解决方法是不安全的，应该不是真的都做到，但往往是在做开发（它不应该在生产中进行）。

正确的解决办法应该是把自签名证书在受信任的根存储或度日（这已经是你的服务器信任）现有的证书颁发机构签署一个适当的证书。

----
此文章以记录自己还在坑里努力往上爬



