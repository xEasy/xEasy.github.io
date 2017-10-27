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

由于微信开发都是在微信内浏览器运行，不管是登陆还是支付都不能在PC开发端愉快地玩耍，但世界我们还是需要拯救的。

由于自己换了新电脑，第一次做微信开发跟大家分享一下我的开发环境，欢迎拍砖交流。

一般的第三方开发永远离不开两个配置，域名 & 回调，有一些平台支持localhost的域名&回调会方便，更有支持随意更改端口的，微信上面貌似都没有 :anger:。

好了，假定我们的域名是playmonkey.me

## 域名
---

微信登陆，JSAPI以及支付都有域名限制，所以需要让手机微信上打开palymonkey.me时，访问到我们本地的development server。

干这个勾当的当属 Charles 神器。

- 下载安装打开Charles
-  `Proxy -> Proxy Setting -> Http Proxy`勾选Enable.... 开启http proxy代理，端口默认是8888
-  `Tool -> DNS Spoofing Settings` 勾选Enable DNS Spoofing
-  然后把palymonkey.me加到DNS Spoofs，指向本机127.0.0.1

Charles好了，下一步配置手机，确保你的手机网络和电脑在同一局域网，配置手机的HTTP代理到电脑的IP上，端口8888；

访问`http://playmonkey.me`；

什么？挂了？当然，酱紫访问的是本地的80端口。

好了，我们再来用 Nginx 反向代理搞定这个问题，让手机端的playmonkey.me访问到本地development server的3000端口上。

首先安装启动Nginx，用Apache的朋友对不起了，你们要自食其力XD

配置Nginx

```
server {
      listen 80;
      server_name playmonkey.me;
      charset utf-8;
      location / {
        proxy_pass          http://monkey_servers;
        proxy_redirect      default;
        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header    X-Real-IP $remote_addr;
        proxy_set_header    Host $http_host;
        proxy_next_upstream http_502 http_504 error timeout invalid_header;

      }

    }
    upstream palymonkey_servers{
      server 127.0.0.1:3000;
    }
```

再用手机访问`http://playmonkey.me` ，Nice....这回看到页面了。
到此我们可以在微信上用playmonkey.me访问本地的dev server，可以愉快地调用JSAPI & 微信oauth登陆 & 发起支付。

噢，如果做的是PC端的oauth微博登录怎么办？ 在本地host加上 127.0.0.1 palymonkey.com。Nice XD、

## 异步回调
----

上面说到发起支付，微信的支付结果是通过异步回调的，所以需要做内网穿透，这里推荐两个工具 `ngrok OR localtunnel`

#### localtunnel

localtunnel 是node写的，用起来非常简单

```
$ npm install -g localtunnel
$ lt --port 3000
your url is: https://gqgh.localtunnel.me
```

这样外网打开`https://gqgh.localtunnel.me` 就可以访问到本地的3000端口

但是localtunnel是国外的，公司网络刚刚搭好，经常不稳定果断抛弃转入ngrok；

#### ngrok（不想折腾的可以忽略XD）
---
悲催的是ngrok也是国外的，而且服务器给墙了，BUT，问题不大，我们可以pull ngrok 1.X的源码自己编译，2.0会报证书错误如果没有合法的https证书。

好了我们来编译ngrok，噢，对了，ngrok是Go写的，编译出来直接是一个可执行文件，爽歪歪有木有，哈~

#### 在服务器端（每家都有一只staging服务器吧）
---
NGROK_DOMAIN 设置为您自己拥有的域名，并指向Ngrok server运行的服务器

```
NGROK_DOMAIN="my.domain.com"
git clone https://github.com/inconshreveable/ngrok.git
cd ngrok

openssl genrsa -out rootCA.key 2048
openssl req -x509 -new -nodes -key rootCA.key -subj "/CN=$NGROK_DOMAIN" -days 5000 -out rootCA.pem
openssl genrsa -out device.key 2048
openssl req -new -key device.key -subj "/CN=$NGROK_DOMAIN" -out device.csr
openssl x509 -req -in device.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out device.crt -days 5000

cp rootCA.pem assets/client/tls/ngrokroot.crt
# make clean
make release-server release-client
```

编译完有两个可执行文件 bin/ngrok & bin/ngrokd
将bin/ngrok copy到你本机
在服务器上开启ngrok server
`bin/ngrokd -tlsKey=device.key -tlsCrt=device.crt -domain="$NGROK_DOMAIN" -httpAddr=":8000" -httpsAddr=":8001"`

#### 本地端
---

```
NGROK_DOMAIN="my.domain.com"
echo -e "server_addr: $NGROK_DOMAIN:4443\ntrust_host_root_certs: false" > ngrok-config
./ngrok -config=ngrok-config -subdomain=playmonkey 3000 // 配置subdomain到本地3000端口，这样外网通过playmonkey.NGROK_DOMAIN 就可以访问到本地dev server的3000端口
```

或者用SSH forwarding

`./ngrok -config=ngrok-config -subdomain=3000 --proto=tcp 22`

以上Ngrok编译&配置出自 [How to setup Ngrok with a self-signed SSL cert](https://gist.github.com/lyoshenka/002b7fbd801d0fd21f2f)

至此我们在发起微信支付时回调URL的HOST就可以设置为playmonkey.NGROK_DOMAIN

*至此我们可以愉快地开发测试 微信的oauth登录 & JSAPI & 支付 & 支付回调，好吧，很多BUG一点都不愉快*
