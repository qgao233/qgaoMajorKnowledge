# 锁

每个对象中都内置了一个 ObjectMonitor对象。另外，wait/notify等方法也依赖于monitor对象，这就是为什么只有在同步的块或者方法中才能调用wait/notify等方法，否则会抛出java.lang.IllegalMonitorStateException的异常的原因。

synchronized 主要存在四种状态，依次是：无锁状态、偏向锁状态、轻量级锁状态、重量级锁状态。

## synchronized或reentrantlock

reentrantlock相比synchronized拥有一个tryLock的优势，它可以先尝试获得锁，或者设置在一定时间内尝试获得锁，如果能获得，再lock()，要不然就直接放弃，而synchronized会直接堵塞未获得锁的线程，没有商量的余地。