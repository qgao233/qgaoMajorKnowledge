
# 数组/列表互换

* 数组转列表：new ArrayList<>(Arrays.asList(T[] t));

* 列表转数组：List对象的toArray(new T[len]); //只能是**对象数组**，不能是基本类型数组

**将多个数组转化成一个链表**
```
list.add(new ArrayList<>(Arrays.asList(nums[i], nums[left], nums[right])));
```

**转成二维数组**
```
List<int[]> list = new ArrayList<>();
list.toArray(new int[list.size()][]);
```

## 将列表转为int数组（基本类型数组）

使用流。

```
ArrayList<Integer> res = new ArrayList<>();
int[] a = res.stream().mapToInt(Integer::valueOf).toArray();
```