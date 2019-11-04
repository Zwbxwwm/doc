# 数据结构

## B树、B+树、B*树
参考文章：[B-树，B+树，B*树详解](https://blog.csdn.net/aqzwss/article/details/53074186)

    B-树：
    多路搜索树，每个结点存储M/2到M个关键字，非叶子结点存储指向关键字范围的子结点；所有关键字在整颗树中出现，且只出现一次，非叶子结点可以命中；
    
    B+树：
    在B-树基础上，为叶子结点增加链表指针，所有关键字都在叶子结点中出现，非叶子结点作为叶子结点的索引；B+树总是到叶子结点才命中；
    
    B*树：
    在B+树基础上，为非叶子结点也增加链表指针，将结点的最低利用率从1/2提高到2/3。

##  1、hashMap的数据结构是什么，是如何实现的，和hashTable，concurrentHashMap有什么区别

​		hashMap是由数组+链表的结构来实现的，是一个成员为链表的数组，数组中的每个位置上都储存着一条用于处理哈希碰撞的链表，实现的原理是每次put操作进来的数据都会进行一次hash操作得出hash code，通过哈希得出的hash code来决定put进来的这个entry应该放在数组的哪个位置，如果放入的数组位置已经存在了entry这时候就会以链表的形式维护下去。

​		与hashMap相比，hashTable和concurrentHashMap增加了线程安全的操作，还有就是性能上的优化，hashTable和concurrentHashMap的区别在于hashTable采用的线程安全措施比较单一并且是重量级的锁，hashTable的初始容量是11，扩容为2n+1，concurrentHashMap的初始化容量是16，扩容为2n;hashTable采用的是synchronize关键字这样的效率会非常地低下。

​		concurrentHashMap在jdk1.7和jdk1.8的区别：在jdk1.7中，concurrentHashMap的并发措施采用的是分段锁的形式实现的，即对put和get的操作不会锁住整个数组而是只对那一个位置的链表进行锁定，只有在获取全局变量的时候才会锁住整个数组的变量，这样的好处在于降低锁的粒度，提高整个map的并发性能。jdk1.8的时候就放弃了传统的synchronize锁的形式，使用CAS机制+红黑树的形式对concurrentHashMap整体的性能进行了提升，这里的红黑树的操作只有在链表的长度超过8的时候就会将链表维护成红黑树的形式，这样在链表的长度过长的时候就造成查找的性能降低，所以自动变换成红黑树的形式提高查找性能。

------
