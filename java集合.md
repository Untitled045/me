# java集合

java框架大类可以可以分为两大体系：

1. Collection，是单列集合
2. Map，是双列集合

## 1. Collection

collection包括两大体系：

1. List
2. Set

### 1.1List

#### ArrayList--非同步

1. 底层采用**数组**实现，查询速度快，增删速度慢
2. 该集合是可变长度数组，数组扩容时，会将老数组的元素重新拷贝到新的数组中去，每次数组容量增长大约是其容量的1.5倍，这种操作代价很高。
3. 采用Fail-Fast机制，面对并发的修改时，迭代器很快就会完全失败，而不是冒着在将来在某个不确定时间发生任意不确定行为的风险。
4. remover发明合法会让下标到数组末尾的元素向前移动一个单位，并把最后一位地值置空，方便GC

#### LinkedList--非同步

1. 实现双向链表非同步，并允许包括null在内的所有元素。
2. 底层数据结构是**基于双线链表**的，该数据结构称为节点。
3. 节点类为Node，成员变量有prev、next、item。
4. 其查找时，分两半查找，先判断index在链表的那一半，然后再去对应区域查找，这样最多只需要遍历链表的一半节点。
5. 使用LinkedList可以实现队列和栈，其提供了特殊的方法可以对头尾元素操作。
6. 查询速度慢，增删操作快。

#### Vector--同步

1. Vector已经过时，被ArrayList取代，他的底层实现与ArrayList很相似
2. Vector和他的子类Stack是线程安全的。

### 1.2Set

Set的特点：元素不重复，存取无序、无下标

#### HashSet--非同步

1. 底层是由**HashMap（哈希表）**实例实现的

2. 他不保证迭代顺序也不保证存储顺序

3. 他的API也是对于HashMap的行为进行了封装

4. 向HashSet集合存储自定义对象时，为了保证set集合的唯一性，必须重写equals方法和hashCode方法。原因在Map中介绍。

   ```java
   //HashSet源码分析
   //成员变量map为存储集合，PRESENT为map中的值对象
   private transient HashMap<E,Object> map;
   private static final Object PRESENT = new Object();
   //添加方法
   public boolean add(E e) {
           return map.put(e, PRESENT)==null;
   }
   ```

   

对于更详细的说明在下方的HashMap详解。

#### LinkedHashSet--非同步

1. 是基于链表和哈希表实现的
2. 存取有序，元素唯一
3. LinkedHashSet继承自HashSet又基于LinkedHashMap实现，他的底层使用的是LinkedHashMap来保存元素，继承与HashSet所有方法操作上由于HashSet相同。

#### TreeSet--非同步

特点：存取无序，元素唯一，可以进行排序（在添加时排序）

TreeSet是基于二叉树（红黑树）的数据结构

TreeSet保证元素唯一性的两种方式：

1. 自定义对象实现Conparable接口，从写comparaTo方法
2. 在创建TreeSet的时候向构造器传入比较器Comparator接口的实现类，接口中重写compara方法

如果TreeSet存入自定义对象是，自定义类没有实现Compararle接口或者没有传入Comparator比较器，会出现ClassCaseException

### Collection体系总结

- List：存取有序，元素有索引，元素可重复
  - ArrayList：数组结构，查询快，增删慢，线程不安全，效率高
  - Vector： 数组结构，查询快，增删慢，线程安全，效率低
  - LinkedList：链表结构，查询慢，增删快，线程不安全，效率高

- Set：存取无序，元素无索引，元素不可以重复
  - HashSet：底层是哈希表（HashMap），通过hashCode保证元素唯一
  - LinkedHashSet：存储有序，元素不可重复，底层是LinkedHashMap
  - TreeSet：底层是红黑树，存取无序，但可以排序：自然排序、比较器排序

## 2. Map

Map的特点：是一个双列集合，保存键值对，键保证唯一性，值可以重复，存取无无序，键不可重复。Map在存储的时候将键值传入Entry，然后存储Entry对象。

### 2.1HashMap**

- 底层是bucket数组+Node链表+红黑树（1.8），默认链表长度大于8时转为树，采用链地址法

<img src="C:\Users\15893\Pictures\Saved Pictures\5786888-12173e75fb90ffad.webp" style="zoom:60%;" />

```java
//链表节点
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
//红黑树节点
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
        TreeNode<K,V> parent;  // red-black tree links
        TreeNode<K,V> left;
        TreeNode<K,V> right;
        TreeNode<K,V> prev;    // needed to unlink next upon deletion
        boolean red;
        TreeNode(int hash, K key, V val, Node<K,V> next) {
            super(hash, key, val, next);
        }

        /**
         * Returns root of tree containing this node.
         */
        final TreeNode<K,V> root() {
            for (TreeNode<K,V> r = this, p;;) {
                if ((p = r.parent) == null)
                    return r;
                r = p;
            }
        }
```

