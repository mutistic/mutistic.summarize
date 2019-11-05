# 二 Java容器
### 2-1 容器概览：
容器主要包括 Collection 和 Map 两种，Collection 存储着对象的集合，而 Map 存储着键值对（两个对象）的映射表。
![抽象容器类与集合和具体容器类之间的对应关系如图所示](http://static.zybuluo.com/ZzzJoe/ckk77jz8n66lxx64rkknpvn8/QQ%E6%88%AA%E5%9B%BE20180317171302.png)

其中虚线表示接口，包含Collection，List，Set，Queue，Deque和Map。
同时包含六个抽象容器类：
* AbstractCollection： 实现了Collection接口，被抽象类AbstractList、AbstractSet、AbstractQueue继承，ArrayDeque也继承自AbstractCollection。
* AbstractList： 父类是AbstractCollection，实现了List接口，被ArrayList、AbstractSequentialList继承。
* AbstractSequentialList： 父类是AbstractList，被LinkedList继承。
* AbstractMap： 实现了Map接口，被TreeMap、HashMap、EnumMap继承。
* AbstractSet： 父类是AbstractCollection，实现了Set接口，被HashMap
* AbstractQueue： 父类是AbstractCollection，实现了Queue接口，被PriorityQueue继承。

### 2-2 Collection：
![]()  
**2.2.1、Set**  
* TreeSet：基于红黑树实现，支持有序性操作，例如根据一个范围查找元素的操作。但是查找效率不如
HashSet，HashSet 查找的时间复杂度为 O(1)，TreeSet 则为 O(logN)。
* HashSet：基于哈希表实现，支持快速查找，但不支持有序性操作。并且失去了元素的插入顺序信息，也就是说
使用 Iterator 遍历 HashSet 得到的结果是不确定的。
* LinkedHashSet：具有 HashSet 的查找效率，且内部使用双向链表维护元素的插入顺序

**2.2.2、List**  
* ArrayList：基于动态数组实现，支持随机访问。
* Vector：和 ArrayList 类似，但它是线程安全的。
LinkedList：基于双向链表实现，只能顺序访问，但是可以快速地在链表中间插入和删除元素。不仅如此，
* LinkedList 还可以用作栈、队列和双向队列。
**2.2.3、Queue**  
* LinkedList：可以用它来实现双向队列。
* PriorityQueue：基于堆结构实现，可以用它来实现优先队列。

### 2-3 Map
![]()  
* TreeMap：基于红黑树实现。
* HashMap：基于哈希表实现。
* HashTable：和 HashMap 类似，但它是线程安全的，这意味着同一时刻多个线程可以同时写入 HashTable 并且不会导致数据不一致。它是遗留类，不应该去使用它。现在可以使用 ConcurrentHashMap 来支持线程安
全，并且 ConcurrentHashMap 的效率会更高，因为 ConcurrentHashMap 引入了分段锁。
* LinkedHashMap：使用双向链表来维护元素的顺序，顺序为插入顺序或者最近最少使用（LRU）顺序。
* ConcurrentHashMap：使用了分段锁作为一个线程安全且高效的HashMap实现

### 2-4 容器中的设计模式
**2.4.1、迭代器模式：**  
![]()  
Collection 继承了 Iterable 接口，其中的 iterator() 方法能够产生一个 Iterator 对象，通过这个对象就可以迭代遍历
Collection 中的元素。  
从 JDK 1.5 之后可以使用 foreach 方法来遍历实现了 Iterable 接口的聚合对象。

**2.4.2、适配器模式：**  
![]()  
java.util.Arrays#asList() 可以把数组类型转换为 List 类型。
```Java
@SafeVarargs
public static <T> List<T> asList(T... a)

// 应该注意的是 asList() 的参数为泛型的变长参数，不能使用基本类型数组作为参数，只能使用相应的包装类型数组。
Integer[] arr = {1, 2, 3};
List list = Arrays.asList(arr);
// 也可以使用以下方式调用 asList()：
List list = Arrays.asList(1, 2, 3);
```

### 2-5 源码分析
如果没有特别说明，以下源码分析基于 JDK 1.8。
**2.5.1、Arraylist**  
* 概览：  

因为 ArrayList 是基于数组实现的，所以支持快速随机访问。RandomAccess 接口标识着该类支持快速随机访问。
```Java
public class ArrayList<E> extends AbstractList<E>
  implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```
* 数组的默认大小为 10：  
```
//  Default initial capacity.
private static final int DEFAULT_CAPACITY = 10;
transient Object[] elementData;
```
* 初始化机制：  
```Java
private static final Object[] EMPTY_ELEMENTDATA = {};
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

// 自定义初始化容量
public ArrayList(int initialCapacity) {
  if (initialCapacity > 0) {
    this.elementData = new Object[initialCapacity];
  } else if (initialCapacity == 0) {
    this.elementData = EMPTY_ELEMENTDATA;
  } else {
    throw new IllegalArgumentException("Illegal Capacity: "+ initialCapacity);
  }
}
// 默认初始化方法
public ArrayList() {
  this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```
* 扩容机制：

添加元素时使用 ensureCapacityInternal() 方法来保证容量足够，
如果不够时，需要使用 grow() 方法进行扩容，新容量的大小为`oldCapacity + (oldCapacity >> 1)` ，也就是旧容量的 1.5 倍。  
扩容操作需要调用  Arrays.copyOf()把原数组整个复制到新数组中，这个操作代价很高，
因此最好在创建ArrayList 对象时就指定大概的容量大小，减少扩容操作的次数。
```Java
// 触发扩容动作
public boolean add(E e) {
  ensureCapacityInternal(size + 1);  // Increments modCount!!
  elementData[size++] = e;
  return true;
}
// 扩容方法
private void ensureCapacityInternal(int minCapacity) {
// 如果使用的是默认构造函数，则默认容量为 DEFAULT_CAPACITY和minCapacity中的较大值
  if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
    minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
  }
  ensureExplicitCapacity(minCapacity);
}
// 确保是否能扩容
private void ensureExplicitCapacity(int minCapacity) {
  modCount++;
  // overflow-conscious code
  if (minCapacity - elementData.length > 0)
    grow(minCapacity);
}
// 具体扩容实现 
private void grow(int minCapacity) {
  // overflow-conscious code
  int oldCapacity = elementData.length;
  // 默认扩容1.5倍
  int newCapacity = oldCapacity + (oldCapacity >> 1);
  if (newCapacity - minCapacity < 0)
    newCapacity = minCapacity;
  if (newCapacity - MAX_ARRAY_SIZE > 0)
    newCapacity = hugeCapacity(minCapacity);
  // minCapacity is usually close to size, so this is a win:
  // 数组拷贝动作
  elementData = Arrays.copyOf(elementData, newCapacity);
}
```
* 删除元素

需要调用 System.arraycopy() 将 index+1 后面的元素都复制到 index 位置上，该操作的时间复杂度为 O(N)，可以看出ArrayList 删除元素的代价是非常高的。
```Java
public E remove(int index) {
  rangeCheck(index);
  checkForComodification();
  E result = parent.remove(parentOffset + index);
  this.modCount = parent.modCount;
  this.size--;
  return result;
}
```
* Fail-Fast：快速失败策略
modCount 用来记录 ArrayList 结构发生变化的次数。结构发生变化是指添加或者删除至少一个元素的所有操作，或者是调整内部数组的大小，仅仅只是设置元素的值不算结构发生变化。  
在进行序列化或者迭代等操作时，需要比较操作前后 modCount 是否改变，如果改变了需要抛出`ConcurrentModificationException`。
```Java
private void writeObject(java.io.ObjectOutputStream s) throws java.io.IOException{
  // Write out element count, and any hidden stuff
  int expectedModCount = modCount;
  s.defaultWriteObject();

  // Write out size as capacity for behavioural compatibility with clone()
  s.writeInt(size);

  // Write out all elements in the proper order.
  for (int i=0; i<size; i++) {
    s.writeObject(elementData[i]);
  }

  if (modCount != expectedModCount) {
    throw new ConcurrentModificationException();
  }
}
```
* 序列化：

ArrayList 基于数组实现，并且具有动态扩容特性，因此保存元素的数组不一定都会被使用，那么就没必要全部进行序列化。  
保存元素的数组 elementData 使用 transient修饰，该关键字声明数组默认不会被序列化。
```Java
transient Object[] elementData; // non-private to simplify nested class access
```
ArrayList 实现了 writeObject() 和 readObject() 来控制只序列化数组中有元素填充那部分内容。
```Java
private void readObject(java.io.ObjectInputStream s)
  throws java.io.IOException, ClassNotFoundException {
  elementData = EMPTY_ELEMENTDATA;

  // Read in size, and any hidden stuff
  s.defaultReadObject();

  // Read in capacity
  s.readInt(); // ignored

  if (size > 0) {
    // be like clone(), allocate array based upon size not capacity
    ensureCapacityInternal(size);

    Object[] a = elementData;
    // Read in all elements in the proper order.
    for (int i=0; i<size; i++) {
      a[i] = s.readObject();
    }
  }
}

private void writeObject(java.io.ObjectOutputStream s) throws java.io.IOException{
  // ...
}
```
序列化时需要使用 ObjectOutputStream 的 writeObject() 将对象转换为字节流并输出。而 writeObject() 方法在传
入的对象存在 writeObject() 的时候会去反射调用该对象的 writeObject() 来实现序列化。反序列化使用的是
ObjectInputStream 的 readObject() 方法，原理类似。
```Java
ArrayList list = new ArrayList();
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(file));
oos.writeObject(list);
```