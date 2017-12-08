

+++
date = "2017-11-16T22:07:46+08:00"
title = "Java.泛型"
draft = false
tags = ["整理","Java"]
share = true
+++

[TOC]

# 泛型
## 与 C++ 的比较
- 优点
	- 泛型实现`参数化类型`
	- 创建泛型的一个实例时，编译器负责转型并保证类型的正确性
- 缺点
	- 其它语言能做、好做的，java 中却不支持，难以实现
- 注
	- 理解泛型的边界！
## 简单泛型
- 告诉编译器想使用类型，然后由编译器帮你处理一切细节
- 元组：一组对象打包存储于一单一对象
## 泛型接口
- 事例
	```
	public interface Generator<T> {
	    T next();
	}
	```
- 基本类型无法作为类型参数
## 泛型方法
- 事例
	```
	public <T> void printClass(T t) {
        System.out.println(t.getClass().getName());
    }
	```
- 类型推断只对赋值操作有效
## 泛型内部类
## 构建复杂模型
- 类型安全且可管理
## 擦除的神秘之处
- 在泛型代码内部，无法获得任何有关泛型参数类型的信息
- **T extends class/interface**
- 于静态类型检查期后，所有泛型类型均被擦除，并替换为其非泛型上界
	- `List<T> -> List`
	- `<T> -> Object`
- 使用擦除实现，唯一知道是在使用一个对象
	- `List<String>= List<Object>= List`
	- `List<Integer>= List<Object>= List`
- 与 C++ 对比
	```
		template<Class T> class Manipulator{
			T obj;
			public:
				Manipulator(T t){obj=t;}
				void manipulator(){**obj.f();**}	
					// 实例时，检查是否有f()方法，如果没有则返回编译期错误
					// java 则是编译不通过
						// 但可通过 T extends XXX 来限定
		}
	```
- 原因
	- 从非泛化代码转变为泛化代码过程中，在不破坏现有类库条件下，通过类型擦除来兼容
- 缺点
	- 转型|instanceof|new 等均无法显式调用
- 擦除在方法体中移除了类型信息，即在运行时的问题就是边界（对象进入和离开方法的地点）
## 擦除的补偿
## 边界
- class Solid<T extends Dimension & HasColor & Weight>
- class Bounded extends Dimension implements HasColor, Weight