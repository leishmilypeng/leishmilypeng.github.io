JDK定义多种集合，是为了通过不同的方式来操作集合对象

1. Collection

2. 1. List
   2. Set         不会包含重复元素的列表，对象类型按照hash值判断是否重复
   3. Queue   队列
   4. Deque    双端队列
   5. Stack

3. Map



------

List

代表一个有序、可重复的元素集合，允许加入重复元素

​    **ArrayList** 

​    基于数组实现的List类，内部通过动态的数组实现



**private transient** Object[] **elementData**;

*/***

** Constructs an empty list with an initial capacity of ten.*

​    *构造函数组，默认的初始容量是10*

**/*

**public** ArrayList() {

**this**(10);

}

注意：数组方式存储列表，有各自的索引，方便快速定位元素，但是不适合做remove操作



​    **LinkedList**

​    也实现了Deque接口，可以通过队列的方式进行操作，可以当双端队列进行使用和操作，可以通过索引的方式进行元素获取与操作。



Queue

模拟队列都数据结构，进行先进先出，新插入的元素都放到队尾，队列不允许随机访问队列中的元素

**ConcurrentLinkedQueue**







Set

放进该集合里面的元素没有明显的顺序，不能包含重复元素

**HashSet**

**private transient** HashMap<E,Object> **map**;

通过HashMap来存储元素，索引不能添加重复元素

TreeSet





Map

HashMap

TreeMap

**HashTable:线程安全**

```java
// 线程安全类
public synchronized V get(Object key) {
    Entry tab[] = table;
    int hash = hash(key);
    int index = (hash & 0x7FFFFFFF) % tab.length;
    for (Entry<K,V> e = tab[index] ; e != null ; e = e.next) {
        if ((e.hash == hash) && e.key.equals(key)) {
            return e.value;
        }
    }
    return null;
}
HashTable 属于实例锁，针对方法进行加锁
```

**ConcurrentHashMap**

**Map.Entry:**

```java
public class MapEntityTest {
    public static void main(String[] args) {
        Map<String,String> map =new HashMap<>();
        //返回的是集合对象，对象包括key和value
        Set<Map.Entry<String, String>> sets = map.entrySet();
        Iterator<Map.Entry<String, String>> its = sets.iterator();
        while(its.hasNext()){
            Map.Entry entry = its.next();
            //获取key
            entry.getKey();
            //获取value
            entry.getValue();
        }
        // 传统方式
        Set<String> keys = map.keySet();
        for(String k:keys){
            String key  = k;
            String value = map.get(k);
        }
    }
}
```

TreeMap和TreeSet都是经过排序的集合





------

同步集合 和 并发集合



直到今天，没有根本的搞清楚什么是同步，什么是并发？ 之前一直就把这两个混为一谈

扩展了解下并发与同步



并发，英文单词为concurrent

同步，英文单词为synchronize



拿过桥举例

两岸通过唯一的桥连接在一起，上桥处连接多条道路，下桥处也连接多条道路

上桥处的多条道路同时来了很多人，这就叫并发

然后能够上桥的人按照先来后到的顺序过桥，按照时间刻度分到很小时，是不存在同时，可以分一个先后。这就叫同步



并发和同步都是线程安全的

Collections.synchronizedCollection

Collections.synchronizedMap

Collections.synchronizedSortedMap

Collections.synchronizedSet

Collections.synchronizedSortedSet

Collections.synchronizedList

```java
@ThreadSafe
class GoodListHelper <E> {
    public List<E> list = Collections.synchronizedList(new ArrayList<E>());
    public boolean putIfAbsent(E x) {
        synchronized (list) {  //获得list锁对象，其他线程将不能获得list锁来来改变list对象。
            boolean absent = !list.contains(x);
            if (absent)
                list.add(x);
            return absent;
        }
    }
}

返回指定列表支持的同步（线程安全的）列表。为了保证按顺序访问，必须通过返回的列表完成所有对底层实现列表的访问。
在返回的列表上进行迭代时，用户必须手工在返回的列表上进行同步：
不遵从此建议将导致无法确定的行为。
-- 理解一下这句话的意思
通过同步方法获取到的列表，可以采用线程同步synchronized的方式来对底层元素进行访问，不然就无法进行同步，只有采用线程安全类
```

