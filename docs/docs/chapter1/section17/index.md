# java命令行操作

[javac的相关的参数](https://blog.csdn.net/zhihaoma/article/details/44620171)
[java -c_JAVAC 命令使用方法](https://blog.csdn.net/weixin_33648177/article/details/114180144)

## 1 javac

笔记就主要记4个参数：

* -sourcepath: 指定源文件(.java)的位置
* -classpath: 指定类文件(.class,通常源文件中依赖的其它类文件)的位置
* -d: 指定在哪里放编译后生成的类文件(通常源文件不应该和类文件在同一个目录下)
* @: @后跟的文件名对应的文件中可以放很多个需要编译的java文件名

### 1.1 @

`javac @sourcefiles`

sourcefiles这个文件名对应的文件中可以写任意多个.java的文件：

```
MyClass1.java
MyClass2.java
MyClass3.java
```

### 1.2 -classpath

**1 有类路径**

设置用户类路径，它将**覆盖**环境变量 `CLASSPATH` 中的用户类路径。

示例，设置当前目录中的`examples`为类路径：

`javac -classpath \examples \examples\greetings\Hi.java`

>若classpath指定的包含jar包，需要加冒号进行连接：
>`javac -sourcepath src -classpath classes:lib\Banners.jar \`
>* 意思是类路径为classes\lib下有个叫Banners.jar的jar包里的类，最后一个`\`指的是将src下所有且不同层级的.java全部进行编译。
>
>java命令碰到jar包也是用冒号连接

**2 没有类路径**

若:

* 没有环境变量 `CLASSPATH` 
* 又未指定 `-classpath`，

则用户类路径由**当前文件夹**构成。有关具体信息，请參阅设置类路径。

示例：

`javac greetings\Hi.java`

### 1.3 -sourcepath

若未指定 `-sourcepath` 选项，则将在用户类路径中查找类文件和源文件。

示例见上面**1 有类路径**的情况。

### 1.4 -d

设置编译后生成的类文件的目标文件夹。

假设某个类是一个包的组成部分，则 javac 将把该类文件放入反映包名的子文件夹中，必要时创建文件夹。

比如，假设指定 `-d c:\myclasses` 而且该类名叫 `com.mypackage.MyClass`，那么类文件就叫作 `c:\myclasses\com\mypackage\MyClass.class`。

若未指定 `-d` 选项，则 javac 将把类文件放到与源文件**同样的文件夹**中。

>注意： -d 选项指定的文件夹不会被自己主动增加到用户类路径中。

## 2 java

见首部参考链接。