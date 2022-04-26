# 反射

泛型参数的安全检查发生在编译时, 在"运行"期间，所有的泛型信息都会被擦掉

反射之所以被称为框架的灵魂，主要是因为它赋予了我们在"运行时"分析类以及执行类中方法的能力。可以无视泛型参数的安全检查。

反射为各种框架提供开箱即用的功能提供了便利。

## 编译时静态加载类，与运行时动态加载类

```
package com.imooc.reflect;
public class ClassDemo1 {
    public static void main(String[] args) {
        //Foo的实例对象如何表示  
        Foo foo1 = new Foo();
        //foo1就表示出来了.  
        //Foo这个类 也是一个实例对象，Class类的实例对象,如何表示呢  
        //任何一个类都是Class的实例对象，这个实例对象有三种表示方式  
        //第一种表示方式--->实际在告诉我们任何一个类都有一个隐含的静态成员变量class  
        Class c1 = Foo.class;
        //第二中表达方式  已经知道该类的对象通过getClass方法  
        Class c2 = foo1.getClass();
        /*官网 c1 ,c2 表示了Foo类的类类型(class type) 
             * 万事万物皆对象， 
             * 类也是对象，是Class类的实例对象 
             * 这个对象我们称为该类的类类型 
             *  
             */
        //不管c1  or c2都代表了Foo类的类类型，一个类只可能是Class类的一个实例对象  
        System.out.println(c1 == c2);
        //第三种表达方式  
        Class c3 = null;
        try {
            c3 = Class.forName("com.imooc.reflect.Foo");
        }
        catch (ClassNotFoundException e) {
            // TODO Auto-generated catch block  
            e.printStackTrace();
        }
        System.out.println(c2==c3);
        //我们完全可以通过类的类类型创建该类的对象实例---->通过c1 or c2 or c3创建Foo的实例对象  
        try {
            Foo foo = (Foo)c1.newInstance();
            //需要有无参数的构造方法  
            foo.print();
        }
        catch (InstantiationException e) {
            // TODO Auto-generated catch block  
            e.printStackTrace();
        }
        catch (IllegalAccessException e) {
            // TODO Auto-generated catch block  
            e.printStackTrace();
        }
    }
}
class Foo{
    void print(){
        System.out.println("foo");
    }
}
```

## 方法反射

```
package com.imooc.reflect;
import java.lang.reflect.Method;
public class MethodDemo1 {
    public static void main(String[] args) {
        //要获取print(int ,int )方法  1.要获取一个方法就是获取类的信息，获取类的信息首先要获取类的类类型  
        A a1 = new A();
        Class c = a1.getClass();
        /* 
             * 2.获取方法 名称和参数列表来决定   
             * getMethod获取的是public的方法 
             * getDelcaredMethod自己声明的方法 
             */
        try {
            //Method m =  c.getMethod("print", new Class[]{int.class,int.class});  
            Method m = c.getMethod("print", int.class,int.class);
            //方法的反射操作    
            //a1.print(10, 20);方法的反射操作是用m对象来进行方法调用 和a1.print调用的效果完全相同  
            //方法如果没有返回值返回null,有返回值返回具体的返回值  
            //Object o = m.invoke(a1,new Object[]{10,20});  
            Object o = m.invoke(a1, 10,20);
            System.out.println("==================");
            //获取方法print(String,String)  
            Method m1 = c.getMethod("print",String.class,String.class);
            //用方法进行反射操作  
            //a1.print("hello", "WORLD");  
            o = m1.invoke(a1, "hello","WORLD");
            System.out.println("===================");
            //  Method m2 = c.getMethod("print", new Class[]{});  
            Method m2 = c.getMethod("print");
            // m2.invoke(a1, new Object[]{});  
            m2.invoke(a1);
        }
        catch (Exception e) {
            // TODO Auto-generated catch block  
            e.printStackTrace();
        }
    }
}
class A{
    public void print(){
        System.out.println("helloworld");
    }
    public void print(int a,int b){
        System.out.println(a+b);
    }
    public void print(String a,String b){
        System.out.println(a.toUpperCase()+","+b.toLowerCase());
    }
}
```

## 反射绕过泛型限制

```
package com.imooc.reflect;
import java.lang.reflect.Method;
import java.util.ArrayList;
public class MethodDemo4 {
    public static void main(String[] args) {
        ArrayList list = new ArrayList();
        ArrayList<String> list1 = new ArrayList<String>();
        list1.add("hello");
        //list1.add(20);错误的  
        Class c1 = list.getClass();
        Class c2 = list1.getClass();
        System.out.println(c1 == c2);
        //反射的操作都是编译之后的操作  
        /* 
             * c1==c2结果返回true说明编译之后集合的泛型是去泛型化的 
             * Java中集合的泛型，是防止错误输入的，只在编译阶段有效， 
             * 绕过编译就无效了 
             * 验证：我们可以通过方法的反射来操作，绕过编译 
             */
        try {
            Method m = c2.getMethod("add", Object.class);
            m.invoke(list1, 20);
            //绕过编译操作就绕过了泛型  
            System.out.println(list1.size());
            System.out.println(list1);
            /*for (String string : list1) { 
                    System.out.println(string); 
                }*/
            //现在不能这样遍历
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
