---
title: "Java.Collections 60%"
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
	- 无限制优先级队列
		- 构建时通过自然排序或指定排序方式进行优先级调整
	- 不允许空值、无法比较的对象
	- 注意多个元素同为最小值情况
	- 注意
		- `iterator()` 不保障队列以任意顺序排列
			- 排序遍历情况可使用 `Arrays.sort(pq.toArray())`
		- 非线程安全，可用 `PriorityBlockingQueue` 代替

- params
	- `transient Object[] queue`

		```java
		平衡二进制堆，将队列以树形式进行对比存储，排序最低值存于[0]位
		queue[n]= queue[2n+1]+ queue[2*(n+1)]
		存储形式
			[0]	[1]	[2]	[3]	[4]	[5]	[6]
		记录形式
				[0]
				[1]	[2]
			[3]	[4]	[5]	[6]
		```

- methods

	- `public PriorityQueue(int initialCapacity)`
	- `public PriorityQueue(Collection<? extends E> c)`

		```java
		private void initFromCollection(Collection<? extends E> c) {
			// 拷贝集合元素，并进行非空判断
			initElementsFromCollection(c);
			heapify();
		}

		// 堆化
		private void heapify() {
			for (int i = (size >>> 1) - 1; i >= 0; i--)
				siftDown(i, (E) queue[i]);
		}

		private void siftDown(int k, E x) {
			if (comparator != null)
				siftDownUsingComparator(k, x);
			else
				siftDownComparable(k, x);
		}

		// 插入项，并与低级节点对比至小于等于
		private void siftDownComparable(int k, E x) {
			Comparable<? super E> key = (Comparable<? super E>)x;
			// 递归到最后一种可能，2*(n+1)
			int half = size >>> 1;
			while (k < half) {
				// 替换父结点、父亲兄弟结点中的最小值
				int child = (k << 1) + 1;
				Object c = queue[child];
				int right = child + 1;
				if (right < size &&
					((Comparable<? super E>) c).compareTo((E) queue[right]) > 0)
					c = queue[child = right];
				if (key.compareTo((E) c) <= 0)
					break;
				queue[k] = c;
				k = child;
			}
			queue[k] = key;
		}
		```

	- `public boolean offer(E e)`

		``` java
		public boolean offer(E e) {
			if (e == null)
				throw new NullPointerException();
			modCount++;
			int i = size;
			if (i >= queue.length)
				grow(i + 1);
			size = i + 1;
			if (i == 0)
				queue[0] = e;
			else
				siftUp(i, e);
			return true;
		}

		// 扩容，小于64时加2，否则加一半容量
		private void grow(int minCapacity) {
			int oldCapacity = queue.length;
			int newCapacity = oldCapacity + ((oldCapacity < 64) ?
											(oldCapacity + 2) :
											(oldCapacity >> 1));
			if (newCapacity - MAX_ARRAY_SIZE > 0)
				newCapacity = hugeCapacity(minCapacity);
			queue = Arrays.copyOf(queue, newCapacity);
		}

		private static int hugeCapacity(int minCapacity) {
			if (minCapacity < 0) // overflow
				throw new OutOfMemoryError();
			return (minCapacity > MAX_ARRAY_SIZE) ?
				Integer.MAX_VALUE :
				MAX_ARRAY_SIZE;
		}

		private void siftUp(int k, E x) {
			if (comparator != null)
				siftUpUsingComparator(k, x);
			else
				siftUpComparable(k, x);
		}

		// 向根节点递归比较、替换，siftDown 的简化版本
		private void siftUpComparable(int k, E x) {
			Comparable<? super E> key = (Comparable<? super E>) x;
			while (k > 0) {
				int parent = (k - 1) >>> 1;
				Object e = queue[parent];
				if (key.compareTo((E) e) >= 0)
					break;
				queue[k] = e;
				k = parent;
			}
			queue[k] = key;
		}
		```

	- `E removeAt(int i)`

		```java
		private E removeAt(int i) {
			// assert i >= 0 && i < size;
			modCount++;
			int s = --size;
			if (s == i) // removed last element
				queue[i] = null;
			else {
				E moved = (E) queue[s];
				queue[s] = null;
				siftDown(i, moved);
				if (queue[i] == moved) {
					siftUp(i, moved);
					if (queue[i] != moved)
						return moved;
				}
			}
			return null;
		}
		```
	
	- `void (read|write)Object(Object(Input|Output)Stream s)`

