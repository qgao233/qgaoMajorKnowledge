# mysql主键断号解决
mysql删除记录，对自增主键ID进行重新排序

Mysql数据库表的自增主键ID号经过一段时间的添加与删除之后乱了，需要重新排列。

原理：删除原有的自增ID，重新建立新的自增ID。

1，删除原有主键：
```
ALTER  TABLE  `table_name` DROP `id`;
```
2，添加新主键字段：
```
ALTER  TABLE  `table_name` ADD `id` MEDIUMINT( 8 ) NOT NULL  FIRST;
```
3，设置新主键：
```
ALTER  TABLE  `table_name` MODIFY COLUMN  `id` MEDIUMINT( 8 ) NOT NULL  AUTO_INCREMENT,ADD PRIMARY  KEY(id);
```