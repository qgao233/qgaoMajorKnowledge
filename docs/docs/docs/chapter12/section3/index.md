# div垂直居中

子div在父div垂直居中的最好方法

父div设display:flex;align-items:center;


此处注明一个以前我一直以为可以垂直居中的使用方法，其实是错误的，如果不信，可以自己自行试一遍就知道了。

下面是详细的错误方法

父div设vertical-align:middle;还要设line-height
子div设display:inline-block;
两个div都是有高度的。

事实上子div在父div中并没有垂直居中（文字垂直居中了，但是也不是绝对意义上的），自行尝试以上设置。

这里讲一下为什么我会被骗，以为可以垂直居中。

首先一个父div，两个子div.
其中两个子div高度不同。

然后通过上述设置后，会发现两个子div是默认底部对齐，并且两个子div相对父div也绝不是垂直居中（绝对意义上的）

这时有意思的就来了，你在两个子div中相对较高的那个插入vertical-align:middle;

你看一下效果，是不是好像两个子div就相对于父div垂直居中了。

解释：
其实，当你对较高的子div设置了vertical-align:middle后，就是指这个子div（inline-block）里的内容要垂直居中，那么相对于子div来说（因为之前里面的东西是底部对齐），它就往下移了。

不懂的，仔细看两遍，或者自己实验一下就明白了。

以前一直没注意过这个问题，希望能帮助到大家。
