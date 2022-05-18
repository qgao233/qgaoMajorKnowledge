# information_schema

INFORMATION_SCHEMA在每一个MySQL都存在一个**实例对象**，该实例可以使用，但是只能读取表的内容，不能对其执行INSERT、UPDATE、DELETE操作。

INFORMATION_SCHEMA 提供对数据库元数据、有关MySQL服务器的信息（如数据库或表的名称、列的数据类型或访问权限）的访问。有时用于此信息的其他术语是数据字典和系统目录。

官方推荐使用INFORMATION_SCHEMA替代SHOW方式（SHOW DATABASES, SHOW TABLES）。

对于INFORMATION_SCHEMA中的大多数信息，每个MySQL用户都有权访问它们，但只能看到表中与用户具有适当访问权限的对象相对应的行。在使用表时注意INFORMATION_SCHEMA您必须对某个对象具有某种特权才能查看有关该对象的信息

## 查询例子

1. 查询MySQL某个数据库下所有的表信息：
```
SELECT table_name  表名称 , table_type 表类型  , engine  存储引擎
       FROM information_schema.tables
       WHERE table_schema = 'test' ----- table_schema: 数据库名称 
       ORDER BY table_name;
```

>查看当前已登录数据库可以：将'test'替换为(SELECT DATABASE())

2. 查询当前数据库某个表列以及列的属性信息：
```
SELECT column_name     -- 列名称    
	,     CASE              
		WHEN is_nullable = 'no'
		AND column_key != 'PRI' THEN '1'
		ELSE NULL
	END AS is_required        --  是否必须
	, CASE 
		WHEN column_key = 'PRI' THEN '1'
		ELSE '0'
	END AS is_pk,             -- 是否主键
	ordinal_position AS sort,  -- 列位置
	 column_comment      -- 列注释
	, CASE   
		WHEN extra = 'auto_increment' THEN '1'
		ELSE '0'          
	END AS is_increment   -- 是否自增
	, column_type      -- 列类型
FROM information_schema.columns
WHERE table_schema = (
		SELECT DATABASE()
	)
	AND table_name = 'scores'
ORDER BY ordinal_position;
```

3. 查询数据库存储引擎属性
```
SELECT *  from INFORMATION_SCHEMA.ENGINES
```

4. 查询当前正在执行的线程的信息

```
SELECT *  from INFORMATION_SCHEMA.PROCESSLIST
```

5. 查询所有数据库模式信息
```
SELECT *  from INFORMATION_SCHEMA.SCHEMATA
```

6. 查询所有的触发器
```
SELECT *  from INFORMATION_SCHEMA.TRIGGERS
```

7. 查询库中所有用户的权限信息

```
SELECT *  from INFORMATION_SCHEMA.USER_PRIVILEGES
```

8. 查询所有的视图信息
```
SELECT *  from INFORMATION_SCHEMA.VIEWS
```

## INFORMATION_SCHEMA实例数据表参考文档
https://dev.mysql.com/doc/refman/8.0/en/information-schema-table-reference.html