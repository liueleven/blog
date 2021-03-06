
# 深入理解JVM

更新于2018-07-22

### 前言
本笔记是我看深入理解JVM一书做的学习笔记，旨在了解jvm原理，反馈下自己，对于每一个知识点，我会先问自己它是什么？它有什么作用？有什么实例可以补充说明下？只有回答了这三个问句，我才能对自己说，这一点我看懂了，然后向其它人转述的过程中，可以说，然后再用实例去证明我说的，我觉得这就是我理想的看书结果。所以该笔记我会带着这三个问题去看每一个知识点。

#### 第二章：jvm自动内存管理
* 要点一：
jvm中内存是如何划分的
	Java虚拟机会在执行java程序的时候，将他所管理的内存划分为若干区域，分别是：**方法区，虚拟机栈，堆，本地方法栈，程序计数器**。
#####  1.	程序计数器
什么是程序计数器？
程序计数器在内存中只占很少的一块内存，它充当指示标记功能，例如字节码解释器就是通过它才能知道下一条要执行的字节码指令是什么，当多线程进行切换执行任务时，执行完后，又正确回到原来的位置，且各个线程之间的程序计数器是互不影响，独立存储。因此这一部分为“线程私有”。

##### 2.	虚拟机栈
什么是虚拟机栈？
虚拟机栈描述的是java方法执行的内存模型，每个方法执行时会创建一个栈帧（栈帧是方法运行期的基础数据结构），用于存储局部变量表，操作栈，动态链接，方法出口等信息，也就是说：一个方法从被调用到执行完成，也就意味着对应的一个栈帧从入栈到出栈的过程。

其中局部变量表也就是java八大基本数据类型和对象引用64位的long和double都是占用2个局部变量空间，其余都是一个，局部变量表所需的内存空间都是在编译器分配完成的。
值的一提的是虚拟机栈也是线程私有的，虚拟机栈就是为java方法服务的

 ##### 3.  本地方法栈
本地方法栈作用和虚拟机栈类似，只不过本地方法栈是为虚拟机中使用到的Native方法服务。

##### 4.	Java堆
什么是堆？
	堆的唯一目的就是用来存放对象实例。
特点：是java虚拟机中内存最大的一块区域，是所有线程共享的一块区域，在虚拟机启动时创建，几乎所有的对象实例和数组都在这里分配内存。也是垃圾收集器管理的主要区域
##### 5.	方法区
什么是方法区？
特点：方法区也是各线程共享的内存区域，它用于存放已经被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。在方法区，垃圾收集行为比较少，主要回收目标是针对常量池的回收和对类型的卸载
##### 6.	运行时常量池
什么是运行常量池？
是方法区的一部分。
一个class文件有类的版本，字段，方法，接口等描述信息，还有常量池。
它有什么作用？
用于存放编译期生成的各种字面量和符号引用，会在类加载后，存放到常量池中。
##### 7．直接内存
什么是直接内存？
就是本机内存，并不属于虚拟机运行时数据区中
它有什么作用？
 在java的IO中被频繁的使用到。
 


* 要点二：jvm中的内存溢出是怎样产生的

* 要点三：几款垃圾收集算法特点及原理

##### 如何判断堆中的一个对象已经死了？
1. 引用计数算法
算法思路：
    每一个堆中的对象每调用一次，都会有一个相应的引用计数器加1，当引用失效时就减1，也就是说当一个对象的引用计数器为0时，那么就可以被GC回收了。这是一个很牛的回收算法，但是java并不是通过这个算法来判断的。

2. 根搜索算法
算法思路：通过一系列名为“GC Roots”的对象最为起点，从这些节点向下搜索，搜索所走过的路称为引用链，当一个对象没有任何引用链到GC Roots，那么这个对象是不可用的，这个对象就是个可回收对象。java用的是这种算法。

