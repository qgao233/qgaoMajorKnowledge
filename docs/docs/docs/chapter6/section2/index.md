# AOP

AOP功能可以通过JavaSE动态代理和字节码生成实现

spring的AOP功能是通过**JavaSE动态代理**和**cglib**实现的

* JavaSE动态代理：一个类需要实现了某个接口，才能在运行期间动态的构造这个接口的实现对象，通过反射实现。

* cglib：本身是动态字节码生成工具，可以对没有实现业务接口的对象进行增强。


\<aop:config>是Spring AOP配置的根元素，其中有个属性为proxy-target-class

如果为true,则使用cglib生成代理对象，相反为false，

>默认如果目标类实现了接口，选择用jdk动态代理，没有实现接口就采用cglib生成一个被代理对象的子类来作为代理。

可以用注解配置Spring AOP

## **使用实例**

1. 导入aop模块：Spring AOP：（spring-aspects）
1. 定义一个业务逻辑类（MathCalculator）；在业务逻辑运行的时候讲日志进行打印（方法之前、方法运行结束、方法出现异常等)
1. 定义一个日志切面类（LOgAspects）；切面类里面的方法需要动态感知MathCalculator.div运行到哪里然后执行对应的切面方法；
         通知方法：
                 前置通知(@Before)：logStart:在目标方法div()运行之前运行
                 后置通知(@After)：logEnd：在目标方法div()运行结束之后运行
                 返回通知(@AfterReturning)：logReturn：在目标方法div()正常返回之后运行
                 异常通知(@AfterThrowing)：logException：在目标方法div()出现异常之后运行
                 环绕通知：动态代理，手动推进目标方法运行（joinPoint.procced()）
1. 给切面类的目标方法标注何时何地运行（通知注解）
1. 将切面类和业务逻辑类（目标方法所在类）都加入到容器中；
1. 必须告诉Spring哪个类是切面类（给切面类上加一个注解：@Aspect）
1. ※给配置类中加@EnableAspectJAutoProxy 开启基于注解的AOP模式
    >在Spring中很多的@EnableXXX都是表示要开启XXX功能

 主要三步：
 1. 将业务逻辑组件和切面类都加入到容器中；告诉Spring哪个类是切面类（@Aspect）
 2. 在切面类上的每一个通知方法上标注通知注解，告诉Spring何时何地运行（切入点表达式）
 3. 开启基于注解的AOP模式；@EnableAspectJAutoProxy

```
@EnableAspectJAutoProxy
@Configuration
public class MainConfigOfAop {
	
	//业务逻辑类加入到容器中
	@Bean
	public MathCalculator mathCalculator() {
		System.out.println("mathCalculator bean");
		return new MathCalculator();
	}
 
	//切面类加入到容器中
	@Bean
	public LogAspects logAspects() {
		return new LogAspects();
	}
}

public class MathCalculator {
 
	public int div(int i, int j) {
		System.out.println("MathCalculator >> div");
		return i / j;
	}
 
}

@Aspect
public class LogAspects {
	
	//抽取公共的切入点表达式
	//1、本类引用
	//2、其他的切面引用
	@Pointcut("execution(public int com.spring.aop.MathCalculator.*(..))")
	private void pointCut(){};
	
	//@Before在目标方法之前切入；切入点表达式（指定在哪个方法切入）
	//JoinPoint一定要出现在参数列表的第一位
	@Before(value = "pointCut()")
	public void logStart(JoinPoint joinpoint) {
		System.out.println("logStart>>>>"+joinpoint.getSignature().getName()+">>>>"+Arrays.toString(joinpoint.getArgs()));
	}
 
	@After(value ="com.spring.aop.LogAspects.pointCut()")
	public void logEnd(JoinPoint joinpoint) {
		System.out.println("logEnd>>>>>"+joinpoint.getSignature().getName()+">>>>"+Arrays.toString(joinpoint.getArgs()));
	}
 
	@AfterReturning(value ="execution(public int com.spring.aop.MathCalculator.*(..))",returning="object")
	public void logReturn(Object object) {
		System.out.println("logReturn>>>>"+object);
	}
 
	@AfterThrowing(value = "execution(public int com.spring.aop.MathCalculator.*(..))",throwing = "object")
	public void logException(Exception object) {
		System.out.println("logException>>>>"+object);
	}
}

public class IOCTestAOP {
	@Test
	public void test01() {
		AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(MainConfigOfAop.class);
		
		MathCalculator mathCalculator = applicationContext.getBean(MathCalculator.class);
		mathCalculator.div(10, 0);
		applicationContext.close();
	}
}
```

## **增强执行顺序**

