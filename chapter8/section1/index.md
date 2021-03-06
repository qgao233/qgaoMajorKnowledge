# 范式

**函数依赖**：若在一张表中，在属性（或属性组）X 的值确定的情况下，必定能确定属性 Y 的值，那么就可以说 Y 函数依赖于 X，写作 X → Y。

**部分函数依赖**：比如学生基本信息表 R 中（学号，身份证号，姓名）当然学号属性取值是唯一的，在 R 关系中，
（学号，身份证号）->（姓名），
（学号）->（姓名），（身份证号）->（姓名）；
所以（姓名）部分函数依赖于（学号，身份证号）；

**完全函数依赖**：比如学生基本信息表 R（学号，班级，姓名）假设不同的班级学号有相同的，班级内学号不能相同，在 R 关系中，
（学号，班级）->（姓名），
但是（学号）->(姓名)不成立，（班级）->(姓名)不成立，
所以（姓名）完全函数依赖于（学号，班级）；

**传递函数依赖**：比如在关系 R(学号 ,姓名, 系名，系主任)中，
学号 → 系名，系名 → 系主任，
所以存在非主属性（系主任）对于（学号）的传递函数依赖。

* 1NF：属性**不可再分**。
* 2NF：1NF 的基础之上，**消除**了非主属性对于码的**部分函数依赖**。
* 3NF：3NF 在 2NF 的基础之上，**消除**了非主属性对于码的**传递函数依赖**。
