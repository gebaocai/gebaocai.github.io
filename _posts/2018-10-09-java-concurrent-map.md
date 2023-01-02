---
layout: post
title: Java之ConcurrentMap
author: "gebaocai"
header-style: text
lang: zh
tags:
  - Java
  - ConcurrentMap
---
# 什么是ConcurrentMap?
提供线程安全和原子保证的`Map`
# 主要实现类?
- ConcurrentHashMap

**线程安全**

采用CAS（Compare and Swap）技术保证同步, 链表情况下通过`synchronized`加锁来保证线程安全。具体可参见`putVal`方法:
```
final V putVal(K key, V value, boolean onlyIfAbsent) {
    if (key == null || value == null) throw new NullPointerException();
    int hash = spread(key.hashCode());
    int binCount = 0;
    for (Node<K,V>[] tab = table;;) {
        Node<K,V> f; int n, i, fh;
        if (tab == null || (n = tab.length) == 0)
            tab = initTable();
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            if (casTabAt(tab, i, null,
                            new Node<K,V>(hash, key, value, null)))
                break;                   // no lock when adding to empty bin
        }
        else if ((fh = f.hash) == MOVED)
            tab = helpTransfer(tab, f);
        else {
            V oldVal = null;
            synchronized (f) {
                if (tabAt(tab, i) == f) {
                    if (fh >= 0) {
                        ...
                    }
                    else if (f instanceof TreeBin) {
                        Node<K,V> p;
                        binCount = 2;
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                        value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                }
            }
            if (binCount != 0) {
                if (binCount >= TREEIFY_THRESHOLD)
                    treeifyBin(tab, i);
                if (oldVal != null)
                    return oldVal;
                break;
            }
        }
    }
    addCount(1L, binCount);
    return null;
}
```
**数据结构**

TreeNode:树节点类,也是整个数据结构中的链表
```
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    volatile V val;
    volatile Node<K,V> next;
}
```
TreeBin是TreeNode链表的根节点.这是与HashMap不同的地方,HashMap整个链表结构都是通过TreeNode来实现的.而这里,数组里边存储的是一个TreeBin,TreeBin封装了TreeNode,并同时提供了链表转红黑树的实现.

```
 static final class TreeBin<K,V> extends Node<K,V> {
    TreeNode<K,V> root;
    volatile TreeNode<K,V> first;
    volatile Thread waiter;
    volatile int lockState;
    // values for lockState
    static final int WRITER = 1; // set while holding write lock
    static final int WAITER = 2; // set when waiting for write lock
    static final int READER = 4; // increment value for setting read lock
 }
```
**扩容实现**
在执行putVal方法时,如果链表长度大于8时,就会执行扩容触发转换红黑树的操作:
```
if (binCount != 0) {
    if (binCount >= TREEIFY_THRESHOLD)
        treeifyBin(tab, i);
    if (oldVal != null)
        return oldVal;
    break;
}
```
```
/**
* Replaces all linked nodes in bin at given index unless table is
* too small, in which case resizes instead.
*/
private final void treeifyBin(Node<K,V>[] tab, int index) {
    Node<K,V> b; int n, sc;
    if (tab != null) {
        if ((n = tab.length) < MIN_TREEIFY_CAPACITY)
            tryPresize(n << 1);
        else if ((b = tabAt(tab, index)) != null && b.hash >= 0) {
            synchronized (b) {
                if (tabAt(tab, index) == b) {
                    TreeNode<K,V> hd = null, tl = null;
                    for (Node<K,V> e = b; e != null; e = e.next) {
                        TreeNode<K,V> p =
                            new TreeNode<K,V>(e.hash, e.key, e.val,
                                                null, null);
                        if ((p.prev = tl) == null)
                            hd = p;
                        else
                            tl.next = p;
                        tl = p;
                    }
                    setTabAt(tab, index, new TreeBin<K,V>(hd));
                }
            }
        }
    }
}
```
数组扩容:
```
/**
* Tries to presize table to accommodate the given number of elements.
*
* @param size number of elements (doesn't need to be perfectly accurate)
*/
private final void tryPresize(int size) {
    int c = (size >= (MAXIMUM_CAPACITY >>> 1)) ? MAXIMUM_CAPACITY :
        tableSizeFor(size + (size >>> 1) + 1);
    int sc;
    while ((sc = sizeCtl) >= 0) {
        Node<K,V>[] tab = table; int n;
        if (tab == null || (n = tab.length) == 0) {
            n = (sc > c) ? sc : c;
            if (U.compareAndSwapInt(this, SIZECTL, sc, -1)) {
                try {
                    if (table == tab) {
                        @SuppressWarnings("unchecked")
                        Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];
                        table = nt;
                        sc = n - (n >>> 2);
                    }
                } finally {
                    sizeCtl = sc;
                }
            }
        }
        else if (c <= sc || n >= MAXIMUM_CAPACITY)
            break;
        else if (tab == table) {
            int rs = resizeStamp(n);
            if (sc < 0) {
                Node<K,V>[] nt;
                if ((sc >>> RESIZE_STAMP_SHIFT) != rs || sc == rs + 1 ||
                    sc == rs + MAX_RESIZERS || (nt = nextTable) == null ||
                    transferIndex <= 0)
                    break;
                if (U.compareAndSwapInt(this, SIZECTL, sc, sc + 1))
                    transfer(tab, nt);
            }
            else if (U.compareAndSwapInt(this, SIZECTL, sc,
                                            (rs << RESIZE_STAMP_SHIFT) + 2))
                transfer(tab, null);
        }
    }
}
```
- ConcurrentSkipListMap

ConcurrentSkipListMap是一个内部使用跳表，并且支持排序和并发的一个Map。是根据其键的自然顺序排序的，或者由在映射创建时提供的比较器排序，这取决于使用哪个构造函数。

**跳表**

跳表是一种允许在一个有顺序的序列中进行快速查询的数据结构。

跳表具有以下几种特性：

1.由很多层组成，level越高的层节点越少，最后一层level用有所有的节点数据。

2.每一层的节点数据也都是有顺序的。

3.上面层的节点肯定会在下面层中出现。

4.每个节点都有两个指针，分别是同一层的下一个节点指针和下一层节点的指针。

使用跳表查询元素的时间复杂度是O(log n)，跟红黑树一样。查询效率还是不错的，

但是跳表的存储容量变大了，本来一共只有7个节点的数据，使用跳表之后变成了14个节点。

所以跳表是一种使用”空间换时间”的概念用来提高查询效率的链表，开源软件Redis、LevelDB都使用到了跳表。跳表相比B树，红黑树，AVL树时间复杂度一样，但是耗费更多存储空间，但是跳表的优势就是它相比树，实现简单，不需要考虑树的一些rebalance问题。

**数据结构**
```
// 每个节点的封装，跟层数没有关系
static final class Node<K,V> {
    final K key; // 节点的关键字
    volatile Object value; // 节点的值
    volatile Node<K,V> next; // 节点的next节点引用
    ...
}

// 每一层节点的封装，叫做索引
static class Index<K,V> {
    final Node<K,V> node; // 对应的节点
    final Index<K,V> down; // 下一层索引
    volatile Index<K,V> right; // 同一层的下一个索引
    ...
}

// 每一层的头索引
static final class HeadIndex<K,V> extends Index<K,V> {
    final int level; // Level 级别
    HeadIndex(Node<K,V> node, Index<K,V> down, Index<K,V> right, int level) {
        super(node, down, right);
        this.level = level;
    }
    ...
}
```