对于一个切面中多个不同Advice的执行顺序，是由对应增强器的invoke方法本身实现的，具体顺序如下所示：

>目标方法正常执行：
>@Around前 ->@Before ->执行方法 ->  @Around后 -> @After -> @AfterReturning
>
>目标方法抛异常：
>@Around前 ->@Before ->方法报错 -> @After -> @AfterThrowing

## **AOP实现原理**

### 注册

Spring容器启动时，全局搜索启动AOP的自定义标签`<aop:aspectj-autoproxy>`，
>自定义标签包括所有已存在的标签都有个解析parse方法，aop也不例外，

定位到注册该自定义标签的解析类AopNamespaceHandler中的parse方法。

1. 其中注册了类为**AnnotationAwareAspectJAutoProxyCreator**的BeanDefinition
>BeanDefinition为类，起标识作用。

2. 处理proxy-target-class跟expose-proxy（以key-value的形式放入BeanDefinition的propertyValues属性中）

若有多个切面类，Spring容器启动时会按照你配置的方式（在XML中以Bean标签的形式配置或者在切面类上加注解的方式）将这多个切面类一齐作为一个BeanDefinition加载注册到容器中，

### 获得代理类

其中**AnnotationAwareAspectJAutoProxyCreator类**“继承”了BeanPostProcessor接口，其中有个postProcessorAfterInitialization方法。

此方法的执行时机是spring容器启动，初始化所有bean后，
>即IOC的实现，getBean时，初始化完Bean对象之后。

该方法会判断该类中是否有被代理（AOP就是代理实现的），如果有代理，则会生成该类的对象的代理对象并返回。

**postProcessorAfterInitialization方法**，关键点有两处：

1. getAdvicesAndAdvisorsForBean方法获取到所有增强，以Object[]的形式存放；
   >遍历所有的bean(),校验每一个beanName对应的type，如果有AspectJ的注解，则通过advisorFactory.getAdvisor(factory)方法获取此切面类下的所有增强方法（先找到有Advice类注解(如@Before、@Around等)的方法，然后给每一个切点生成对应PointCut对象，用InstantiationModelAwarePointcutAdvisorImpl统一封装，并对不同的PoinCut使用对应的增强器初始化（如@Before对应AspectJMethodBeforeAdvice）增强器），以List<Advisor>形式存放。其中，切面中**每个增强+对应的PointCut**对应**一个Advisor**。
   >然后筛选获取到的所有增强器，只取到与当前bean相关的Advisor。
   >**注：由此可知，若有多个切面类增加同一个方法，其以类为单位的增强的调用顺序由bean注册进spring容器的顺序决定。**
2. createProxy方法针对增强创建代理，最终postProcessorAfterInitialization方法返回的对象就是这个创建的代理对象，而此代理对象最后就成了getBean方法获取到的对象。
   >先创建了AopProxyFactory，再创建AopProxy（创建AopProxy时，会根据配置项或者代理类的特性选择是Jdk还是Cglib），最后通过getProxy方法获得代理对象。
   >如果该类在上一步扫描时被多个Advisor增强了，则将其封装进此代理对象的属性AdvisedSupport advised中。

### AOP使用

假设该AOP选择jdk动态代理，那么所有的增强都会在invoke方法进行处理，其中夹杂着真正的对象的方法的调用（反射的method.invoke）。

代理的invoke方法中两个重要的点：
1. 处理exposeProxy：如果之前解析到的exposeProxy为true，则通过AopContext.setCurrentProxy(proxy)，在代码中使用AopContext.getCurrentProxy才能获取到当前的代理对象。
2. 拦截器链（即增强）的调用：在invoke方法中，将当前方法的所有拦截器都封装进ReflectiveMethodInvocation中，调用其proceed()方法，使所有拦截器生效。不同的增强器，如@Before、@After，他们的执行顺序由他们自身功能来控制。

## **多个切面类切同一个方法，哪个切面类中的增强器先执行？**

AOP实现原理中可知AOP中没有规定不同切面的执行顺序，都是把切面打乱放进了List<Advisor>中，但从放入List中的顺序追溯，

可知对应的是Spring加载类后注册BeanDefinition的顺序，即Spring注册BeanDefinition的顺序。而此顺序有两个方法控制，
* 一个是在切面类上加@Order(123)注解，后面的数字越小越早加载；
* 另一个是切面类实现Ordered接口，重写getOrder方法，返回的值越小越早加载。

## AOP使用场景

事务管理：https://www.cnblogs.com/xss512/p/10944697.html

日志管理：上面已经演示过了

权限控制：https://www.cnblogs.com/sxkgeek/p/9985929.html
>不过我用的shiro来权限控制