# 集合

由两大接口衍生而来，一是`Collection`接口，主要用于存放单一元素；另一个是`Map`接口，主要用于存放键值对。

## List

### ArrayList & Vector

`ArrayList`是`List`的主要实现类，底层使用`Object[]`存储，适合频繁的查找任务，线程不安全；

`Vector`是`List`的古老实现类，底层使用`Object[]`存储，线程安全；

### ArrayList & LinkedList

- **是否保证线程安全**： `ArrayList`和`LinkedList`都是不同步的，无法保证线程安全。
- **底层数据结构：**`ArrayList`底层使用`Object[]`；`LinkedList`底层使用的是**双向链表**数据结构
- **插入删除：**`ArrayList`插入删除时间复杂度受元素位置影响。当元素直接插在末尾时，复杂度为O(1)，但在指定位置i插入删除，时间复杂度为O(n-i)。`LinkedList`在头尾插入是复杂度为O(1)，指定位置i插入复杂度为O(n)
- **快速随机访问：**`ArrayList`实现了`RandomAccess`接口，所以能实现随机访问。
- **内存空间占用：**`ArrayList`会在list的结尾预留一定的容量空间，`LinkedList`会使用更多的空间存放后继前驱和数据

## Set

### 无序性 & 不可重复性

- 无序性不等于随机性，指存储的数据在底层数组中并非按照数组索引的顺序添加，而是根据数据的哈希值决定的。
- 不可重复性指添加的元素按照`equal()`判断时，返回false。

###  HashSet, LinkedHashSet & TreeSet

- 都是`Set`接口的实现类，都能保证元素的唯一，都不是线程安全的
- `HashSet`的底层是`HashMap`。`LinkedList`的底层是链表和哈希表，元素的进出顺序满足FIFO。`TreeSet`的底层为红黑树，元素是有序的。

## Queue

`ArrayDequeue`和`LinkedList`都实现了`Deque`接口，两者都具有队列的功能

- `ArrayDeque` 不支持存储 `NULL` 数据，但 `LinkedList` 支持。
- `ArrayDeque` 插入时可能存在扩容过程, 不过均摊后的插入操作依然为 O(1)。虽然 `LinkedList` 不需要扩容，但是每次插入数据时均需要申请新的堆空间，均摊性能相比更慢。

从性能的角度上，选用 `ArrayDeque` 来实现队列要比 `LinkedList` 更好。此外，`ArrayDeque` 也可以用于实现栈。

## PriorityQueue

- 使用二叉堆的数据结构来实现，底层使用可变长的数组来存储数据
  - Heap property: the key of root node is smaller than the key of its left and right children
  - Balance property: the number of node of left and right sub-tree differ by at most two
- 通过堆元素的上浮和下沉，实现了在 O(logn) 的时间复杂度内插入元素和删除堆顶元素。
- 非线程安全的，且不支持存储 `NULL` 和 `non-comparable` 的对象。
- 默认是小顶堆，但可以接收一个 `Comparator` 作为构造参数，从而来自定义元素优先级的先后。

