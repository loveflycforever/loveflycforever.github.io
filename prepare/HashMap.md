7、8

7为什么使用头插法之所以把Entry6放在头节点，是因为HashMap的发明者认为，后插入的Entry被查找的可能性更大。

HashMap的默认初始长度时16，并且每次自动扩展或者手动初始化时，长度必须时2的幂。
当put一个值时，会首先计算出hash code，根据hash code决定放在数组的位置，考虑效率问题并没有采用取模的方式，而是采用按位与的方式
可以说，Hash算法最终得到的index结果，完全取决于Key的Hashcode值的最后几位。


HashMap是线程不安全的，原因就在于HashMap的rehash。rehash是HashMap扩容过程中的一个步骤。

HashMap需要扩展它的长度，也就是进行Resize。


影响发生Resize的因素有两个：

1.Capacity

HashMap的当前长度。上一期曾经说过，HashMap的长度是2的幂。

2.LoadFactor

HashMap负载因子，默认值为0.75f。

衡量HashMap是否进行Resize的条件如下：

HashMap.Size   >=  Capacity * LoadFactor


HashMap的扩容主要分为两步：

1.扩容

创建一个新的Entry空数组，长度是原数组的2倍

2.ReHash

遍历原Entry数组，把所有的Entry重新Hash到新数组。为什么要重新Hash呢？因为长度扩大以后，Hash的规则也随之改变。

属性字段

java.util.HashMap.treeifyBin
```text
package java.util;

......

public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {
    
    ......
    
    static final int MIN_TREEIFY_CAPACITY = 64;

    final void treeifyBin(Node<K,V>[] tab, int hash) {
        int n, index; Node<K,V> e;
        if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
            resize();
        else if ((e = tab[index = (n - 1) & hash]) != null) {
            ......
        }
    }
    ......
}
```
注意：链表长度大于等于8 树化的时候 还有判断的哦，tab[].length > 64  才会进行树化，不然会进行再次扩容

TreeNodes占用空间是普通Nodes的两倍

不是一开始就将其转换为TreeNodes，而是需要一定节点数才转为TreeNodes，说白了就是trade-off，空间和时间的权衡：

链表长度达到8就转成红黑树，当长度降到6就转成普通bin。（bin就是bucket，即HashMap中hashCode值一样的元素保存的地方）

当hashCode离散性很好的时候，树型bin用到的概率非常小，因为数据均匀分布在每个bin中，几乎不会有bin中链表长度会达到阈值。

![sss](https://img-blog.csdnimg.cn/20190726141154928.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dvMTkwMTQ0NjQwOQ==,size_16,color_FFFFFF,t_70)

但是在随机hashCode下，离散性可能会变差，然而JDK又不能阻止用户实现这种不好的hash算法，因此就可能导致不均匀的数据分布。

不过理想情况下随机hashCode算法下所有bin中节点的分布频率会遵循泊松分布，可以看到，一个bin中链表长度达到8个元素的概率为0.00000006，几乎是不可能事件。

由此可见，发展30年的Java每一项改动和优化都是非常严谨和科学的。




transient Node<K,V>[] table; //HashMap的哈希桶数组，非常重要的存储结构，用于存放表示键值对数据的Node元素。

transient Set<Map.Entry<K,V>> entrySet; //HashMap将数据转换成set的另一种存储形式，这个变量主要用于迭代功能。

transient int size; //HashMap中实际存在的Node数量，注意这个数量不等于table的长度，甚至可能大于它，因为在table的每个节点上是一个链表（或RBT）结构，可能不止有一个Node元素存在。

transient int modCount; //HashMap的数据被修改的次数，这个变量用于迭代过程中的Fail-Fast机制，其存在的意义在于保证发生了线程安全问题时，能及时的发现（操作前备份的count和当前modCount不相等）并抛出异常终止操作。

int threshold; //HashMap的扩容阈值，在HashMap中存储的Node键值对超过这个数量时，自动扩容容量为原来的二倍。

final float loadFactor; //HashMap的负载因子，可计算出当前table长度下的扩容阈值：threshold = loadFactor * table.length。 


//默认的初始容量为16，必须是2的幂次
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; 

//最大容量即2的30次方
static final int MAXIMUM_CAPACITY = 1 << 30;

//默认加载因子
static final float DEFAULT_LOAD_FACTOR = 0.75f;

//当put一个元素时，其链表长度达到8时将链表转换为红黑树
static final int TREEIFY_THRESHOLD = 8;

//链表长度小于6时，解散红黑树
static final int UNTREEIFY_THRESHOLD = 6;

//默认的最小的扩容量64，为避免重新扩容冲突，至少为4 * TREEIFY_THRESHOLD=32，即默认初始容量的2倍
static final int MIN_TREEIFY_CAPACITY = 64;



红黑树
2-3树