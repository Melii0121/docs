# Collection

## 数组和集合的区别：

1. 长度的区别  
    - 数组的长度固定
    - 集合的长度可变
2. 内容不容
    - 数组存储的是同一种类型的元素
    - 集合可以存储不同类型的元素(但是一般我们不这样干..)
3. 元素的数据类型
    - 数组可以存储基本数据类型,也可以存储引用类型
    - 集合只能存储引用类型(你存储的是简单的int，它会自动装箱成Integer)


---

## Collection的功能

把集合共性的内容不断往上提取,最终形成集合的继承体系---->Collection

### 包含的常用集合类

    List
        ArrayList
        Vector
        LinkedList
    Set
        HashSet
        TreeSet

### 方法汇总

    添加功能：
        add(E):boolean // 添加一个元素
        addAll(Collection<? extends E>):boolean // 添加一个集合的元素
    删除功能：
        clear():boolean // 移除所有的元素
        remove(Object):boolean // 移除一个元素
        removeAll(Collection<?>):boolean // 移除一个集合的元素
        removeIf(Predicate<? super E>):boolean // 删除所有该集合满足给定谓词的元素。
    判断功能：
        contains(Object):boolean // 判断集合是否包含该元素
        containsAll(Object):boolean // 判断集合是否包含指定的元素集合，只有包含所有的元素，才叫包含
        isEmpty():boolean // 判断集合是否为空
    获取功能：
        iterator():Iteratior<E> // 迭代器
        hashcode():int // 返回此集合的哈希码值
        stream():Stream<E> // 返回一个连续Stream与此集合作为其源。
        parallelStream():Strean<E> // 返回一个连续Stream与此集合作为其源。 这是允许的这个方法返回一个连续的数据流。
        spliterator():Spliterator<E> // 创建Spliterator在这个集合中的元素。 实现应记录由spliterator报道的特征值。
        toArray():Object[] // 返回包含此集合中的元素的数组。
        toArray(T[]):T[] // 返回包含此集合中的元素的阵列; 返回的数组的运行时类型是指定的数组的。
    长度功能：
        size():int // 元素的个数
    交集功能：
        retainAll(Collection<?>):boolean // 移除此collection中未包含在指定collection中的所有元素，集合A和集合B做交集，最终结果保存在集合A，返回值表示的是A是否发生过变化。
  
  

---

## 迭代器Iterator

Collection继承了Iterable，存在iterator()方法，返回的是Iterator。  
Iterator中存在三个方法：
- hasNext() 如果迭代器中存在更多元素则返回true
- next() 返回迭代中的下一个元素。
- remove() 移除此迭代器返回的最后一个元素


我们遍历集合（Collection）的元素都可以使用Iterator，他的具体实现是以内部类的方式实现的。

---


## List集合介绍

List集合的特点就是：有序，可重复。

Collection返回的是Iterator迭代器接口，而List中又有它自己对应的实现：  

    ListIterator接口：ListIterator可以往前遍历，添加元素，设置元素

#### List集合常用子类

- ArrayList: 底层数据结构是数组。线程不安全
- LinkedList: 底层数据结构是链表。线程不安全
- Vector: 底层数据结构是数组。线程安全

---

## Set集合介绍

Set集合的特点就是：元素不可重复

#### Set集合常用子类

- HashSet: 底层数据结构是哈希表(是一个元素为链表的数组)
- TreeSet: 底层数据结构是红黑树(是一个自平衡的二叉树)、保证元素的排序方式
- LinkedHashSet: 底层数据结构由哈希表和链表组成。


## Queue队列介绍
Queue可以作为消息队列来使用

- PriorityQueue: 