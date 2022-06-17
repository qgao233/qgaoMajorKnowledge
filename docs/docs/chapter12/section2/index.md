# 宽高自适应

有两种

一、子元素设固定宽高，父元素设置inline-block，父元素宽高就可以随子元素的宽高变化

不过当父元素设置了inline-block后就不仅会受到block特性的换行符影响，还会受到inline的lineheight、fontsize以及vertical-align的共同影响，
其中最重要的是line-height,因为当你为父元素设置了inline-block后，你会发现父元素的实际高度总是要比子元素高一些，这就是line-height在起作用，此时只要把父元素的line-height设为0，就可以解决此问题。

二、父元素设固定宽高，子元素设置宽高100%，子元素随父元素变化
