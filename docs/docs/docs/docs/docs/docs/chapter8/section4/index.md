# 删表不删库
```
SELECT concat('DROP TABLE IF EXISTS ', table_name, ';')
FROM information_schema.tables
WHERE table_schema = 'mydb';
```
mydb换成你想删除的数据库的名字,这样可以生成一个批量处理的sql语句，你需要再运行一次这个结果集就可以删除所有的表而不删除数据库了
