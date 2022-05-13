# 二叉堆

使用数组实现，空出arr[0]，把 arr[1] 作为整棵树的根的话，每个节点的父节点和左右孩子的索引都可以通过**简单的运算**得到，这就是二叉堆设计的一个巧妙之处。

>插入上浮，删除交换再下沉；
>上浮不到顶，下沉不到底；
>上浮比父母，下沉比双子；

```
public class MaxPQ  
<Key extends Comparable<Key» {  
    //存储元素的数组, Java 的泛型，Key可以是任何一种可比较大小的数据类型，你可以认为它是 int、char 等  
    private Key[] pq;  
    //当前Priority Queue中的元素个数   
    private int N = 0;  
    public MaxPQ(int cap) {  
	//索引0不用，所以多分配一个空间  
	pq = (Key[]) new Comparable[cap + 1];  
    }  
    /*返回当前队列中最大元素*/  
    public Key max() {  
	return pq[1];  
    }  
    /*插入元素e */  
    public void insert(Key e) {  
	N++;  
	// 先把新元素加到最后  
	pq[N] = e;  
	// 然后让它上浮到正确的位置  
	swim(N);  
    }  
    /*删除并返回当前队列中最大元素*/  
    public Key delMax() {  
	// 最大堆的堆顶就是最大元素  
	Key max = pq[1];  
	// 把这个最大元素换到最后，删除之  
	exch(1, N);  
	pq[N] = null;  
	N--;  
	// 让 pq[1] 下沉到正确位置  
	sink(1);  
	return max;  
    }  
    /*上浮第k个元素，以维护最大堆性质*/  
    private void swim(int k) {  
	//如果浮到堆顶，就不能再上浮了  
	while (k > 1 && less(parent(k), k)) {  
	    //如果第k个元素比上层大  
	    //将索引k中存储的元素换上去  
	    each(parent(k), k);  
	    k = parent(k);  
	}  
    }  
    /*下沉第k个元素，以维护最大堆性质*/  
    private void sink(int k) {  
	//如果沉到堆底，就沉不下去了  
	while (left(k) <= N) {  
	    //先假设左边节点较大  
	    int older = left(k);  
	    //如果右边节点存在，比一下大小  
	    if (right(k) <= N && less(older, right(k)))   
		older = right(k);  
	    //结点k比俩孩子都大，就不必下沉了  
	    if (less(older, k)) break;  
	    //否则，不符合最大堆的结构，下沉k结点  
	    each(k, older);  
	    k = older;  
	}  
    }  
    /*交换数组的两个元素*/  
    private void each(int i, int j) {  
	Key temp = pq[i];  
	pq[i] = pq[j];  
	pq[j] = temp;  
    }  
    /* pq[i]是否比 pq[j]小？ */  
    private Boolean less(int i, int j) {  
	return pq[i].compareTo(pq[j]) < 0;  
	)  
    }  
    // 父节点的索引  
    int parent(int root) {  
	return root / 2;  
    }  
    // 左孩子的索引  
    int left(int root) {  
	return root * 2;  
    }  
    // 右孩子的索引  
    int right(int root) {  
	return root * 2 + 1;  
    }  
}  
```