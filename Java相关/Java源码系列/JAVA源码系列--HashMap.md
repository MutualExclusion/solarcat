###前言

&emsp;&emsp;HashMap是Java最常用的集合之一，现今HashMap、Hashtable、ConcurrentHashMap三大集合已经成为面试常考的考点。这三个集合都是以key-value的键值对形式存储元素。除了三者之间的区别和联系。我们更要弄懂每个集合的存储方式、扩容机制、和并发处理。在Java中的源码是非常多的，两三千行代码不可能都记住，所以我们要深刻理解源代码，尽量不要死记硬背。1.8版本的HashMap实现了java.util.function包下的接口，是为新特性提供支持。此外1.8版本的另一个新特性就是HashMap的红黑树。下面直接我们来一起学习HashMap

###概述

-	HashMap是常用的Java集合之一，是基于哈希表的Map接口的实现。与HashTable主要区别为不支持同步和允许null作为key和value。

-	HashMap非线程安全，即任一时刻可以有多个线程同时写HashMap，可能会导致数据的不一致。

-	如果需要满足线程安全，可以用 Collections的synchronizedMap方法使HashMap具有线程安全的能力，或者使用ConcurrentHashMap。

-	在JDK1.6中，HashMap采用数组+链表实现，即使用链表处理冲突，同一hash值的链表都存储在一个链表里。

-	但是当位于一个数组中的元素较多，即hash值相等的元素较多时，通过key值依次查找的效率较低。

-	而JDK1.8中，HashMap采用数组+链表+红黑树实现，当链表长度超过阈值8时，将链表转换为红黑树，这样大大减少了查找时间。

-	原本Map.Entry接口的实现类Entry改名为了Node。转化为红黑树时改用另一种实现TreeNode

###1.8版本HashMap的重要属性

|1.8版本HashMap的重要属性|说明|
|:-:|:-:|
|static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;|默认的初始容量（容量为HashMap中槽的数目）是16，且实际容量必须是2的整数次幂。|
|static final int MAXIMUM_CAPACITY = 1 << 30;|最大容量（必须是2的幂且小于2的30次方，传入容量过大将被这个值替换）|
|static final float DEFAULT_LOAD_FACTOR = 0.75f;|默认装填因子0.75，如果当前键值对个数 >= HashMap最大容量*装填因子，进行rehash操作|
|static final int TREEIFY_THRESHOLD = 8;|JDK1.8 新加，Entry链表最大长度，当桶中节点数目大于该长度时，将链表转成红黑树存储；|
|static final int UNTREEIFY_THRESHOLD = 6;|JDK1.8 新加，当桶中节点数小于该长度，将红黑树转为链表存储；|
|static final int MIN_TREEIFY_CAPACITY = 64;|桶可能被转化为树形结构的最小容量。当哈希表的大小超过这个阈值，才会把链式结构转化成树型结构，否则仅采取扩容来尝试减少冲突。应该至少4*TREEIFY_THRESHOLD来避免扩容和树形结构化之间的冲突。|
|transient Node<K, V>[] table;|哈希桶数组，分配的时候，table的长度总是2的幂|
|transient Set<Map.Entry<K, V>> entrySet;|HashMap将数据转换成set的另一种存储形式，这个变量主要用于迭代功能|
|transient int size;|实际存储的数量，则HashMap的size()方法，实际返回的就是这个值，isEmpty()也是判断该值是否为0|
|transient int modCount;|hashmap结构被改变的次数，fail-fast机制|
|int threshold;|HashMap的扩容阈值，在HashMap中存储的Node键值对超过这个数量时，自动扩容容量为原来的二倍|
|final float loadFactor;|HashMap的负加载因子，可计算出当前table长度下的扩容阈值：threshold = loadFactor * table.length|

###1.8版本HashMap重要的方法

&emsp;&emsp;可以从上面的属性看出来HashMap的构建、扩容还有转化是一件相当麻烦的事情了，下面我们就从构造函数开始深入源码吧。  

    /**
     * 使用指定的初始化容量initial capacity 和加载因子load factor构造一个空HashMap
     *
     * @param initialCapacity 初始化容量
     * @param loadFactor      加载因子
     * @throws IllegalArgumentException 如果指定的初始化容量为负数或者加载因子为非正数
     */
    public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                    initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                    loadFactor);
        this.loadFactor = loadFactor;
        this.threshold = tableSizeFor(initialCapacity);
    }

