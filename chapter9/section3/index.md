# 事务

Redis 中的事务就理解为 ：Redis 事务提供了一种将多个命令请求打包的功能。然后，再按顺序执行打包的所有命令，并且不会被中途打断。

multi: 开始事务(在开始事务后可以使用discard丢弃之前输入过的命令）

exec: 提交事务

不过一旦我watch过某个变量，要是在事务中你更改了它并提交事务就会让在事务中执行过的操作全部失败。