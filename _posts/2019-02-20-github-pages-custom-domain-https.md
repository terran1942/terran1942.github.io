---
layout: post
title:  "GitHub Pages为自定义域名启用HTTPS"
date:   2019-02-20 16:34:00 +0800
categories:  technology
tags: [Github Pages, HTTPS]
---

在笔者以往的记忆中，访问GitHub Pages，域名默认是_**https://xxx.github.io**_，使用自定义域名并没有HTTPS支持，想要HTTPS需要使用cloudflare等CDN的https进行支持。
最近得知GitHub Pages在2018年5月1日起已经提供对HTTPS的支持：[GitHub blog](https://github.blog/2018-05-01-github-pages-custom-domains-https/)，于是进行了尝试，为本blog开启HTTPS。

# 设置域名
+ 如果自定义域名使用CNAME或ALIAS记录，那么全部设置已经完成，并且网站应该可以通过HTTPS访问，[DNS设置方法](https://help.github.com/articles/setting-up-an-apex-domain/#configuring-an-alias-or-aname-record-with-your-dns-provider)。

+ 如果使用的是A记录，必须使用新的IP地址更新站点的DNS记录，[DNS设置方法](https://help.github.com/articles/setting-up-an-apex-domain/#configuring-a-records-with-your-dns-provider)。

# 开启强制执行HTTPS
一旦更新了DNS记录，并且已确认站点通过HTTPS正确加载，可以选择在存储库设置中为自定义域名“强制执行HTTPS”，确保通过HTTP请求站点的用户升级到HTTPS。

![GitHub signed commit](/assets/2019-02-20/snapshot-0.png)