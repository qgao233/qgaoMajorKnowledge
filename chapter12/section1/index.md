# $(document)&$(window)

document是window的一个属性

$(document)在dom结构绘制完毕后，就执行
$(window)必须等到页面内包括加载完各种js、css、image、iframe/frame资源才执行。
$(parent)parent就是父元素的window对象(iframe中用到）


所有浏览器都支持 window 对象。它表示浏览器窗口。

所有 JavaScript 全局对象、函数以及变量均自动成为 window 对象的成员。

全局变量是 window 对象的属性。

全局函数是 window 对象的方法。

甚至 HTML DOM 的 document 也是 window 对象的属性之一
