
# **Queue 与 Deque 的区别**
Queue 扩展了 Collection 的接口，是单端队列，只能从一端插入元素，另一端删除元素，

Deque 扩展了 Queue 的接口，是双端队列，增加了在队首和队尾进行插入和删除的方法，在队列的两端均可以插入或删除元素。


|**Queue 接口**|**抛出异常**|**返回特殊值**|
| :- | :- | :- |
|插入队尾|add(E e)|offer(E e)|
|删除队首|remove()|poll()|
|查询队首元素|element()|peek()|


|**Deque 接口**|**抛出异常**|**返回特殊值**|
| :- | :- | :- |
|插入队首|addFirst(E e)|offerFirst(E e)|
|插入队尾|addLast(E e)|offerLast(E e)|
|删除队首|removeFirst()|pollFirst()|
|删除队尾|removeLast()|pollLast()|
|查询队首元素|getFirst()|peekFirst()|
|查询队尾元素|getLast()|peekLast()|

Deque 还提供有 push() 和 pop() 等其他方法，可用于模拟栈。实现类LinkedList既可以当栈使用也可以当队列。

