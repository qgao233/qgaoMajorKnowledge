# 构造方法的访问级别

public、protected、private、默认访问级别都可以。在此重点记录由private修饰的构造方法。

## private修饰的构造方法的访问级别：

当构造方法别声明为private时，就意味着只有当前类的方法可以调用它，
>1. 当前类的其它构造方法可以通过this关键字来调用。
>2. 当前类的成员方法可以通过new语句调用它。

## 把构造方法声明为private的理由：

1. 这个类中仅仅包含供其它类调用的静态方法，没有实例方法。这意味着当某个类想要调用该类中的方法时，无需创建该类的实例，即不会触及到该类的构造方法。
2. 禁止这个类被继承。
3. 这个类需要把自身实现的细节封装起来，不允许其它程序通过new语句来创建这个类的实例。这个类向其他程序提供了获取自身实例的静态方法，这种方法称为静态工厂方法。

## 疑问
疑问1: 也许有人会有疑问：“用abstract修饰词修饰的类也不可以创建实例，在此和要使用private访问权限词限定构造访问的区别是什么？”
疑问2：final修饰词修饰的类也不能被继承，在此和要使用private访问权限词限定构造访问的区别是什么
 
原因：用private访问权限限定词限定类的构造方法，表示该类既不能被继承又不能创建该类的实例。
>* abstract，可以被继承，不能创建实例。
>* final，不可以被继承，可以创建实例。
