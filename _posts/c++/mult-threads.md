
并发编程有两种基本模式: message passing, shared memory

并发编程的时候
1. 最低限度地使用共享对象（shared memory）,减少同步的场合，
2. 使用高级的并发编程构件: pipeline、Producer-Consumer Queue, TaskQueue, CountDownLatch, etc.
3. 不得以必须使用同步原语时，只用非递归的互斥锁和条件变量，慎用读写锁，不用信号量
4. 除了使用atomic整数外，不要自己编写lock-free代码

### Synchronization primitives 同步原语
fundamental synchronization mechanisms
	mutexes, readerwriter locks, condition variables

#### mutex 是加锁原语，用来排他性地访问共享数据
- 确保`同一时间`只有`一个线程`访问`共享资源`
  - 共享资源？？
- 任何其他试图再次对互斥量加锁的线程将会被阻塞直到当前线程释放该互斥量
- 释放互斥量的时候如果有多个线程阻塞，只有第一个变为运行状态的线程可以对互斥量加锁，其他线程继续阻塞
- 所有线程必须遵守相同的数据访问规则（都要用，而且要用同一个互斥量），否则还是会出现数据不一致的问题
```
An Important Note
	All threads that use the protected data items must use the same protocol to acquire and release the associated lock. Otherwise, mutual exclusion will not be guaranteed. More precisely, if three threads A, B and C share the same set of data items, and if only threads A and B acquire and release the associated lock, then while thread A is executing the instructions of the critical section and thread B is waiting outside, thread C can enter the critical section because it does not have to wait until thread A releases the lock. As a result, a race condition occurs and the final result of the computation may be incorrect.
```

- api
```
1.
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
2.
pthread_mutex_init(pthread_mutex_t * mutex, const pthread_mutex_t *attr);
pthread_mutex_destroy(pthread_mutex_t *mutex);

pthread_mutex_lock(&mutex);
pthread_mutex_trylock(&mutex); // unlocked => return 0, locked => return EBUSY
pthread_mutex_unlock(&mutex);
```

- 减小互斥锁的粒度和数量
	特大的互斥量分解为几个小的互斥量来提高性能。每个互斥量保护的区域应则尽量小。
	互斥量需要时间来加锁和解锁。所以，互斥量应该尽量少，够用即可。

!!!!线程锁机制同时也不是异步信号安全的，也就是说，不应该在信号处理过程中使用互斥锁，否则容易造成死锁。

#### condition variable 是等待原语，用来等待某个条件成立
在条件不满足时阻塞线程，等条件为真时再唤醒该线程
需要和互斥量mutex共同使用


```
pthread_cond_init
pthread_cond_destroy

pthread_cond_wait 等待条件变量
	int pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex)
	当 pthread_cond_wait() 被调用后，它解锁互斥量并停止线程的执行。在别的线程唤醒它之前它会一直保持暂停状态。这些操作是“原子操作”
pthread_cond_timedwait

唤醒：
pthread_cond_signal 条件成立，唤醒等待的一个线程
pthread_cond_broadcast 条件成立，唤醒所有等待线程
```
与互斥锁不同，条件变量是用来等待而不是用来上锁的。条件变量用来自动阻塞一个线程，直到某特殊情况发生为止
条件变量本身不是原子操作,所以任何对条件变量的操作都要用一个线程锁来保护.

条件变量用于 多线程的同步，甚至进程同步
	如果两进程共享可读写的内存，条件变量可以被用来实现这两进程间的线程同步。

#### readerwriter locks 读写锁

```
pthread_rwlock_t rwlock = PTHREAD_RWLOCK_INITIALIZER;
pthread_rwlock_wrlock(&rwlock);
pthread_rwlock_unlock(&rwlock);
```

### crital section 临界区
- 多线程编程中，访问共享资源的一块代码叫临界区。简单的说就是需要同步机制的，使数据访问串行化的一块代码
```
In concurrent programming, a critical section is a part of a multi-process program that may not be concurrently executed by more than one of the program's processes. In other words, it is a piece of a program that requires mutual exclusion of access. Typically, the critical section accesses a shared resource, such as a data structure, a peripheral device, or a network connection, that does not allow multiple concurrent accesses.
```

