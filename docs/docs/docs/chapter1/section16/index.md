# try catch finally 中的return

[try-catch-finally 中，如果 catch 中 return 了，finally 还会执行吗](https://blog.csdn.net/ananyatouanan/article/details/117304695)

## 1 执行顺序

```
try{

}catch(){

}finally{

}
```

* 无异常：try->finally
* 有异常：try->catch->finally

## 2 return在不同的块中

### 2.1 return在try中

* 无异常：保存try中return后的表达式计算结果(a=0)->finally随便怎么修改(a=1)->依然return之前保存的结果(a=0)

```
int a = 0;
try{
    return a;
}catch(){

}finally{
    ++a;
}
```

>但如果a是引用类型的话，finally中修改了a里面的成员字段的值，这样在最后return后的值是起效的。

### 2.2 return在catch中

* 有异常：try抛异常->保存catch中return后的表达式结果(a=1)->finally随便怎么修改(a=2)->依然return之前保存的结果(a=1)

```
int a = 0;
try{
    //抛异常
    return a;
}catch(){
    return ++a;
}finally{
    ++a;
}
```

>如果a是引用类型的话，同上。

### 2.3 return在finally中

* 假设无异常：保存try中return后的表达式计算结果(a=0)->保存finally中return后的表达式计算结果(a=1),直接返回1。

```
int a = 0;
try{
    return a;
}catch(){

}finally{
    return ++a;
}
```

>有异常也同理，都会在finally中直接返回