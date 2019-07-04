---
layout: post
title:  "使用 Apache Email 发送邮件"
tags: [Java, Apache Email]
comments: true
---
QQ 邮箱需要开启邮箱的 POP3/SMTP 服务或者 IMAP/SMTP 服务，开通成功将会给予一个识别码，这个识别码将是您使用程序发送邮件登陆的的通行证。

QQ 邮箱的 POP3 和 SMTP 服务器地址

| 邮箱 | POP3服务器（端口995） | SMTP服务器（端口465或587） |
|:--------|:--------|:--------|
| qq.com | pop.qq.com | smtp.qq.com |
|=====
{: rules="groups"}

SMTP服务器需要身份认证。

``` java
SimpleEmail simpleEmail = new SimpleEmail();
simpleEmail.setHostName("smtp.qq.com");
simpleEmail.setAuthentication("3*******8", "g**************i");
simpleEmail.setSmtpPort(587);
simpleEmail.setFrom("3*******8@qq.com", "y*******n", "UTF-8");
simpleEmail.addTo("9*******3@qq.com", "c*****n");
simpleEmail.setSubject("标题内容");
simpleEmail.setMsg("正文内容");
simpleEmail.send();
```
