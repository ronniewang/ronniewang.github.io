# Servlet 中的 8 个 Listener

各位 Java 程序员们，估计好久没有接触过 Servlet 的概念了吧，有人会说，Spring MVC 不香吗，为啥要用 Servlet

当然，Sping MVC 已经算是 Java Web 开发标准框架了，但是 Spring MVC 的核心就是一个 Servlet 啊，所以，了解 Servlet 是深入学习 
Spring MVC 的第一步

这里简单介绍一下 Servlet 中提供的几个 Listener，实现这些 Listener 使你可以感知到 Servlet 的变化，并执行相应的动作，顺便说一句，这里的 Listener 就是观察者模式中的 Observer

Servlet 中一共有 8 个 Listener，分别如下

## ServletContextAttributeListener

场景：你想知道一个 Web 应用上下文中是否增加、删除或替换了一个属性

监听者接口：javax.servlet.ServletContextAttributeListener

方法

* attributeAdded
* attributeRemoved
* attributeReplaced

事件类型：ServletContextAttributeEvent

## HttpSessionListener

场景：你想知道有多少个并发用户，也就是说，你想跟踪活动的会话

监听者接口：javax.servlet.http.HttpSessionListener

方法

* sessionCreated
* sessionDestroyed

事件类型：HttpSessionEvent

## ServletRequestListener

场景：每次请求到来时你都想知道，以便建立日志记录

监听者接口：javax.servlet.ServletRequestListener

方法：

* requestInitialized
* requestDestroyed

事件类型：ServletRequestEvent

## ServletRequestAttributeListener

场景：增加、删除或替换一个请求属性时你希望能够知道

监听者接口：javax.servlet.ServletRequestAttributeListener

方法： 
* attributeAdded
* attributeRemoved
* attributeReplaced

事件类型：ServletRequestAttributeListener

## HttpSessoinBindingListener

场景：你有一个属性类，而且你希望这个类型的对象在绑定到一个会话或从会话中或从会话删除时得到通知

监听者接口：javax.servlet.HttpSessoinBindingListener

方法

* valueBound
* valueUnbound

事件类型：HttpSessionBindingEvent

## HttpSessionAttributeListener

场景：增加、删除或添加一个会话属性时你希望能够知道

监听者接口：javax.servlet.HttpSessionAttributeListener

方法

* attributeAdded
* attributeRemoved
* attributeReplaced

事件类型：HttpSessionBindingEvent

## ServletContextListener

场景：你想知道是否创建或撤销了一个上下文

监听者接口：javax.servlet.ServletContextListener

方法    
* contextInitializated
* contextDestroyed

事件类型：ServletContextEvent

## HttpSessionActivationListener

场景：你有一个属性类，而且希望这个类型的对象在其绑定的会话迁移到另一个JVM时得到通知

监听者接口：javax.servlet.http.HttpSessionActivationListener

方法：

* sessionDidActivite
* sessionWillPassivate

事件类型：HttpSessionEvent

### 注意：

HttpSessionListener和HttpSessionActivationListener共用HttpSessionEvent事件

ServletSessionBindingListener和ServletSessionAttributeListener共用HttpSessionBindingEvent事件

## 举例

这里我们简单举一个例子说明一下如何实现上述的 Listener，Spring MVC 中的 `org.springframework.web.context.ContextLoaderListener` 实现了 `ServletContextListener`，主要代码如下

```java
@Override
public void contextInitialized(ServletContextEvent event) {
  initWebApplicationContext(event.getServletContext());
}

@Override
public void contextDestroyed(ServletContextEvent event) {
  closeWebApplicationContext(event.getServletContext());
  ContextCleanupListener.cleanupAttributes(event.getServletContext());
}
  ```
  
从代码中我们看出，contextInitialized 方法初始化了 WebApplicationContext，contextDestroyed 方法关闭了 WebApplicationContext，并做了一些清理工作

## 总结

虽然 Servlet 现在主要是在框架中使用，但是我们如果了解了相关的原理，对使用框架和解决问题会更有帮助

