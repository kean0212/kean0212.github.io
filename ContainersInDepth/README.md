## Coding
1. `Map` is not iterable. Therefore, the way to iterate through the `Map` is like below:
    ```java
    for(Map.Entry<T, E> entry : map.entrySet()) { ... }
    ```
    Method `entrySet` returns a `Set` of entries, all included.

2. `Entry` is an inner interface of `Map`, and it is **NOT**  `Comparable`.
Methods `getKey()` and `getValue()` return the key and value respectively. However, `Entry` cannot be added to `Map` 
directly, only using `put(key, value)`.

3. The mechanism used to add element to `Set` is: checks if the element exists in the `Set` 
based on hash code, and then create and add a new element in the `Set`.

4. The element in `TreeSet` has to be `Comparable`.

5. Remember the pain caused by ***flyweight design pattern***, see 
[SetFilling.java](https://github.com/kean0212/Thinking-In-Java-Notes/blob/master/ContainersInDepth/SetFilling.java#L23).


## Filling Containers

### `Collections` -- `fill` and `nCopies` for `List`
1. `fill(list, object)`: replaces all elements with the specified object
    * An **empty** list after `fill` is still **empty**

2. `nCopies(number, object)`: returns an **immutable** list of the specified objects.
    ```java
    // Output: "immutable" list: [world, world, world, world]
    List<String> list = Collections.nCopies(4, "world"); 
      
    // To make it "mutable" - construct a "mutable" list
    list = new ArrayList<String>(list);
    ```
**Note**: No matter it's `fill` or `nCopies`, the reference of the specified object (even for the object instantiated 
using `new` ) is used to fill the `List`, which means if the specified object changes, the `List` changes. Even though 
the result of `nCopies` is immutable (which means the value (reference) of the element is immutable), the `List` still
reflects the change.
  
### A Generator Solution
1. All `Collection` subtypes have a constructor which takes another `Collection` instance 
to fill the new container
    ```java
    List<String> stringList = Arrays.asList({"hello", "world"});
    Set<String> stringSet = new HashSet<String>(stringList);
    List<String> stringListTwo = new ArrayList<String>(stringList);
    Queue<String> stringQueue = new Queue<String>(stringList);
    ```
    
2. Method `toCollection.addAll(fromCollection)` can be used to fill a `Collection` as well.

### Map Generators
1. Read-only **Data Transfer Object**:
    ```java
    // This is a data object
    // "public" makes it easy to access,
    // "final" makes it immutable
    public class DTO<T> {
        public final T property;
        ...
    }
    ```

### Using Abstract Classes
1. All containers including `Collection`, `List`, `Set`, and `Map` have abstract classes, 
so that we only need to implement partial methods to produce the desired container.

2. **Flyweight** design pattern: it is a design pattern that uses only simple properties to satisfy
a complex need, such as adding a `size` property in a `EntrySet` to indicate what data is stored in
the `EntrySet` instead of storing the real data inside the `EntrySet`.

### Unsupported Operations
It appears that some methods in `interface` are optional, meaning that the implementing classes
do **NOT** have to implement those methods. For example, the `List` returned by `Arrays.asList(array)`
does not support the optional methods, such as `add()`, `remove()`, becuase the list is fixed-size.

### ListIterator
1. A **bidirectional** iterator of `List`.

