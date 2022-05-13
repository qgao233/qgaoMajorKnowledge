# 使用多线程原因

比起用多进程，线程切换的成本要低一些。

* 在单核cpu时代，如果只有一个线程，当它堵塞在io时，cpu就闲置了，因此多线程可以提升效率。
* 在多核cpu时代，在无竞争情况下，一核可以运行一个线程，如果只有一个线程运行，其它核岂不是浪费。

再说现在这个互联网时代，并发量更高才能承载更多的流量，钱不香吗。




