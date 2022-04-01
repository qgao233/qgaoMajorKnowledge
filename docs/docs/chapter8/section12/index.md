# 设计规范

https://snailclimb.gitee.io/javaguide/#/docs/database/mysql/MySQL%E9%AB%98%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96%E8%A7%84%E8%8C%83%E5%BB%BA%E8%AE%AE

## Mysql性能优化max_allowed_packet
### 一、max_allowed_packet是什么？
指mysql服务器端和客户端在一次传送数据包的过程当中最大允许的数据包大小。
### 二、什么情况下遇到？
有时候大的插入和更新会被max_allowed_packet 参数限制掉，导致失败。
场景一：将本地数据库迁移到远程数据库时运行sql错误。错误信息是max_allowed_packet

场景二：插入数据时某个字段数据过于庞大(使用Elmentui编辑器自带的图片加密，图片过多，地址超级长，最好用的时候改成自定义的)，会报
Packet for query is too large (20682943>1048576). You can change this value on the server by setting the max_allowed_packet’ variable.

### 三、解决办法？
* 调整mysql的配置文件

mysql 56中该参数修改好像无效，所以需要升级数据库到mysql57
window下修改配置文件my.ini 在mysqld段下添加
```
max_allowed_packet = 64M 
```
后面的数字根据实际情况调优
linux下修改etc/my.cnf ,同样在mysqld段下添加
```
max_allowed_packet = 64M 
```
注意改完参数后需要重启mysql服务

查看目前配置
```
show VARIABLES like '%max_allowed_packet%';
```
* 临时修改
```
set global max_allowed_packet = 10 * 1024 * 1024;
```

>注意：
>1. 命令行修改时，不能用M、G，只能这算成字节数设置。配置文件修改才允许设置M、G单位。
>2. 命令行修改之后，需要退出当前回话(关闭当前mysql server链接)，然后重新登录才能查看修改后的值。通过命令行修改只能临时生效，下次数据库重启后又复原了。
>3. max_allowed_packet 最大值是1G(1073741824)，如果设置超过1G，查看最终生效结果也只有1G。