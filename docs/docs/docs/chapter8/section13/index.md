# 分布式锁

1. DB分布式锁的实现：通过主键id的唯一性进行加锁，说白了就是加锁的形式是向一张表中插入一条数据，该条数据的id就是一把分布式锁，例如当一次请求插入了一条id为1的数据，其他想要进行插入数据的并发请求必须等第一次请求执行完成后删除这条id为1的数据才能继续插入，实现了分布式锁的功能。

```
def lock ：  
    exec sql: insert into locked-table (xxx) values (xxx)  
    if result == true :  
        return true  
    else :  
        return false  
    
def unlock ：  
    exec sql: delete from locked-table where order_id='order_id'  
```
2. 使用流水号+时间戳做幂等操作，可以看作是一个不会释放的锁。即每一个线程的操作都记录下来，根据记录的个数来决定如商品减少的个数之类的原子操作。