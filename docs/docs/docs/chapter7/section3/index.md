# 常见面试题

## 1. #{}和${}的区别

* \${}是 Properties 文件中的**变量占位符**，它可以用于标签属性值和 sql 内部，属于静态文本替换，比如`${driver}`会被静态替换为`com.mysql.jdbc. Driver`。
* #{}是 sql 的**参数占位符**，MyBatis 会将 sql 中的#{}替换为? 号，在 sql 执行前会使用 PreparedStatement 的参数设置方法，按序给 sql 的? 号占位符设置参数值，比如 `ps.setInt(0, parameterValue)`，`#{item.name}` 的取值方式为使用反射从参数对象中获取 item 对象的 name 属性值，相当于 `param.getItem().getName()`。

## 2. 最佳实践中，通常一个 Xml 映射文件，都会写一个 Dao 接口与之对应，请问，这个 Dao 接口的工作原理是什么？

Dao 接口，就是人们常说的 Mapper 接口，

* 接口的全限名，就是映射文件中的 namespace 的值，

* 接口的方法名，就是映射文件中 MappedStatement 的 id 值，

* 接口方法内的参数，就是传递给 sql 的参数。 

Mapper 接口是没有实现类的，当调用接口方法时，接口全限名+方法名拼接字符串作为 key 值，可唯一定位一个 MappedStatement ，

举例： `com.mybatis3.mappers. StudentDao.findStudentById` ，可以唯一找到 namespace 为 `com.mybatis3.mappers. StudentDao` 下面 id = `findStudentById` 的 MappedStatement 。

在 MyBatis 中，每一个 `<select> 、 <insert> 、 <update> 、 <delete>` 标签，都会被解析为一个 MappedStatement 对象。

## 3. Dao 接口里的方法，参数不同时，方法能重载吗？

Dao 接口里的方法可以重载，但是 Mybatis 的 XML 里面的 ID 不允许重复！也就是说多个同名方法对应在xml中的映射必须只有一个，否则启动会报错。

```
/**
 * Mapper接口里面方法重载
 */
public interface StuMapper {

    List<Student> getAllStu();

    List<Student> getAllStu(@Param("id") Integer id);
}
<!-- StuMapper.xml 中利用 Mybatis 的动态 sql  -->
<select id="getAllStu" resultType="com.pojo.Student">
    select * from student
    <where>
        <if test="id != null">
            id = #{id}
        </if>
    </where>
</select>
```

## 4. Mybatis 的一级缓存与二级缓存

* 一级缓存是SqlSession级别的缓存（默认开启）：

Mybatis对缓存提供支持，但是在没有配置的默认情况下，它只开启一级缓存。一级缓存在操作数据库时需要构造sqlSession对象，在对象中有一个数据结构用于存储缓存数据。不同的sqlSession之间的缓存数据区域是互相不影响的。也就是他只能作用在同一个sqlSession中，不同的sqlSession中的缓存是互相不能读取的。

* 二级缓存是表级缓存

namespace级别，即一个mapper.xml一个缓存。

1、和一级缓存一样，insert,update,delete操作会清空全部缓存，二级是清空所在namespace下的。
2、通常使用MyBatis Generator生成的代码中，都是各个表独立的，每个表都有自己的namespace。
3、多表操作一定不要使用二级缓存，因为多表操作进行更新操作，一定会产生脏数据。

开启缓存后，二级缓存 -> 一级缓存 -> 数据库

https://www.cnblogs.com/cxuanBlog/p/11333021.html

```
<!-- mybatis.xml配置文件中加入 -->
<settings>    
   <!--开启二级缓存-->    
    <setting name="cacheEnabled" value="true"/>    
</settings>
<!-- 在需要开启二级缓存的mapper.xml中加入cache标签 -->
<cache/> 
<!-- 让使用二级缓存的POJO类实现Serializable接口 -->
public class User implements Serializable {}
```

## 5. dao接口的工作原理

Dao 接口的工作原理是 JDK 动态代理，MyBatis 运行时会使用 JDK 动态代理为 Dao 接口生成代理 proxy 对象，代理对象 proxy 会拦截接口方法，转而执行 MappedStatement 所代表的 sql，然后将 sql 执行结果返回。

## 6. xml文件中的标签

常见的： `<select>、<insert>、<update>、<delete>`

需要用到的：`<resultMap> 、 <parameterMap> 、 <sql> 、 <include> 、 <selectKey> `

动态 sql 的 9 个标签： trim|where|set|foreach|if|choose|when|otherwise|bind 等。

>动态 sql原理：使用 OGNL 从 sql 参数对象中计算表达式的值，根据表达式的值动态拼接 sql。

## 7. 一对一、一对多查询

https://www.cnblogs.com/xdp-gacl/p/4264440.html

分为：
* 嵌套查询：执行多条sql语句最后赋给主对象
* join查询：执行一条sql语句赋给主对象

## 8. 延迟加载

https://blog.csdn.net/shui2104/article/details/107849706

延迟加载：在真正使用数据时才发起查询，不用的时候不查询，按需加载（懒加载）。常用于关联的对象为“多”时，即一对**多**，多对**多**。

立刻加载：管用不用，在调用方法的时候就立即执行，马上发起查询。常用于关联的对象为“一”时，即多对**壹**，一对**壹**。

MyBatis 仅支持 association 关联对象和 collection 关联集合对象的延迟加载，

association 指的就是一对**壹**查询，

collection 指的就是一对**多**查询。

## 9. 延迟加载的原理

几乎所有orm的原理都是，使用 CGLIB 创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用 `a.getB().getName()` ，拦截器 invoke() 方法发现 a.getB() 是 null 值，那么就会单独发送事先保存好的查询关联 B 对象的 sql，把 B 查询上来，然后调用 `a.setB(b)`，于是 a 的对象 b 属性就有值了，接着完成 `a.getB().getName()` 方法的调用。这就是延迟加载的基本原理。

## 10. mybatis批量插入

https://www.jianshu.com/p/97e484b55d04

## 11. mybatis分页

https://blog.csdn.net/feinifi/article/details/88769101

常见的三种方式：

1. 直接写sql
2. 自定义拦截器进行字符串limit的拼接
3. 利用第三方写好的拦截器PageHelper拼接limit

## 12. MyBatis 都有哪些 Executor 执行器？它们之间的区别是什么？

MyBatis 有三种基本的 Executor 执行器，**SimpleExecutor 、 ReuseExecutor 、 BatchExecutor 。**

* **SimpleExecutor ：** 每执行一次 update 或 select，就开启一个 Statement 对象，用完立刻关闭 Statement 对象。

* **ReuseExecutor ：** 执行 update 或 select，以 sql 作为 key 查找 Statement 对象，存在就使用，不存在就创建，用完后，不关闭 Statement 对象，而是放置于 Map<String, Statement>内，供下一次使用。简言之，就是重复使用 Statement 对象。

* **BatchExecutor ：** 执行 update（没有 select，JDBC 批处理不支持 select），将所有 sql 都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个 Statement 对象，每个 Statement 对象都是 addBatch()完毕后，等待逐一执行 executeBatch()批处理。与 JDBC 批处理相同。

**作用范围**：Executor 的这些特点，都严格限制在 SqlSession 生命周期范围内。

>在 MyBatis 配置文件中，可以指定默认的 ExecutorType 执行器类型，也可以手动给 DefaultSqlSessionFactory 的创建 SqlSession 的方法传递 ExecutorType 类型参数。