---
layout: post
title: ；LogBack
description: 从Log4迁移到LogBack的理由
category: JavaWeb学习笔记
---
无论从设计上还是实现上，Logback相对log4j而言有了相对多的改进。不过尽管难以一一细数，这里还是列举部分理由为什么选择logback而不是log4j。牢记logback与log4j在概念上面是很相似的，它们都是有同一群开发者建立。所以如果你已经对log4j很熟悉，你也可以很快上手logback。如果你喜欢使用log4j,你也许会迷上使用logback。 

## 更快的执行速度

基于我们先前在log4j上的工作，logback 重写了内部的实现，在某些特定的场景上面，甚至可以比之前的速度快上10倍。在保证logback的组件更加快速的同时，同时所需的内存更加少。


## 充分的测试

Logback 历经了几年，数不清小时数的测试。尽管log4j也是测试过的，但是Logback的测试更加充分，跟log4j不在同一个级别。我们认为，这正是人们选择Logback而不是log4j的最重要的原因。人们都希望即使在恶劣的条件下，你的日记框架依然稳定而可靠。
## logback-classic 非常自然的实现了SLF4J
logback-classic中的登陆类自然的实现了SLF4J。当你使用 logback-classic作为底层实现时，涉及到LF4J日记系统的问题你完全不需要考虑。更进一步来说，由于 logback-classic强烈建议使用SLF4J作为客户端日记系统实现，如果需要切换到log4j或者其他，你只需要替换一个jar包即可，不需要去改变那些通过

SLF4J API 实现的代码。这可以大大减少更换日记系统的工作量。

## 扩展文档

Logback附带详细的和不断更新的文档。
## 使用XML配置文件或者Groovy

配置logback的传统方法是通过XML文件。在文档中，大部分例子都是是用XML语法。但是，对于logback版本0.9.22，通过Groovy编写的配置文件也得到支持。相比于XML，Groovy风格的配置文件更加直观，连贯和简短的语法。

现在， 已经有一个工具自动把logback.xml文件迁移至logback.groovy。

## 自动重新载入配置文件

Logback-classic可以在配置文件被修改后，自动重新载入。这个扫描过程很快，无资源争用，并且可以动态扩展支持在上百个线程之间每秒上百万个调用。它和应用服务器结合良好，并且在JEE环境通用，因为它不会调用创建一个单独的线程来做扫描。
## 优雅地从I/O错误中恢复

FileAppender和它的子类，包括RollingFileAppender，可以优雅的从I/O错误中恢复。所以，如果一个文件服务器临时宕机，你再也不需要重启你的应用，而日志功能就能正常工作。当文件服务器恢复工作，logback相关的appender就会透明地和快速的从上一个错误中恢复。

## 自动清除旧的日志归档文件

通过设置TimeBasedRollingPolicy 或者 SizeAndTimeBasedFNATP的 maxHistory 属性，你就可以控制日志归档文件的最大数量。如果你的回滚策略是每月回滚的，并且你希望保存一年的日志，那么只需简单的设置maxHistory属性为12。对于12个月之前的归档日志文件将被自动清除。
## 自动压缩归档日志文件

RollingFileAppender可以在回滚操作中，自动压缩归档日志文件。压缩通常是异步执行的，所以即使是很大的日志文件，你的应用都不会因此而被阻塞。

## 谨慎模式

在谨慎模式中，在多个JVM中运行的多个FileAppender实例，可以安全的写入统一个日志文件。谨慎模式可以在一定的限制条件下应用于RollingFileAppender。
Lilith
Lilith是logback的一个记录和访问事件查看器。它相当于log4j的 chainsaw，但是Lilith设计的目的是处理大量的日志记录。


## 配置文件中的条件处理

开发者通常需要在不同的目标环境中变换logback的配置文件，例如开发环境，测试环境和生产环境。这些配置文件大体是一样的，除了某部分会有不同。为了避免重复，logback支持配置文件中的条件处理，只需使用<if>,<then>和<else>，那么同一个配置文件就可以在不同的环境中使用了。

## 过滤

