---
title: Could not initialize class sun.awt.X11GraphicsEnvironment
date: 2018-11-26 22:55:24
categories:
- Java
tags:
---

今天公司自建的接口管理工具-rap挂了，上服务器重启后页面无法生成验证码图片，查看日志发现报了以下异常：<!--more-->

```
java.lang.NoClassDefFoundError: Could not initialize class sun.awt.X11GraphicsEnvironment
	at java.lang.Class.forName0(Native Method)
	at java.lang.Class.forName(Class.java:264)
	at java.awt.GraphicsEnvironment.createGE(GraphicsEnvironment.java:102)
	at java.awt.GraphicsEnvironment.getLocalGraphicsEnvironment(GraphicsEnvironment.java:81)
	at java.awt.image.BufferedImage.createGraphics(BufferedImage.java:1182)
	at com.google.code.kaptcha.text.impl.DefaultWordRenderer.renderWord(DefaultWordRenderer.java:39)
	at com.google.code.kaptcha.impl.DefaultKaptcha.createImage(DefaultKaptcha.java:43)
	at com.google.code.kaptcha.servlet.KaptchaServlet.doGet(KaptchaServlet.java:84)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:622)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:729)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:292)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:207)
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:52)
```

经查询后发现是tomcat 启动用户无法访问XServer 或者XServer没有启动，但是服务器环境一般不会去更改，所以想通过应用模拟的方式来解决这个问题；方法为去掉 `JAVA_OPTS` 并添加 `-Djava.awt.headless=true`配置，如下形式：

```
# Uncomment the following line to make the umask available when using the
# org.apache.catalina.security.SecurityListener
JAVA_OPTS="$JAVA_OPTS -Djava.awt.headless=true"
```





