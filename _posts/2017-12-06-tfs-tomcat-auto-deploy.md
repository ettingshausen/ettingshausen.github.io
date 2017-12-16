---
layout: post
title:  "Team Foundation Server Tomcat 自动部署"
date:   2017-12-06 12:15:20 +0800
categories: Tomcat
published: true
author: ettingshausen
---

正常的项目开发，不同的功能会分配给不同的开发人员来完成。阶段性的联合调试，在整个开发流程中就必不可少。需要一个能够实时把最新代码编译，打包，部署。看似简单的一个流程，人工来操作非常的繁琐，每次部署都要消耗大量的时间，而且无法保证代码是最新的。  

[Nightly Build](https://baike.baidu.com/item/Nightly%20Build) 便应用而生， 夜间无人工作，所以在夜间获取代码，能够保证代码最新，也能充分利用计算机资源。  

最近在项目中尝试了夜间自动部署，现在将方案简要记录。

# Tomcat 配置
---

编辑 `tomcat-users.xml` 文件，修改用户密码， 添加 `admin-gui` , `manager-gui` , `manager-script` 角色。其中 `manager-script` 是开启自动部署必须要的。

```xml
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
<user username="admin" password="admin" roles="admin-gui,manager-gui,manager-script" />
```


# Maven 项目配置
---  

自动部署需要在 `pom.xml` 中配置 Tomcat 插件

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.tomcat.maven</groupId>
                <artifactId>tomcat7-maven-plugin</artifactId>
                <configuration>
                    <url>http://localhost:8866/manager/text</url>
                    <username>admin</username>
                    <password>admin</password>
                    <path>/${build.finalName}</path>
                    <update>true</update>
                </configuration>
        </plugin>
    </plugins>
</build>
```

>**注意**：这里 `configuration` 标签下的 url 为 `http://localhost:8866/manager/text`， `username` 与 `password` 就是刚才在Tomcat中配置的。  


使用如下命令完成部署。
```bash
mvn tomcat7: deploy
```

如果是重新部署则运行:
```bash
mvn tomcat7: redeploy
```  

如果第一次部署使用 `mvn tomcat7:redeploy`，则只会执行上传war文件，服务器不会自动解压部署。 如果路径在tomcat服务器中已存在并且使用 `mvn tomcat7:deploy` 命令的话，上面的配置中一定要配置 `<update>true</update>`，不然会报错。

如果项目已经编译打包，则可以使用 `mvn tomcat7:deploy-only` 来跳过重新打包的时间，直接将现有的包发布到Tomcat。  

```bash
cd /d cd /d d:\tfs\project\code\mocha
mvn tomcat7:deploy-only -pl mocha-web
```  
`mocha-web` 为工程下的模块。 `-pl` 指定执行哪个模块。将上边的脚本保存为批处理文件 `deploy.cmd`




# TFS 获取最新代码
---  

公司的项目代码通过 Team Fondation Server `TFS` 来管理，所以需要 Visual Studio 或者 Visual Studio Team Explorer 来完成代码的管理，这里使用功能更轻量级的后者。  

安装好 Visual Studio Team Explorer 之后，连接下Team Fondation Server，需要输入账号、密码。这时就可以通过命令行工具，获取最新代码了。使用前需要配置下工作区[^workplace]映射。  
>**建议**： 将代码目录单独映射一个工作区。通常，多数人喜欢将 TFS 根目录直接映射到工作区，这样虽然看起来方便，但会导致文件夹目录很深，而且要将工作区更新，就需要把 TFS 上所有的内容都更新一遍。  

之后将 `\Common7\IDE\CommonExtensions\` 下的 `Microsoft\TeamFoundation\Team Explorer` 加入 `Path` 环境变量。 这样就可以使用其中的 `TF.EXE` 提供命令来完成一些操作了。  这里主要使用一个命令 `get` [^get]  。

```bash
tf get [itemspec] [/version:versionspec] [/all] [/overwrite] [/force] [/remap]
[/recursive] [/preview] [/noautoresolve] [/noprompt]
[/login:username,[password]]
```
`get` 支持通配符，例如： `tf get *.java` 获取目录下所有的 Java 源文件。  

>**Note**: 如果不加任何参数， `get` 会更新整个工作区。  

```bash
cd /d d:\tfs\project\code
tf get
```  

可以将这段脚本保存为批处理文件如：`update.cmd`

# 编译

获取到最新代码就可以通过 maven 来编译项目了。

```bash
cd /d d:\tfs\project\code\mocha
mvn clean install -pl mocha-web -am -amd
```

同样将这段代码保存为批处理文件，如：`build.cmd`

# 定时任务
---
获取最新代码、编译、部署，都实现了，现在需要做的就是把这几个步骤串起来，每天晚上调用即可。  
先用一个脚本将上边的几个脚本串起来执行。  

```bash
%comspec% update.cmd
%comspec% build.cmd
%comspec% deploy.cmd
```  
>**Note**: comspec [^comspec] 是Windows系统的一个内置环境变量。  

保存为`task-doploy.cmd`

Windows 提供了任务计划程序， 在其中添加一个任务，操作执行`task-doploy.cmd`


# 内存泄漏
--- 

有文章指出，使用上面的方法进行部署后会出现严重的内存泄漏现象[^memoryleak]。实际情况确实如此，偶尔会出现部署失败的情况。按照文中的方案配置了，`classloader-leak-prevention-servlet`，问题依然存在。目前仍未找到原因，待后续找原因，再做分析吧。


# 参考资料
---

[^get]: [Gets (downloads) either the latest version or a specified version of one or more files or folders from Team Foundation Server to the workspace.](https://docs.microsoft.com/en-us/vsts/tfvc/get-command)  
[^workplace]: [Your workspace is your local copy of the team’s codebase. ](https://docs.microsoft.com/en-us/vsts/tfvc/create-work-workspaces)  
[^memoryleak]: [部署时产生内存泄漏的原因是每次（重新）部署时，Tomcat会为项目新建一个类加载器，而旧的类加载器没有被GC回收](http://blog.csdn.net/wudinaniya/article/details/77659358)  
[^comspec]:[COMSPEC or ComSpec is one of the environment variables used in DOS, OS/2 and Windows, which normally points to the command line interpreter, which is by default COMMAND.COM in DOS or CMD.EXE in OS/2 and Windows NT. ](https://en.wikipedia.org/wiki/COMSPEC)