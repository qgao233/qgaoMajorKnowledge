# 事件驱动模型: 加载ContextLoaderListener

>观察者模式: ContextLoaderListener

每一个整合spring框架的项目中，总是不可避免地要在web.xml中加入这样一段配置:

```
<!-- 配置spring核心监听器，默认会以 /WEB-INF/applicationContext.xml作为配置文件 -->  
<listener>  
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>  
</listener>  
<!-- contextConfigLocation参数用来指定Spring的配置文件 -->  
<context-param>  
    <param-name>contextConfigLocation</param-name>  
    <param-value>/WEB-INF/applicationContext*.xml</param-value>  
</context-param>　
```

类定义：
```
public class ContextLoaderListener extends ContextLoader implements ServletContextListener  
```
可以看到ContextLoaderListener继承自ContextLoader，实现的是ServletContextListener接口。

## 继承ContextLoader有什么作用？

ContextLoaderListener可以指定在Web应用程序启动时载入Ioc容器，正是通过ContextLoader来实现的，可以说是Ioc容器的初始化工作。

## 实现ServletContextListener又有什么作用？

ServletContextListener接口里的函数会结合Web容器的生命周期被调用。因为ServletContextListener是ServletContext的监听者，如果ServletContext发生变化，会触发相应的事件，而监听器一直对事件监听，如果接收到了变化，就会做出预先设计好的相应动作。由于ServletContext变化而触发的监听器的响应具体包括：在服务器启动时，ServletContext被创建的时候，服务器关闭时，ServletContext将被销毁的时候等，相当于web的生命周期创建与效果的过程。

## 那么ContextLoaderListener的作用是什么？

ContextLoaderListener的作用就是启动Web容器时，读取在contextConfigLocation中定义的xml文件，自动装配ApplicationContext的配置信息，并产生WebApplicationContext对象，然后将这个对象放置在ServletContext的属性里，这样我们只要得到Servlet就可以得到WebApplicationContext对象，并利用这个对象访问spring容器管理的bean。
简单来说，就是上面这段配置为项目提供了spring支持，初始化了Ioc容器。


## 那又是怎么为我们的项目提供spring支持的呢？

上面说到“监听器一直对事件监听，如果接收到了变化，就会做出预先设计好的相应动作”。而监听器的响应动作就是在服务器启动时contextInitialized会被调用，关闭的时候contextDestroyed被调用。这里我们关注的是WebApplicationContext如何完成创建。因此销毁方法就暂不讨论。

```
//重写ServletContextListener接口里的方法  
@Override  
public void contextInitialized(ServletContextEvent event) {  
    //初始化webApplicationCotext,调用的是父类ContextLoader的方法  
    initWebApplicationContext(event.getServletContext());  
}  
```
WebApplicationContext根据在context-params中配置的contextClass和contextConfigLocation完成初始化。

```
public WebApplicationContext initWebApplicationContext(  
        ServletContext servletContext) {  
        
    // application对象中存放了spring context，则抛出异常  
    // 其中ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE = WebApplicationContext.class.getName() + ".ROOT";  
    if (servletContext  
            .getAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE) != null) {  
            
        throw new IllegalStateException(  
                "Cannot initialize context because there is already a root application context present - "  
                        + "check whether you have multiple ContextLoader* definitions in your web.xml!");  
    }  
        
    // 创建得到WebApplicationContext  
    // createWebApplicationContext最后返回值被强制转换为ConfigurableWebApplicationContext类型  
    if (this.context == null) {  
        this.context = createWebApplicationContext(servletContext);  
    }  
        
    // 只要上一步强转成功，进入此方法（事实上走的就是这条路）  
    if (this.context instanceof ConfigurableWebApplicationContext) {  
            
        // 强制转换为ConfigurableWebApplicationContext类型  
        ConfigurableWebApplicationContext cwac = (ConfigurableWebApplicationContext) this.context;  
            
        // cwac尚未被激活，目前还没有进行配置文件加载  
        if (!cwac.isActive()) {  
                
            // 加载配置文件  
            configureAndRefreshWebApplicationContext(cwac, servletContext);  
                
            【点击进入该方法发现这样一段：  
                
                //为wac绑定servletContext  
                wac.setServletContext(sc);  
                
                //CONFIG_LOCATION_PARAM=contextConfigLocation  
                //getInitParameter(CONFIG_LOCATION_PARAM)解释了为什么配置文件中需要有contextConfigLocation项  
                //需要注意还有sevletConfig.getInitParameter和servletContext.getInitParameter作用范围是不一样的  
                String initParameter = sc.getInitParameter(CONFIG_LOCATION_PARAM);  
                if (initParameter != null) {  
                    //装配ApplicationContext的配置信息  
                    wac.setConfigLocation(initParameter);  
                }  
            】  
        }  
    }  
        
    // 把创建好的spring context，交给application内置对象，提供给监听器/过滤器/拦截器使用  
    servletContext.setAttribute(  
            WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE,  
            this.context);  
        
    // 返回webApplicationContext  
    return this.context;  
}  
```

initWebApplicationContext中加载了contextConfigLocation的配置信息，初始化Ioc容器，说明了上述配置的必要性。