&emsp;&emsp;这段构造函数代码的作用就是构建一个合法的HashMap,它验证初始容量的大小和加载因子的范围。其中tableSizeFor是HashMap的一个方法。

    /**
     * 结果为>=cap的最小2的自然数幂
     */
    static final int tableSizeFor(int cap) {
        //先移位再或运算，最终保证返回值是2的整数幂
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }
&emsp;&emsp;这个算法真的是高科技，有兴趣的同学可以去看一看大神的分析([点击查看链接](http://blog.csdn.net/huzhigenlaohu/article/details/51802457 "HashMap方法hash()、tableSizeFor()")，反正他说的我是看懂了)，其他的构造函数就不分析了，粘过来只是浪费版面。还有一个重要的方法hash()计算key的哈希值，上面链接中大神已经讲的很详细了，我也不多哔哔了。现在假设我们已经创建了一个加载因子为0.75，初始容量为16的HashMap，下面我们要开始填入key-value。（一直填入直到zha）

	public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }

&emsp;&emsp;可以看到HashMap直接调用了另一个内部函数。并将key值进行高16位和低16位异或操作。（h >>> 16，表示无符号右移16位，高位补0，任何数跟0异或都是其本身，因此key的hash值高16位不变。）。

    public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }
    /**
     * putVal方法可以分为下面的几个步骤:
     * 1.如果哈希表为空，调用resize()创建一个哈希表。
     * 2.如果指定参数hash在表中没有对应的桶，即为没有碰撞，直接将键值对插入到哈希表中即可。
     * 3.如果有碰撞，遍历桶，找到key映射的节点
     * 3.1桶中的第一个节点就匹配了，将桶中的第一个节点记录起来。
     * 3.2如果桶中的第一个节点没有匹配，且桶中结构为红黑树，则调用红黑树对应的方法插入键值对。
     * 3.3如果不是红黑树，那么就肯定是链表。遍历链表，如果找到了key映射的节点，就记录这个节点，退出循环。如果没有找到，在链表尾部插入节点。插入后，如果链的长度大于TREEIFY_THRESHOLD这个临界值，则使用treeifyBin方法把链表转为红黑树。
     * 4.如果找到了key映射的节点，且节点不为null
     * 4.1记录节点的vlaue。
     * 4.2如果参数onlyIfAbsent为false，或者oldValue为null，替换value，否则不替换。
     * 4.3返回记录下来的节点的value。
     * 5.如果没有找到key映射的节点（2、3步中讲了，这种情况会插入到hashMap中），插入节点后size会加1，这时要检查size是否大于临界值threshold，如果大于会使用resize方法进行扩容。
     *
     * @param hash 高16为异或低16位操作后的hash值
     * @param key 参数key值
     * @param value 参数value值
     * @param onlyIfAbsent 如果为true，即使指定参数key在map中已经存在，也不会替换value
     * @param evict 如果为false，数组table在创建模式中
     * @return 如果value被替换，则返回旧的value，否则返回null。当然，可能key对应的value就是null。
     */
    final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        // 如果哈希表为空，调用resize()创建一个哈希表，并用变量n记录哈希表长度
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        // 如果指定参数hash在表中没有对应的桶，即为没有冲突
        // (n-1) & hash计算key在table数组中的下标
        if ((p = tab[i = (n - 1) & hash]) == null)
        	// 直接插入新的Node
            tab[i] = newNode(hash, key, value, null);
        // 此时p就是当前桶的第一个元素
        else {
            Node<K,V> e; K k;
            // 比较第一个结点的hash与该hash值是否相同
            // 并且第一个元素的key值与之相等或者key值不为空
            // 并且key的哈希值等于第一结点的哈希值
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
            	// 将第一个元素赋值给e，用e来记录
                e = p;
            // 当前桶中无该键值对，且桶是红黑树结构，按照红黑树结构插入
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
            		// 当前桶中无该键值对，且桶是链表结构，按照链表结构插入到尾部
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        // 检查链表长度是否达到阈值，达到将该槽位节点组织形式转为红黑树
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    // 链表节点的<key, value>与put操作<key, value>相同时，不做重复操作，跳出循环
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            // 找到或新建一个key和hashCode与插入元素相等的键值对，进行put操作
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                //onlyIfAbsent为false或旧值为null时，允许替换旧值，否则无需替换
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                //访问后回调
                afterNodeAccess(e);
                //返回旧值
                return oldValue;
            }
        }
        //更改结构化信息
        ++modCount;
        // 键值对数目超过阈值时，进行rehash
        if (++size > threshold)
            resize();
        // 插入后回调
        afterNodeInsertion(evict);
        return null;
    }
