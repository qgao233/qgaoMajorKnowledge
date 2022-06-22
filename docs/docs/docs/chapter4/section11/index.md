# CAS

**CAS**不是保证原子的更新，而是使用**死循环保证更新成功时候只有一个线程更新**，不包括主工作内存的同步。 

**CAS配合volatile**既保证了**只有一个线程更新又保证了多个线程更新获得的是最新的值**互不影响。

## aba问题

考虑如下操作：

>并发1（上）：获取出数据的初始值是A，后续计划实施CAS乐观锁，期望数据仍是A的时候，修改才能成功
>
>并发2：将数据修改成B
>
>并发3：将数据修改回A
>
>并发1（下）：CAS乐观锁，检测发现初始值还是A，进行数据修改

上述并发环境下，并发1在修改数据时，虽然还是A，但已经不是初始条件的A了，中间发生了A变B，B又变A的变化，此A已经非彼A，数据却成功修改，可能导致错误，这就是CAS引发的所谓的ABA问题。

**解决办法**

ABA问题导致的原因，是CAS过程中**只简单进行了“值”的校验**，再有些情况下，“值”相同不会引入错误的业务逻辑（例如库存），有些情况下，“值”虽然相同，却已经不是原来的数据了。

优化方向：CAS不能只比对“值”，还必须确保的是原来的数据，才能修改成功。

常见实践：“版本号”的比对，一个数据一个版本，版本变化，即使值相同，也不应该修改成功。

```
/*
如果当前引用为{@code ==}到预期引用且当前戳记等于预期戳记，则以原子方式将引用和戳记的值设置为给定的更新值。 
*/
public class AtomicStampedReference<V> {
    public boolean compareAndSet(V   expectedReference,
                                V   newReference,
                                int expectedStamp,
                                int newStamp) {
        Pair<V> current = pair;
        return
            expectedReference == current.reference &&
            expectedStamp == current.stamp &&
            ((newReference == current.reference &&
            newStamp == current.stamp) ||
            casPair(current, Pair.of(newReference, newStamp)));
        }
    private boolean casPair(Pair<V> cmp, Pair<V> val) {
        return UNSAFE.compareAndSwapObject(this, pairOffset, cmp, val);
    }
}
```