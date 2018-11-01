---
layout: cayman
title:  "WebService over HTTPS"
date:   2018-11-01 11:21 +0800
categories: WebService Java
published: true
author: ettingshausen
---  

最近踩了 WebService 一个的坑。与其他部门有个常规的 WebService 接口，对方提供服务，这边调用。像往常一样，使用 cxf 生成了客户端的代码并调用，开发的过程中并没有遇到什么问题，测试也没发现问题。然而一个礼拜之后正式上线的生产环境中，客户端调用接口总是抛出异常 `服务器发送了 HTTP 状态代码 302: Moved Temporarily`。

```java
    @Test
    public void test() throws Exception {

        URL url = new URL("https://home.winning.com/web-service/cxf/findReportEntity?wsdl");

        ReportEntityService service = new ReportEntityServiceImplService(url)
                .getReportEntityServiceImplPort();

        ReportEntity res = service.findOne("");
        System.out.println(res.getEnterpirseName());
    }
```

```
com.sun.xml.internal.ws.client.ClientTransportException: 服务器发送了 HTTP 状态代码 302: Moved Temporarily
	at com.sun.xml.internal.ws.transport.http.client.HttpTransportPipe.checkStatusCode(HttpTransportPipe.java:310)
	at com.sun.xml.internal.ws.transport.http.client.HttpTransportPipe.createResponsePacket(HttpTransportPipe.java:259)
	at com.sun.xml.internal.ws.transport.http.client.HttpTransportPipe.process(HttpTransportPipe.java:217)
	at com.sun.xml.internal.ws.transport.http.client.HttpTransportPipe.processRequest(HttpTransportPipe.java:130)
	at com.sun.xml.internal.ws.transport.DeferredTransportPipe.processRequest(DeferredTransportPipe.java:95)
	at com.sun.xml.internal.ws.api.pipe.Fiber.__doRun(Fiber.java:1121)
	at com.sun.xml.internal.ws.api.pipe.Fiber._doRun(Fiber.java:1035)
	at com.sun.xml.internal.ws.api.pipe.Fiber.doRun(Fiber.java:1004)
	at com.sun.xml.internal.ws.api.pipe.Fiber.runSync(Fiber.java:862)
	at com.sun.xml.internal.ws.client.Stub.process(Stub.java:448)
	at com.sun.xml.internal.ws.client.sei.SEIStub.doProcess(SEIStub.java:178)
	at com.sun.xml.internal.ws.client.sei.SyncMethodHandler.invoke(SyncMethodHandler.java:93)
	at com.sun.xml.internal.ws.client.sei.SyncMethodHandler.invoke(SyncMethodHandler.java:77)
	at com.sun.xml.internal.ws.client.sei.SEIStub.invoke(SEIStub.java:147)
	at com.sun.proxy.$Proxy32.findOne(Unknown Source)
	at SkytechTest.test(SkytechTest.java:30)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
```  
# 问题排查
----

检查了下配置的 URL 发现，实际配置的地址是HTTPS形式的。因为测试环境与生产环境的配置都是一样的，除了URL，测试环境是直接通过 IP 访问的。基本上可以确定问题就出在HTTPS上了。初步判断，应该是对方在生产环境中使用了 nginx 做了反向代理，并且开启了 SSL，导致访问接口的时候被重定向了。但是，`wsdl` 是可以通过 https 访问的，这就有些奇怪了。 为了排查问题，用C# 快速写了一个测试程序：

```c#
ReportEntityServiceClient client = new ReportEntityServiceClient();
try
{
    reportEntity report = client.findOne("");
    Console.WriteLine(report.enterpirseName);
}
catch (Exception ex)
{
     Console.WriteLine(ex.Message);
}
``` 

同样抛出了异常，异常消息与 Java 有些不同， `No binding operation info while invoking unknown method with params unknown.`, 将 `app.config` 文件中的 `endpoint address` 节点修改为 https, 运行提示：`提供的 URI 方案“https”无效，应为“http”。参数名: via` 问题可以确定，就是 https 导致的问题。通过 Wireshark 抓包来看，接口调用请求的为 http，虽然配置的 URL 是 https 但是实际上调用的其实是 http。  

# 解决方法
----

如果 WebService 调用可以设置代理，把代理地址设置成 https 应该就能解决问题。 经过一番 Google 貌似找到一个解决方法, 通过 `BindingProvider` 拿到请求上下文，添加 `BindingProvider.ENDPOINT_ADDRESS_PROPERTY`:  
```java
    @Test
    public void test() throws Exception {
        String wsdlLocation = "https://home.winning.com/web-service/cxf/findReportEntity?wsdl";
        URL url = new URL(wsdlLocation);

        ReportEntityService service = new ReportEntityServiceImplService(url)
                .getReportEntityServiceImplPort();
        ((BindingProvider) service).getRequestContext()
                .put(BindingProvider.ENDPOINT_ADDRESS_PROPERTY, wsdlLocation);
        ReportEntity res = service.findOne("");
        System.out.println(res.getEnterpirseName());
    }
```  

问题就这么解决了，只需要添加一行代码就搞定了。对于 .NET 需要修改 `app.config` ， 在 `binding` 节点中添加 `security` 配置，并且将 `endpoint address` 节点修改为 https。详细配置如下：
```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <bindings>
      <basicHttpBinding>
        <binding name="ReportEntityServiceImplServiceSoapBinding" >
          <security mode="Transport">
            <transport clientCredentialType="None" proxyCredentialType="None" realm=""/>
            <message clientCredentialType="Certificate" algorithmSuite="Default" />
          </security>
        </binding>
      </basicHttpBinding>
    </bindings>
    <client>
      <endpoint address="https://home.winning.com/web-service/cxf/findReportEntity"
          binding="basicHttpBinding" bindingConfiguration="ReportEntityServiceImplServiceSoapBinding"
          contract="ServiceReference.ReportEntityService" name="ReportEntityServiceImplPort" />
    </client>
  </system.serviceModel>
</configuration>
```  

# 总结分析
---

`BindingProvider` 定义了获取 request 与 response 上下文 (context)的方法，context 是一个 Map， 可以设置一些属性。 除了 `ENDPOINT_ADDRESS_PROPERTY` 以外，还有 `USERNAME_PROPERTY` 、`PASSWORD_PROPERTY` 等属性。 参考 [Purpose of BindingProvider in JAX-WS web service](https://stackoverflow.com/questions/32805144/purpose-of-bindingprovider-in-jax-ws-web-service)