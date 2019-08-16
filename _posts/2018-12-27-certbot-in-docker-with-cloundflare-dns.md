---
layout: cayman
title:  "在Docker中通过DNS解析申请SSL证书"
date:   2018-12-18 14:22 +0800
categories: SSL
published: true
author: ettingshausen
--- 
![](https://wx1.sinaimg.cn/large/685ea4faly1ftdhi7juslj20i209974s.jpg)  
*Let's Encrypt*  
{:.image-caption}  

之前写了[通过DNS解析申请Let's Encrypt证书]({% post_url 2018-07-18-letsencrypt-certificate %})，现在看来方案有些落后了。[certbot](https://github.com/certbot/certbot) 已经有了[cloudflare](https://www.cloudflare.com/) DNS插件，并且提供了 Docker 镜像，获取证书更加容易了。终于可以通过 DNS 解析自动获取证书啦。  


#### 1. 获取 Docker 镜像
---

```bash
docker pull certbot/dns-cloudflare
```
#### 2. 获取 Global API Key 生成 cloudflare.ini  
---
登录 [cloudflare](https://www.cloudflare.com/) 在[个人信息](https://www.cloudflare.com/a/account/my-account)的底部找到 Global API Key。
![](https://user-images.githubusercontent.com/9806325/63151202-89dbd600-c03b-11e9-9f5e-d04c60f58d2a.jpg)  

创建 `cloudflare.ini` 文件， `dns_cloudflare_api_key`为 Global API Key，`dns_cloudflare_email` 为账号的申请邮箱。
```
# Cloudflare API credentials used by Certbot
dns_cloudflare_email = example@example.com
dns_cloudflare_api_key = e89af256949eed91bb04ab0e0dec40e80
```


#### 3. 启动 Docker 容器
---  

执行下面命令： 

```bash
docker run -it --rm --name certbot `
            -v d:/docker/certbot/etc:/etc/letsencrypt `
            -v d:/docker/certbot/lib:/var/lib/letsencrypt `
            -v d:/docker/certbot:/.secrets `
            certbot/dns-cloudflare certonly `
            --dns-cloudflare-credentials /.secrets/cloudflare.ini `
            --dns-cloudflare-propagation-seconds 60 `
            --server https://acme-v02.api.letsencrypt.org/directory `
            -d '*.XXX.tk'
```  

以上是PowerShell的命令，Linux用户可以把行末尾的 `` ` `` 替换未 `\`

```bash
PS C:\Users\Edward> docker run -it --rm --name certbot `
>>             -v d:/docker/certbot/etc:/etc/letsencrypt `
>>             -v d:/docker/certbot/lib:/var/lib/letsencrypt `
>>             -v d:/docker/certbot:/.secrets `
>>             certbot/dns-cloudflare certonly `
>>             --dns-cloudflare-credentials /.secrets/cloudflare.ini `
>>             --dns-cloudflare-propagation-seconds 60 `
>>             --server https://acme-v02.api.letsencrypt.org/directory `
>>             -d '*.XXX.tk'
Saving debug log to /var/log/letsencrypt/letsencrypt.log

How would you like to authenticate with the ACME CA?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: Obtain certificates using a DNS TXT record (if you are using Cloudflare for
DNS). (dns-cloudflare)
2: Spin up a temporary webserver (standalone)
3: Place files in webroot directory (webroot)
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate number [1-3] then [enter] (press 'c' to cancel): 1
Plugins selected: Authenticator dns-cloudflare, Installer None
Obtaining a new certificate
Performing the following challenges:
dns-01 challenge for XXX.tk
Unsafe permissions on credentials configuration file: /.secrets/cloudflare.ini
Waiting 60 seconds for DNS changes to propagate
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/XXX.tk/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/XXX.tk/privkey.pem
   Your cert will expire on 2019-03-27. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```  

Done! 如果一切顺利，证书已经 生成在 `/etc/letsencrypt/archive/XXX.tk` 目录下。

# 参考资料
---
[Running with Docker](https://certbot.eff.org/docs/install.html#running-with-docker)  
[Welcome to certbot-dns-cloudflare’s documentation!](https://certbot-dns-cloudflare.readthedocs.io/en/stable/)

