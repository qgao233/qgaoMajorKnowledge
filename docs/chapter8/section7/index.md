# 索引

https://www.bilibili.com/video/BV1KW411u7vy
https://blog.csdn.net/feichitianxia/article/details/107997795

结合视频和博客，对mysql的索引和锁有了具体的了解。

总的来说，对sql性能的优化，就是要建立正确的索引，

无论是查询涉及到的where, 排序用的order by，或者是先排序后分组的group by，对高并发支持的innodb引擎的行锁，都要依赖于索引。

行锁锁的对象就是索引，所以如果更新条件where没用到索引，那么行锁就会**升级**成表锁，降低并发性能。

## 联合索引图

![](media/1.png)

## 索引最左前缀原则

常见联合索引
索引index1:(a,b,c)，只会走a、a,b、a,b,c 三种类型的查询，其实这里说的有一点问题，a,c也走，但是只走a字段索引，不会走c字段。

另外还有一个特殊情况说明下，select * from table where a = '1' and b > ‘2’ and c='3' 这种类型的也只会有a与b走索引，c不会走。

原因如下：

索引是有序的，index1索引在索引文件中的排列是有序的，首先根据a来排序，然后才是根据b来排序，最后是根据c来排序，

像select * from table where a = '1' and b > ‘2’ and c='3' 这种类型的sql语句，在a、b走完索引后，c肯定是无序了，所以c就没法走索引，数据库会觉得还不如全表扫描c字段来的快。（感觉这一块说的始终有点牵强，无序又怎样，从剩下的里面挨着挨着找不就好了吗？）
