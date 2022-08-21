# List

List中主要有以下3个常用的子类

- ArrayList 底层数据结构是数组。线程不安全
- LinkedList 底层数据结构是链表。线程不安全
- Vector 底层数据结构是数组。线程安全
 

## ArrayList

ArrayList是用的最频繁的集合

### 基本屬性
```java
/**
 * 初始化容量为10
 */
private static final int DEFAULT_CAPACITY = 10;

/**
 * 指定该ArrayList容量为0时， 返回该空数组
 */
private static final Object[] EMPTY_ELEMENTDATA = {};

/**
 * 与EMPTY_ELEMENTDATA的区别是，此属性是默认返回值
 */
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

/**
 * 保存添加到ArrayList中的元素，第一次添加元素时进行扩容，扩容值DEFAULT_CAPACITY
 */
transient Object[] elementData; // non-private to simplify nested class access

/**
 * ArrayList实际的大小
 */
private int size;
```

### 构造方法

```java
/**
 * 指定了容量之后，数组初始化成对应的容量
 */
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    }
}

/**
 * 未指定容量，返回默认值
 */
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```

### 检查角标是否越界
```
/**
 * 基本操作，有关下标的都会使用此方法
 */
private void rangeCheck(int index) {
    if (index >= size)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
```

### Add方法

```java
/**
 * 直接添加元素
 */
public boolean add(E e) {
    // 判断集合容量是否需要扩容 
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    // 添加元素，并增加长度
    elementData[size++] = e;
    return true;
}

/**
 * 插入到指定位置
 */
public void add(int index, E element) {
    // 检查角标是否越界
    rangeCheckForAdd(index);
    // 判断集合容量是否需要扩容 
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    // 调用array()进行插入
    System.arraycopy(elementData, index, elementData, index + 1,
                     size - index);
    // 将值放入指定位置
    elementData[index] = element;
    size++;
}
```

- 与扩容相关的ArrayList的add方法底层其实都是arraycopy()来实现的
- arraycopy()，此方法是由C/C++来编写的，并非java实现。

###  remove方法
```java
/**
 * 根据下标删除元素
 */
public E remove(int index) {
    // 检查角标是否越界
    rangeCheck(index);
    // 修改次数+1
    modCount++;
    // 得到要删除的元素
    E oldValue = elementData(index);
    // 要移动的数据的长度
    int numMoved = size - index - 1;
    // 即删除的内容不是结尾的内容
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    // 移动之后最后的那个数据就是多余的了 （它已经复制到了前一个位置）
    elementData[--size] = null; // clear to let GC do its work
    // 输出删除的数据
    return oldValue;
}


/**
 * 根据元素内容删除
 */
public boolean remove(Object o) {
    // 输入信息是否为空
    // 删除所匹配的第一个数据后结束。
    if (o == null) {
        for (int index = 0; index < size; index++) {
            if (elementData[index] == null) {
                fastRemove(index);
                return true;
            }
        }
    } else {
        for (int index = 0; index < size; index++)
            if (o.equals(elementData[index])) {
                fastRemove(index);
                return true;
            }
    }
    return false;
}
```

### get方法
```java
/**
 * 根据下标获取元素
 */
public E get(int index) {
    // 检查角标是否越界
    rangeCheck(index);
    // 输出指定下标的数据
    return elementData(index);
}

E elementData(int index) {
    return (E) elementData[index];
}
```

### set方法
```java
/** 
 * 修改指定下标的数据
 */
public E set(int index, E element) {
    // 检查角标是否越界
    rangeCheck(index);
    // 获取指定下标上的值
    E oldValue = elementData(index);
    // 更新元素值
    elementData[index] = element;
    // 返回旧值
    return oldValue;
}
```

### 其他细节

- ArrayList是基于动态数组实现的，在增删时候，需要数组的拷贝复制。
- ArrayList的默认初始化容量是10，每次扩容时候增加原先容量的一半，也就是变为原来的1.5倍
- ArrayList不是线程安全的。它能存放null值。
- 删除元素时不会减少容量，若希望减少容量则调用trimToSize()
```java
public void trimToSize() {
    modCount++;
    if (size < elementData.length) {
        elementData = (size == 0)
          ? EMPTY_ELEMENTDATA
          : Arrays.copyOf(elementData, size);
    }
}
```

## Vector

Vector底层也是数组，与ArrayList最大的区别就是：同步(线程安全)

- 在要求非同步的情况下，我们一般都是使用ArrayList来替代Vector的了
- 如果想要ArrayList实现同步，可以使用Collections的方法：List list = Collections.synchronizedList(new ArrayList(...));，就可以实现同步了
- ArrayList在底层数组不够用时在原来的基础上扩展0.5倍，Vector是扩展1倍。


## LinkedList

- LinkedList底层是双向链表实现的，即如下结构
```java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```
- 实现了Deque接口，因此，操作LinkedList像操作队列和栈一样


