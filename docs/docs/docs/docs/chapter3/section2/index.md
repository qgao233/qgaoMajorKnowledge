# 单例模式

多线程时，

## 在懒汉情况下

>是在第一次被引用时，才会实例化。
>如果运用单例模式那个类获得实例的方法被同时访问，可能会创建多个实例，这时需要做**双重锁**来确保线程安全（指的是获得时的线程安全）

```
/**
 * 懒汉单例
 */
public class LazySingleton {

    private static volatile LazySingleton instance;

    private LazySingleton(){

    }

    public static LazySingleton getInstance() {
        if(instance == null){
            synchronized (LazySingleton.class){
                if(instance == null){
                    instance = new LazySingleton();
                }
            }
        }
        return instance;
    }
}
```

* 第1个check：如果去掉，那么所有线程都会串行执行
* 第2个check：如果同时有多个线程绕过了第1个check，那么只会有一个线程进入同步块，等第1个线程创建好再退出同步块后，后面堵塞的线程判断第2个check会发现已经创建好，直接退出同步块。
* volatile修饰：`instance = new LazySingleton();`不是原子操作，步骤包括：
  1. 分配内存
  2. 初始化零值
  3. 设置对象头（该对象是某类的实例等）
  4. 调用构造方法
  >在第3步时，对象已不为null，若此时当前线程时间片到期，有另外的线程判断第1个check：
  >* 若没有volatile,会发现不为null，直接返回未初始完成的对象。
  >* 若有volatile,因为storeload屏障的存在，另外的线程会阻塞在当前对象的读取上，直到能够初始该对象的线程写入（即构造方法调用）完成。


## 在饿汉情况下

>就是静态初始化的方式，是类一加载就实例化的对象，就不会出现多线程安全问题

```
/**
 * 饿汉单例
 */
public class HungrySingleton {

    private static final HungrySingleton instance = new HungrySingleton();

    private HungrySingleton(){

    }

    public static HungrySingleton getInstance() {
        return instance;
    }
}
```

## **spring的单例 bean 存在线程案例**

主要是因为当多个线程操作同一个对象的时候是存在资源竞争的。

常见的有两种解决办法：

1. 在 bean 中尽量避免定义可变的成员变量。
2. 在类中定义一个 ThreadLocal 成员变量，将需要的可变成员变量保存在 ThreadLocal 中（推荐的一种方式）。

不过，大部分 bean 实际都是无状态（没有实例变量）的（比如 Dao、Service），这种情况下， bean 是线程安全的。