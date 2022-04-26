# 内省和反射的区别

反射：Java反射机制是在运行中，对任意一个类，能够获取得到这个类的所有属性和方法；它针对的是任意类
内省（Introspector）：是Java语言对JavaBean类属性、事件的处理方法

1. 反射可以操作各种类的属性，而内省只是通过反射来操作JavaBean的属性
2. 内省设置属性值肯定会调用setter方法，反射可以不用（反射可直接操作属性Field）
3. 反射就像照镜子，然后能看到.class的所有，是客观的事实。内省更像主观的判断：比如看到getName()，内省就会认为这个类中有name字段，但事实上并不一定会有name；通过内省可以获取bean的getter/setter

使用示例：
```
class People{
    String name;
    int age;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
}
public class Main {
    public static void main(String[] args) throws Exception{
        BeanInfo beanInfo = Introspector.getBeanInfo(People.class);
        PropertyDescriptor[] propertyDescriptors = beanInfo.getPropertyDescriptors();
        for (PropertyDescriptor propertyDescriptor : propertyDescriptors) {
            System.out.print(propertyDescriptor.getName()+"   ");
        }
    }
}
```

程序输出：
```
age   class   name   
```
为什么会输出class呢？前文中有提到，“看到getName()，内省就会认为这个类中有name字段，但事实上并不一定会有name”，我们知道每个对象都会有getClass方法，所以使用内省时，默认就认为它具有class这个字段  
