# Java虚拟机

![1552356115849](F:\mine\java虚拟机\%5CUsers%5CAdministrator%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1552356115849.png)

### 程序计数器（Program Counter Register）

~~~
线程私有
一块较小的内存空间，可以看作是当前线程所执行的字节码的行号指示器。（Java虚拟机中唯一一个没有规定任何OutOfMemoryError情况的区域）
~~~

### Java虚拟机栈（Java Virtual Machine  Stacks）

~~~
线程私有
方法执行的内存模型	每个方法在执行的同时都会创建一个栈帧（Stack Frame）用于存储局部变量表、操作数栈、动态链接、方法出口等信息。每个方法从调用直至执行完成的过程，就对应着一个栈帧在虚拟机栈中入栈到出栈的过程。
	两种异常状况：如果线程请求的栈深度大于虚拟机所允许的深度，将抛出StackOverFlowError异常：如果虚拟机栈可以动态扩展（大部分Java虚拟机可动态扩展，只不过Java虚拟机规范中也允许固定长度的虚拟机栈），若果扩展式无法申请到足够的内存，就会抛出OutOfMemoryError异常。
~~~

### 本地方法栈（Native Method Stack）

~~~
	线程私有
	与虚拟机栈所发挥的作用是非常相似的，它们之间的区别是虚拟机栈为虚拟机执行Java方法（也就是字节码）服务，而本地方法栈则为虚拟机使用到Native方法服务。
	会抛出StackOverFlowError和OutOfMemoryError异常。
~~~

### Java堆（heap）

~~~
	线程共享
	是Java虚拟机所管理的内存中最大的一块。（新生代和老年代；Eden空间、From Survivor空间，To Survivor空间）
	Java堆可以处于物理上不连续的内存空间中，只要逻辑上是连续的即可。大小可固定也可动态扩展（-Xmx和-Xms控制）。
	会抛出OutOfMemoryError异常。
~~~

### 方法区（Method Area）

~~~
	线程共享
	用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。
	别名Non-Heap（非堆），目的是与Java堆区分开。
	HotSpot虚拟机把防范去称为“永久代（Permanent Generation）”，-XX:MaxPermSize
	会抛出OutOfMemoryError异常。
~~~

#### 运行时常量池（Runtime Constant pool）

~~~
	方法区的一部分
	Class文件中除了有类的版本、字段、方法、接口等描述信息外，还有一项信息是常量池（Constant Pool Table），用于存放编译期生成的字面量和符号引用，这部分内容将在类加载后进入方法区的运行时常量池中存放。
	会抛出OutOfMemoryError异常。
~~~

### 直接内存（Native Memory）

~~~
	直接内存（Direct Memory）并不是虚拟机运行时数据区的一部分，也不是Java
虚拟机规范中定义的内存区域。
	会抛出OutOfMemoryError异常。
	NIO（New Input/Output）类，引入了一种基于通道（Channel）与缓冲区（Buffer）的I/O方式，它可以使用Native函数库直接分配堆外内存，然后通过一个存储在Java堆中的DirectByteBuffer对象作为这块内存的引用进行操作。这样能在一些场景中显著提高性能，因为避免了在Java堆和Native堆中来回复制数据。
~~~



对象内存分配方式：“指针碰撞（Bump the Pointer）”和“空闲列表（Free List）”

一种是对分配内存空间的动作进行同步处理——实际上虚拟机采用CAS配上失败重试的方式保证更新操作的原子性；另一种是把内存分配的动作按照线程划分在不同的空间之
中进行，即每个线程在Java堆中预先分配一小块内存，称为本地线程分配缓冲**（Thread Local Allocation Buffer,TLAB）**。

**虚拟机是否使用TLAB，可以通过-XX:+/-UseTLAB参数来设定。**

~~~
HotSpot虚拟机中，对象在内存中存储的布局可以分为3部分：对象头（header）、实例数据（Instance Data）、对其填充（Padding）
~~~

