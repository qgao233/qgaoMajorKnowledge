# springMVC参数绑定

springMVC参数绑定

1. 简单类型绑定，方法形参名和前台参数名一样
1. 不一样，使用@RequestParam
1. 对象类，其属性是基本类型，前台参数名需和对象属性名一样，方法形参为对象
1. 如果有转换错误的，需自定义转换器，并配置，如Date类
1. 对象类，其属性有是对象类的，和③一样，比如A类里有属性B类对象b，B类里有属性是基本类型c，前台参数写b.c，方法形参为A类对象a，可以将b封装到a中
1. 数组，前台有多个相同name参数，方法形参可直接采用数组接收，名字同①
1. List，方法形参为List alist，前台参数为alist[index].b(c,d)，其中b，c，d为alist的泛形对象的属性名
1. Map，再百度

**注意**:

第7和8有点错误，list和map，如果方法形参直接采用list或map来接收，这样的方式是不行的，会报异常:
```
Could not instantiate bean class [java.util.List]: Specifiedclass is an interface
```

必须得新建一个包装类专门来包装类似的需要接收的list参数

>前台参数名：user.contactList[0].phone，方法形参名：User user,这里User为“包装类”

为什么直接不行？

因为spring mvc 中获取参数的方式不管有多少种，他的本质依然是
```
request.getParameter("name")
```
那把这个参数封装到一个对象中，也只能是同setter方法，那问题的关键是如何找到这个setter方法？肯定是setName中的name和request中的name对应。这才能找到。你想，如果你单纯接收一个list参数，list虽然有get和set方法，但是没有名字呀，只能根据数组下标来判断参数位置。所以只能通过第二种方法进行参数传递.


当然方法形参也可以直接用list，只要前台是用ajax的json类型传来，那么方法形参的list可以用@RequestBody来解释json，具体百度
