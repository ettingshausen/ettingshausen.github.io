---
layout: cayman
title:  "通过DNS解析申请Let's Encrypt证书"
date:   2018-07-18 02:25 +0800
categories: SSL
published: true
author: ettingshausen
--- 
![](https://wx1.sinaimg.cn/large/685ea4faly1ftdhi7juslj20i209974s.jpg)  
*Let's Encrypt*  
{:.image-caption}  

[Let's Encrypt](https://letsencrypt.org/) 证书免费，并且得到众多公司和组织的支持。比如，Chrome、Mozilla、Cisco等。虽然证书只有90天的有效期，但是可以通过脚本（[certbot](https://github.com/certbot/certbot)）可以自动续期，可以说是完美了。网上有许多详细的教程可以参考，比如[免费 Https 证书（Let's Encrypt）申请与配置](https://keelii.github.io/2016/06/12/free-https-cert-lets-encrypt-apply-install/)。  

然而，要是没有备案的话，运营商会屏蔽掉 `80`、`443`两个端口，大部分教程中的方法都会失效。经过数个小时的摸索，惊喜的发现 certbot 还支持一个 `DNS plugins`，通过校验域名的TXT记录获取证书。下面就把整个过程作以记录。

#### 1. 下载 certbot
---

```bash
git clone https://github.com/certbot/certbot
cd certbot
```
#### 2. 添加A记录  
---

平常使用DNSPod，但是在申请证书的时候发现，响应比较慢。建议使用[cloudflare](https://www.cloudflare.com/)。 首先添加需要申请域名的A记录，指向执行certbot脚本的服务器。

![](https://wx2.sinaimg.cn/large/685ea4faly1ftdgx1l8foj20qo0a4aat.jpg)  
#### 3. 执行脚本
---  

执行下面命令： 
```bash
./certbot-auto -d hxxx.xxx.tk --manual --preferred-challenges dns certonly
```

提示如下，中途会提示 `Are you OK with your IP being logged?` (没错是 `Are you OK ... ` :sweat_smile:)，输入 `y`

```
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator manual, Installer None
Cert is due for renewal, auto-renewing...
Renewing an existing certificate
Performing the following challenges:
dns-01 challenge for hxxx.xxx.tk

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
NOTE: The IP of this machine will be publicly logged as having requested this
certificate. If you're running certbot in manual mode on a machine that is not
your server, please ensure you're okay with that.

Are you OK with your IP being logged?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: y

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please deploy a DNS TXT record under the name
_acme-challenge.hxxx.xxx.tk with the following value:

a16ragY...............OcYdcJyudR0

Before continuing, verify the record is deployed.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Press Enter to Continue
```  
#### 4. 添加TXT记录   
---

按照上边的提示添加一个name为`_acme-challenge.hxxx.xxx.tk`， value为 `a16ragY...............OcYdcJyudR0`的TXT解析记录（参考上图），保存之后稍等一会儿再按回车，DNS解析需要点时间，大概需要一分钟就能生效。回车之后，稍等片刻，如果申请成功会看到以下信息。

```
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/hxxx.xxx.tk/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/hxxx.xxx.tk/privkey.pem
   Your cert will expire on 2018-10-14. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot-auto
   again. To non-interactively renew *all* of your certificates, run
   "certbot-auto renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```  

修改nginx配置
```bash
listen 443

ssl on;
ssl_certificate /etc/letsencrypt/live/hxxx.xxx.tk/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/hxxx.xxx.tk/privkey.pem;
ssl_dhparam /etc/ssl/certs/dhparams.pem;
ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2;
ssl_ciphers HIGH:!aNULL:!MD5;
```
重启nginx使配置生效，然后就能看到浏览器地址栏挂上漂亮的小绿锁了。美中不足的是没办法自动续签证书。
```bash
nginx -s reload
```  