### 对象的访问定位

~~~
	两种：句柄和指针
~~~

使用句柄访问的话，那么Java堆中将会划分出一块内存来作为句柄池，reference中存储的就是对象的句柄地址，而句柄中包含了对象实例数据与类型数据各自的具体地址信息。

![1552454443090](F:\mine\java虚拟机\%5CUsers%5CAdministrator%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1552454443090.png)

### 垃圾回收

~~~
1.引用计数器（Reference Counting）--判断对象的引用数量
2.可达性分析（Reachability Analysis）--判断对象的引用链是否可达，判定对象是否存活都与“引用”有关。
	在Java语言中，可作为GC Roots的对象包括下面几种：
		虚拟机栈（栈帧中的本地变量表）中引用的对象。
		方法区中类静态属性引用的对象。
		方法区中常量引用的对象。
		本地方法栈中JNI（即一般说的Native方法）引用的对象。
~~~

![1552466067889](F:\mine\java虚拟机\%5CUsers%5CAdministrator%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1552466067889.png)

### 应用

~~~
	强引用（Strong Reference），类似“Object obj=new Object()”
	软引用（Soft Reference）（用来描述一些还有用但并非必需的对象。对于软引用关联着的对象，在系统将要发生内存溢出异常之前，将会把这些对象列进回收范围之中进行第二次回收。如果这次回收还没有足够的内存，才会抛出内存溢出异常。）
	弱引用（weak Reference）
	虚引用（Phantom Reference）引用强度逐步递减
~~~

### 垃圾收集算法

~~~
1.“标记-清除”（Mark-Sweep）算法
	分为“标记”和“清除”两个阶段：首先标记出所有需要回收的对象，在标记完成后统一回收所有被标记的对象，它的标记过程其实在前一节讲述对象标记判定时已经介绍过了。之所以说它是最基础的收集算法，是因为后续的收集算法都是基于这种思路并对其不足进行改进而得到的。它的主要不足有两个：一个是效率问题，标记和清除两个过程的效率都不高；另一个是空间问题，标记清除之后会产生大量不连续的内存碎片，空间碎片太多可能会导致以后在程序运行过程中需要分配较大对象时，无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作。
~~~

![1552526215690](F:\mine\java虚拟机\%5CUsers%5CAdministrator%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1552526215690.png)

~~~
2.复制（Copying）算法
	它将可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活着的对象复制到另外一块上面，然后再把已使用过的内存空间一次清理掉。这样使得每次都是对整个半区进行内存回收，内存分配时也就不用考虑内存碎片等复杂情况，只要移动堆顶指针，按顺序分配内存即可，实现简单，运行高效。只是这种算法的代价是将内存缩小为了原来的一半，未免太高了一点.
~~~

![1552526371671](F:\mine\java虚拟机\%5CUsers%5CAdministrator%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1552526371671.png)

~~~
3.“标记-整理”（Mark-Compact）算法
	标记过程仍然与“标记-清除”算法一样，但后续步骤不是直接对可回收对象进行清理，而是让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存，“标记-整理”算法的示意图如图3-4所示。
~~~

![1552527150266](F:\mine\java虚拟机\%5CUsers%5CAdministrator%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1552527150266.png)

~~~
4.分代收集（Generational Collection）算法
	根据对象存活周期的不同将内存划分为几块。一般是把Java堆分为新生代和老年代，这样就可以根据各个年代的特点采用最适当的收集算法。在新生代中，每次垃圾收集时都发现有大批对象死去，只有少量存活，那就选用复制算法，只需要付出少量存活对象的复制成本就可以完成收集。而老年代中因为对象存活率高、没有额外空间对它进行分配担保，就必须使用“标记—清理”或者“标记—整理”算法来进行回收。
~~~

### 垃圾收集器

![1552530816391](F:\mine\java虚拟机\%5CUsers%5CAdministrator%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1552530816391.png)

