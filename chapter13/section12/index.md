# 点、像素、分辨率

* 点：表示一个点，是屏幕的物理尺寸。
* 像素：px，组成图象的最基本单元要素。
* 分辨率：长方向上拥有的像素个数*宽方向上拥有的像素个数。如（1920*1080），这样的写法叫分辨率，但如果乘出来的结果，称为有这么多像素点

```
px / dp = dpi / 160

dpi：实际每英寸所打印的点数。
160：标准dip，认为1英寸应该有160个点(dpi)，每个点应该对应1px，即1英寸160px（像素密度）
从公式推出在标准情况下，1px刚好等于1dp，因此1英寸应该有160dp。
```

解析上述公式：即当dpi为160时，那么1dp=1px，但如果dpi不为160，即假设dpi=80，也就是1英寸只有80个点，此时1dp=0.5px，

如果按照标准，那么实际使用中1inch=160dp，则1dp=1/160inch，是一个固定值。

**总结：**
在保持dp数值和标准dip不变的情况下，当在屏幕总面积不变时，分辨率增加，其实就是像素密度（单位英寸内的像素）增加，也就是dpi（每英寸打印的点数）增加，

通过公式，可以得出，在dp设置保持不变时，px可以随分辨率动态变化，这也就是设置dp可以适配不同分辨率的屏幕的原因。

>ps：屏幕的像素密度=对角线的分辨率（通过长和宽的像素个数，然后勾股）/屏幕的尺寸（对角线的长度）