## AbstractSet

- description
	- `Set` 的最小化实现框架

- methods
	- `boolean equals(Object o)`
 	- `int hashCode()`

	 	```java
		int h= 0 
		h += next().hashCode();
		```

	- `boolean removeAll(Collection<?> c)`

## EnumSet

- description
	- 创建时指定使用的枚举类型
		- 内部将枚举值转换为位向量，以实现空间和时间执行上的优化
	- 迭代器为弱关联，即生成迭代器时进行修改，不会抛出 `ConcurrentModificationException` 异常，但不一定会显示修改情况
	- 非线程安全，推荐依赖对象进行同步限制，或使用 `synchronizedSet` 封装
		- `Set<MyEnum> s = Collections.synchronizedSet(EnumSet.noneOf(MyEnum.class));`
	- 所有基础方法均可在常量时间内执行

- params
	- `final Class<E> elementType`
	- `final Enum<?>[] universe`

- methods
	- 实现
		- `EnumSet(Class<E>elementType, Enum<?>[] universe)`
		- `EnumSet<E> clone()`
	- 初始化
		- `static <E extends Enum<E>> EnumSet<E> （all|none)Of(Class<E> elementType)`
		- `static <E extends Enum<E>> EnumSet<E> copyOf((EnumSet|Collection)<E> s)`
		- `static <E extends Enum<E>> EnumSet<E> of(E first, E... rest)`

			```java
			// 将枚举类型设置至 elementType
			EnumSet<E> result = noneOf(first.getDeclaringClass());
			// 添加枚举元素
			result.add(first);
			for (E e : rest)
				result.add(e);
			return result;
			```
	- 抽象接口
		- `abstract void addAll()`
		- `abstract void addRange(E from, E to)`
		- `abstract void complement()`


## JumboEnumSet

- description
	- `EnumSet` 的私有实现，针对超过64个元素大枚举类型使用
	- 实现思路参照 `RegularEnumSet`，只是通过 Long 数组来支持更长纬度的数据
- params
	- `long elements[]`
	- `int size = 0`
- methods
	- `void addAll()`
	- `void addRange(E from, E to)`

		```java
		void addRange(E from, E to) {

			// 计算当前序列位于第几个 Long 的区间上
			int fromIndex = from.ordinal() >>> 6;
			int toIndex = to.ordinal() >>> 6;

			// 同一区间的话，将该区间视同 RegularEnumSet 即可
			if (fromIndex == toIndex) {
				elements[fromIndex] = (-1L >>>  (from.ordinal() - to.ordinal() - 1))
								<< from.ordinal();

			// 不同区间情况下，需要填充中间区间，并于头尾处各自处理								
			} else {
				elements[fromIndex] = (-1L << from.ordinal());
				for (int i = fromIndex + 1; i < toIndex; i++)
					elements[i] = -1;
				elements[toIndex] = -1L >>> (63 - to.ordinal());
			}
			size = to.ordinal() - from.ordinal() + 1;
		}
		```

	- `boolean add(E e)`

		```java
		/***
		通过移位跳转到对应区间，然后对该区间长整型
		*/
		public boolean add(E e) {
			typeCheck(e);

			int eOrdinal = e.ordinal();
			int eWordNum = eOrdinal >>> 6;

			long oldElements = elements[eWordNum];
			elements[eWordNum] |= (1L << eOrdinal);
			boolean result = (elements[eWordNum] != oldElements);
			if (result)
				size++;
			return result;
		}
		```


