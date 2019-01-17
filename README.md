# WeakSoftHashMapTree
软引用，弱引用实现原理技术研究


<pre>
WeakHashMap 继承于AbstractMap，实现了Map接口。

    和HashMap一样，WeakHashMap 也是一个散列表，它存储的内容也是键值对(key-value)映射，
    而且键和值都可以是null。

    不过WeakHashMap的键是“弱键”。在 WeakHashMap 中，当某个键不再正常使用时，会被从
    WeakHashMap中被自动移除。更精确地说，对于一个给定的键，其映射的存在并不阻止垃圾回收器对
    该键的丢弃，这就使该键成为可终止的，被终止，然后被回收。某个键被终止时，它对应的键值对也
    就从映射中有效地移除了。

    这个“弱键”的原理呢？大致上就是，通过WeakReference和ReferenceQueue实现的。 
    WeakHashMap的key是“弱键”，即是WeakReference类型的；ReferenceQueue是一个队列，它会
    保存被GC回收的“弱键”。实现步骤是：

       1）新建WeakHashMap，将“键值对”添加到WeakHashMap中。 实际上，WeakHashMap是通过数
          组table保存Entry(键值对)；每一个Entry实际上是一个单向链表，即Entry是键值对链表。
       2）当某“弱键”不再被其它对象引用，并被GC回收时。在GC回收该“弱键”时，这个“弱键”也同时
          会被添加到ReferenceQueue(queue)队列中。
       3）当下一次我们需要操作WeakHashMap时，会先同步table和queue。table中保存了全部的
          键值对，而queue中保存被GC回收的键值对；同步它们，就是删除table中被GC回收的键值对。

     这就是“弱键”如何被自动从WeakHashMap中删除的步骤了。

     和HashMap一样，WeakHashMap是不同步的。可以使用 Collections.synchronizedMap 方法
     来构造同步的 WeakHashMap。

     要明白 WeekHashMap 的工作原理，还需要引入一个概念：弱引用（WeakReference）。我们都
     知道Java中内存是通过GC自动管理的，GC会在程序运行过程中自动判断哪些对象是可以被回收
     的，并在合适的时机进行内存释放。GC判断某个对象是否可被回收的依据是，是否有有效的引用指
     向该对象。如果没有有效引用指向该对象（基本意味着不存在访问该对象的方式），那么该对象就
     是可回收的。这里的“有效引用”并不包括弱引用。也就是说，虽然弱引用可以用来访问对象，但进行
     垃圾回收时弱引用并不会被考虑在内，仅有弱引用指向的对象仍然会被GC回收。

     WeakHashMap 内部是通过弱引用来管理entry的，弱引用的特性对应到 WeakHashMap 上意味着
     什么呢？将一对key, value放入到 WeakHashMap 里并不能避免该key值被GC回收，除非在 
     WeakHashMap 之外还有对该key的强引用。
</pre>
