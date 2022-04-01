# maven打包

因为是用的springboot的maven插件打的jar包，所以它会默认去找启动类，如果要打的包不需要启动类，那首先最好是把当前模块中的所有main方法注释，再
```
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <layout>NONE</layout>
        <classifier>exec</classifier>
    </configuration>
</plugin>
```
layout标签是指明不需要main方法，然后如果只有这一个标签还是不行，如果你试着打包的话，会发现打的jar包，当前项目的类都在一个BOOT-INF的目录下，如果有要依赖当前模块的模块要打jar包的话，会报compile错误，也就是报找不到依赖的类，所以在当前模块还要加上classifier这个标签并配置exec，把当前模块依赖的和本身的类的jar包分开，这样就行了，classifier具体作用不清楚，找个时间仔细看一下maven的各种插件，