Logback拥有远比log4j更丰富的过滤能力。例如，让我们假设，有一个相当重要的商业应用部署在生产环境。考虑到大量的交易数据需要处理，记录级别被设置为WARN，那么只有警告和错误信息才会被记录。现在，想象一下，你在开发环境遇到了一个臭虫，但是在测试平台中却很难发现，因为一些环境之间(生产环境/测试环境)的未知差异。


使用log4j，你只能选择在生产系统中降低记录的级别到DEBUG，来尝试发现问题。但是很不幸，这会生成大量的日志记录，让分析变得困难。更重要的是，多余的日志记录会影响到生产环境的性能。

使用logback，你可以选择保留只所有用户的WARN级别的日志，而除了某个用户，例如Alice，而她就是问题的相关用户。当Alice登录系统，她就会以DEBUG级别被记录，而其他用户仍然是以WARN级别来记录日志。这个功能，可以通过在配置文件的XML中添加4行。请在相关章节中查找MDCFilter

## SiftingAppender

SiftingAppender是一个全能的追加器。它可以基于任何给定的实时属性分开（或者筛选）日志。例如，SiftingAppender可以基于用户会话分开日志事件，这样，可以为每一个用户建立一个独立的日志文件。
堆栈轨迹信息包含包的数据

当logback打印一个异常，堆栈轨迹信息将包含包的相关数据。下面是一个通过 logback-demo 生成的堆栈信息：

14:28:48.835 [btpool0-7] INFO  c.q.l.demo.prime.PrimeAction - 99 is not a valid value
java.lang.Exception: 99 is invalid
  at ch.qos.logback.demo.prime.PrimeAction.execute(PrimeAction.java:28) [classes/:na]
  at org.apache.struts.action.RequestProcessor.processActionPerform(RequestProcessor.java:431) [struts-1.2.9.jar:1.2.9]
  at org.apache.struts.action.RequestProcessor.process(RequestProcessor.java:236) [struts-1.2.9.jar:1.2.9]
  at org.apache.struts.action.ActionServlet.doPost(ActionServlet.java:432) [struts-1.2.9.jar:1.2.9]
  at javax.servlet.http.HttpServlet.service(HttpServlet.java:820) [servlet-api-2.5-6.1.12.jar:6.1.12]
  at org.mortbay.jetty.servlet.ServletHolder.handle(ServletHolder.java:502) [jetty-6.1.12.jar:6.1.12]
  at ch.qos.logback.demo.UserServletFilter.doFilter(UserServletFilter.java:44) [classes/:na]
  at org.mortbay.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1115) [jetty-6.1.12.jar:6.1.12]
  at org.mortbay.jetty.servlet.ServletHandler.handle(ServletHandler.java:361) [jetty-6.1.12.jar:6.1.12]
  at org.mortbay.jetty.webapp.WebAppContext.handle(WebAppContext.java:417) [jetty-6.1.12.jar:6.1.12]
  at org.mortbay.jetty.handler.ContextHandlerCollection.handle(ContextHandlerCollection.java:230) [jetty-6.1.12.jar:6.1.12]

从上面的信息，你可以发现这个应用使用Struts 1.2.9 而且是使用 jetty 6.1.12部署的。所以，堆栈轨迹信息将快速的告诉读者，关于异常发生的类还有包和包的版本。当你的客户发送一个堆栈轨迹信息给你，作为一个开发人员，你就不需要让他们告诉你他们正在使用的包的版本。这项信息已经包括在堆栈轨迹信息中。详细请参考  "%xThrowable" conversion word.

这项功能可以非常有帮助的说明，有些用户误以为这是IDE的功能。

Logback-access模块，提供了通过HTTP访问日志的能力，是logback不可或缺的组成部分

最后但绝非最不重要的是，作为logback发布包的一部分，logback-access模块可与Jetty或者Tomcat进行集成，提供了非常丰富而强大的通过HTTP访问日志的功能。因为logback-access模块是logback初期设计方案中的一部分，因此，所有你所喜欢的logback-classic模块所提供的全部特性logback-access同样也具备。
结束语

我们给出了许多选择logback而不选择log4j的理由。简而言之，既然logback构建于我们先前所构建的log4j之上，logback可以说就是一个更好的log4j。

