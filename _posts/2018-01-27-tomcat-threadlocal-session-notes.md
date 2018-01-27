---
layout: post
title:  "Tomcat Session “混乱” :scream:"
date:   2018-01-27 02:12:21 +0800
categories: Java
published: true
author: ettingshausen
---


# 发现问题

最近客户反应，有时候自己的患者，会自动变成其他科室。这个问题颇为诡异，不容易再现。后来，偶然一次发现，在 Session 失效的情况下，有的页面竟然还能保存 :scream: 。问题一下子有了眉目，可能是Tomcat的线程池，线程复用导致的问题。


为了使用方便，系统在登录验证成功时，在过滤器中使用ThreadLocal存储了Session，大致代码如下：

```java
public void doFilter(ServletRequest arg0, ServletResponse arg1, FilterChain arg2)
            throws IOException, ServletException {

    if (session.getAttribute(SessionName) == null) {
        ThreadLocalObjBO.setObj(session.getAttribute(SessionName));
    }
    arg2.doFilter(arg1, arg2);
}
```

# 推理分析
----

这个操作是没有什么问题的，使用ThreadLocal存储Session也是比较常见的做法。那么问题出在哪里呢？ 我们来推演一下。

假定Session过期的时间为5分钟。A用户登录并打开需要保存的页面，并没有操作。5分钟后，Session失效，A用过的线程中取出的Session(`ThreadLocalObjBo.getObj()`) 都是null。如果在这段时间内，有其他用户登录并操作，且他们的Session没有失效，这时线程池中就缓存了一些存储了Session的线程。

由于过滤器只过滤了登录的请求，所以A用户此时点击页面的保存按钮依然能够触发保存服务，这是Tomcat会从线程池中分配一个线程给这次请求，如果刚好，被分配的这个线程被其他用户用过Session还未失效。那这时A用户通过`ThreadLocalObjBo.getObj()`获取到的信息，其实是其他用户缓存下来的。

而医生的科室信息确实是从Session中读取然后保存的，与实际的情况吻合。问题基本上找到原因了，要是能及时清理掉其它用户的缓存，问题就应该解决。

# 解决方案
-----

解决方法并不是太复杂，只需要添加一个拦截器，并在`postHandle`方法中，调用`ThreadLocal.remove()` 方法清空掉当前线程的“局部变量”的值即可。

```java
public class SessionInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, 
    Object handler) throws Exception {
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, 
    Object handler, ModelAndView modelAndView) throws Exception {

        ThreadLocalObjBO.remove();
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, 
    Object handler, Exception ex) throws Exception {

    }
}
```
添加Spring 配置

```xml
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/**"/>
        <bean id="sessionInterceptor" class="com.npkw.interceptor.SessionInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```

# 总结
-----

要解决这个问题首先得理解要解决这个问题首先得理解，关于ThreadLocal可以单独写一篇文章来讲，关于ThreadLocal可以单独写一篇文章来讲，[深入理解ThreadLocal](http://vence.github.io/2016/05/28/threadlocal-info/) 这篇文章结合源码，分析的挺详细。

这个问题牵扯出来的问题挺多，比如线程池，ThreadLocal是否会导致内存泄漏等等。
参考[深入理解ThreadLocal](http://vence.github.io/2016/05/28/threadlocal-info/)一文，ThreadLocal使用建议如下：
>
1. ThreadLocal应定义为静态成员变量。
1. 能通过传值传递的参数，不要通过ThreadLocal存储，以免造成ThreadLocal的滥用。
1. 在线程池的情况下，在ThreadLocal业务周期处理完成时，最好显式的调用remove()方法，清空”线程局部变量”中的值。
1. 正常情况下使用ThreadLocal不会造成内存溢出，弱引用的只是threadLocal，保存的值依然是强引用的，如果threadLocal依然被其他对象强引用，”线程局部变量”是无法回收的。


# 参考资料
---
1. [一次Session“混乱”的问题定位](http://footstone.github.io/2014/05/23/session-problem-solved.html)
1. [ThreadLocal可能引起的内存泄露](http://www.cnblogs.com/onlywujun/p/3524675.html)
1. [深入分析 ThreadLocal 内存泄漏问题](http://blog.xiaohansong.com/2016/08/06/ThreadLocal-memory-leak/)
1. [理解Java中的ThreadLocal](https://droidyue.com/blog/2016/03/13/learning-threadlocal-in-java/)
1. [在Tomcat中使用ThreadLocal以及Session](http://orchidflower.oschina.io/2017/05/04/ThreadLocal-and-Session-in-Tomcat/)
1. [ThreadPool对ThreadLocal的影响](http://mouzt.github.io/2014/09/10/Threadlocal-note/)
1. [深入理解ThreadLocal](http://vence.github.io/2016/05/28/threadlocal-info/)
