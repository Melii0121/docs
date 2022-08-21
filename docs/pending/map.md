# 常用API

```java
// 获取指定键的值
V get(Object key);
// 添加键值对
V put(K key, V value);
// 将指定map中的键值对复制到此map中
void putAll(Map<? extends K, ? extends V> m);
// 删除指定键的键值对
V remove(Object key);

// 判断是否存在该键的键值对
boolean containsKey(Object key);
// 判断是否存在该值的键值对
boolean containsValue(Object value);

// 单独抽取key序列，将所有的key生成一个Set
Set<K> keySet();
// 单独抽取value序列，将所有的value生成一个Collection
Collection<V> values();
// 获得key-value对（Entry）的Set集合
Set<Map.Entry<K, V>> entrySet();

// 清除哈希表中的所有键值对
void clear();
// 返回哈希表中的所有键值对的数量 = 数组中的键值对 + 链表中的键值对
int size();
// 判断HashMap是否为空，seize == 0 时表示为空
boolean isEmpty();
```

**循环案例**



```java
public static void main(String[] args) {
    Map<String, String> map = new HashMap<>();

    map.put("1", "壹");
    map.put("2", "贰");
    map.put("3", "叁");
    map.put("4", "肆");
    map.put("5", "伍");

    forEachForEntry(map);
    forEachForKey(map);
    forEachForValues(map);
}

/**
 * entrySet 方法进行循环
 * @param map
 */
public static void forEachForEntry(Map<String, String> map) {
    Set<Map.Entry<String, String>> entrySet = map.entrySet();
    System.out.println("----------------entrySet(forEach)---------------");
    entrySet.stream().forEach(a -> {
        System.out.print(a.getKey() + ":" + a.getValue() + ",");
    });
    System.out.println();

    System.out.println("----------------entrySet(Iterator)---------------");
    Iterator<Map.Entry<String, String>> setiterator = entrySet.iterator();
    while (setiterator.hasNext()) {
        Map.Entry<String, String> entry = setiterator.next();
        System.out.print(entry.getKey() + ":" + entry.getValue() + ",");
    }
    System.out.println();
}

/**
 * keySet 方法进行循环
 * @param map
 */
public static void forEachForKey(Map<String, String> map) {
    Set<String> sets = map.keySet();
    System.out.println("----------------keySet(forEach)---------------");
    sets.stream().forEach(a -> {
        System.out.print(a + ":" + map.get(a) + ",");
    });
    System.out.println();

    System.out.println("----------------keySet(Iterator)---------------");
    Iterator<String> setiterator = sets.iterator();
    while (setiterator.hasNext()) {
        String key = setiterator.next();
        System.out.print(key + ":" + map.get(key) + ",");
    }
    System.out.println();
}

/**
 * values 方法进行循环
 * @param map
 */
public static void forEachForValues(Map<String, String> map) {
    Collection<String> values = map.values();
    System.out.println("----------------values(forEach)---------------");
    values.stream().forEach(a -> {
        System.out.print(a + ",");
    });
    System.out.println();

    System.out.println("----------------values(Iterator)---------------");
    Iterator valueIterator = values.iterator();
    while (valueIterator.hasNext()) {
        System.out.print(valueIterator.next() + ",");
    }
    System.out.println();
}
```

# 数据结构

HashMap 是散列表的一种，HashMap本身采用数组来进行存储。同时HashMap采用拉链法来解决hash冲突。所以从整体上看，HashMap采用的数据结构为：数组（主）+ 单链表（副）

```
竖向数组
0   0-1 0-2 0-3   横向链表
1   1-1
2   2-1
3   3-1
4   4-1
```

- 每个链表就算哈希表的桶（bucket）
- 链表的节点值就是一个键值对


# 重要参数介绍

```
// 默认的创建初始容量（HashMap中数组[强调是数组，不是元素个数]的长度）
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

```

# 数组-Entry
# put()
# get()
# 扩容
# 扩容出现的死循环链表