#### Serial、Serial Old

~~~
	特点：单线程
~~~

![1552533216258](F:\mine\java虚拟机\%5CUsers%5CAdministrator%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1552533216258.png)

#### Parallel Scavenge/Parallel Old

~~~
	特点：多线程，吞吐量优先
~~~

![1552534109287](F:\mine\java虚拟机\%5CUsers%5CAdministrator%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1552534109287.png)

#### CMS（Concurrent Mark Sweep）收集器

~~~
是一种以获取最短回收停顿时间为目标的收集器。
初始标记（CMS initial mark）
并发标记（CMS concurrent mark）
重新标记（CMS remark）
并发清除（CMS concurrent sweep）
	其中，初始标记、重新标记这两个步骤仍然需要“Stop The World”。初始标记仅仅只是标记一下GC Roots能直接关联到的对象，速度很快，并发标记阶段就是进行
GC RootsTracing的过程，而重新标记阶段则是为了修正并发标记期间因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，这个阶段的停顿时间一般会比初始标记阶段稍长一些，但远比并发标记的时间短。

优点：并发收集、低停顿，Sun公司的一些官方文档中也称之为并发低停顿收集器（ConcurrentLow Pause Collector）
缺点：
	1.CMS收集器对CPU资源非常敏感。在并发阶段，它虽然不会导致用户线程停顿，但是会因为占用了一部分线程（或者说CPU资源）而导致应用程序变慢，总吞吐量会降低。
	2.CMS收集器无法处理浮动垃圾（Floating Garbage），可能出现“Concurrent Mode Failure”失败而导致另一次Full GC的产生。由于CMS并发清理阶段用户线程还在运行着，伴随程序运行自然就还会有新的垃圾不断产生，这一部分垃圾出现在标记过程之后，CMS无法在当次收集中处理掉它们，只好留待下一次GC时再清理掉。这一部分垃圾就称为“浮动垃圾”。
-XX:CMSInitiatingOccupancyFraction（CMS收集器的启动阈值）
	3.“标记—清除”算法实现的收集器,会产生大量空间碎片，往往会出现老年代还有很大空间剩余，但是无法找到足够大的连续空间来分配当前对象，不得不提前触发一次Full GC。
-XX:+UseCMSCompactAtFullCollection开启碎片整理
-XX:CMSFullGCsBeforeCompaction设置执行多少次不压缩的Full GC后，跟着来一次带压缩

~~~

![1552540511955](F:\mine\java虚拟机\%5CUsers%5CAdministrator%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1552540511955.png)

### G1收集器（Garbage-First）

~~~

~~~

### 垃圾收集器参数总结

| 参数                   | 描述                                                         |
| ---------------------- | ------------------------------------------------------------ |
| UseSerialGC            | 虚拟机运行在Client模式下的默认值，打开此开关后，使用Serial+Serial Old的收集器组合进行内存回收 |
| UseParNewGC            | 打开此开关后，使用ParNew+Serial Old的收集器组合进行内存回收  |
| UseParallelGC          | 虚拟机运行在Server模式下的默认值，打开此开关后，使用Parallel Scavenge+Serial Old（PSMarkSweep）的收集器组合进行内存回收 |
| UseParallelOldGC       | 打开此开关后，使用Parallel Scavenge+Parallel Old的收集器组合进行内存回收 |
| SurvivorRatio          | 新生代中的Eden区域与Survivor区域的容量比值，默认为8，代表Eden：Survivor=8:1 |
| PretenureSizeThreshold | 直接晋升到老年代的对象大小，设置这个参数后，大于这个参数的对象直接在老年代分配 |
| MaxTenuringThreshold   | 晋升到老年代的对象年龄。每个对象在坚持过一次Minor GC之后，年龄就增加1，当超过这个参数值时就进入老年代。 |
|                        |                                                              |
|                        |                                                              |

