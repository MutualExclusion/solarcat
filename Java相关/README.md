# Java相关

## Java基础 ##
-	[值传递和引用传递](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java基础知识点/Java基础知识点.md#值传递和引用传递)
-	[fast-fail失败机制](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java基础知识点/Java基础知识点.md#fast-fail失败机制)
## Java源码系列 ##
-	[JAVA源码系列--HashMap](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java源码系列/JAVA源码系列--HashMap.md "JAVA源码系列--HashMap")
-	[JAVA源码系列--Hashtable](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java源码系列/JAVA源码系列--Hashtable.md "JAVA源码系列--Hashtable")
## Java并发 ##
### 基础知识 ###
-	#### 1.1 [并发编程的优缺点](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/并发编程的优缺点.md) ####
	-	[为什么要用到并发？（优点）](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/并发编程的优缺点.md#1-为什么要用到并发)
	-	[并发编程的缺点;](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/并发编程的优缺点.md#2-并发编程有哪些缺点)
	-	[易混淆的概念;](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/并发编程的优缺点.md#3-应该了解的概念)
-	#### 1.2 [线程的状态和基本操作](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/线程的状态转换以及基本操作.md) ####
	-	[如何新建线程;](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/线程的状态转换以及基本操作.md#1-新建线程)
	-	[线程状态的转换;](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/线程的状态转换以及基本操作.md#2-线程状态转换)
	-	[线程的基本操作;](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/线程的状态转换以及基本操作.md#3-线程状态的基本操作)
	-	[守护线程Daemon;](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/线程的状态转换以及基本操作.md#4守护线程daemon)
### 并发理论（JMM）###
-	#### [java内存模型以及happens-before规则](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/java内存模型以及happens-before规则.md) ####
	-	[JMM内存结构;](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/java内存模型以及happens-before规则.md#2-内存模型抽象结构)
	-	[重排序;](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/java内存模型以及happens-before规则.md#3-重排序)
	-	[happens-before规则;](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/java内存模型以及happens-before规则.md#4-happens-before规则)
### 并发关键字 ###
-	#### 3.1 [让你彻底理解Synchronized](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/让你彻底理解Synchronized.md) ####
	-	[如何使用synchronized](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/让你彻底理解Synchronized.md#2-synchronized实现原理);
	-	[monitor机制](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/让你彻底理解Synchronized.md#21-对象锁monitor机制);
	-	[synchronized的happens-before关系](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/让你彻底理解Synchronized.md#22-synchronized的happens-before关系);
	-	[synchronized的内存语义](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/让你彻底理解Synchronized.md#23-锁获取和锁释放的内存语义);

-	#### 3.2 [让你彻底理解volatile](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/让你彻底理解volatile.md) ####
	-	[实现原理](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/让你彻底理解volatile.md#2-volatile实现原理);
	-	[happens-before的关系推导](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/让你彻底理解volatile.md#3-volatile的happens-before关系);
	-	[内存语义](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/让你彻底理解volatile.md#4-volatile的内存语义);
	-	[内存语义的实现](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/让你彻底理解volatile.md#41-volatile的内存语义实现);

-	#### 3.3 [你以为你真的了解final吗？](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/你以为你真的了解final吗？.md) ####
	-	[如何使用](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/你以为你真的了解final吗？.md#2-final的具体使用场景);
	-	[final的重排序规则](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/你以为你真的了解final吗？.md#41-final域重排序规则);
	-	[final实现原理](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/你以为你真的了解final吗？.md#5-final的实现原理);
	-	[final引用不能从构造函数中“溢出”（this逃逸）](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/Java并发系列/你以为你真的了解final吗？.md#6-为什么final引用不能从构造函数中溢出);

-	#### 3.4 三大性质总结：原子性，有序性，可见性 ####
	-	原子性：synchronized;
	-	可见性：synchronized，volatile;
	-	有序性：synchronized，volatile;
### Lock体系 ###
-	#### 4.1 初识Lock与AbstractQueuedSynchronizer(AQS) ####
	-	Lock和synchronized的比较;
	-	AQS设计意图;
	-	如何使用AQS实现自定义同步组件;
	-	可重写的方法;
	-	AQS提供的模板方法;

-	#### 4.2 深入理解AbstractQueuedSynchronizer(AQS) ####
	-	AQS同步队列的数据结构;
	-	独占式锁;
	-	共享式锁;
-	#### 4.3 再一次理解ReentrantLock ####
	-	重入锁的实现原理;
	-	公平锁的实现原理;
	-	非公平锁的实现原理;
	-	公平锁和非公平锁的比较;
-	#### 4.4 深入理解读写锁ReentrantReadWriteLock ####
	-	如何表示读写状态;
	-	WriteLock的获取和释放;
	-	ReadLock的获取和释放;
	-	锁降级策略;
	-	生成Condition等待队列;
	-	应用场景;
-	#### 4.5详解Condition的await和signal等待/通知机制 ####
	-	与Object的wait/notify机制相比具有的特性;
	-	与Object的wait/notify相对应的方法;
	-	底层数据结构;
	-	await实现原理;
	-	signal/signalAll实现原理;
	-	await和signal/signalAll的结合使用;
-	#### 4.6 LockSupport工具 ####
	-	主要功能;
	-	与synchronized阻塞唤醒相比具有的特色;
## 深入理解JVM ##
-	### [深入JVM总结图](https://github.com/MutualExclusion/solarcat/blob/master/Java相关/深入理解JVM/深入理解Java虚拟机总结.md) ###

#### 至此	*MutualExclusion 12/27/2018 5:46:47 PM* ####