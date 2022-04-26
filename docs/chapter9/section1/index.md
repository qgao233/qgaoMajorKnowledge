# 总览

**缓存key设计**：表名:列名:主键名:主键值

1.Redis 支持更丰富的数据类型
2.Redis 支持数据的持久化
3.Redis 目前是原生支持 cluster 模式
4.Redis 使用单线程的多路 IO 复用模型
5.Redis 同时使用了惰性删除与定期删除

Redis6引进的多线程只是在网络数据的读写这类耗时操作上使用了，执行命令仍然是单线程顺序执行。因此不需要担心线程安全问题。

淘汰策略：allkeys-lru（least recently used）
