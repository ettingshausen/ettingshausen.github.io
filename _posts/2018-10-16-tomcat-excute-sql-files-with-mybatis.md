---
layout: cayman
title:  "Tomcat启动时执行SQL脚本"
date:   2018-10-16 13:37:00 +0800
tags: tomcat
categories: tomcat
published: true
author: ettingshausen
---  

Tomcat启动时执行脚本，可以利用通过监听器来实现。定义一个监听器实现 `ServletContextListener` 接口。在 `contextInitialized` 方法中完成脚本执行的操作。 使用 `ScriptRunner` 执行脚本：

```java
public class ContextInitializeListener implements ServletContextListener {

    private Logger logger = LoggerFactory.getLogger(ContextInitializeListener.class);

    @Override
    public void contextInitialized(ServletContextEvent sce) {

        ApplicationContext applicationContext = 
        WebApplicationContextUtils.getWebApplicationContext(sce.getServletContext());

        SqlSessionFactory sqlSessionFactory = applicationContext.getBean(SqlSessionFactory.class);

        SqlSession sqlSession = sqlSessionFactory.openSession(ExecutorType.SIMPLE, true);
        ScriptRunner sqlRunner = new ScriptRunner(sqlSession.getConnection());
        sqlRunner.setAutoCommit(true);
        sqlRunner.setSendFullScript(true);
        sqlRunner.setDelimiter("GO");
        try {
            Resources.setCharset(Charset.forName("GBK"));
            sqlRunner.runScript(Resources.getResourceAsReader("script/TaskReturnNotification.sql"));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {

    }
}

```
在 `web.xml` 添加监听器配置：

```xml
 <listener>
        <listener-class>com.nsbc.listener.ContextInitializeListener</listener-class>
 </listener>
```

# 注意事项
----

`ScriptRunner` 默认的分隔符 `DEFAULT_DELIMITER` 是 `;` 