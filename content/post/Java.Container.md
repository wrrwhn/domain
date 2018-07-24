---
title: "Java.Container"
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
- ![java.container.png](http://otzm88f21.bkt.clouddn.com/32445d0d-3135-4eb3-a0f7-2217ff29d269.png)

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
			- AbstractSequentiaList
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

- params
- methods

## ArrayList

- description
- params
- methods

## SubList

- description
- params
- methods

## RandomAccessSubList

- description
- params
- methods


## AbstractSequentiaList

- description
- params
- methods


## LinkedList
## ArrayDeque
## AbstractQueue
## PriorityQueue
## AbstractSet
## EnumSet
## RegularEnumSet
## JumboEnumSet
## TreeSet
## HashSet
## LinkedHashSet
## Queue
## Set
## SortedSet
## NavigableSet

    
# Reference
- []()
- []()
- []()
- []()
- []()
- []()