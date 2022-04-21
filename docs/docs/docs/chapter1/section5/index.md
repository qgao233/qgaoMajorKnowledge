# **枚举enum**
枚举方式定义的常量使代码更具可读性，允许进行编译时检查，预先记录可接受值的列表，并避免由于传入无效值而引起的意外行为。

== 运算符可提供枚举变量编译时和运行时的安全性：即编译时若为null，则无法通过编译；运行时为null，正常运行，不会报错。
## **语法（定义）**
创建枚举类型要使用 enum 关键字，隐含了所创建的类型都是 java.lang.Enum 类的子类（java.lang.Enum 是一个抽象类）。枚举类型符合通用模式 Class Enum<E extends Enum<E>>，而 E 表示枚举类型的名称。枚举类型的每一个值都将映射到protected Enum(String name, int ordinal) 构造函数中，在这里，每个值的名称都被转换成一个字符串，并且序数设置表示了此设置被创建的顺序。
```
package com.hmw.test;
public enum EnumTest {
    MON, TUE, WED, THU, FRI, SAT, SUN;
}
```
上述代码实际上调用了7次 Enum(String name, int ordinal)：
```
new Enum<EnumTest>("MON",0);
new Enum<EnumTest>("TUE",1);
new Enum<EnumTest>("WED",2);
    ... ...
```

## **向枚举中添加新方法**
如果打算自定义自己的方法，那么必须在enum实例序列的最后添加一个分号。而且 Java 要求必须先定义 enum 实例。



**Java代码** 
```
	public enum Color {  
	    RED("红色", 1), GREEN("绿色", 2), BLANK("白色", 3), YELLO("黄色", 4);  
	    // 成员变量  
	    private String name;  
	    private int index;  
	    // 构造方法  
	    private Color(String name, int index) {  
	        this.name = name;  
	        this.index = index;  
	    }  
	    // 普通方法  
	    public static String getName(int index) {  
	        for (Color c : Color.values()) {  
	            if (c.getIndex() == index) {  
	                return c.name;  
	            }  
	        }  
	        return null;  
	    }  
	    // get set 方法  
	    public String getName() {  
	        return name;  
	    }  
	    public void setName(String name) {  
	        this.name = name;  
	    }  
	    public int getIndex() {  
	        return index;  
	    }  
	    public void setIndex(int index) {  
	        this.index = index;  
	    }  
	}  


```
## **实现接口**
所有的枚举都继承自java.lang.Enum类。由于Java 不支持多继承，所以枚举对象不能再继承其他类。



**Java代码** 
```
	public interface Behaviour {  
	    void print();  
	    String getInfo();  
	}  
	public enum Color implements Behaviour{  
	    RED("红色", 1), GREEN("绿色", 2), BLANK("白色", 3), YELLO("黄色", 4);  
	    // 成员变量  
	    private String name;  
	    private int index;  
	    // 构造方法  
	    private Color(String name, int index) {  
	        this.name = name;  
	        this.index = index;  
	    }  
	//接口方法  
	    @Override  
	    public String getInfo() {  
	        return this.name;  
	    }  
	    //接口方法  
	    @Override  
	    public void print() {  
	        System.out.println(this.index+":"+this.name);  
	    }  
	}  

```