### 基本属性
```java
// 链表长度
transient int size = 0;
// 顶部元素
transient Node<E> first;
// 尾部元素
transient Node<E> last;
```

### 构造方法
```java
public LinkedList() {}

public LinkedList(Collection<? extends E> c) {
    this();
    addAll(c);
}
```

### add方法
```java
// 添加元素 （到末尾）
public boolean add(E e) {
    linkLast(e);
    return true;
}

// 添加元素到指定位置
public void add(int index, E element) {
    // 验证角标是否越界
    checkPositionIndex(index);
    if (index == size)
        linkLast(element);
    else
        linkBefore(element, node(index));
}

// 添加元素到头部
public void addFirst(E e) {
    linkFirst(e);
}

// 添加元素到尾部
public void addLast(E e) {
    linkLast(e);
}

// 头部添加
private void linkFirst(E e) {
    // 得到当前头部元素
    final Node<E> f = first;
    // 新的节点
    final Node<E> newNode = new Node<>(null, e, f);
    // 修改头部元素
    first = newNode;
    // 若当前链表为空 则newNode也是尾部
    // 若不为空 则将newNode置于当前头部的prev
    if (f == null)
        last = newNode;
    else
        f.prev = newNode;
    // 长度增加
    size++;
    // 修改次数增加
    modCount++;
}

// 尾部添加
void linkLast(E e) {
    // 得到当前尾部元素
    final Node<E> l = last;
    // 新的节点
    final Node<E> newNode = new Node<>(l, e, null);
    // 修改尾部元素
    last = newNode;
    // 若当前链表为空 则newNode也是头部
    // 若不为空 则将newNode置于当前尾部的next
    if (l == null)
        first = newNode;
    else
        l.next = newNode;
    // 长度增加
    size++;
    // 修改次数增加
    modCount++;
}

// 指定位置添加
void linkBefore(E e, Node<E> succ) {
    // assert succ != null;
    // 得到指定位置的前一位
    final Node<E> pred = succ.prev;
    // 创建新的节点，并设置前后节点
    final Node<E> newNode = new Node<>(pred, e, succ);
    // 原index节点设置prev节点为新的node
    succ.prev = newNode;
    // 如插入的是头部，则更新头部元素
    // 若不是头部，则建立原index-1位置上元素与newNode之间的连接
    if (pred == null)
        first = newNode;
    else
        pred.next = newNode;
    // 长度增加
    size++;
    // 修改次数增加
    modCount++;
}

// 根据下标获取Node节点
Node<E> node(int index) {
    // size >> 1 相当于 size / 2
    // 即判断从后向前，还是从前向后
    if (index < (size >> 1)) {
        Node<E> x = first;
        for (int i = 0; i < index; i++)
            x = x.next;
        return x;
    } else {
        Node<E> x = last;
        for (int i = size - 1; i > index; i--)
            x = x.prev;
        return x;
    }
}

```
### remove方法

```java
// 通过下标删除
public E remove(int index) {
    // 检查角标是否越界
    checkElementIndex(index);
    // 断开从节点的连接
    return unlink(node(index));
}

// 通过元素删除
public boolean remove(Object o) {
    // 判断是否为null， 一个使用==判断， 一个使用equals验证
    if (o == null) {
        for (Node<E> x = first; x != null; x = x.next) {
            if (x.item == null) {
                unlink(x);
                return true;
            }
        }
    } else {
        for (Node<E> x = first; x != null; x = x.next) {
            if (o.equals(x.item)) {
                unlink(x);
                return true;
            }
        }
    }
    return false;
}

// 断开节点x的连接
E unlink(Node<E> x) {
    // assert x != null;
    // x 的值
    final E element = x.item;
    // 后一位
    final Node<E> next = x.next;
    // 前一位
    final Node<E> prev = x.prev;

    // 前一位为空，则first为x节点的下一个节点
    // 前一位不为空 则x节点的前一位的next引用应为x节点的后一位, 并且移除x节点的prev引用
    if (prev == null) {
        first = next;
    } else {
        prev.next = next;
        x.prev = null;
    }

    // 后一位为空，则last为x节点的前一个节点
    // 后一位不为空 则x节点的后后一位的prev引用应为x节点的前一位, 并且移除x节点的prev引用
    if (next == null) {
        last = prev;
    } else {
        next.prev = prev;
        x.next = null;
    }
    // x节点数据删除
    x.item = null;
    // 长队减一
    size--;
    // 操作次数加1
    modCount++;
    // 输出删除节点的值
    return element;
}
```

### get方法
```
// 通过下标获取元素
public E get(int index) {
    // 检查角标越界
    checkElementIndex(index);
    // node方法可看上文
    return node(index).item;
}
```


### set方法
```java
public E set(int index, E element) {
    // 检查角标越界
    checkElementIndex(index);
    // 得到指定节点
    Node<E> x = node(index);
    // 获取节点旧数据
    E oldVal = x.item;
    // 更新数据
    x.item = element;
    // 返回旧数据
    return oldVal;
}
```