## RegularEnumSet

- description
	- `EnumSet` 的私有实现，针对元素于64位以内
	- 用64位的 `long` 的各位来指定枚举值在枚举中的对应数值
- params
	- `long elements = 0L= 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 `
- methods
	- `void addAll()`

		```java
		elements = -1L >>> -universe.length;

		/**
		-1L		= 	11111111 11111111 11111111 11111111 11111111 11111111 11111111 11111111
		length		=	6
		>>> -6 		= 	>>> (64+ (-6))
		-1L>>>-6	= 	00000000 00000000 00000000 00000000 00000000 00000000 00000000 00111111
		*/
		```
	- `void addRange(E from, E to)`

		```java
		void addRange(E from, E to) {
			elements = (-1L >>>  (from.ordinal() - to.ordinal() - 1)) << from.ordinal();

			/**
			from= 5
			to= 10
			-1l=	11111111 11111111 11111111 11111111 11111111 11111111 11111111 11111111 
			-1l >>> (5- 10- 1)
				-> -1l >>> -6
				-> -1l >>> (64- 6)		// 根据结果推导，不确定
				-> 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00111111
				-> 63
			(-1l >>> (5- 10- 1)) << 5
				-> 63 << 5
				-> 2016
			*/
		}
		```
	- `void complement()`

	- `public Iterator<E> iterator()`

		```java
		public Iterator<E> iterator() {
			return new EnumSetIterator<>();
		}

		private class EnumSetIterator<E extends Enum<E>> implements Iterator<E> {
			long unseen;
			long lastReturned = 0;

			EnumSetIterator() {
				unseen = elements;
			}

			public boolean hasNext() {
				return unseen != 0;
			}

			@SuppressWarnings("unchecked")
			public E next() {
				if (unseen == 0)
					throw new NoSuchElementException();

				// 找出最后一位并抛出
				lastReturned = unseen & -unseen;
				unseen -= lastReturned;
				return (E) universe[Long.numberOfTrailingZeros(lastReturned)];
			}

			public void remove() {
				if (lastReturned == 0)
					throw new IllegalStateException();
				elements &= ~lastReturned;
				lastReturned = 0;
			}
		}

		```

	- `int size()`

		```java
		return Long.bitCount(elements);

		public static int bitCount(long i) {
			// 每格两位存储这两位的 1 的数量，等价于 i= i & 0x5555555555555555L + (i>>>1) & 0x5555555555555555L
			i = i - ((i >>> 1) & 0x5555555555555555L);
			// 此后每次翻倍移位，进行数量累加
			i = (i & 0x3333333333333333L) + ((i >>> 2) & 0x3333333333333333L);
			i = (i + (i >>> 4)) & 0x0f0f0f0f0f0f0f0fL;
			i = i + (i >>> 8);
			i = i + (i >>> 16);
			i = i + (i >>> 32);
			return (int)i & 0x7f;
		}
		```

	- `boolean contains(Object e)`
		
		```java
		return (elements & (1L << ((Enum<?>)e).ordinal())) != 0;
		
		/***
		elements	=	0000 1111
		e.ordinal	=	3
		1L<<3		=	0000 0100
		elements&(1L<<3)= 
			0000 1111
		&	0000 0100
			0000 0100	= 4	!=	0
			*指定位比较，其它位清零*
		*/
		```
	- `boolean add(E e)`
		- `elements |= (1L << ((Enum<?>)e).ordinal())`
	- `boolean remove(Object e)`
		- `elements &= ~(1L << ((Enum<?>)e).ordinal())`


## TreeSet

- description
	- 基于 `TreeMap` 的对 `NavigableSet` 的实现
	- `add`、`remove`、`contains` 均可在 `log(n)` 时间内完成
	- `Set` 的比较通过 `TreeSet` 的 `compareTo ` 实现
	- 非同步实现，须于外部进行同步扩展
		- `SortedSet s = Collections.synchronizedSortedSet(new TreeSet(...));`
	- 迭代器在操作时碰到修改，会快速失败以避免数据风险