- 变量

```java
/**
 * 默认初始容量16(必须是2的幂次方)
 */
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;

/**
 * 最大容量，2的30次方
 */
static final int MAXIMUM_CAPACITY = 1 << 30;

/**
 * 默认加载因子，用来计算threshold
 */
static final float DEFAULT_LOAD_FACTOR = 0.75f;

/**
 * 链表转成树的阈值，当桶中链表长度大于8时转成树 
   threshold = capacity * loadFactor
 */
static final int TREEIFY_THRESHOLD = 8;

/**
 * 进行resize操作时，若桶中数量少于6则从树转成链表
 */
static final int UNTREEIFY_THRESHOLD = 6;

/**
 * 桶中结构转化为红黑树对应的table的最小大小

 当需要将解决 hash 冲突的链表转变为红黑树时，
 需要判断下此时数组容量，
 若是由于数组容量太小（小于　MIN_TREEIFY_CAPACITY　）
 导致的 hash 冲突太多，则不进行链表转变为红黑树操作，
 转为利用　resize() 函数对　hashMap 扩容
 */
static final int MIN_TREEIFY_CAPACITY = 64;
/**
 保存Node<K,V>节点的数组
 该表在首次使用时初始化，并根据需要调整大小。 分配时，
 长度始终是2的幂。
 */
transient Node<K,V>[] table;

/**
 * 存放具体元素的集
 */
transient Set<Map.Entry<K,V>> entrySet;

/**
 * 记录 hashMap 当前存储的元素的数量
 */
transient int size;

/**
 * 每次更改map结构的计数器
 */
transient int modCount;

/**
 * 临界值 当实际大小(容量*填充因子)超过临界值时，会进行扩容
 */
int threshold;

/**
 * 负载因子：要调整大小的下一个大小值（容量*加载因子）。
 */
final float loadFactor;

```

Hash的默认初始容量为16，最大容量为2^30，默认加载（负载）因子为0.75。

-  对于 HashMap 来说，负载因子是一个很重要的参数，该参数反应了 HashMap 桶数组的使用情况。通过调节负载因子，可使 HashMap 时间和空间复杂度上有不同的表现。 
- 当我们调低负载因子时，HashMap 所能容纳的键值对数量变少。扩容时，重新将键值对存储新的桶数组里，键的键之间产生的碰撞会下降，链表长度变短。此时，HashMap 的增删改查等操作的效率将会变高，这里是典型的拿空间换时间。相反，如果增加负载因子（负载因子可以大于1），HashMap 所能容纳的键值对数量变多，空间利用率高，但碰撞率也高。这意味着链表长度变长，效率也随之降低，这种情况是拿时间换空间。至于负载因子怎么调节，这个看使用场景了。

- 添加和查找时判断相等

