# redis 持久化机制（恢复数据）

Redis 的持久化方式：

1. 快照（snapshotting，RDB）
2. 只追加文件（append-only file, AOF）

## 快照（snapshotting）【RDB】

>RDB：Redis DataBase

快照持久化是 Redis 默认采用的持久化方式，

Redis 可以通过创建快照来获得存储在内存里面的数据在某个时间点上的副本。

Redis 创建快照之后，

* 可以对快照进行备份，然后将快照复制到其他服务器从而创建具有相同数据的服务器副本（Redis 主从结构，主要用来**提高 Redis 性能**），
* 还可以将快照留在原地以便重启服务器的时候使用 **（恢复数据）**。

## 只追加文件（append-only file）【AOF】

与快照持久化相比，AOF 持久化的实时性更好，因此已成为主流的持久化方案。默认情况下 Redis 没有开启 AOF（append only file）方式的持久化，可以通过 appendonly 参数开启：
```
appendonly yes
```

开启 AOF 持久化后，每执行一条会更改 Redis 中的数据的命令，Redis 就会将该命令写入到内存缓存 server.aof_buf 中，然后再根据 appendfsync 配置来决定何时将其同步到硬盘中的 AOF 文件。

AOF 文件的保存位置和 RDB 文件的位置相同，都是通过 dir 参数设置的，默认的文件名是 appendonly.aof。

在 Redis 的配置文件中存在三种不同的 AOF 持久化方式，它们分别是：
```
appendfsync always    #每次有数据修改发生时都会写入AOF文件,这样会严重降低Redis的速度
appendfsync everysec  #每秒钟同步一次，显示地将多个写命令同步到硬盘
appendfsync no        #让操作系统决定何时进行同步
```

为了兼顾数据和写入性能，用户可以考虑 **appendfsync everysec** 选项 ，让 Redis 每秒同步一次 AOF 文件，Redis 性能几乎没受到任何影响。而且这样即使出现系统崩溃，用户最多只会丢失一秒之内产生的数据。当硬盘忙于执行写入操作的时候，Redis 还会优雅的放慢自己的速度以便适应硬盘的最大写入速度。