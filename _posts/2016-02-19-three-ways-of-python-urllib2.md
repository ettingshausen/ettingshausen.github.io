---
layout: post
title:  "Python urllib2 三种方法"
date:   2016-02-19 16:05:06 +0800
categories: Python
---

#### 第一种：

```python
# coding:utf8
import urllib2, cookielib
url = "http://www.baidu.com"
print u'第一种方法'
response1 = urllib2.urlopen(url)
print response1.getcode()
print len(response1.read())
```

#### 第二种：

```python
print u"第二种方法"
request = urllib2.Request(url)
request.add_header("user-agent", "Mozilla/5.0")
response2 = urllib2.urlopen(request)
print response2.getcode()
print len(response2.read())
```

#### 第三种：
```python
print u"第三种方法"
cj = cookielib.CookieJar()
opener = urllib2.build_opener(urllib2.HTTPCookieProcessor(cj))
urllib2.install_opener(opener)
response3 = urllib2.urlopen(url)
print response3.getcode()
print cj
print response3.read()
```