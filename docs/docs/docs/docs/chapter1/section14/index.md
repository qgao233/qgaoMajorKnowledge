# hashcode()&equals()

在插入桶中的判断时，会先比较旧值和新插入值的hashcode,碰到hashCode相同的值，才会去调用equals方法判断两个对象是否真的相等。

而本身默认的方法产生的hashCode一定不会相等，那么即使重写了equals方法，这个equals方法也不会被调用，那不白重写了。

因此重写equals必须重写自己的业务hashCode，让两个对象有可能生成相等的hashCode。
