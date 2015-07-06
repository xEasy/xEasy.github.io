---
layout: post
title: "Wechat Developing"
description: ""
category:
- wechat
- oauth2
tags:
- wechat
- oauth2
permalink: wechat-developing
---

### 微信登陆
---

#### 微信登录配置：

- 开发者中心 -> 配置项 -> 网页授权获取用户基本信息 [修改页面回调域名]
- 只有回调域名下所有目录有登陆授权权限，次级域名无法授权

#### 微信支付配置(H5)：

- 在商户平台设置支付秘钥，用于加密&验证回调信息
- 在公众平台微信支付设置支付目录，native回调地址（只用于native的回调信息），告警通知地址
- 支付目录需要精确最后一级目录，否则最终页面将调用失败

```
Native App 的需要另外申请开放平台的支付功能，配置使用H5页面支付的内容
```

##### 开发环境配置：

1. 内网穿透(ngrok, localtunnel)
2. charles代理

确保你的手机和电脑在同一局域网，电脑开启charles，开启charles的代理功能。

开启charles的DNS Spoofing功能，添加你需要的域名以及IP指向 `uboss.cc -> 127.0.0.1`，将uboss.cc 指向电脑本机。

配置nginx将Uboss.cc域名指向你本地开启的development server.

charles默认端口为8888，配置手机网络的代理到charles。

打开微信，访问uboss.cc，bingo！

