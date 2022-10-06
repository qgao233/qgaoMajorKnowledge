# Collections&Arrays

## 1 collections

```
void reverse(List list)//反转
void shuffle(List list)//随机排序
void sort(List list)//按自然排序的升序排序
void sort(List list, Comparator c)//定制排序，由Comparator控制排序逻辑
void swap(List list, int i , int j)//交换两个索引位置的元素
void rotate(List list, int distance)//旋转。当distance为正数时，将list后distance个元素整体移到前面。当distance为负数时，将 list的前distance个元素整体移到后面
int binarySearch(List list, Object key)//对List进行二分查找，返回索引，注意List必须是有序的
int max(Collection coll)//根据元素的自然顺序，返回最大的元素。 类比int min(Collection coll)
int max(Collection coll, Comparator c)//根据定制排序，返回最大元素，排序规则由Comparatator类控制。类比int min(Collection coll, Comparator c)
void fill(List list, Object obj)//用指定的元素代替指定list中的所有元素
int frequency(Collection c, Object o)//统计元素出现次数
int indexOfSubList(List list, List target)//统计target在list中第一次出现的索引，找不到则返回-1，类比int lastIndexOfSubList(List source, list target)
boolean replaceAll(List list, Object oldVal, Object newVal)//用新元素替换旧元素
```

## 2 Arrays

```
copyOfRange(int[] original, int from, int to)//返回某个范围的元素形成的数组
copyOf(int[] original, int newLength)//复制数组，内部实际调用System.arraycopy
toString()
sort
binarySearch
equals
fill
```

### 2.1 System.arraycopy

`System.arraycopy(Object src, int srcPos, Object dest, int destPos, int length);`

从`src`中索引`srcPos`开始的`length`个item复制到`dest`中索引为`destPos`的位置上。

>* 浅拷贝：假设B复制了A，当修改A时，B也跟着变了
>* 深拷贝：B没变

该方法针对不同类型复制的类型不同：

* 浅拷贝：二维数组
* 深拷贝：基本数据类型(int/long...)、Object类型(自定义的)、String类型

该方法**线程不安全**，参考[System.arraycopy线程安全问题](https://qgao233.github.io/qgaoFacedProblems/work/5_28/)