### race condition 竞态条件
- 线程的调度算法是抢占式的。因此如果对共享资源的操作不是原子操作，则会导致数据不一致的情况
```
A race condition occurs when two or more threads can access shared data and they try to change it at the same time. Because the thread scheduling algorithm can swap between threads at any time, you don't know the order in which the threads will attempt to access the shared data. Therefore, the result of the change in data is dependent on the thread scheduling algorithm, i.e. both threads are "racing" to access/change the data.
```

- 例子
```
//Problems often occur when one thread does a "check-then-act" (e.g. "check" if the value is X, then "act" to do something that //depends on the value being X) and another thread does something to the value in between the "check" and the "act". E.g:

if (x == 5) // The "Check"
{
   y = x * 2; // The "Act"

   // If another thread changed x in between "if (x == 5)" and "y = x * 2" above,
   // y will not be equal to 10.
}
```

### atomic operation 原子操作
- 原子操作：多步的操作，要么全部执行完，要么一步都不执行。不可能只执行到中间。
- 原子操作的话，竞态条件对数据结果的一致性没有影响

### deadlock 死锁
- 死锁的调试

- 易错点，注意跳转语句容易造成死锁，没有unlock
    pthread_mutex_lock(&mutex);
    if(timer_list.num >= timer_list.max_num) {
    	pthread_mutex_unlock(&mutex);  /*容易漏写*/
        return INVALID_TIMER_ID;
    }
    pthread_mutex_unlock(&mutex);


### IPC问题



最好的教程：
https://computing.llnl.gov/tutorials/pthreads/

http://www.ibm.com/developerworks/cn/linux/l-pthred/
https://www.ibm.com/developerworks/cn/linux/thread/posix_thread2/

=====================================================================
No manual entry for pthread_*
=====================================================================
aptitude install manpages-posix-dev


## pthread vs process
pthread - Posix pthread. POSIX.1 specifies a set of interfaces (functions, header files) for threaded programming commonly known as POSIX threads, or Pthreads
man 7 pthreads

attributes are process-wide, shared among threads
1. pid
2. ppid
3. pgid, sid
4. controlling terminal
5. user and group IDS? uid, gid
6. open file descriptors
7. record locks?
8. signal disposition
	The signal disposition is a per-process attribute: in a multithreaded application, the disposition of a particular signal is the same for all threads.
9. file mode creation mask
10. current directory
11. interval timers
12. nice value?
13. resource limits
14. measurements of the consumption of CPU time and resources
15. data and heap segments

1. text section
2. data, rodata, bss section

attributes are per-thread, distinct for each pthred
1. thread ID
2. signal mask, Set of pending and blocked signals
	A thread can manipulate its signal mask using pthread_sigmask(3)
3. Thread specific data
	such as: the errno variable
4. alternate signal stack
5. real-time scheduling policy and priority
6. capabilities
7. CPU affinity
8. stack(automatic variables)

registers ??


创建和管理线程的开销比进程小一个数量级
When compared to the cost of creating and managing a process, a thread can be created with much less operating system overhead. Managing threads requires fewer system resources than managing processes.


### Thread Termination 线程终止
A single thread can exit in three ways, thereby stopping its flow of control, without terminating the entire process.
1. The thread can simply return from the start routine. The return value is the thread's exit code.
2. The thread can be canceled by another thread in the same process.
3. The thread can call pthread_exit.


### Thread-safeness
针对程序或者函数来说的
in a nutshell, refers an application's ability to execute multiple threads simultaneously without "clobbering" shared data or creating "race" conditions.

### Thread Limits
For example, the maximum number of threads permitted, and the default thread stack size are two important limits to consider when designing your program.


### Thread-Specific Data (TSD) 线程数据
它和全局变量很象，在线程内部，各个函数可以象使用全局变量一样调用它，但它对线程外部的其它线程是不可见的
如errno

=====================================================================
http://www.parallellabs.com/2010/12/04/why-should-we-be-care-of-volatile-keyword-in-multithreaded-applications/
注意：
	多线程中，不应该使用volatile变量来做同步。
	因为虽然volatile意味着每次读和写都是直接去内存地址中去操作，但是volatile在C/C++现有标准中即不能保证原子性（Atomicity）也不能保证顺序性（Ordering），所以几乎所有试图用volatile来进行多线程同步的方案都是错的。

