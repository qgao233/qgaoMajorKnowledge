# mysql的乐观锁和悲观锁

乐观锁本质上也是cas，而悲观锁就是排他锁。

一样的，乐观锁只适合高并发读，低并发写，而悲观锁则相反。

只是实现方式的问题。

## 乐观锁实现

version方式：一般是在数据表中加上一个数据版本号version字段，表示数据被修改的次数，当数据被修改时，version值会加一。当线程A要更新数据值时，在读取数据的同时也会读取version值，在提交更新时，若刚才读取到的version值为当前数据库中的version值相等时才更新，否则重试更新操作，直到更新成功。

## 悲观锁实现

是由数据库自己实现的，要用的时候，我们直接调用数据库的相关语句就可以了(原理：共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程)，如行锁、读锁和写锁等，都是在操作之前加锁，在Java中，synchronized的思想也是悲观锁。

要使用悲观锁，我们必须关闭mysql数据库的自动提交属性。

```
set autocommit=0;　　
//设置完autocommit后，我们就可以执行我们的正常业务了。具体如下：
//0.开始事务
begin;/begin work;/start transaction; (三者选一就可以)
//1.查询出商品信息
select status from t_goods where id=1 for update;
//2.根据商品信息生成订单
insert into t_orders (id,goods_id) values (null,1);
//3.修改商品status为2
update t_goods set status=2;
//4.提交事务
commit;/commit work;
```

　　注：上面的begin/commit为事务的开始和结束，因为在前一步我们关闭了mysql的autocommit，所以需要手动控制事务的提交，在这里就不细表了。

　　上面的第一步我们执行了一次查询操作：select status from t_goods where id=1 for update;与普通查询不一样的是，我们使用了select…for update的方式，这样就通过数据库实现了悲观锁。此时在t_goods表中，id为1的那条数据就被我们锁定了，其它的事务必须等本次事务提交之后才能执行。这样我们可以保证当前的数据不会被其它事务修改。

　　注：需要注意的是，在事务中，只有SELECT ... FOR UPDATE 或LOCK IN SHARE MODE 相同数据时会等待其它事务结束后才执行，一般SELECT ... 则不受此影响。拿上面的实例来说，当我执行select status from t_goods where id=1 for update;后。我在另外的事务中如果再次执行select status from t_goods where id=1 for update;则第二个事务会一直等待第一个事务的提交，此时第二个查询处于阻塞的状态，但是如果我是在第二个事务中执行select status from t_goods where id=1;则能正常查询出数据，不会受第一个事务的影响。