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
	- **排序**数据，允许**重复**或**空**
- methods
	- operate
	    - get(int index)
	    - set(int index, E element)
	    - add(int index, E element)
	    - remove(int index)
	    - **indexOf(Object o)**
	    - lastIndexOf(Object o)
	    - containsAll(Collection<?> c)
	    - addAll(int index, Collection<? extends E> c)
	    - **default void replaceAll(UnaryOperator<E> operator)**
	    - **sort(Comparator<? super E> c)**
	- stream
	    - **subList(int fromIndex, int toIndex)**


## AbstractCollection
- description
    - 最小接口框架
- methods
    - abstract Iterator<E> iterator()
    - abstract int size()
    - add(E e) {throw new UnsupportedOperationException();}

## AbstractList
- description
    - 随机存取的最小框架
    - 不强制要求提供 `iterator` 实现
- methods
    - subList(int fromIndex, int toIndex)
    - removeRange(int fromIndex, int toIndex)


## Vector
- description
    - 
- params
    - 
- methods
    - 
    - 
    - 


    
# Reference
- []()
- []()
- []()
- []()
- []()
- []()