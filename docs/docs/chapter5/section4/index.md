# 类加载器

JVM 中内置了三个重要的 ClassLoader，除了 BootstrapClassLoader 其他类加载器均由 Java 实现且全部继承自java.lang.ClassLoader（自定义类加载器也是继承这个）：
 
1.	BootstrapClassLoader(启动类加载器) ：最顶层的加载类，由 C++实现，负责加载 **%JAVA_HOME%/lib**目录下的 jar 包和类或者被 -Xbootclasspath参数指定的路径中的所有类。 【加载虚拟机专用的类】
2.	ExtensionClassLoader(扩展类加载器) ：主要负责加载 **%JRE_HOME%/lib/ext** 目录下的 jar 包和类，或被 java.ext.dirs 系统变量所指定的路径下的 jar 包。 【如Object类】
3.	AppClassLoader(应用程序类加载器)：面向我们用户的加载器，负责加载当前应用 **classpath** 下的所有 jar 包和类。【用户定义的类加载器的默认父类加载器】

## 1 双亲委派模型

![model](media/1.png)

每一个类都有一个对应它的类加载器。系统中的 ClassLoader 在协同工作的时候会默认使用 双亲委派模型 。即在类加载的时候，系统会首先判断当前类是否被加载过。已经被加载的类会直接返回，否则才会尝试加载。加载的时候，首先会把该请求委派给父类加载器的 loadClass() 处理，因此所有的请求最终都应该传送到顶层的启动类加载器 BootstrapClassLoader 中。当父类加载器无法处理时，才由自己来处理。当父类加载器为 null 时，会使用启动类加载器 BootstrapClassLoader 作为父类加载器。

>类加载器之间的“父子”关系也不是通过继承来体现的，是由“优先级”来决定。

```
public class ClassLoaderDemo {  
    public static void main(String[] args) {  
        System.out.println("ClassLodarDemo's ClassLoader is " + ClassLoaderDemo.class.getClassLoader());  
        System.out.println("The Parent of ClassLodarDemo's ClassLoader is " + ClassLoaderDemo.class.getClassLoader().getParent());  
        System.out.println("The GrandParent of ClassLodarDemo's ClassLoader is " + ClassLoaderDemo.class.getClassLoader().getParent().getParent());  
    }  
} 
```
output:
```
ClassLodarDemo's ClassLoader is sun.misc.Launcher$AppClassLoader@18b4aac2  
The Parent of ClassLodarDemo's ClassLoader is sun.misc.Launcher$ExtClassLoader@1b6d3586  
The GrandParent of ClassLodarDemo's ClassLoader is null 
```
AppClassLoader的父类加载器为ExtClassLoader，
ExtClassLoader的父类加载器为 null，null 并不代表ExtClassLoader没有父类加载器，而是 BootstrapClassLoader。

## 2 双亲委派模型实现源码

双亲委派模型的实现代码非常简单，逻辑非常清晰，都集中在 java.lang.ClassLoader 的 loadClass() 中，相关代码如下所示：
```
private final ClassLoader parent;  
protected Class<?> loadClass(String name, boolean resolve)  
        throws ClassNotFoundException  
    {  
        synchronized (getClassLoadingLock(name)) {  
            // 首先，检查请求的类是否已经被加载过  
            Class<?> c = findLoadedClass(name);  
            if (c == null) {  
                long t0 = System.nanoTime();  
                try {  
                    if (parent != null) {//父加载器不为空，调用父加载器loadClass()方法处理  
                        c = parent.loadClass(name, false);  
                    } else {//父加载器为空，使用启动类加载器 BootstrapClassLoader 加载  
                        c = findBootstrapClassOrNull(name);  
                    }  
                } catch (ClassNotFoundException e) {  
                    //抛出异常说明父类加载器无法完成加载请求  
                }  
    
                if (c == null) {  
                    long t1 = System.nanoTime();  
                    //自己尝试加载  
                    c = findClass(name);  
    
                    // this is the defining class loader; record the stats  
                    sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);  
                    sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);  
                    sun.misc.PerfCounter.getFindClasses().increment();  
                }  
            }  
            if (resolve) {  
                resolveClass(c);  
            }  
            return c;  
        }  
    } 
```
## 3 双亲委派模型的好处

双亲委派模型保证了 Java 程序的稳定运行，可以避免类的重复加载（JVM 区分不同类的方式不仅仅根据类名，相同的类文件被不同的类加载器加载产生的是两个不同的类），也保证了 Java 的核心 API 不被篡改。如果没有使用双亲委派模型，而是每个类加载器加载自己的话就会出现一些问题，比如我们编写一个称为 java.lang.Object 类的话，那么程序运行的时候，系统就会出现多个不同的 Object 类。

## 4 避免双亲委托机制

自定义加载器的话，需要继承 ClassLoader 。
* 如果不想打破双亲委派模型，就重写 ClassLoader 类中的 findClass() 方法即可，无法被父类加载器加载的类最终会通过这个方法被加载。
* 如果想打破双亲委派模型则需要重写 loadClass() 方法（重写方法内部不调用父亲的loadClass即可）

## 5 什么时候需要自定义类加载器？

[什么时候需要自定义类加载器？](https://blog.csdn.net/mrlin6688/article/details/104709740/)

1. **加密**：众所周知，java代码很容易被反编译，如果你需要把自己的代码进行加密，可以先将编译后的代码用某种加密算法加密，然后实现自己的类加载器，负责将这段加密后的代码还原。

2. **从非标准的来源加载代码**：例如你的部分字节码是放在数据库中甚至是网络上的，就可以自己写个类加载器，从指定的来源加载类。

3. **动态创建**：为了性能等等可能的理由，根据实际情况动态创建代码并执行。