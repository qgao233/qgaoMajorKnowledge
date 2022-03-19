# 内省

**核心概念**
 首先可以先了解下JavaBean的概念：一种特殊的类，主要用于传递数据信息。这种类中的方法主要用于访问私有的字段，且方法名符合某种命名规则。如果在两个模块之间传递信息，可以将信息封装进JavaBean中，这种对象称为“值对象”(Value Object)，或“VO”。

因此JavaBean都有如下几个特征：

* 属性都是私有的；
* 有无参的public构造方法；
* 对私有属性根据需要提供公有的getXxx方法以及setXxx方法；
* getters必须有返回值没有方法参数；setter值没有返回值，有方法参数；

符合这些特征的类，被称为JavaBean；

JDK中提供了一套API用来访问某个属性的getter/setter方法，这些API存放在java.beans中，这就是内省(Introspector)。
