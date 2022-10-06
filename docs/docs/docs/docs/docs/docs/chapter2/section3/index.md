
# Stack
```
             boolean       empty()  
synchronized E             peek()  
synchronized E             pop()  
             E             push(E object)  
synchronized int           search(Object o)  
```
1. Stack实际上也是通过数组去实现的。
>* 执行push时(即，将元素推入栈中)，是通过将元素追加的数组的末尾中。
>* 执行peek时(即，取出栈顶元素，不执行删除)，是返回数组末尾的元素。
>* 执行pop时(即，取出栈顶元素，并将该元素从栈中删除)，是取出数组末尾的元素，然后将该元素从数组中删除。

2. Stack继承于Vector，意味着Vector拥有的属性和功能，Stack都拥有。


