# 深入理解Java虚拟机——虚拟机类加载机制 #
## 前言 ##
&emsp;&emsp;Java虚拟机会把描述类的数据从Class文件加载到内存，并对数据进行校验、转换解析和初始化，最终形成可以被虚拟机直接使用的Java类型，这就是虚拟机的类加载机制。如图所示:

![类的生命周期]()

&emsp;&emsp;而类加载的全过程包括加载、验证、准备、解析、初始化,在这里不在赘述。

## 类加载器 ##
   
判断两个类是否相等，只有在这两个类由同一个类加载器加载的前提下才有意义。

-	从Java虚拟机的角度讲：
	-	有两种类加载器。一种是启动类加载器，使用C++语言实现，是虚拟机自身的一部分。另一种是其他的类加载器，由Java实现，独立于虚拟机。并且全都继承自抽象类`java.lang.ClassLoader`。

-	从Java开发人员的角度来讲



其中，自定义classLoader的使用场景：加密、热加载。

在这里说明一下，除了顶层的启动类加载器外，其余的类加载器都应当有自己的父类加载器，这里的类加载器之间的父子关系不是以继承的关系来实现的，而是以组合关系来复用父加载器的代码。如图所示，其余的类加载器的实际继承关系。




双亲委任/委派：

保证所有的类都被加载一次，避免重复加载。被加载过的类不能重复加载，否则运行出错。这实际上这是一种安全机制。

双亲委派的工作过程：一个类加载器每次加载类的时候，它不会自己试图加载，而是委托给其父类加载器加载，因为每一个非启动类加载器都有父类加载器，所以递归的，最终这个类都会由启动类加载器首先试图加载，如果加载不成功，则由这条加载器链的下一个加载器试图加载，以此类推，直到加载成功或到最后加载失败。如图所示：



具体实现：

	protected Class<?> loadClass(String name, boolean resolve)
        throws ClassNotFoundException
    {
        synchronized (getClassLoadingLock(name)) {
            /**
             * 最先，会查找该类是否已经被加载过
             * 这里的查找是native方法实现，其实现逻辑是同一个全名称类+同
             * 一个加载器方为同一个类，方为该类已加载
             */
            Class c = findLoadedClass(name);
            if (c == null) {//如果未被加载
                long t0 = System.nanoTime();
                try {
                    if (parent != null) {//如果存在父类，则由父类加载
                        c = parent.loadClass(name, false);//父类递归加载
                    } else {//不存在父类，则是启动类加载器加载
                        c = findBootstrapClassOrNull(name);
                    }
                } catch (ClassNotFoundException e) {
                //父类无法加载，这里ClassNotFoundException异常由最末加载器抛出
                }
 
                if (c == null) {
                    //父加载器无法加载，则由自己试图加载
                    long t1 = System.nanoTime();
                    c = findClass(name);
 
                    // 记录加载信息
                    sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                    sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                    sun.misc.PerfCounter.getFindClasses().increment();
                }
            }
            if (resolve) {
                resolveClass(c);
            }
            return c;//记住，这里是自下而上（加载器链）试图加载的，上面不行，接着
            //由下面加载器加载，直到最末
        }
    }