volatile的意思是“易变的”，这个关键字最早就是为了针对那些“异常”的内存操作而准备的。它的效果是让编译器不要对这个变量的读写操作做任何优化，每次读的时候都直接去该变量的内存地址中去读，每次写的时候都直接写到该变量的内存地址中去，即不做任何缓存优化。它经常用在需要处理中断的嵌入式系统中，其典型的应用有下面几种：
a. 避免用通用寄存器对内存读写的优化。
编译器常做的一种优化就是：把常用变量的频繁读写弄到通用寄存器中，最后不用的时候再存回内存中。但是如果某个内存地址中的值是由片外决定的（例如另一个线程或是另一个设备可能更改它），那就需要volatile关键字了。
b. 硬件寄存器可能被其他设备改变的情况。

在多线程环境下，每个线程都有一个独立的寄存器，用于保存当前执行的指令。假设我们定义了一个全局变量，每个线程都会访问这个全局变量，这时候线程的寄存器可能会存储全量变量的当前值用于后续的访问。当某个线程修改了全局变量的值时，系统会立即更新该线程寄存器中对应的值，其他线程并不知道这个全局变量已经修改，可能还是从寄存器中获取这个变量的值，这个时候就会存在不一致
的情况。


一句话，volatile对多线程编程是非常危险的。
安全起见，大家还是用Pthreads，Java.util.concurrent，TBB等并行库提供的lock/spinlock，conditional variable, barrier, Atomic Variable之类的同步方法来干活的好，因为它们的内部实现都调用了相应的memory barrier来保证memory ordering，你只要保证你的多线程程序没有data race，那么它们就能帮你保证你的程序是正确的


===============================================================================
task queue + thread pool
===============================================================================
http://swind.code-life.info/posts/c-thread-pool.html

为什么要创建线程池？
线程池属于对象池.所有对象池都具有一个非常重要的共性,就是为了最大程度复用对象.那么
线程池的最重要的特征也就是最大程度利用线程.
从编程模型模型上说讲,在处理多任务时,每个任务一个线程是非常好的模型.如果确实可以这么
做我们将可以使用编程模型更清楚,更优化.但是在实际应用中,每个任务一个线程会使用系统限
入"过度切换"和"过度开销"的泥潭.
首先,创建线程本身需要额外(相对于执行任务而必须的资源)的开销.
其次,过多的线程将导致过度的切换.
所以线程池的目的就是为了减少创建和切换线程的额外开销,利用已经的线程多次循环执行多个任
务从而提高系统的处理能力.也就是在"社会主义初级阶段"一件衣服应该尽量多穿一些天数.

线程池 生产者消费者模式

thread pool
http://www.ibm.com/developerworks/cn/java/l-threadPool/


==============================================================

==============================================================
使用 C++11 编写 Linux 多线程程序：http://blog.jobbole.com/81265/

需要注意一点，因为单个 CPU 内核下多个线程并不是真正的并行，有些问题，比如 CPU 缓存不一致问题，不一定能表现出来，一旦这些代码被放到了多核或者多 CPU 的环境运行，就很可能会出现“在开发测试环境一切没有问题，到了实施现场就莫名其妙”的情况，所以，在进行多线程开发时，开发与测试环境应该是多核或者多 CPU 的，以避免出现这类情况。

=========================================================
数据处理同步问题
=========================================================
锁只是同步方法的一种，同步分阻塞同步和非阻塞同步。
lockfree就可以保证非阻塞同步，同时也尽量保证了waitfree，但依然做不到完全的waitfree。

2.既然讨论无锁，就得说说有锁会导致的问题
a）死锁
多个锁不按顺序的话
b）优先级反转
可能高优先级的等待低优先级的
c）影响实时性
等锁时间不定
d）信号安全
中断/信号处理函数中不能加锁
e）崩溃处理
崩溃时可能占着锁
f）抢占的影响
被抢占时可能还占着锁
g）影响整体性能
切换进程影响性能

 Lock-Free
 业界在原子操作的基础上提出了著名的 CAS（Compare - And - Swap）操作来实现 Lock-Free 算法
 Bool CAS(T* addr, T expected, T newValue)
 {
      if( *addr == expected )
     {
          *addr =  newValue;
           return true;
     }
     else
           return false;
 }
