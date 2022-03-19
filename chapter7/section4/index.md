# jdbc

## connection

### 错误的优化
```
private static Connection connection;
```
当一个开发者考虑到性能的时候,可能会写出上面的代码，因为创建连接的开销是非常大的，然而这样写是线程不安全的，可以想象,在多线程环境中,线程A开启了事务,然后线程B却意外的connection.commit,

如果在没有事务的情况下,且仅仅是简单的SQL Select操作,似乎在不会出现数据错乱,一切看起来比较安全.但是这并非意味着提升了性能,JDBC的各种实现中,connection的各种操作都进行了同步:

```
//PreparedStatement  
public int executeUpdate(){  
    synchronized(connection){  
        //do      
    }  
}  
```

因此，通常情况下,一个Connection实例只会在一个线程中使用,使用完之后close().

### 连接池的出现

TCP链接的创建开支是昂贵的,当然DB server所能承载的TCP并发连接数也是有限制的.因此每次调用都创建一个Connection,这是不现实的;所以才有了数据库连接池的出现.

数据库连接池中保持了一定数量的connection实例,当需要DB操作的时候"borrow"一个出来,使用结束之后"return"到连接池中,多线程环境中,连接池中的connection实例交替性的被多个线程使用.

```
public List<Object> select(String sql){  
    Connection connection = pool.getConnection();  
    //do  
    pool.release(connection);//归还资源  
}  
```

### 使用连接池后，一个线程中所有的DB操作使用的是同一个connection吗?

* 没有spring的环境下

dataSourcePool本身是否使用了threadLocal来保存线程与connection实例的引用关系;

如果使用了threadLocal,那么一个线程多次从pool中获取是同一个connection,直到线程消亡或者调用向pool归还资源..

* spring环境下（或者mybatis等orm中）

**非事务场景**下,一切都很简单,每一次调用,都是从pool中取出一个connection实例,调用完毕之后归还资源,因此多次调用,应该是不同的connection实例.

```
public List<Object> select(String sql){  
    Connection connection = pool.getConnection();  
    //do  
    pool.release(connection);//归还资源  
}  
```

**使用事务**的场景下，一个事务只用一个connection.

## 批量插入

```

public static void batchInsert() throws SQLException {
    long start = System.currentTimeMillis();
    Connection conn = getConnection();
    conn.setAutoCommit(false);
    PreparedStatement ps = null;
    String sql = "insert into xxx values (?,'1','1')";
    ps = conn.prepareStatement(sql); // 批量插入时ps对象必须放到for循环外面
    for (int i=0;i < 50000;i++){
        ps.setString(1, i+"");
        ps.addBatch();
        // 每1000条记录插入一次
        if (i % 1000 == 0){
            ps.executeBatch();
            conn.commit();
            ps.clearBatch();
        }
    }
    // 剩余数量不足1000
    ps.executeBatch();
    conn.commit();
    ps.clearBatch();
    long end = System.currentTimeMillis();
    System.out.println(end - start);
}
```