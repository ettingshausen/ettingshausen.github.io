---
layout: cayman
title:  "Nextcloud在Docker中的升级方法"
date:   2018-10-19 02:46 +0800
categories: docker nextcloud
published: true
author: ettingshausen
---   

![Nextcloud](https://i.loli.net/2020/04/15/6MNE7KZwcSsWgft.jpg)
*Nextcloud*  
{:.image-caption}   

`Nextcloud` 是一款优秀的开源网盘，它的前身是 `OwnCloud`。最近 `Nextcloud` 更新挺频繁。由于服务运行在`docker` 中，貌似不能通过升级管理器自动升级。 实际上可以通过 `docker` 的特性完成升级。

#### 1. 更新镜像
---

```sh
docker pull nextcloud
```

#### 2. 创建临时容器
---

通过 `--volumes-from` 选项加载原来容器的数据卷， `7f551f623f2a` 为原容器的ID。临时指定一个端口，以便测试。

```sh
docker run -d --name tmpcloud -p 3000:80 --volumes-from 7f551f623f2a nextcloud
```  

#### 3. 删除原来的容器并创建新容器
---
```sh
docker rm 7f551f623f2a
docker run -d --name nextcloud --restart=always -p 4009:80 --volumes-from tmpcloud nextcloud
```

#### 4. 删除掉旧的镜像与临时容器
----

```sh
docker image rm c6d89012cfd7
docker stop tmpcloud
docker rm tmpcloud
```