- params
	- `transient NavigableMap<E,Object> m`
	- `static final Object PRESENT = new Object()`
- methods

	- `TreeSet([|NavigableMap|Comparator|Comparator|SortedSet])`



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
	- 不包含重复元素
	- 元素为可变对象时，需小心元素的变更，影响到集合中对象的 `equals` 判断
		- 禁止将自己作为元素加入到集合中
		- 通过抛出 `NullPointerException`、`ClassCastException` 异常或 `false` 来限制填充元素
- methods
	- `int size()`
	- `boolean contains(Object o)`
	- `boolean add(E e)`
	- `boolean remove(Object o)`
	- `Iterator<E> iterator()`
	- `Object[] toArray()`
	- `boolean (contains|add|retain|remove)All(Collection<?> c)`
	- `void clear()`
	- `boolean equals(Object o)`
	- `int hashCode()`



## SortedSet
- description
	- 迭代器按升序遍历
	- 所有元素须实现 `Comparable` 接口，相互比较
	- 默认四种标准构建函数
		- `SortedSet()`
			- 空构建函数，创建空的排序集合
		- `SortedSet(Comparator comparator)`
			- 仅提供比较器，并创建以此判断的空集合
		- `SortedSet(Collection coleection)`
			- 将集合的元素通过原始排序初始化成新的集合
		- `SortedSet(SortedSet set)`
			- 拷贝传入的排序器
- params
	- `Comparator<? super E> comparator()`
- methods
	- `SortedSet<E> subSet(E fromElement, E toElement)`
	- `SortedSet<E> (head|tail)Set(E toElement)`
	- `E (first|last)()`

## NavigableSet

- description
	- 支持搜索、倒序输出，并可指定子集的上下边界是否包含
	- 提供最接近匹配的搜索匹配
	- 升序操作会比降序操作更快
	- 允许添加 `null`，但鼓励实现时限制，以避免混淆不清
- methods
	- `E lower(E e);`
	- `E floor(E e);`
	- `E ceiling(E e);`
	- `E higher(E e);`
	- `E pollFirst();`
	- `E pollLast();`


 ## ServiceLoader


## Map

- description
	- 用于映射键值对，不允许重复的键值
	- 提供三种视图来查看映射内容：键的集合，值的列表和键值对的映射集合
	- 极度小心键值为可变对象的情况，例如严禁将映射自己做为键值，但允许将自身做为值存储
		- 对象的变化会影响到 `equals` 和 `hashcode` 的计算
- methods
	- Query
		- `int size()`
		- `boolean containsKey(Object key)`
		- `boolean containsValue(Object value)`
		- `V get(Object key)`

	- Modification		
		- `V put(K key, V value)`
		- `V remove(Object key)`

	- Bulk
		- `void putAll(Map<? extends K, ? extends V> m)`
		- `void clear()`

	- Views
		- `Set<K> keySet()`
		- `Collection<V> values()`
		- `Set<Map.Entry<K, V>> entrySet()`

	- Comparison
		- `boolean equals(Object o)`
		- `int hashCode()`

	- Default
		- `default V getOrDefault(Object key, V defaultValue)`
		- `default void forEach(BiConsumer<? super K, ? super V> action)`
		- `default void replaceAll(BiFunction<? super K, ? super V, ? extends V> function) `
		- `default V putIfAbsent(K key, V value)`
		- `default boolean remove(Object key, Object value)`
			- 仅当保存值为指定值时移除指定键值
		- `default boolean replace(K key, V oldValue, V newValue)`
			- 仅当指定键对应值为指定值时，覆盖以新值
		- `default V replace(K key, V value)`
		- `default V computeIf(Absent|Present)(K key, Function<? super K, ? extends V> mappingFunction)`
			- 仅当键值（不存在|存在）情况下，覆盖以新计算值


