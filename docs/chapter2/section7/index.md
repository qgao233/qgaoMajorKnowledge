
# **高并发下的集合替换**

|**多线程**|**单线程**|
| - | - |
|**CopyOnWriteArrayList （内部支持读写锁思想）**|arraylist|
|**ConcurrentLinkedQueue（非阻塞队列，cas来控制多线程并发）**|linkedlist|
|**BlockingQueue （阻塞队列，用锁来控制多线程并发，多用于实现生产者-消费者模式）**||
|**ConcurrentSkipListMap（有序）**||
|**ConcurrentHashMap（无序）**|hashMap|

