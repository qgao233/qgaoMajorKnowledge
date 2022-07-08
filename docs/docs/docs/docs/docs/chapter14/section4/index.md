# makefile

项目的代码管理工具。

记录了如何编译程序的步骤。

规则三要素：目标、依赖、命令。
格式:
```
目标：依赖条件。
    命令。
```

最终目标必须写在文件最上面。

例:
```
app:main.c add.c sub.c mul.c
	gcc main.c add.c sub.c mal.c –o app
```
命令前必须是**tab缩进**。
执行makefile：make命令就是去找makefile里的终极“目标”，然后执行对应的命令。

为了让编译效率提高，可修改为:
```
app:main.o add.o sub.o mul.o
	gcc main.o add.o sub.o mal.o –o app
main.o:main.c
	gcc –c main.c
add.o:add.c
	gcc –c add.c
sub.o: sub.c
	gcc –c sub.c
mul.o: mul.c
	gcc –c mul.c
```
此时如果只修改了add.c，再make时，只会对add.c进行重新编译（原理是在寻找add.o依赖时，会对add.o和add.c的修改时间进行对比）
 

makefile中有【变量】【模式匹配】【自动变量】

**自动变量**只能在某条规则内部的【命令】里使用。
* $<：所属规则中的第一个依赖;
* $@：所属规则中目标;
* $^：规则中的所有依赖;

因此上面的makefile可以改为:
```
obj =main.o add.o sub.o mul.o    # obj:变量，引用时用$()，和shell编程一样
target=app
$(target):$(obj)
	gcc $(obj) –o $(target)
% .o:%.c                         # %:模式匹配，当上面规则在找依赖时会自动将其代入到%，如查找第一个main.o时，会将%.o替换成main.o
	gcc –c $< -o $@
```

makefile里还有函数，所有的函数都有返回值。
就不用再手动给obj变量赋值需要的.o文件了，只需要通过函数调用即可完成，makefile可被修改为:
```
src=$(wildcard  ./*.c)				##查找某目录下的.c (wildcard: 函数名，后面跟参数)
obj=$(patsubst ./%.c, ./%.o, $(src))		##将.c替换成.o
CC=gcc				                ##makefile本身维护的变量（大写）（有默认值），
##用户可以修改
target=app
$(target):$(obj)
	gcc $(obj) –o $(target)
%.o:%.c
	gcc –c $< -o $@
clean:							
	rm $(obj) $(target)
```
上面的clean用来清理.o和可执行程序
使用命令：make clean（此时不会再执行上面的规则，只会执行make后面对应的目标）