## SortedMap

- description
	- 进一步在键值上进行整体性的排序
	- 建议实现的构建函数
		- `SortedMap()`
		- `SortedMap(Comparator)`
		- `SortedMap(Map)`
		- `SortedMap(SortedMap)`

- methods
	- Sub
		- `SortedMap<K,V> subMap(K fromKey, K toKey)`
			- [fromKey,toKey)
		- `SortedMap<K,V> headMap(K toKey)`
			- [,toKey)
		- `SortedMap<K,V> tailMap(K fromKey)`
			- [fromKey,]
	- Query
		- `K firstKey()`
		- `K lastKey()`
	- View
		- `Set<K> keySet()`
		- `Collection<V> values()`
		- `Set<Map.Entry<K, V>> entrySet()`


## NavigableMap

- description
	- 在 `SortedSet` 的基础上扩展导航、搜索功能
	- 支持正反向查询、遍历，但升序速度更快
	- 新增 `pollFirst` 和 `pollLast` 方法，并可通过参数获取指定上下限数据
	- 建议不允许添加 `null`

- methods
	- Query
		- `E lower(E e)`
			- `<`
		- `E floor(E e)`
			- `<=`
		- `E ceiling(E e)`
			- `>=`
		- `E higher(E e)`
			- `>`
		- `E pollFirst()`
		- `E pollLast()`

	- View
		- `Iterator<E> iterator()`
		- `NavigableSet<E> descendingSet()`
		- `Iterator<E> descendingIterator()`
	- SubSet	
		- `NavigableSet<E> subSet(E fromElement, boolean fromInclusive, E toElement,   boolean toInclusive)`
		- `NavigableSet<E> headSet(E toElement, boolean inclusive)`
		- `NavigableSet<E> tailSet(E fromElement, boolean inclusive)`
		- `SortedSet<E> subSet(E fromElement, E toElement)`
		- `SortedSet<E> headSet(E toElement)`
		- `SortedSet<E> tailSet(E fromElement)`

## Abstractmap

- description
- params
- methods

## WeakHashMap

- description
- params
- methods

## IdentityHashMap

- description
- params
- methods

## EnumMap

- description
- params
- methods

## TreeMap

- description
	- 基于 `NavigableMap` 的红黑树实现，键值根据指定比较器排序
	- 红黑树算法保障了 `containsKey`、`get`、`put` 和 `remove` 的 `long(n)` 的复杂度
	- 指定的比较器需支持相等判定
	- 不支持同步操作，但可通过 `SortedMap m = Collections.synchronizedSortedMap(new TreeMap(...));` 支持
	- `iterator` 生成时遵从快速失败原则
- params
	final Comparator<? super K> comparator
	transient Entry<K,V> root
	transient int size = 0
- methods
	- Constructor
		- `TreeMap()`
		- `TreeMap(Comparator<? super K> comparator)`
		- `TreeMap(Map<? extends K, ? extends V> m)`
		- `TreeMap(SortedMap<K, ? extends V> m)`




## HashMap

- description
- params
- methods

## LinkedHashMap

- description
- params
- methods

## Dictionary

- description
- params
- methods

## Hashtable

- description
- params
- methods

## Properties

- description
- params
- methods

## Utils

- description
- params
- methods

## Collections

- description
- params
- methods

## Arrays

- description
- params
- methods





# Reference
## 官方
- [15.19. Shift Operators](https://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.19)
- []()
- []()
- []()

## 补充
- [ArrayList中elementData为什么被transient修饰？](https://blog.csdn.net/zero__007/article/details/52166306)
- [rrayDeque集合的妙用](http://cakin24.iteye.com/blog/2324030)
- [JDK源码解读之RegularEnumSet](https://blog.csdn.net/java_4_ever/article/details/42263297)
- []()
- []()