##### 垃圾收集算法
1. 标记清除算法
算法思想：首先标记出所有要回收的对象，然后再清除所有被标记过的对象，这种算法有2个缺点，第一个是效率太慢，第二是被回收的地方容易产生零碎的内存，如果下次需要一个大的内存空间，又会触发一次垃圾收集动作。
2. 复制算法  这种算法将内存划分为2块大小一样的区域，每次只使用其中的一块，如果这一块用完了，就把还活着的复制到另外一块中，这样每次只要回收这一块就行了，也不用考虑到内存碎片等问题。缺点是内存缩小为原来的一半
3. 标记整理算法，它不直接清除，而是将对象先移到一端，然后再清除，避免了内存碎片化的问题
4. 分代收集算法
将对象的存活周期不同将内存分为几块，一般将java堆分为==新生代==和==老生代==，然后根据不同的年代，使用不同的回收算法进行收集
5. CMD收集器 是一种获取最短回收停顿时间为目标的收集器，时间短
 
##### 垃圾收集器
 明确一个观点：`没有最好的收集器，只有在合适的场景使用合适的收集器 `

这幅图可以看出把堆中的内存划分为2个部分，young和old，young区域有3个收集器可以使用，分别是：Serial，ParNew，Parallel Scavenge ;old有3个收集器,分别是：CMS,Serial old，Parallel Old。还有一个超级厉害的收集器G1，老少通吃！
![image](http://ww3.sinaimg.cn/large/0060lm7Tly1fpq1b0j0tvj30e20d9go2.jpg)（连线表示和可以搭配使用）

1. **Serial收集器**   这是一个新生代收集器，是个‘单线程’收集器，是许多client默认的收集器，它的特点是只用一个线程进行收集，并且它开始工作的时候，会停掉其他所有的线程（超级蛋疼！！！不过也是合情合理，比如打扫房间，边打扫，边扔垃圾，确实不行）
2. **ParNew收集器 **
这是一个新生代收集器，是Serial的多线程版，是许多server默认的收集器，它能实现所谓的边打扫边扔垃圾式收集。但是当它作为老年代收集器时，效果并不是很理想
3. **Parallel Scavenger收集器** 它也是一个新生代收集器，也是使用复制算法的收集器，又是并行的多线程收集器，它的目的是要做到可控制的==吞吐量==（吞吐量即：用户运行代码的时间/（用户运行代码的时间+垃圾收集的时间）），吞吐量越高，对CPU的利用率越高，
4. **Serial Old收集器**
是Serial的老年代版本，也是一个单线程收集器，使用标记整理算法，被Client模式下的虚拟机使用，在Server模式下2个用途：
- 和Parallel Scavenger收集器搭配使用
- 作为CMS的后备方案，发生Concurrent Mode Failure的时候使用
5. **Parallel Old**是Parallel Scavenger收集器的老年版本，使用多线程和标记整理算法，这个收集器的主要意义在于当你选择了新生代的收集器是Parallel Scavenger，那么老年代收集器就可选Parallel进行搭配工作了，因为他无法和CMS搭配使用
6. **CMS收集器** （Concurrent Mark Sweep），特点是回收目标是停顿时间最短。现在大多数B/S系统都采用的是这种回收方式。该收集器工作分为4个过程：
- 初始标记 initial mark
- 并发标记concurrent mark
- 重新标记remark
- 并发清除concurrent sweep
cms尽管很优秀，但前面已经说过没有万能的收集器，它同样也有3个缺点：
- 工作时会占用cpu资源，导致应用变慢
- 无法回收浮动的垃圾，也就是说当它在工作时用户还在工作，会产生新的垃圾，这些垃圾是没有标记过的，只能等待下一次进行回收，这就叫浮动垃圾，由于是和用户同步工作的所以要留足够的内存给用户线程使用
- 采用的标记清除算法，回收后会产生大量的碎片空间
7. **G1** 收集器（Garbage First）是基于标记整理算法的收集器，不会产生碎片空间，可以精确控制停顿

**垃圾收集器的总结：**
对于不懂年代的使用不同的收集器进行搭配使用，在Client和Server都有默认的收集器搭配组合

* **内存分配和回收策略**
自动内存管理解决了2个问题：一个是给对象分配内存，一个是回收分配给对象的内存，

要点四：常见工具故障处理
要点五：实战故障处理和调优

第三章：jvm执行子系统
要点一：class文件结构中组成部分
要点二：类的加载，类加载器工作原理
要点三：虚拟机是如何找到方法的
要点四：实战类加载器，字节码

第四章：程序的编译和代码的优化
要点一：java语法糖
要点二：编译器解析及优化

第五章：java高效并发原理
要点一：虚拟机的java内存模型的结构和操作
要点二：线程安全


