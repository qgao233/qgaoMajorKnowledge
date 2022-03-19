# web.xml内的标签加载顺序

https://blog.csdn.net/qq_22075041/article/details/78692780

当启动一个WEB项目容器时，容器包括(JBoss,Tomcat等)。首先会去读取web.xml配置文件里的配置。
顺序为：
```
<listener> -> <context-param> -> <filter> -> <servlet> 
```
## 流程

启动WEB项目的时候，容器首先会去读取web.xml配置文件中的两个节点：`<listener> </listener>`和`<context-param> </context-param>`。

容器创建\<listener>\</listener>中的类实例，根据配置的class类路径\<listener-class>来创建监听，在监听中会有初始化方法，启动Web应用时，系统调用Listener的该方法 contextInitialized(ServletContextEvent args)，在这个方法中获得：
```
//容器创建一个ServletContext(application),这个web项目的所有部分都将共享这个上下文。  
ServletContext application =ServletContextEvent.getServletContext();  
//容器以<context-param></context-param>的name作为键，value作为值，将其转化为键值对，存入ServletContext  
context-param的值 = application.getInitParameter("context-param的键"); 
```

>注：ServletConfig获取配置参数的方法和ServletContext获取配置参数的方法完全一样，只是ServletConfig是取得当前Servlet的配置参数，而ServletContext是获取整个web应用的配置参数。

得到这个context-param的值之后，你就可以做一些操作了。

　　举例：
>你可能想在项目启动之前就打开数据库，那么这里就可以在<context-param>中设置数据库的连接方式（驱动、url、user、password），在监听类中初始化数据库的连接。这个监听是自己写的一个类，除了初始化方法，它还有销毁方法，用于关闭应用前释放资源。比如:说数据库连接的关闭，此时，调用contextDestroyed(ServletContextEvent args)，关闭Web应用时，系统调用Listener的该方法。

　　接着，容器会读取`<filter></filter>`，根据指定的类路径来实例化过滤器。

以上都是在WEB项目还没有完全启动起来的时候就已经完成了的工作。

如果系统中有`<servlet>`，则Servlet是在第一次发起请求的时候被实例化的，而且一般不会被容器销毁，它可以服务于多个用户的请求。所以，Servlet的初始化都要比上面提到的那几个要迟。

不过如果需要在web容器启动时就加载某servlet的话，可以在某servlet标签内部加上<load-on-startup>让该servlet紧接着filter加载完后进行加载，如：

```
<servlet>  
    <servlet-name>springmvc</servlet-name>  
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>  
    <init-param>  
    <param-name>contextConfigLocation</param-name>  
    <param-value>classpath:/config/spring-servlet.xml</param-value>  
    </init-param>  
    <load-on-startup>1</load-on-startup>  
</servlet>  
<servlet-mapping>  
    <servlet-name>springmvc</servlet-name>  
    <url-pattern>/rest/*</url-pattern>  
</servlet-mapping>  
```