&emsp;&emsp;其中，如果插入桶中的数量大于或者等于TREEIFY_THRESHOLD - 1,将该桶中的链表转化成红黑树（红黑树的知识点在这里不总结了，以后会专门写下一篇，然后再补充吧）。此外如果键值对的数目超过了阈值我们还需要对HashMap进行重构。这两个方法有点恶心，请一定耐心观看。

    /**
     * 对table进行初始化或者扩容。
     * 如果table为null，则对table进行初始化
     * 如果对table扩容，因为每次扩容都是翻倍，与原来计算（n-1）&hash的结果相比，节点要么就在原来的位置，要么就被分配到“原位置+旧容量”这个位置
     * resize的步骤总结为:
     * 1.计算扩容后的容量，临界值。
     * 2.将hashMap的临界值修改为扩容后的临界值
     * 3.根据扩容后的容量新建数组，然后将hashMap的table的引用指向新数组。
     * 4.将旧数组的元素复制到table中。
     *
     * @return the table
     */
    final Node<K,V>[] resize() {
    	// 将旧的table保存到
        Node<K,V>[] oldTab = table;
        // 获取原来数组的长度
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        // 获取原数组扩容的临界值
        int oldThr = threshold;
        // 声明新的数组长度、新的扩容临界值
        int newCap, newThr = 0;
        if (oldCap > 0) {
        	// 如果原来的数组长度大于最大值(2^30)
            if (oldCap >= MAXIMUM_CAPACITY) {
            	// 扩容临界值提高到正无穷
                threshold = Integer.MAX_VALUE;
                // 无法进行扩容，返回原来的数组
                return oldTab;
            }
            // 如果现在容量的两倍小于MAXIMUM_CAPACITY且现在的容量大于DEFAULT_INITIAL_CAPACITY
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
            	// 临界值变为原来的2倍
                newThr = oldThr << 1; // double threshold
        }
        //如果旧容量 <= 0，而且旧临界值 > 0
        else if (oldThr > 0) // initial capacity was placed in threshold
        	//数组的新容量设置为老数组扩容的临界值
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
        	//如果旧容量 <= 0，且旧临界值 <= 0，新容量扩充为默认初始化容量，新临界值为DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY
        	newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        // 计算新的resize上限
        if (newThr == 0) {
        	// 在当上面的条件判断中，只有oldThr > 0成立时，newThr == 0
            // ft为临时临界值，下面会确定这个临界值是否合法，如果合法，那就是真正的临界值
            float ft = (float)newCap * loadFactor;
            // 当新容量< MAXIMUM_CAPACITY且ft < (float)MAXIMUM_CAPACITY，新的临界值为ft，否则为Integer.MAX_VALUE
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        // 将扩容后hashMap的临界值设置为newThr
        threshold = newThr;
        @SuppressWarnings({"unchecked"})
        // 创建新的table，初始化容量为newCap
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        // 修改hashMap的table为新建的newTab
        table = newTab;
        // 上面的代码是用来确定新的table大小，下面的数组是用来从旧数组中遍历复制元素
        // 如果旧table不为空，将旧table中的元素复制到新的table中
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                // 如果旧桶不为null，使用e记录旧桶
                if ((e = oldTab[j]) != null) {
                	//将旧桶置为null
                    oldTab[j] = null;
                    // 如果旧桶中只有一个node
                    if (e.next == null)
                    	// 将e也就是oldTab[j]放入newTab中e.hash & (newCap - 1)的位置
                        newTab[e.hash & (newCap - 1)] = e;
                    //如果旧桶中的结构为红黑树
                    else if (e instanceof TreeNode)
                    	// 将树中的node分离
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                    	// 如果旧桶中的结构为链表,链表重排，jdk1.8做的一系列优化
                    	// 如果扩容后，元素的index依然与原来一样，那么使用这个head和tail指针
                        Node<K,V> loHead = null, loTail = null;
                        // 如果扩容后，元素的index=index+oldCap，那么使用这个head和tail指针
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        //遍历整个链表中的节点
                        do {
                            next = e.next;
                            // 元素的索引与原来一样
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        // 原索引放到桶中
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        // 原索引+oldCap放到桶中
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }

    /**
     * 将链表转化为红黑树
     * @param tab 桶中链表数据
     * @param hash 结点哈希值
     */
    final void treeifyBin(Node<K,V>[] tab, int hash) {
        int n, index; Node<K,V> e;
        // 如果桶数组table为空，或者桶数组table的长度小于MIN_TREEIFY_CAPACITY，不符合转化为红黑树的条件
        if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
            // 对数组进行扩容或者初始化
        	resize();
        // 如果符合转化为红黑树的条件，而且hash对应的桶不为null
        else if ((e = tab[index = (n - 1) & hash]) != null) {
        	// 红黑树的头结点、尾结点
            TreeNode<K,V> hd = null, tl = null;
            do {
            	// 替换链表node为TreeNode，建立双向链表
                TreeNode<K,V> p = replacementTreeNode(e, null);
                // 确定头结点
                if (tl == null)
                    hd = p;
                else {
                    p.prev = tl;
                    tl.next = p;
                }
                tl = p;
            } while ((e = e.next) != null);
            // 遍历链表插入每个节点到红黑树
            if ((tab[index] = hd) != null)
                hd.treeify(tab);
        }
    }
&emsp;&emsp;上面的代码解释的已经足够清楚了，但是本人有点懒，说实在的，代码、思路这东西我是在不愿看，所以我一般都是先看一遍，然后自己画张图，便于理解+记忆。

![HashMap](http://raw.githubusercontent.com/MutualExclusion/solarcat/master/Java%E7%9B%B8%E5%85%B3/Java%E6%BA%90%E7%A0%81%E7%B3%BB%E5%88%97/HashMap.png)

&emsp;&emsp;现在，我们已经对HashMap有了较为深刻的认识了，赶紧为自己鼓鼓掌吧。

&emsp;&emsp;再继续学习HashMap之前，我们还要思考一个问题，HashMap在多线程的情况下，链表成环问题。这个问题是在1.7版本中的java出现的，目前1.8已经对HashMap做了改进。具体问题在这里不做说明了。哈哈！

&emsp;&emsp;接下俩我们看一下get这个方法吧：

	/**
     * 返回指定的key映射的value，如果value为null，则返回null
     * get可以分为三个步骤：
     * 1.通过hash(Object key)方法计算key的哈希值hash。
     * 2.通过getNode( int hash, Object key)方法获取node。
     * 3.如果node为null，返回null，否则返回node.value。
     *
     * @see #put(Object, Object)
     */
    public V get(Object key) {
        Node<K, V> e;
        //根据key及其hash值查询node节点，如果存在，则返回该节点的value值
        return (e = getNode(hash(key), key)) == null ? null : e.value;
    }
&emsp;&emsp;看到这么少的代码是不是有种感动要死的赶脚，但是别急getNode这个方法不是还没看呢么？但是也快结束了，哈哈。

    /**
     * 根据key的哈希值和key获取对应的节点
     * getNode可分为以下几个步骤：
     * 1.如果哈希表为空，或key对应的桶为空，返回null
     * 2.如果桶中的第一个节点就和指定参数hash和key匹配上了，返回这个节点。
     * 3.如果桶中的第一个节点没有匹配上，而且有后续节点
     * 3.1如果当前的桶采用红黑树，则调用红黑树的get方法去获取节点
     * 3.2如果当前的桶不采用红黑树，即桶中节点结构为链式结构，遍历链表，直到key匹配
     * 4.找到节点返回null，否则返回null。
     *
     * @param hash 指定参数key的哈希值
     * @param key  指定参数key
     * @return 返回node，如果没有则返回null
     */
    final Node<K, V> getNode(int hash, Object key) {
        Node<K, V>[] tab;
        Node<K, V> first, e;
        int n;
        K k;
        // 如果哈希表不为空，而且key对应的桶上不为空
        if ((tab = table) != null && (n = tab.length) > 0 &&
                (first = tab[(n - 1) & hash]) != null) {
            // 如果桶中的第一个节点就和指定参数hash和key匹配上了
            if (first.hash == hash && // always check first node
                    ((k = first.key) == key || (key != null && key.equals(k))))
                // 返回桶中的第一个节点
                return first;
            // 如果桶中的第一个节点没有匹配上，而且有后续节点
            if ((e = first.next) != null) {
                // 如果当前的桶采用红黑树，则调用红黑树的get方法去获取节点
                if (first instanceof TreeNode)
                    return ((TreeNode<K, V>) first).getTreeNode(hash, key);
                // 如果当前的桶不采用红黑树，即桶中节点结构为链式结构
                do {
                    // 遍历链表，直到key匹配
                    if (e.hash == hash &&
                            ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        // 如果哈希表为空，或者没有找到节点，返回null
        return null;
    }

&emsp;&emsp;以上代码大致就是按照HashMap这种特殊数组+链表+红黑树数据结构所设计的查找元素方式。HashMap的的重点在于其特殊的扩容机制、数据结构。我们在前面已经阐述的较为详细了。关于线程安全等一系列问题我在前面的概述中说的已经比较详细了，不在说了。