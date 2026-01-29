## 概念

### 数组与集合区别，用过哪些？

区别：

- 长度：数组是固定长度的，集合是长度是可变的
- 数据类型：数组包含基本数据类型和对象，集合只能包含对象
- 访问：数组可以直接访问，集合需要通过迭代器或者其他方法访问

1. **ArrayList：** 动态数组，实现了List接口，支持动态增长。
2. **LinkedList：** 双向链表，也实现了List接口，支持快速的插入和删除操作。
3. **HashMap：** 基于哈希表的Map实现，存储键值对，通过键快速查找值。
4. **HashSet：** 基于HashMap实现的Set集合，用于存储唯一元素。
5. **TreeMap：** 基于红黑树实现的有序Map集合，可以按照键的顺序进行排序。
6. **LinkedHashMap：** 基于哈希表和双向链表实现的Map集合，保持插入顺序或访问顺序。
7. **PriorityQueue：** 优先队列，可以按照比较器或元素的自然顺序进行排序。
8. **ArrayDeque：** 基于可变长度的数组（循环数组）实现。它在内部维护一个连续的数组空间，并通过头尾指针来管理元素的入队和出队操作。

### 说说Java中的集合？

![img](https://cdn.xiaolincoding.com//picgo/1717481094793-b8ffe6ae-2ee6-4de5-b61b-8468e32bf269.webp)

**List：（有序、可重复）**

- ArrayList：动态数组，随机访问快，中间删除慢，**最常用**，适用于读多写少、按索引查询
- LinkedList：双向链表，头尾增删快，随机访问慢，适用于频繁在首尾增删，或需要实现栈、队列
- Vector：线程安全的动态数组，性能低，遗留类，已被 `CopyOnWriteArrayList` 或 `Collections.synchronizedList` 替代，不推荐使用
- Stack：继承自Vector，推荐使用Deque接口的实现

**Set：（无序、唯一）**

- HashSet：基于HashMap，无序，唯一，查找快，适用于快速去重，不关心顺序
- TreeSet：基于TreeMap，元素自然排序或自定义排序，适用于需要自动排序的去重集合
- LinkedHashSet：基于LinkedHashMap，适用于需要去重，又需要保持插入顺序

**Queue：**

- ArrayDeque：基于可变循环数组的双端队列，头尾高效增删，适用于队列、栈等
- PriorityQueue：基于**二叉堆（通常是最小堆）** 实现的优先级队列。元素按自然顺序或自定义比较器的顺序出队，每次操作 (`offer`, `poll`) 时间复杂度为 `O(log n)`。适用于需要**按优先级处理任务**的场景。

**Map：（键值对）**

- HashMap：数组+链表/红黑树，无序键值对，访问快，**最常用**，适用于键值对存储、缓存
- TreeMap：基于红黑树，键自然排序或自定义排序，适用于按键排序的键值对集合
- LinkedHashMap：在HashMap的基础上增加双向链表，适用于需要保持键的插入顺序或访问顺序
- HashTable：线程安全的键值对集合，遗留类，被`ConcurrentHashMap` 替代

### Java中的线程安全的集合是什么？

在 java.util 包中的线程安全的类主要 2 个：

- **Vector**：线程安全的动态数组，其内部方法基本都经过synchronized修饰，如果不需要线程安全，并不建议选择，毕竟同步是有额外开销的。Vector 内部是使用对象数组来保存数据，可以根据需要自动的增加容量，当数组已满时，会创建新的数组，并拷贝原有数组数据。
- **Hashtable**：线程安全的哈希表，HashTable 的加锁方法是给每个方法加上 synchronized 关键字，这样锁住的是整个 Table 对象，不支持 null 键和值，由于同步导致的性能开销，所以已经很少被推荐使用，如果要保证线程安全的哈希表，可以用ConcurrentHashMap。

java.util.concurrent 包提供的都是线程安全的集合：

并发Map：

- **ConcurrentHashMap**：它与 HashTable 的主要区别是二者加锁粒度的不同，在**JDK1.7**，ConcurrentHashMap加的是分段锁，也就是Segment锁，每个Segment 含有整个 table 的一部分，这样不同分段之间的并发操作就互不影响。在**JDK 1.8** ，它取消了Segment字段，直接在table元素上加锁，实现对每一行进行加锁，进一步减小了并发冲突的概率。对于put操作，如果Key对应的数组元素为null，则通过CAS操作（Compare and Swap）将其设置为当前值。如果Key对应的数组元素（也即链表表头或者树的根元素）不为null，则对该元素使用 synchronized 关键字申请锁，然后进行操作。如果该 put 操作使得当前链表长度超过一定阈值，则将该链表转换为红黑树，从而提高寻址效率。
- **ConcurrentSkipListMap**：实现了一个基于SkipList（跳表）算法的可排序的并发集合，SkipList是一种可以在对数预期时间内完成搜索、插入、删除等操作的数据结构，通过维护多个指向其他元素的“跳跃”链接来实现高效查找。

并发Set：

- **ConcurrentSkipListSet**：是线程安全的有序的集合。底层是使用ConcurrentSkipListMap实现。
- **CopyOnWriteArraySet**：是线程安全的Set实现，它是线程安全的无序的集合，可以将它理解成线程安全的HashSet。有意思的是，CopyOnWriteArraySet和HashSet虽然都继承于共同的父类AbstractSet；但是，HashSet是通过“散列表”实现的，而CopyOnWriteArraySet则是通过“动态数组(CopyOnWriteArrayList)”实现的，并不是散列表。

并发List：

- **CopyOnWriteArrayList**：它是 ArrayList 的线程安全的变体，其中所有写操作（add，set等）都通过对底层数组进行全新复制来实现，允许存储 null 元素。即当对象进行写操作时，使用了Lock锁做同步处理，内部拷贝了原数组，并在新数组上进行添加操作，最后将新数组替换掉旧数组；若进行的读操作，则直接返回结果，操作过程中不需要进行同步。

并发 Queue：

- **ConcurrentLinkedQueue**：是一个适用于高并发场景下的队列，它通过无锁的方式(CAS)，实现了高并发状态下的高性能。通常，ConcurrentLinkedQueue 的性能要好于 BlockingQueue 。
- **BlockingQueue**：与 ConcurrentLinkedQueue 的使用场景不同，BlockingQueue 的主要功能并不是在于提升高并发时的队列性能，而在于简化多线程间的数据共享。BlockingQueue 提供一种读写阻塞等待的机制，即如果消费者速度较快，则 BlockingQueue 则可能被清空，此时消费线程再试图从 BlockingQueue 读取数据时就会被阻塞。反之，如果生产线程较快，则 BlockingQueue 可能会被装满，此时，生产线程再试图向 BlockingQueue 队列装入数据时，便会被阻塞等待。

并发 Deque：

- **LinkedBlockingDeque**：是一个线程安全的双端队列实现。它的内部使用链表结构，每一个节点都维护了一个前驱节点和一个后驱节点。LinkedBlockingDeque 没有进行读写锁的分离，因此同一时间只能有一个线程对其进行操作
- **ConcurrentLinkedDeque**：ConcurrentLinkedDeque是一种基于链接节点的无限并发链表。可以安全地并发执行插入、删除和访问操作。当许多线程同时访问一个公共集合时，ConcurrentLinkedDeque是一个合适的选择。

### Collections和Collection的区别

- Collection是Java集合框架中的一个接口，它是所有集合类的基础接口。它定义了一组通用的操作和方法，如添加、删除、遍历等，用于操作和管理一组对象。Collection接口有许多实现类，如List、Set和Queue等。
- Collections是Java提供的一个工具类，位于java.util包中。只提供静态方法，不可实例化。Collections类中的方法包括排序、查找、替换、反转、随机化等等。

### 集合遍历的方法有哪些？

- **普通 for 循环：**`for (int i = 0; i < list.size(); i++)`

- **增强 for 循环（for-each循环）**：`for (String element : list) `

- **Iterator 迭代器**：单向遍历、仅删除

 ```java
  Iterator<String> iterator = list.iterator();
  while(iterator.hasNext()) {
      String element = iterator.next();
      System.out.println(element);
  }
 ```

- **ListIterator 列表迭代器**：双向遍历，可删除、修改、新增

 ```java
  ListIterator<String> listIterator= list.listIterator();
  while(listIterator.hasNext()) {
      String element = listIterator.next();
      System.out.println(element);
  }
 ```

- **使用 forEach 方法**：`list.forEach(element -> System.out.println(element));`

- **Stream API**：`list.stream().forEach(element -> System.out.println(element));`

## List

### list可以一边遍历一边修改元素吗？

普通for循环可以，foreach不建议修改，可能破坏迭代器内部状态，抛出异常。使用迭代器，可以使用remove方法删除元素，如果是替换不可变对象，必须通过ListIterator的set方法。

### list如何快速删除某个指定下标的元素？

通过`remove(int index)`

- `ArrayList`：删除后，后面的元素会向前移，删除末尾元素 O (1)，中间 O (n)
- `LinkedList`：末尾元素 O (1)，中间 O (n)
- `CopyOnWriteArrayList`：写操作会新建数组，删除时间取决于数组复制速度，O(n)。并发环境下不影响读操作。

### Arraylist和Vector 区别是什么？

- 线程安全
- 性能
- 扩容机制：Vector可以指定扩容策略，默认是2，ArrayList默认是1.5。

### 把ArrayList变成线程安全有哪些方法？

- 使用Collections类的synchronizedList方法将ArrayList包装成线程安全的List：

```java
List<String> synchronizedList = Collections.synchronizedList(arrayList);
```

- 使用CopyOnWriteArrayList类代替ArrayList，它是一个线程安全的List实现：

```java
CopyOnWriteArrayList<String> copyOnWriteArrayList = new CopyOnWriteArrayList<>(arrayList);
```

- 使用Vector类代替ArrayList，Vector是线程安全的List实现：

```java
Vector<String> vector = new Vector<>(arrayList);
```

### 为什么ArrayList不是线程安全的，具体来说是哪里不安全？

在高并发添加数据下，ArrayList会暴露三个问题;

- 部分值为null（我们并没有add null进去）：线程1、2同时修改了倒数第二个有效索引，同时将指针加一
- 索引越界异常：线程1、2同时检测出有一个剩余容量，线程1set完后size++，这时线程2修改会越界
- size与我们add的数量不符：线程1、2同时修改同一个地方

### ArrayList 和 LinkedList 的应用场景？

- ArrayList适用于需要频繁访问和遍历集合元素，并且集合大小不经常改变时，推荐使用ArrayList
- LinkedList适用于需要频繁进行插入和删除操作，或者集合大小经常改变时，可以考虑使用LinkedList。

### ArrayList的扩容机制说一下

- 计算新的容量：一般情况下，新的容量会扩大为原容量的1.5倍（在JDK 10之后，扩容策略做了调整），然后检查是否超过了最大容量限制。
- 创建新的数组：根据计算得到的新容量，创建一个新的更大的数组。
- 将元素复制：将原来数组中的元素逐个复制到新数组中。
- 更新引用：将ArrayList内部指向原数组的引用指向新数组。
- 完成扩容：扩容完成后，可以继续添加新元素。

之所以扩容是 1.5 倍，是因为 1.5 可以充分利用移位操作，减少浮点数或者运算时间和运算次数。

### 线程安全的CopyonWriteArraylist是如何实现线程安全的

CopyOnWriteArrayList底层是通过一个数组保存数据，使用volatile关键字修饰数组，保证当前线程对数组对象重新赋值后，其他线程可以及时感知到。

在写入操作时，加了一把互斥锁ReentrantLock以保证线程安全。

### List<>里面填基本数据类型为什么会报错？

`List<>` 等泛型集合类要求填充的必须是**引用类型**（对象类型），而不能直接使用**基本数据类型**（如 `int`、`char`、`double` 等），否则会编译报错。

这么设计的原因是：

- **泛型的类型擦除机制**：Java 泛型在编译后会被擦除为 `Object` 类型，而 `Object` 只能接收引用类型，不能接收基本数据类型。
- **历史原因**：Java 最初设计时基本数据类型和引用类型是严格区分的，泛型是后期（JDK 1.5）才引入的特性，为了兼容已有的类型系统，选择只支持引用类型。

通过使用包装类，结合 Java 的**自动装箱**（基本类型 → 包装类）和**自动拆箱**（包装类 → 基本类型）机制，可以很方便地在泛型集合中操作基本数据类型的数据

### List和数组如何互相转换？

**List 转数组：**

- 无参 toArray ()（返回 Object []，不推荐）`Object[] objArr = strList.toArray();`

- 带参 toArray (T [] a)（推荐，指定类型）：`String[] strArr2 = strList.toArray(new String[0]);`

**数组转 List：**

使用`Arrays.asList()`

- 对象数组转List：

  - 不可变(不能add/remove)：`List<String> strList1 = Arrays.asList(strArr);`
  - 可变：`List<String> strList2 = new ArrayList<>(Arrays.asList(strArr));`

- 基本类型数组转List：`int[]`直接转List会变成`List<int[]>`

  - 手动装箱：

   ```java
    List<Integer> numList1 = new ArrayList<>();
    for (int num : numArr) {
        numList1.add(num);
    }
   ```

  - Stream流：`List<Integer> numList2 = Arrays.stream(numArr).boxed().collect(Collectors.toList());`

## Set

### Java 集合中 List 和 Set区别是什么？

核心的区别就是「是否允许元素重复」和「是否保证有序」

- List：有序、允许元素重复、可以存多个null值，适合保留顺序的场景，购物车列表、订单明细
- Set：元素唯一，最多有一个null，适合去重场景，用户标签、分类、抽奖名单

### 如何对Set排序？

- LinkedHashSet 可以保留添加顺序

- TreeSet 底层是红黑树，插入会自动按元素大小排序

## Map

常见的Map集合（**非线程安全**）：

- `HashMap`是基于哈希表实现的`Map`，它根据键的哈希值来存储和获取键值对，JDK 1.8中是用数组+链表+红黑树来实现的。
- `LinkedHashMap`继承自`HashMap`，它在`HashMap`的基础上，使用双向链表维护了键值对的插入顺序或访问顺序，使得迭代顺序与插入顺序或访问顺序一致。
- `TreeMap`是基于红黑树实现的`Map`，它可以对键进行排序，默认按照自然顺序排序，也可以通过指定的比较器进行排序。

常见的Map集合（**线程安全**）：

- `Hashtable`是早期 Java 提供的线程安全的`Map`实现，它的实现方式与`HashMap`类似，但在方法上使用了`synchronized`关键字来保证线程安全。通过在每个可能修改`Hashtable`状态的方法上加上`synchronized`关键字，使得在同一时刻，只能有一个线程能够访问`Hashtable`的这些方法，从而保证了线程安全。
- `ConcurrentHashMap`在 JDK 1.8 以前采用了分段锁等技术来提高并发性能。在`ConcurrentHashMap`中，将数据分成多个段（Segment），每个段都有自己的锁。在进行插入、删除等操作时，只需要获取相应段的锁，而不是整个`Map`的锁，这样可以允许多个线程同时访问不同的段，提高了并发访问的效率。在 JDK 1.8 以后是通过 volatile + CAS 或者 synchronized 来保证线程安全的。

### 如何对map进行快速遍历？ 

- 使用for-each循环和entrySet()方法：

 ```java
  // 使用for-each循环和entrySet()遍历Map
          for (Map.Entry<String, Integer> entry : map.entrySet()) {
              System.out.println("Key: " + entry.getKey() + ", Value: " + entry.getValue());
          }
 ```

- 使用for-each循环和keySet()方法：如果只需要遍历`Map`中的键，可以使用`keySet()`方法

 ```java
  // 使用for-each循环和keySet()遍历Map的键
          for (String key : map.keySet()) {
              System.out.println("Key: " + key + ", Value: " + map.get(key));
          }
 ```

- 使用迭代器：通过获取Map的entrySet()或keySet()的迭代器，也可以实现对Map的遍历，这种方式在需要删除元素等操作时比较有用。

 ```java
  // 使用迭代器遍历Map
          Iterator<Entry<String, Integer>> iterator = map.entrySet().iterator();
          while (iterator.hasNext()) {
              Entry<String, Integer> entry = iterator.next();
              System.out.println("Key: " + entry.getKey() + ", Value: " + entry.getValue());
          }
 ```

- 使用 Lambda 表达式和forEach()方法

 ```java
  // 使用Lambda表达式和forEach()方法遍历Map
          map.forEach((key, value) -> System.out.println("Key: " + key + ", Value: " + value));
 ```

- 使用Stream API

 ```java
  // 使用Stream API遍历Map
          map.entrySet().stream()
            .forEach(entry -> System.out.println("Key: " + entry.getKey() + ", Value: " + entry.getValue()));
  
          // 还可以进行其他操作，如过滤、映射等
          Map<String, Integer> filteredMap = map.entrySet().stream()
                                              .filter(entry -> entry.getValue() > 1)
                                              .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
          System.out.println(filteredMap);
 ```

### HashMap实现原理介绍一下？

JDK1.7之前，使用数组和链表来实现，通过哈希算法将元素映射数组的槽位，如果出现了哈希冲突，会以链表的形式存储在同一个槽位上，但链表的查询时间为O(n)，如果很长，效率就很低。所以JDK1.8优化成，当一个链表长度超过8，则会换成红黑树来存储，时间复杂度为O(log n)，如果后面数量小于6，则又会转回链表。

### HashMap链表发生转换后为什么不用平衡二叉树？

因为HashMap转换仅发生链表长度≥8，本身是低频场景，而且对于 HashMap 来说，“增删” 和 “查找” 的频率几乎持平，需要一个综合性能更优的数据结构，而AVL树是严格平衡树，插入和删除开销很大，综合考虑选择红黑树。

### 了解的哈希冲突解决方法有哪些？

- 链接法：使用链表或其他数据结构来存储冲突的键值对，将它们链接在同一个哈希桶中。
- 开放寻址法：在哈希表中找到另一个可用的位置来存储冲突的键值对，而不是存储在链表中。常见的开放寻址方法包括线性探测、二次探测和双重散列。
- 再哈希法（Rehashing）：当发生冲突时，使用另一个哈希函数再次计算键的哈希值，直到找到一个空槽来存储键值对。
- 哈希桶扩容：当哈希冲突过多时，可以动态地扩大哈希桶的数量，重新分配键值对，以减少冲突的概率。

### HashMap是线程安全的吗？

hashmap不是线程安全的，hashmap在多线程会存在下面的问题：

- JDK 1.7 HashMap 采用数组 + 链表的数据结构，多线程背景下，在数组扩容的时候，存在 Entry 链死循环和数据丢失问题。
- JDK 1.8 HashMap 采用数组 + 链表 + 红黑二叉树的数据结构，优化了 1.7 中数组扩容的方案，解决了 Entry 链死循环和数据丢失问题。但是多线程背景下，put 方法存在数据覆盖的问题。

如果要保证线程安全，可以通过这些方法来保证：

- 多线程环境可以使用Collections.synchronizedMap同步加锁的方式，还可以使用HashTable，但是同步的方式显然性能不达标，而ConurrentHashMap更适合高并发场景使用。
- ConcurrentHashmap在JDK1.7和1.8的版本改动比较大，1.7使用Segment+HashEntry分段锁的方式实现，1.8则抛弃了Segment，改为使用CAS+synchronized+Node实现，同样也加入了红黑树，避免链表过长导致性能的问题。

### 在 Java 的 HashMap 中 get一个元素的过程是怎样的？

首先会根据key计算出哈希值，然后判断是否存在这个HashMap的存储数组，长度是否大于1，哈希值的位置是否有节点（数组最大索引&hash）。再判断第一个节点的hash和key是否符合，不是，再判断节点类型，树节点通过红黑树查找算法查找，链表就通过do-while遍历查找，最后都没有查找到，返回null。

### HashMap 的put过程介绍一下

首先会根据key计算出哈希值，然后判断HashMap的存储数组是否为空或长度为0，如果是则进行扩容。
然后根据哈希值计算出插入位置，判断该位置是否为空，为空则直接创建节点插入。
如果不为空，则判断第一个节点的hash和key是否符合，符合则直接覆盖value。
如果不符合，再判断节点类型，如果是树节点则调用红黑树插入方法；如果是链表则遍历链表，尾插法插入新节点，插入后如果链表长度超过阈值（8）且数组长度大于64，则转为红黑树。
最后判断链表中是否有重复key，有则覆盖，无则插入成功，并判断元素个数是否超过阈值，超过则进行扩容。

### 为什么是“链表 > 8”？

在理想的随机 Hash 下，链表长度符合泊松分布，长度达到 8 的概率是 0.00000006 （千万分之六）。如果真的碰到了长度 8，说明 发生了极端的 Hash 冲突 （比如遭遇了 Hash 攻击，或者你的对象 hashCode 方法写得极烂）。

### 为什么退化阈值是 6？（防止抖动）

如果升是 8，降也是 8，在 8 的边缘反复插入、删除会损耗大量的性能。

### HashMap 调用get方法一定安全吗？

- **空指针异常（NullPointerException）**：如果你尝试用 `null` 作为键调用 `get` 方法，而 `HashMap` 没有被初始化（即为 `null`），那么会抛出空指针异常。不过，如果 `HashMap` 已经初始化，使用 `null` 作为键是允许的，因为 `HashMap` 支持 `null` 键。
- **线程安全**：`HashMap` 本身不是线程安全的。如果在多线程环境中，没有适当的同步措施，同时对 `HashMap` 进行读写操作可能会导致不可预测的行为。

### HashMap一般用什么做Key？为啥String适合做Key呢？

用 String 做 key，因为 String对象是不可变的，一旦创建就不能被修改，这确保了Key的稳定性。如果Key是可变的，可能会导致hashCode和equals方法的不一致，进而影响HashMap的正确性。

### HashMap的key可以为null吗？

可以，为空时直接令key的哈希值为0，而且因为会覆盖相同的key，所以为null的key只会有一个。

HashTable 和 ConcurrentHashMap 的 Key 是 不可以 为 null 的（会报空指针异常）

### 重写HashMap的equal和hashcode方法需要注意什么？

- **必须成对重写**，因为HashMap是先算Hash再比equals，如果两个对象 `equals` 相等，它们的 `hashCode` 也必须相等，否则会导致存储在 Map 中的数据无法取出（哈希定位错误）；

- **参与计算的字段必须一致**，即 `equals` 比了哪些字段，`hashCode` 就得用哪些字段算；

- 作为 Key 的对象属性**最好是不可变的**，存进去后一旦修改了属性值导致 Hash 变了，数据就会“失联”（内存泄漏）。

### HashMap的扩容机制介绍一下

当元素超过阈值时会扩容，阈值是数组长度乘以负载因子（0.75）。扩容第一步是新建一个两倍长度的数组，然后把旧数据迁移到新数组里。JDK1.7里需要对每个key重新算Hash，重新取模判断新的位置，而1.8里利用了一个规律，就是数组长度强制为2的幂次方（为了取模时每一位都有效，让数据均匀散开），长度翻倍，实际上是二进制高位多了一位，那么执行看key的Hash在这个高位是0还是1，0就保留原下标，1就是原下标加上旧数组长度。

### 说说hashmap的负载因子为什么是0.75

这是泊松分布算出来的黄金平衡点，在负载因子为0.75的情况下，链表长度达到8的概率极低，而且少点浪费数组空间，多了哈希冲突会很严重。

### Hashmap和Hashtable有什么不一样的？

- 线程安全：HashTable给所有方法都加上了synchronized锁，代价是性能极差
- Null：HashMap允许，一个null key，无数个null value，HashTable有null直接报空指针异常
- 底层结构：HashTable没有红黑树优化
- 扩容机制：HashMap初始16，每次扩容x2，用位运算优化取模。HashTable初始11，每次扩容x2+1，老老实实取模运算比较慢。

### ConcurrentHashMap怎么实现的？
