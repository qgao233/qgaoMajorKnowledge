# 加载DispatcherServlet初始化顺序详解

简单说就是：
* DispatcherServlet的init方法里面load了springmvc的配置信息，
* 然后初始化了spring容器（调用了onRefresh方法），把controller的信息缓存了，比如映射信息；
* 然后DispatcherServlet会拦截所有的请求，根据用户的请求信息通过缓存的映射信息找到对应的controller中对应的方法，继而反射调用（其实底层的源码就是反射调用controller的方法），
* 然后视图裁决、解析等等工作。

## 1. Web容器启动时将调用HttpServletBean的init方法

```
public abstract class HttpServletBean extends HttpServlet implements EnvironmentAware{  
    @Override  
    public final void init() throws ServletException {  
        //省略部分代码  
        //1、如下代码的作用是将Servlet初始化参数(<init-param>)设置到该servlet对象上  
        //初始化参数如contextAttribute、contextClass、namespace、contextConfigLocation；  
        try {  
            PropertyValues pvs = new ServletConfigPropertyValues(getServletConfig(), this.requiredProperties);  
            BeanWrapper bw = PropertyAccessorFactory.forBeanPropertyAccess(this);  
            ResourceLoader resourceLoader = new ServletContextResourceLoader(getServletContext());  
            bw.registerCustomEditor(Resource.class, new ResourceEditor(resourceLoader, this.environment));  
            initBeanWrapper(bw);  
            bw.setPropertyValues(pvs, true);  
        }  
        catch (BeansException ex) {  
            //…………省略其他代码  
        }  
        //2、提供给子类初始化的扩展点，该方法由FrameworkServlet覆盖  
        initServletBean();  
        if (logger.isDebugEnabled()) {  
            logger.debug("Servlet '" + getServletName() + "' configured successfully");  
        }  
    }  
    //…………省略其他代码  
}  
```
将Servlet初始化参数（init-param）设置到该servlet对象上（如contextAttribute、contextClass、namespace、contextConfigLocation这些参数），通过[BeanWrapper（装饰模式）](../section11/index.md) 简化设值过程，方便后续使用；

>注：ServletConfig获取配置参数的方法和ServletContext获取配置参数的方法完全一样，只是ServletConfig是取得当前Servlet的配置参数，而ServletContext是获取整个web应用的配置参数。


提供给子类初始化扩展点: initServletBean()，该方法由FrameworkServlet覆盖。

## 2. FrameworkServlet通过initServletBean()进行Web上下文初始化

该方法主要作用如下：

1. 初始化web上下文； 
2. 提供给子类初始化扩展点；
```
public abstract class FrameworkServlet extends HttpServletBean {  
    @Override  
    protected final void initServletBean() throws ServletException {  
        //省略部分代码  
        try {  
                //1、初始化Web上下文  
            this.webApplicationContext = initWebApplicationContext();  
                //2、提供给子类初始化的扩展点  
            initFrameworkServlet();  
        }  
        //省略部分代码  
    }  
}  
```
initWebApplicationContext():
```
protected WebApplicationContext initWebApplicationContext() {  
    //ROOT上下文（ContextLoaderListener加载的）  
    WebApplicationContext rootContext =  
            WebApplicationContextUtils.getWebApplicationContext(getServletContext());  
    WebApplicationContext wac = null;  
    if (this.webApplicationContext != null) {  
        // 1、在创建该Servlet注入的上下文  
        wac = this.webApplicationContext;  
        if (wac instanceof ConfigurableWebApplicationContext) {  
            ConfigurableWebApplicationContext cwac = (ConfigurableWebApplicationContext) wac;  
            if (!cwac.isActive()) {  
                if (cwac.getParent() == null) {  
                    cwac.setParent(rootContext);  
                }  
                configureAndRefreshWebApplicationContext(cwac);  
            }  
        }  
    }  
    if (wac == null) {  
            //2、查找已经绑定的上下文  
        wac = findWebApplicationContext();  
    }  
    if (wac == null) {  
        //3、如果没有找到相应的上下文，并指定父亲为ContextLoaderListener  
        wac = createWebApplicationContext(rootContext);  
    }  
    if (!this.refreshEventReceived) {  
            //4、刷新上下文（执行一些初始化）  
        onRefresh(wac);  
    }  
    if (this.publishContext) {  
        // Publish the context as a servlet context attribute.  
        String attrName = getServletContextAttributeName();  
        getServletContext().setAttribute(attrName, wac);  
        //省略部分代码  
    }  
    return wac;  
}  
```
从initWebApplicationContext()方法可以看出， **ContextLoaderListener（观察者模式）** 加载了上下文将作为根上下文（DispatcherServlet的父容器）。 最后调用了onRefresh()方法执行容器的一些初始化，这个方法由子类实现，来进行扩展。

