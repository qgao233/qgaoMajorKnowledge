# css渲染过程及reflow和repaint

css的渲染过程

1、根据HTML的结构生成DOM树（DOM树中包含了display:none的节点） 

2、在DOM树的基础上，根据节点的几何属性（margin/padding/width/height/left等）生成render树 

3、在render树的基础上继续渲染color,font等属性

其中如果1中和2中的属性发生变化会发生reflow(回流)，如果仅仅3中的属性发生改变，只会发生repaint（重绘）。显然从css的渲染过程我们也可以看出来：reflow(回流）必伴随着重绘。

reflow(回流)：当render树中的一部分或者全部因为大小边距等问题发生改变而需要重建的过程叫做回流 
repaint(重绘)：当元素的一部分属性发生变化，如外观背景色不会引起布局变化而需要重新渲染的过程叫做重绘

reflow（回流）会影响浏览器css的渲染速度，因此在做网页性能优化的时候要减少回流的发生。
