---
title: "Java.Collections"
date: "2017-08-09"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---


[TOC]

# Structure
## Image
- ![java.collections.png](http://otzm88f21.bkt.clouddn.com/32445d0d-3135-4eb3-a0f7-2217ff29d269.png)

## Text
### Iterable
- Collection
	- List
	- AbstractCollection
		- AbstractList
			- Vector
				- Stack
			- ArrayList
			- SubList
				- RandomAccessSubList
			- AbstractSequentialList
				- LinkedList
		- ArrayDeque
		- AbstractQueue
			- PriorityQueue
		- AbstractSet
			- EnumSet
				- RegularEnumSet
				- JumboEnumSet
			- TreeSet
			- HashSet
				- LinkedHashSet
	- Queue
		- Deque
	- Set
		- SortedSet
			- NavigableSet
- ServiceLoader

### Map
- SortedMap
	- NavigableMap
- Abstractmap
	- WeakHashMap
	- IdentityHashMap
	- EnumMap
	- TreeMap
	- HashMap
		- LinkedHashMap

### Dictionary
- Hashtable
- Properties

### Utils
- Collections
- Arrays


# Tidy

## Iterable
- methods
    - iterator()
    - forEach(Consumer<? super T> action)

## Collection
- methods
	- check
	    - contains(Object o)
	    - retainAll(Collection<?> c)
    - stream
	    - **iterator()**
	    - **Object[] toArray()**
    - operate
	    - add(E e)
	    - remove(Object o)
	    - addAll(Collection<? extends E> c)
	    - removeAll(Collection<?> c)
	    - clear()


## List
- description
	- extends Collection
	- **排序**数据，允许 **重复**或 **空**
- methods
	- operate
	    - `get(int index)`
	    - `set(int index, E element)`
	    - `add(int index, E element)`
	    - `remove(int index)`
	    - **indexOf(Object o)**
	    - `lastIndexOf(Object o)`
	    - `containsAll(Collection<?> c)`
	    - `addAll(int index, Collection<? extends E> c)`
	    - **default void replaceAll(UnaryOperator<E> operator)**
	    - **sort(Comparator<? super E> c)**
	- stream
	    - **subList(int fromIndex, int toIndex)**


## AbstractCollection
- description
    - 最小接口框架
- methods
    - `abstract Iterator<E> iterator()`
    - `abstract int size()`
    - `add(E e) {throw new UnsupportedOperationException();}`

## AbstractList
- description
    - 随机存取的最小框架
    - 不强制要求提供 `iterator` 实现
- methods
    - `subList(int fromIndex, int toIndex)`
    - `removeRange(int fromIndex, int toIndex)`


## Vector
- description
    - 可增长数组，自动进行扩容、收缩
	- 通过 `capacity` 和 `capacityIncrement` 来控制容量的变化
	- `iterator` 在检测到异常时快速失败
		- `iterator`创建后， `vector` 在结构上发生了调整
		- 无法确保非同步情况下的修改同步
	- 在不需要保障线程安全的情况下，推荐使用 `ArrayList` 代替
- params
    - Object[] elementData
    - int elementCount
		- 容器实际使用情况
    - int capacityIncrement
		- 容器单次扩容值，若为负数则每次扩容一倍
	- int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8
		- 允许分配的数组 **最大尺寸**，预留操作系统将头数据存于数组的情况
		- 分配更大的数据情况下，抛出 `OutOfMemoryError`
	- transient int modCount = 0
		- transient 保障序列化前后不影响该值
- methods
    - `iterator()`
    - `elements()`
	- `add(E e)`

		``` java
		ensureCapacityHelper(elementCount + 1)
			int newCapacity = oldCapacity + ((capacityIncrement > 0) ?capacityIncrement : oldCapacity);
		elementData[elementCount++] = e;
		```

## Stack
- description
	- 先进后出栈

- methods
	- `E push(E item)`
	- `synchronized E pop()`

## ArrayList

- description
	- 可扩展队列，允许空值
	- 添加元素花费固定时间 `O(n)`，其它操作为线性时间
	- 不支持多线程同步
		- 必须于外部扩展支持多线程
		- 如 `List list = Collections.synchronizedList(new ArrayList(...));`
	- iterator() 快速失败机制

- params
	- transient Object[] elementData
		- 序列化时忽略该值，并通过 `(write|read)Object` 实现序列结果
		- 保证序列时仅操作存储数据，而非完整数组
	- protected transient int modCount = 0

- methods
	- `void ensureCapacity(int minCapacity)`

		```java
		if (minCapacity > minExpand) {
            ensureExplicitCapacity(minCapacity){

				if (minCapacity - elementData.length > 0)
					grow(minCapacity){

						int oldCapacity = elementData.length;
						int newCapacity = oldCapacity + (**oldCapacity >> 1**);
						if (newCapacity - minCapacity < 0)
							newCapacity = minCapacity;
						if (newCapacity - MAX_ARRAY_SIZE > 0)
							newCapacity = hugeCapacity(minCapacity);
						elementData = Arrays.copyOf(elementData, newCapacity);
					}
			}
		}
		```

	- `void writeObject(java.io.ObjectOutputStream s)`
	- `void readObject(java.io.ObjectInputStream s)`
	- `void sort(Comparator<? super E> c)`

		```java
        final int expectedModCount = modCount;
		// TODO
        if (modCount != expectedModCount) {			// 判断是否值变更以确认是否有其它线程操作
            throw new ConcurrentModificationException();
        }
        modCount++;
		```


## SubList

- description
	- 子列表的相关操作

- params
    - final AbstractList<E> l
		- 存储初始化列表
    - final int offset
		- 指定子列表相对初始化列表的开始位置
    - int size
		- 指定子列表的长度

- methods
	- List.*
		- `E set(int var1, E var2)`

			```java
			public E set(int var1, E var2) {
				this.rangeCheck(var1);				// check index out of boundary
				this.checkForComodification();		// check other thread update the structure
				return this.l.set(var1 + this.offset, var2);
			}
			```


## RandomAccessSubList

- description
	- `SubList` 的重命名版

- params

- methods
	- `RandomAccessSubList(AbstractList<E> list, int fromIndex, int toIndex)`

		```java
		class RandomAccessSubList<E> extends SubList<E> implements RandomAccess {
        
		RandomAccessSubList(AbstractList<E> list, int fromIndex, int toIndex){
			super(list, fromIndex, toIndex);
		}
		```


## AbstractSequentialList

- description
	- 序列化访问列表的最小实现框架
		- 而随机访问的则是 `AbstractList`
	- 列表，需要实现 `listIterator` 和`size`
	- 无法修改的列表，需实现 `hasNext`、`next`、`hasPrevious`、`previous`、`index`
	- 可修改的列表，需实现 `set`
	- 可变长列表，需实现 `remove`、`add`

- params

- methods
	- get|set|add|remove|addAll
	- `iterator()`
	- `abstract ListIterator<E> listIterator(int index)`


## LinkedList

- description
	- 双向连接列表
	- 不支持同步操作
		- 需在外部进行同步支持
		- 或使用 `synchronizedList`
			- 于创建时使用，如 `List list = Collections.synchronizedList(new LinkedList(...));`

- params
	- `transient` int size = 0
	- `transient` Node<E> first
	- `transient` Node<E> last

- methods
	- `(link|unlink|get|remove|add)[(First|Last)]`
	- `(write|read)Object`

		```java
		s.defaultWriteObject();
		s.writeInt(size);
		for (Node<E> x = first; x != null; x = x.next)
            s.writeObject(x.item);
		```

	- `Node<E> node(int index)`

		```java
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
		```

## ArrayDeque

- description
	- 可扩展数组
	- 非线程安全
	- 禁止添加 `Null`
	- 比 `stack`、`LinkedList` **更快**

- params
	- transient Object[] elements
	- transient int head
	- transient int tail

- methods
	- `private boolean delete(int i)`

		```java
		final Object[] elements = this.elements;
        final int mask = elements.length - 1;
        final int h = head;
        final int t = tail;
        final int front = (i - h) & mask;
        final int back  = (t - i) & mask;

        // 判断序列值是否超过实际长度
        if (front >= ((t - h) & mask))
            throw new ConcurrentModificationException();

        // 判断序列值处于前半段或后半段，从而进行最小量的数量移动
        if (front < back) {
            if (h <= i) {
                System.arraycopy(elements, h, elements, h + 1, front);
            } else { // Wrap around
                System.arraycopy(elements, 0, elements, 1, i);
                elements[0] = elements[mask];
                System.arraycopy(elements, h, elements, h + 1, mask - h);
            }
            elements[h] = null;
            head = (h + 1) & mask;
            return false;
        } else {
            if (i < t) { // Copy the null tail as well
                System.arraycopy(elements, i + 1, elements, i, back);
                tail = t - 1;
            } else { // Wrap around
                System.arraycopy(elements, i + 1, elements, i, mask - i);
                elements[mask] = elements[0];
                System.arraycopy(elements, 1, elements, 0, t);
                tail = (t - 1) & mask;
            }
            return true;
        }
		```

	- `private void allocateElements(int numElements)`

		```java
		if (numElements >= initialCapacity) {
			// 转为最接近该值的 2^n 次数值
            initialCapacity = numElements;
            initialCapacity |= (initialCapacity >>>  1);
            initialCapacity |= (initialCapacity >>>  2);
            initialCapacity |= (initialCapacity >>>  4);
            initialCapacity |= (initialCapacity >>>  8);
            initialCapacity |= (initialCapacity >>> 16);
            initialCapacity++;

            if (initialCapacity < 0)   // Too many elements, must back off
                initialCapacity >>>= 1;// Good luck allocating 2 ^ 30 elements
        }
		```

	- `public void addFirst(E e)`

		```java
		elements[head = (head - 1) & (elements.length - 1)] = e;
		```

	- `public void addLast(E e)`
		```java
		elements[tail] = e;
        if ( (tail = (tail + 1) & (elements.length - 1)) == head)
            doubleCapacity();
		```

## AbstractQueue

- description
	- 队列的实现框架
	- 不允许空值，失败时直接抛出异常

- params
- methods
	- `boolean add(E e)`

		```java
		if (offer(e))
            return true;
        else
            throw new IllegalStateException("Queue full");
		```


## PriorityQueue

- description
	无限制优先级队列
	不允许空值、无法比较的对象

- params
- methods


## AbstractSet

- description
- params
- methods


## EnumSet

- description
- params
- methods


## RegularEnumSet

- description
- params
- methods


## JumboEnumSet

- description
- params
- methods


## TreeSet

- description
- params
- methods


## HashSet

- description
- params
- methods


## LinkedHashSet

- description
- params
- methods



## Queue

- description
	- 先进先出队列

- params

- methods
	- boolean add(E e)
		- 插入元素
		- 容量足够时返回 `true`，否则抛出 `IllegalStateException` 异常
	- boolean offer(E e)
		- 插入元素
		- 容量限制情况下，与 `add` 方法一样，无法插入时抛出异常
	- E remove()
		- 获取并移除队列头部
		- 队列空时 **抛出异常**	
	- E poll()
		- 获取并移除队列头部
		- 队列空时返回 null
	- E element()
		- 仅获取队列头部
		- 队列空时 **抛出异常**
	- E peek()
		- 仅获取队列头部
		- 队列空时返回 null

## Deque

- description
	- 双端队列
	- 允许空值
	
- params
	
- methods
	- `(add|offer|remove|poll|get|peekLast)[First|Last]`
	- `push/ pop`



## Set

- description
- params
- methods


## SortedSet

- description
- params
- methods


## NavigableSet

- description
- params
- methods

    
# Reference
## 
- []()
- []()
- []()

## 
- [ArrayList中elementData为什么被transient修饰？](https://blog.csdn.net/zero__007/article/details/52166306)
- [rrayDeque集合的妙用](http://cakin24.iteye.com/blog/2324030)
- []()
- []()
- []()
- []()