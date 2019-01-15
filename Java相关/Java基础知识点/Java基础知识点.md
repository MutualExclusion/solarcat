# Java基础 #
## JAVA面向对象的三大特性 ##
### 封装、继承、多态是面向对象的三大特性 ###
### 封装 ###
#### 1 什么是封装？ ####
&emsp;&emsp;**封装就是将属性私有化，提供公有的方法访问私有属性。**做法就是：修改属性的可见性来限制对属性的访问，并为每个属性创建一对取值（ getter ）方法和赋值（ setter ）方法，用于对这些属性的访问。如： private String name;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

#### 2 为什么需要封装？ ####
&emsp;&emsp;通过封装，可以实现对属性的数据访问限制，同时增加了程序的可维护性。由于取值方法和赋值方法隐藏了实现的变更，因此并不会影响读取或修改该属性的类，避免了大规模的修改，程序的可维护性增强。
### 继承 ###
#### 1 什么是继承？ ####
&emsp;&emsp;继承就是子类继承父类的特征和行为，使得子类对象（实例）具有父类的实例域和方法，或子类从父类继承方法，使得子类具有父类相同的行为。
### 多态 ###
## 值传递和引用传递 ##
###  ###
-	值传递：方法调用时，实际参数把它的值传递给对应的形式参数，形式参数只是用实际参数的值初始化自己的存储单元内容，是两个不同的存储单元。所以方法执行中形式参数值的改变不会导致实际参数值的改变。

-	引用传递：方法调用时，实际参数是对象(或数组)，这时实际参数与形式参数指向同一个地址，在方法执行中，对形式参数的操作实际上就是对实际参数的操作，这个结果在方法结束后被保留了下来，所以方法执行中形式参数的改变将会导致实际参数的改变。
### 说明 ###

**(1)：“在Java里面参数传递都是按值传递”这句话的意思是：按值传递是传递的值的拷贝，按引用传递其实传递的是引用的地址值，所以统称按值传递。**

**(2)：在Java里面只有基本类型和按照下面这种定义方式的String是按值传递，其它的都是按引用传递。就是直接使用双引号定义字符串方式：String str = “Java”。**
#### 至此	*MutualExclusion 12/27/2018 5:46:47 PM* ####
## fast-fail失败机制 ##
### 前言 ###
&emsp;&emsp;首先，先来看一段代码。

	public class FastFail {
		public static void main(String[] args) {
			List<Integer> list = new ArrayList<>();
	
			list.add(1);
			list.add(2);
			list.add(3);
			list.add(4);
			list.add(5);
	
			Iterator<Integer> iterator = list.iterator();
			while (iterator.hasNext()) {
				Integer integer = iterator.next();
				if (integer == 1)
					list.remove(integer); // 注意这个地方
			}
		}
	
	}

&emsp;&emsp;该段代码看起来并没有什么问题，但是运行起来会产生错误，如下:

	Exception in thread "main" java.util.ConcurrentModificationException
		at java.util.ArrayList$Itr.checkForComodification(Unknown Source)
		at java.util.ArrayList$Itr.next(Unknown Source)
		at com.solarcat.FastFail.main(FastFail.java:18)
&emsp;&emsp;由此异常信息可知，异常出现在`ArrayList`内部类Itr的`checkForComodification`方法中。

	private class Itr implements Iterator<E> {
        int cursor;       // index of next element to return
        int lastRet = -1; // index of last element returned; -1 if no such
        int expectedModCount = modCount;

        Itr() {}

        public boolean hasNext() {
            return cursor != size;
        }

        @SuppressWarnings("unchecked")
        public E next() {
            checkForComodification();
            int i = cursor;
            if (i >= size)
                throw new NoSuchElementException();
            Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length)
                throw new ConcurrentModificationException();
            cursor = i + 1;
            return (E) elementData[lastRet = i];
        }

        public void remove() {
            if (lastRet < 0)
                throw new IllegalStateException();
            checkForComodification();

            try {
                ArrayList.this.remove(lastRet);
                cursor = lastRet;
                lastRet = -1;
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }

        @Override
        @SuppressWarnings("unchecked")
        public void forEachRemaining(Consumer<? super E> consumer) {
            Objects.requireNonNull(consumer);
            final int size = ArrayList.this.size;
            int i = cursor;
            if (i >= size) {
                return;
            }
            final Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length) {
                throw new ConcurrentModificationException();
            }
            while (i != size && modCount == expectedModCount) {
                consumer.accept((E) elementData[i++]);
            }
            // update once at end of iteration to reduce heap write traffic
            cursor = i;
            lastRet = i - 1;
            checkForComodification();
        }

        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }
### 发现 ###
&emsp;&emsp;其中

|属性名|说明|
|:-|:-|
|modCount|表示对集合的操作次数，在集合的添加和移除元素的方法中可以看到modCount++|
|cursor|表示下一个要访问的元素的索引|
|lastRet|表示上一个要访问的元素的索引|
|expectedModCount|表示对ArrayList修改次数的期望值，它的初始值为modCount|
&emsp;&emsp;通过函数`checkForComodificatio`中可以看出，如果**对集合的操作数不等于期望的操作数**，就会抛出此异常。那么再来捋顺一下过程：

-	1.使用集合内部迭代器进行迭代。
-	2.`next`元素后判断是否等于1，等于1则移除元素。
-	3.调用`list`的`remove`移除元素后`modCount++`。
-	4.`Itr`内部迭代器的`expetedModCount`期望的修改次数与实际操作次数`modCount`不符，抛出此异常。
### 说明 ###
关键点就在于：调用`list.remove()`方法导致`modCount`和`expectedModCount`的值不一致。注意，像使用`for-each`进行迭代实际上也会出现这种问题。在单线程的情况下，我们可以使用集合内部迭代器的remove方法。如下：

	public class FastFail {
		public static void main(String[] args) {
			List<Integer> list = new ArrayList<>();
	
			list.add(1);
			list.add(2);
			list.add(3);
			list.add(4);
			list.add(5);
	
			Iterator<Integer> iterator = list.iterator();
			while (iterator.hasNext()) {
				Integer integer = iterator.next();
				if (integer == 2) {
					iterator.remove(); // 注意这个地方
				}
			}
			for (Integer integer : list) {
				System.out.println(integer);
			}
	
		}
	
	}

&emsp;&emsp;控制台打印

	1
	3
	4
	5

&emsp;&emsp;多线程情况下使用两种解决办法：
	
-	1）在使用iterator迭代的时候使用synchronized或者Lock进行同步；
-	2）使用并发容器CopyOnWriteArrayList代替ArrayList和Vector。

#### 至此	*MutualExclusion 1/3/2019 12:16:58 PM* ####