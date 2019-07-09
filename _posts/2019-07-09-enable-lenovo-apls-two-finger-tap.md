---
layout: cayman
title:  "联想开启触摸板双指双击功能"
date:   2019-07-09 17:22 +0800
categories: note
published: true
author: ettingshausen
---   


不知不觉已经在这个公司已经过了4个念头了。公司规定，配发的笔记本电脑必须使用满4年才能申请新电脑。老电脑终于可以退役了。 新申请的笔记本从 ThinkPad 换成了 `昭阳 K43c-80` 不过配置还可以。i5-8250U + 12G RAM + 512G SSD + 1T HDD

![](https://user-images.githubusercontent.com/9806325/60888954-7a25e080-a28a-11e9-8fff-18621cf2cd14.png)  
*K43c-80*  
{:.image-caption}  

触摸板的尺寸挺大，手势控制挺精准，操作起来非常的顺滑。但是美中不足的是，触摸板手势竟然不支持双指点击唤起右键菜单。按压触摸板唤起右键菜单的效率真是太低了，整个面板只有底部可以按压，非常的硬。  

在微信公众号上找了联想的人工客服，处理了半天，除了换驱动还是换驱动，新的旧的都换了还是不行。用联想官方的驱动管家也不行。意外的是客服竟然告诉，不要使用这个软件来更新驱动，因为有些时候识别匹配的并不准确，:joy:。  

废话不多了，直接来说解决办法[^forum]。  

>1. 打开注册表  
>2. 找到 `HKEY_CURRENT_USER\SOFTWARE\Apls\Apoint\Gesture` ，编辑  
>    - `2TapShow` 
>    - `2TapSupport`  
>双击将这两个选项值都修改为 `1` 默认值是 `0`     
>3. 与步骤2类似修改 `HKEY_LOCAL_MACHINE\SOFTWARE\Alps\Apoint\Gesture`  
>4. 重启电脑


# 参考资料
--- 
[^forum]: [Two finger tap right click (ALPS, Yoga 500)](https://forums.lenovo.com/t5/Lenovo-Yoga-Series-Notebooks/Two-finger-tap-right-click-ALPS-Yoga-500-Here-s-howto/td-p/2262919)