## 3. DispatcherServlet实现了onRefresh()方法: 提供一些前端控制器相关的配置

**九大组件**
```
public class DispatcherServlet extends FrameworkServlet {  
        //实现子类的onRefresh()方法，该方法委托为initStrategies()方法。  
    @Override  
    protected void onRefresh(ApplicationContext context) {  
        initStrategies(context);  
    }  
    
    //初始化默认的Spring Web MVC框架使用的策略（如HandlerMapping）  
    protected viod initStrategies(ApplicationContext context){  
        initMultipartResolver(context);//初始化上传文件解析器  
        initLocaleResolver(context);//初始化本地解析器  
        initThemeResolver(context);//初始化主题解析器  
        initHandlerMapping(context);//初始化处理器映射器,将请求映射到处理器  
        initHandlerAdapters(context);//初始化处理器适配器  
        initHandlerExceptionResolver(context);//初始化处理器异常解析器,如果执行过程中遇到异常将交给HandlerExceptionResolver来解析  
        initRequestToViewNameTranslator(context);//初始化请求到具体视图名称解析器  
        initViewResolvers(context);//初始化视图解析器,通过ViewResolver解析逻辑视图名到具体视图实现  
        initFlshMapManager(context);//初始化flash映射管理  
    }  
}  
```
initStrategies方法将在WebApplicationContext初始化后自动执行,自动扫描上下文的Bean,根据名称或者类型匹配的机制查找自定义组件,如果没有找到则会装配一套Spring的默认组件.在org.springframework.web.servlet路径下有一个**DispatcherServlet.properties**配置文件,该文件指定了DispatcherServlet所使用的默认组件.

在DispatcherServlet同一个目录下的**DispatchServlet.properties**文件中默认的九大组件：
```
1.org.springframework.web.servlet.LocaleResolver=org.springframework.web.servlet.i18n.AcceptHeaderLocaleResolver  
    
2.org.springframework.web.servlet.ThemeResolver=org.springframework.web.servlet.theme.FixedThemeResolver  
// 带"\"的是多个默认配置Handler类  
3.org.springframework.web.servlet.HandlerMapping=org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping,\  
    org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping  
    
4.org.springframework.web.servlet.HandlerAdapter=org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter,\  
    org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter,\  
    org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter  
    
5.org.springframework.web.servlet.HandlerExceptionResolver=org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerExceptionResolver,\  
    org.springframework.web.servlet.mvc.annotation.ResponseStatusExceptionResolver,\  
    org.springframework.web.servlet.mvc.support.DefaultHandlerExceptionResolver  
    
6.org.springframework.web.servlet.RequestToViewNameTranslator=org.springframework.web.servlet.view.DefaultRequestToViewNameTranslator  
//这个也可以有多个，这里默认只配置了一个而已  
7.org.springframework.web.servlet.ViewResolver=org.springframework.web.servlet.view.InternalResourceViewResolver  
    
8.org.springframework.web.servlet.FlashMapManager=org.springframework.web.servlet.support.SessionFlashMapManager  
```
DispatcherServlet启动时会进行我们需要的Web层Bean的配置，如HandlerMapping、HandlerAdapter等，而且如果我们没有配置，还会给我们提供默认的配置。

整个DispatcherServlet初始化的过程具体主要做了如下两件事情：
1. 初始化Spring Web MVC使用的Web上下文，并且可能指定父容器为（ContextLoaderListener加载了根上下文）；
2. 初始化DispatcherServlet使用的策略，如HandlerMapping、HandlerAdapter等。

