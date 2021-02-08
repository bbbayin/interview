## java相关

**1. 线程池如何进行调度？**
--------------https://blog.csdn.net/aiengelangte/article/details/80397952
>>>>资料：
  1. 线程池的好处：
  - 线程复用，避免线程重复创建和销毁带来的性能开销
  - 控制最大的线程并发数，提高系统资源利用率，避免过多的资源竞争造成阻塞
  - 能对多线程进行简单的控制，方便使用
  2. 线程池的调度过程：
  - 线程池创建的时候线程数量是0
  - 当提交任务（Runnable）执行时要经过一下判断：
    1. 当前线程数量小于核心线程数量，直接创建线程执行任务
    2. 当前线程数量大于核心线程数量同时小于最大线程数量时也是直接创建线程执行任务
    3. 当前线程数量大于等于最大线程数量时，会将任务放到等待队列（BlockQueueList）中
    4. 当线程执行完时，如果等待队列不为空会取出一个任务执行
    5. 当等待队列为空时，如果线程空闲时间超过了keepAliveTime，会将非核心线程销毁，最终将线程池线程数量收缩到核心线程数量
  3. 线程池是如何实现的线程复用？
  
    首先要明白线程的生命周期，创建-就绪-执行-阻塞-死亡，所以当线程的run方法执行结束的时候线程就会被销毁，所以达到复用就是让线程的run方法一直执行下去
    通过Worker实现的线程复用，创建Worker时会同时创建一个线程，它的run方法中包含一个while循环，循环条件是判断当前的task或者BlockingQueue是否为空，BlockingQueue的take()方法如果
    返回null会造成阻塞，所以worker的run方法可以一直运行下去，从而达到了线程的复用
    
**2. 系统提供默认线程池的几种类型**
----
线程池的继承结构：Executor(祖先)-ExecutorService(丰富了接口方法)-AbstractExecutorService(抽象类)-ThreadPoolExecutor(最原始的线程池类)
Executors：线程池的工厂类
线程池构造方法参数解释：
```
public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue){
  this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue, Executors.defaultThreadFactory(), defaultHandler);
  }
```
corePoolSize:核心线程数；
maximumPoolSize:最大可创建的线程数；
keepAliveTime:非核心线程可空闲时间;
unit:时间单位
workQueue:线程池满时，任务的等待队列
系统提供的线程池创建方法都是根据上面的构造方法进行创建的：
  1. newSingleThreadPool: 只有一个线程的线程池，任务串行执行
  2. newFixedThreadPool: 只有固定的核心线程
  3. cachedThreadPool: 无限制线程数量的线程池，线程可自动回收
  4. scheduledThreadPool: 核心线程固定，不限最大线程数量，可周期性执行任务的线程池

**3. jvm内存区域分配？哪些区域会发生OOM，如何发生的？**
	 
- **程序计数器**：
	作用是存放每个线程当前所执行的字节码指令行号，是线程私有的，指令的跳转和线程恢复等都是通过它进行的。
	它是jvm规范中唯一一个未定义OOM异常的区域

- **虚拟机栈**：描述的是java方法执行的内存模型，当执行一个方法时会为其中的局部变量分配内存，当方法所申请的栈深度无法分配时发生StackOverflowException，当虚拟机支持动态扩展时，如果无法分配局部变量所申请的内存时发生OOM。

- **本地方法栈**：与虚拟机栈一样，其对应的是本地方法执行时的内存模型，当无法分配本地方法申请的内存时发生OOM。

- **堆**：唯一的用途是用来存放对象本身，当创建对象时无法分配对应的内存空间就会发生OOM。

- **方法区**：存放类的信息，静态变量，常量等

- **运行时常量池**：存放符号引用和字面量

- **直接内存**：NIO（new input/output）类引入了一种基于通道和缓存区的IO操作方式，它允许native方法直接在堆外开辟一块内存空间，然后通过存放
	在java堆中的一个DirectByteBuffer对象对这块直接内存进行操作。这样在某些场景中显著地提高了性能，减少了java堆和native堆来回复制数据。当管理员在配置jvm内存时往往忽略了这块内存，导致各区域的内存之和大于物理机的内存，从而造成了OOM



3. jvm内存模型，线程同步原理

**4. 类加载过程**
------------
-**类的加载时机**
jvm规范规定有且仅有5种情况：
1. 使用new创建一个对象时，
2. 调用一个类的static变量，或修改类的static变量
3. 子类初始化时，如果父类没有初始化
4. 通过反射加载一个类时
5. MethodHandler....
-**类加载过程**
加载-验证-准备-解析-初始化-使用-卸载

加载：把数据从class文件加载到虚拟机内存
验证：校验类的合法性，保证不会对虚拟机造成伤害
准备：初始化类的成员变量的0值和final常量值
解析：将常量池中的符号引用转化为直接引用
初始化：执行类的<clinit>()方法，初始化所有的类变量，搜集静态代码块，根据其在代码中的顺序进行执行


-**5. volatile能保证原子性吗？**

-**6. 内存泄露原因**

-**7. 对象的引用类型**

-**8. sychornized作用，lock的机制实现原理**



**Android 相关**

***1. binder原理***

***2. 进程间通讯方式***

***3. handler原理，如何处理delay消息，空队列时怎么实现的等待机制***

***4. view绘制原理，requestLayout一定会触发重新测量和重绘吗？***
-----
如果当前View在进行布局就不会执行这一次调用，
requestLayout会把当前View的mPrivateFlags增加两个标志位xxx，然后会递归地调用父布局的requestLayout方法，最终会执行ViewRootImpl#requestLayout
方法，经过一系列的判断和调用会执行ViewRootImpl#scheduleTraversals()方法->ViewRootImpl#performTraversals()，里面会调用performMeasure(),
performLayout(),perforDraw()方法。
它与invalidate()方法区别在于，invalidate()只会对当前的view进行重新绘制，位置大小不会发生改变，requestLayout会对当前的View大小和位置重新计算。
postInvalidate()是通过ViewRootImpl中的主线程Handler发送了一个更新UI的消息，允许在子线程调用。

***5. APP启动流程***

***6. 页面的启动时间如何统计***

***7. 图片加载优化方式，图片软解和硬解区别***

***8. glide和picasso的区别***

***9. glide如何加载的gif***

***10. app性能如何监控，如何定位卡顿和内存过高的问题***

***11. java序列化的方式，有何区别***

***12. 埋点自动收集，如何在自动上报中加入业务定义参数，比如商品id***

***13. git 操作***
1)第一次提交
2)第二次提交
3)第三次提交

*** 14. webview优化 ***