2. It is a cursor between nodes, shown as follow:

    ![alt text](https://github.com/kean0212/Thinking-In-Java-Notes/blob/master/ContainersInDepth/img/ListIterator.png)
3. The call of `previous()` or `next()` will move the cursor.

4. Method `add(element)` adds the element to the left of the cursor; while `set(element)` sets the element
returned by the last call of `previous()` or `next()`. Under both circumstances, the cursor postion is updated.

**Note**: When using `ListIterator` to add element backwards, pay attention to `previous()`. The number of 
the calls in [ListInsertion.java](https://github.com/kean0212/Thinking-In-Java-Notes/blob/master/ContainersInDepth/ListInsertion.java#L39).

### Sets and Storage Order
Set Type|Element Requirements
:-------------:|:-------------
`Set`| The element type should have a proper `equals()`
`HashSet`| The element type should have a proper `hashCode()`
`TreeSet`, `SortedSet`| The element type should implement `Comparable`
`LinkedHashSet`| The element type should have a proper `hashCode()`

**Note:** For good programming style, we should always override `hashCode()` when we override `equals()`.

### SortedSet
1. `SortedSet` is **ONLY** an interface which has special methods, one of the implementing class is `TreeSet`.

2. Useful methods: 
    ```java 
    // List
    list.isEmpty(); 
    
    // LinkedList
    linkedList.add(index, element); 
    linkedList.get(index);
    linkedList.getFirst();
    linkedList.getLast();
    
    // Comparable
    comparableOne.compareTo(comparableTwo);
    
    // SortedSet
    sortedSet.first();
    sortedSet.last();
    sortedSet.subSet(fromElement, toElement); // [fromElement, toElement)
    sortedSet.tailSet(fromElement); // [fromElement...)
    sortedSet.headSet(toElement); // (...toElement)
    
    ```
    
### PriorityQueue
1. The construction of `PriorityQueue` requires either `Comparator` or "natural ordering" (the element implements 
interface `Comparable`).
    ```java
    // Comparator
    PriorityQueue<Integer> priorityQueue = new PriorityQueue<Integer>(10, Collections.reverseOrder());
    
    // Natural ordering
    PriorityQueue<String> priorityQueue = new PrirotityQueue<Integer>();
    ```

1. `PriorityQueue` is a `class`, not an `interface`.

1. Methods: `offer(element), peek(), poll(), size(), clear()`; if the queue is empty, `peek()` and `poll()` will return 
`null`. 

**Recall**: `compareTo(object)` in `Comparable`; `compare(o1, o2)` of `Comparator` in `java.lang`; `compare(o1, o2)` in 
`Comparator` in `java.util`.

### Understanding Maps
Map | Characteristics of Order
:---:|:---
`HashMap`|Order is based on the hash code
`TreeMap`|Order is based on the key
`LinkedHashMap`|Order is same as the insertion order 

### Performance

`Map` | Performance
:----:|:----
`HashMap`|Provides constant-time performance for inserting and locating pairs.
`LinkedHashMap`|When iterating through it, we get the pairs in insertion order, or in least-recently-used(**LRU**).
`TreeMap`|Pairs will be in sorted order(determined by `Comparable` or `Comparator`. The only `Map` provides `subMap()` method.
`WeakHashMap`|If no references to a particular key are held outside the map,that key may be garbage collected.
`ConcurrentHashMap`|A thread-safe `Map` which does not involve synchronization locking.
`IdentityHashMap`|A hash map that uses `==` instead of `equals()` to compare keys.

**Notes**:
1. `put()` or `putAll()` updates the `Map` if the key already exists in it.
2. `keySet()` and `values()` are backed by the `Map`. Therefore, any changes to the `Collection` will be reflected in the
associated `Map`. For details, take a look at [SlowMap.java](https://github.com/kean0212/Thinking-In-Java-Notes/blob/master/ContainersInDepth/SlowMap.java#L21).
3. `Properties` in `java.util` package is backed by `HashTable<Object, Object>`.
4. Extending `AbstractSet` is same as extending `AbstractCollection`. Need to override `size()` and `iterator()`. To make
the "set" modifiable, we also need to override the method `remove()` in `iterator()` and `add(E e)` in the "set". All the
other methods are based upon these. [SlowSet.java](https://github.com/kean0212/Thinking-In-Java-Notes/blob/master/ContainersInDepth/SlowSet.java).

### Hashing for Speed

The idea behind `HashMap`:
1. The hash code of the key is used to locate the **bucket** in the buckets which is an array.
2. Then linear search is used to find the key inside the bucket. 

Apparently, bucket can have **collision** unless the hashing function was a **perfect**.
The real implementation of `HashMap` in jdk1.8 is similar except each bucket has a maximum size of 2. 
If the size exceeds the threshold, the map resizes.

`AbstractMap` has only the default constructor. Therefore, we have to implement others.
The implementation of `size()` in `AbstractMap` returns `entrySet().size()`.
This means that we should pay attention to whether override `entrySet()` or not when subclassing `AbstractMap`.

`AbstractMap` implements `Map` interface. 
Therefore, all of the methods have been implemented.
Many methods depend on `entrySet()` which makes `entrySet()` important.

### Overriding hashCode()
1. The most important factor in creating a `hashCode()` is that, regardless of when `hashCode()` is called,
it produces the same value for a particular object every time. 
Otherwise, it will be impossible to access the stored ones.

2. Do **NOT** use unique object information, such as the address.
Otherwise, it will be impossible again.
For example, 
    ```java
    map.put(instance); // where instance.hashCode() is based on `this`
    Instance instance2 = instance;
    map.get(instance2); // this will return `null`
    ```
    
3. Another factor needs to consider while overriding `hashCode()` is to ensure an even distribution,
so that less collisions happen.
According to ***Effective Java***, use `result = 37 * result + c` where `c` is the `hashCode` of each field of the class.

**Note**:
1. The access modifiers are controlling class access, not instance access.
That's why in 'equals()', private fields can be accessed.
[CountedString.java](https://github.com/kean0212/Thinking-In-Java-Notes/blob/master/ContainersInDepth/CountedString.java#L42).

2. Both `hashCode()` and `compareTo` in 
[ThreeTuple](https://github.com/kean0212/Thinking-In-Java-Notes/blob/master/ContainersInDepth/ThreeTuple.java#L15-L27)
use the parent's implementation which only takes the first two fields.

### Choosing between Lists
The best approach is to choose `ArrayList` as your default and to change to a `LinkedList` 
if you need its extra functionality or you discover performance problems due to many insertions and removals 
from the middle of the list. 
If you are working with a fixed-size group of elements, either use a `List` backed by an array(such as `Arrays.asList()`),
or if necessary, an actual array.

1. For a `List` backed by an array and for an `ArrayList`, 'get' and 'set' are fast and consistent regardless of the 
list size, whereas for a `LinkedList`, the access times grow significantly for larger lists.

2. For `ArrayList`, 'insertion' gets expensive as the list gets larger,
but for a `LinkedList`, it is relatively cheap, and constant regardless of size,
because an `ArrayList` must create space and copy all its references forward during an insertion.

3. For `Queue` which is backed by `LinkedList`, 'insert' and 'remove' elements from the endpoints of the list are optimal.

**Note**:
1. We can instantiate an abstract class by implementing the abstract method in an anonymous class, like 
[ListPerformance.java](https://github.com/kean0212/Thinking-In-Java-Notes/blob/master/ContainersInDepth/ListPerformance.java#L22).

### Choosing between Sets
1. `HashSet` is used for 'insertion' and 'searching'.

2. `TreeSet` is used for sorted set.

3. `LinkedHashSet` is for maintaining inserted order.

## Chossing between Maps
When you are using a `Map`, your first choice should be `HashMap`, 
and only if you need a constantly sorted `Map` will you need `TreeMap`.

The performance of `HashMap` can be tuned through **load factor**(`loadFactor = size / capacity`). 
The lighter it is, the lower chance of collision. 
Therefore, this improves the efficiency of lookups and insertions, but sacrifice iteration and space.
As all other problems, there is a tradeoff between efficiency and space.
**Note:**
1. `? super Comparable` vs `? extends Comparable`.

## Utilities
Some useful methods in `java.util.Collections`:
```java
// Using the natural comparison method of objects
Collections.max(collection);
Collections.min(collection);
Collections.indexOfSubList(List source, List target);
Collections.lastIndexOfSubList(List source, List target);
Collections.replaceAll(List<T>, T oldValue, T newValue);
Collections.reverse(list);
Collections.rotate(list, int distance);
Collections.sort(List<T> list);
Collections.sort(List<T> list, Comparator<? super T> comparator);
// Create an immutable List<T>
Collections.nCopies(int n, T x); 
// Returns true if the two collections have no elements in common
Collections.disjoint(Collection, Collection); 
// Returns the number of elements
Collections.frequencey(collection, Object x);
```

## Sorting and Searching Lists
1. Similar to `Arrays.sort()` and `Arrays.binarySearch()`:
    ```java
    // These methods **ONLY** apply to List
    Collections.sort(list);
    Collections.binarySearch(list, object);
 
    // The comparator used in sorting and searching should be **SAME**
    Collections.sort(list, comparator);
    Collections.binarySearch(list, object, comparator);
    ```

**Note:**
1. The default `toString()` method of Array doesn't call the `toString()` on each of its elements.
Therefore, we need to explicitly define the function.

## Making a Collection or Map unmodifiable
Methods in `java.util.Collections`:
```java
static <T> Collection<T> unmodifiableCollection(Collection<? extends T> c);
static <T> List<T> unmodifiableList(List<? extends T> l);
static <T> Set<T> unmodifiableSet(Set<? extends T> s);
static <T> Map<K,V> unmodifiableMap(Map<? extends K, ? extends V> m);
static <T> SortedMap<K,V> unmodifiableSortedMap(SortedMap<K, ? extends V> m);
static <T> SortedSet<T> unmodifiableSortedSet(SortedSet<T> s);
```