```java
if (e.hash == hash &&
    ((k = e.key) == key || (key != null && key.equals(k))))
    break;
p = e;

// 获取hash值
static final int hash(Object key) {
    int h;
    // 拿到key的hash值后与其五符号右移16位取与
    // 通过这种方式，让高位数据与低位数据进行异或，以此加大低位信息的随机性，变相的让高位数据参与到计算中。
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

由上面源码可以看出，在比较俩对象是否相等时，先判断hashCode，在使用equals方法，所以，我们要使用自定义对象时，**重写对象的Equals方法时，要重写hashCode方法**，

equals和hashCode关系为：

1. 如果两对象equals返回值相同，其hashCode方法返回值也相同
2. 如果两对象hashCode返回值相同，其equals返回值可能不相同，即对象不相等

因为在 HashMap 的链表结构中遍历判断的时候，特定情况下重写的 equals 方法比较对象是否相等的业务逻辑比较复杂，循环下来更是影响查找效率。所以这里把 hashcode 的判断放在前面，只要 hashcode 不相等就玩儿完，不用再去调用复杂的 equals 了。很多程度地提升 HashMap 的使用效率。

所以重写 hashcode 方法是为了让我们能够正常使用 HashMap 等集合类，因为 HashMap 判断对象是否相等既要比较 hashcode 又要使用 equals 比较。而这样的实现是为了提高 HashMap 的效率。

- 扩容机制

 在 HashMap 中，桶数组的长度均是2的幂，阈值大小为桶数组长度与负载因子的乘积。当 HashMap 中的键值对数量超过阈值时，进行扩容。 

 HashMap 按当前桶数组长度的2倍进行扩容，阈值也变为原来的2倍（如果计算过程中，阈值溢出归零，则按阈值公式重新计算）。扩容之后，要重新计算键值对的位置，并把它们移动到合适的位置上去。 

总结起来，一共有三种**扩容方式**：

1. 使用默认构造方法初始化HashMap。从前文可以知道HashMap在一开始初始化的时候会返回一个空的table，并且thershold为0。因此第一次扩容的容量为默认值`DEFAULT_INITIAL_CAPACITY`也就是16。同时`threshold = DEFAULT_INITIAL_CAPACITY * DEFAULT_LOAD_FACTOR = 12`。
2. 指定初始容量的构造方法初始化`HashMap`。那么从下面源码可以看到初始容量会等于`threshold`，接着`threshold = 当前的容量（threshold） * DEFAULT_LOAD_FACTOR`。
3. HashMap不是第一次扩容。如果`HashMap`已经扩容过的话，那么每次table的容量以及`threshold`量为原有的两倍。

**为什么要用红黑树，不用平衡二叉树？**

插入效率比平衡二叉树高，查询效率比普通二叉树高，所以选择性能折中的红黑树。

**为什么HashMap不直接采用红黑树结构？**

因为红黑树需要进行左旋。右旋操作，而单链表不需要

单链表：元素小于8个时查询成本高，新增成本低

红黑树：元素大于8个时查询成本低，新增成本高

### 2.2LinkedHashMap

- LinkedHashMap继承于HashMap，底层使用哈希表和双向链表来保存所有元素，并且它是非同步，允许使用null值和null键。
- 基本操作与父类HashMap相似，通过重写HashMap相关方法，重新定义了数组中保存的元素Entry，来实现自己的链接列表特性。该Entry除了保存当前对象的引用外，还保存了其上一个元素before和下一个元素after的引用，从而构成了双向链接列表。

### 2.3TreeMap

采用红黑数实现，给TreeMap集合中保存自定义对象，自定义对象作为TreeMap集合的key值。由于TreeMap底层使用的二叉树，其中存放进去的所有数据都需要排序，要排序，就要求对象具备比较功能。对象所属的类需要实现Comparable接口。或者给TreeMap集合传递一个Comparator接口对象。

### 2.4ConcurrentHashMap

- ConcurrentHashMap允许多个修改操作并发进行，其关键在于使用了锁分离技术。
- 它使用了多个锁来控制对hash表的不同段进行的修改，每个段其实就是一个小的hashtable，它们有自己的锁。只要多个并发发生在不同的段上，它们就可以并发进行。
- ConcurrentHashMap在底层将key-value当成一个整体进行处理，这个整体就是一个Entry对象。Hashtable底层采用一个Entry[]数组来保存所有的key-value对，当需要存储一个Entry对象时，会根据key的hash算法来决定其在数组中的存储位置，在根据equals方法决定其在该数组位置上的链表中的存储位置；当需要取出一个Entry时，也会根据key的hash算法找到其在数组中的存储位置，再根据equals方法从该位置上的链表中取出该Entry。
- 与HashMap不同的是，ConcurrentHashMap使用多个子Hash表，也就是段(Segment)
  ConcurrentHashMap完全允许多个读操作并发进行，**读操作并不需要加锁**。如果使用传统的技术，如HashMap中的实现，如果允许可以在hash链的中间添加或删除元素，读操作不加锁将得到不一致的数据。ConcurrentHashMap实现技术是保证HashEntry几乎是不可变的。

## 3Hashtable--同步

- Hashtable是基于哈希表的Map接口的同步实现，不允许使用null值和null键
- 底层使用数组实现，数组中每一项是个单链表，即数组和链表的结合体
- Hashtable在底层将key-value当成一个整体进行处理，这个整体就是一个Entry对象。
- Hashtable底层采用一个Entry[]数组来保存所有的key-value对，当需要存储一个Entry对象时，会根据key的hash算法来决定其在数组中的存储位置，在根据equals方法决定其在该数组位置上的链表中的存储位置；当需要取出一个Entry时，也会根据key的hash算法找到其在数组中的存储位置，再根据equals方法从该位置上的链表中取出该Entry。
- synchronized是针对整张Hash表的，即每次锁住整张表让线程独占

## 4.同步集合

java的Collections类提供了6个静态方法将集合转成同步版本

```java
synchronizedCollection(c: Collection): Collection
synchronizedList(list: List): List
synchronizedMap(m: Map): Map
synchronizedSet(s: Set): Set
synchronizedSortedMap(s: SortedMap): SortedMap
synchronizedSortedSet(s: SortedSet): SortedSet
```

