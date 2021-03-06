---	
layout:     post	
title:      『Operating System』 Process And Concurrence	
subtitle:   『操作系统』 进程与并发    
date:       2021-06-02	   
author:     Coekjan 
header-img: img/post-bg-OS.jpg	
catalog:    true    
katex:  true
tags:	
    - Operating System  
---

## 进程与线程

### 并发与并行

**并发**: 设有两个进程p和q, 若在某一指定的时刻t, 无论p和q是在同一处理器上还是在不同的处理器上执行, **只要p和q都处在各自的起点和终点之间的某一处**, 就称p和q是并发的.

**并行**: 设有两个进程, 它们在同一时间度量下**同时运行在不同的处理器上**, 则称这两个进程是并行的.

> 并发可能是伪并行, 也可能是真并行.

#### 前趋图

前趋图是一种有向无循环图, 图中的每一个结点可以代表一条语句, 一段程序或者一个进程, 结点之间的有向边用于表示语句, 程序或进程之间的执行次序.

比如 $A$ 在 $B$ 之前执行, 就可以表示为:

$$
\boxed{A}\rightarrow\boxed{B}
$$

#### 并发执行特征

* **间断性**: 并发进程具有"执行-暂停-执行"的间断性执行规律.
* **非封闭性**: 多个进程共享系统的资源, 这些资源的状态将由多个进程来改变, 致使进程之间相互影响.
* **不可再现性(难复现性)**: 在初始条件相同的情况下, 并发进程的执行结果依赖于执行次序.

#### Bernstein条件

> **竞争**是指多个进程读写同一个共享数据时, 执行结果依赖于执行的次序.

**Bernstein条件**是进程执行结果与执行次序无关的**充分条件**.

定义:
* $R(S_i)$ : 进程 $S_i$ 的读子集, 即其值在 $S_i$ 中被引用的变量集合.
* $W(S_i)$ : 进程 $S_i$ 的写子集, 即其值在 $S_i$ 中被改变的变量集合.

Bernstein条件指:
* $R(S_1)\cap W(S_2)=\varnothing$
* $W(S_1)\cap R(S_2)=\varnothing$
* $W(S_1)\cap W(S_2)=\varnothing$

### 进程

#### 定义与特征

**定义**:
* 进程是指程序的一次执行.
* 是可以和其他计算并发执行的计算.
* 进程可定义为一个数据结构, 及能在其上进行操作的一个程序.
* 进程是一个程序及其数据, 在处理器上顺序执行时发生的活动.
* 进程是程序在一个数据集合上运行的过程, 它是系统进行资源分配和调度的一个独立单位.

**特征**:
* 动态性: 进程是程序的一次执行过程. 它因创建而产生, 因调度而执行, 因无资源而暂停, 因撤销而消亡.
* 并发性: 多个进程实体同时存在于内存中, 能够在一段时间内并发执行.
* 独立性: 在传统的操作系统中, 进程是独立运行的基本单位.
* 异步性: 也叫制约性, 进程之间相互制约, 进程以各自独立的不可预知的速度向前推进.

#### 结构特征

进程的结构特征包含: 程序段, 数据段, 进程控制块(PCB).

一个进程的所有信息包括:
1. 程序代码.
2. 程序数据.
3. 程序计数器的值.
4. 通用寄存器的值, 堆, 栈.
5. 系统资源.

#### 状态与控制

> 进程控制的主要任务是**创建**和**撤销**进程, 实现进程的**状态切换**. 这些任务由操作系统内核完成.

进程有三种基本状态: **就绪**, **执行**, **阻塞**.
* **就绪状态**: 进程已获得除处理器外的所需资源, 等待分配处理器资源.
* **执行状态**: 占用处理器资源. 处于此状态的进程数目小于等于处理器数量. 在没有其他进程可以执行时, 通常会自动执行系统的idle进程(相当于空操作).
* **阻塞状态**: 正在执行的进程, 由于发生某种事件而暂时无法执行, 便放弃处理器而处于阻塞状态.

![]({{ '/img/OS-Process-Status-Transition.svg' | prepend: site.baseurl}})

##### 进程控制块(PCB, Process Control Block)

进程控制块在操作系统中的作用:
* 进程创建与撤销
* 进程的唯一标识
* 限制系统进程数目

进程控制块是进程管理和控制的最重要的数据结构, 每一个进程均有一个PCB. 在创建进程时就创建PCB, 伴随进程运行的全过程. PCB将包含如下内容:
* **进程标识符**: 每一个进程都必须有一个唯一的标识符, 可以是字符串, 也可以是数字. 在进程创建时被系统赋予.
* **程序与数据地址**: 把PCB与其对应的程序与数据联系起来.
* **进程状态**: 为管理方便, 系统将状态相同的进程组成一个队列.
* **进程优先级**: 进程优先级反映进程的紧迫程度, 通常由用户指定和系统设置.
* **现场保留区**: 当进程因某种原因不能占用处理器时, 释放处理器. 此时应把处理器中的各种状态信息保存起来, 将来重获处理器执行权时, 就可以得到恢复.
* **同步互斥机制相关的域**: 用于实现进程间互斥, 同步所需的信号量等.
* **进程通信机制相关的域**: 用于实现进程间通信所需的字段等.
* **资源清单**: 列出所拥有的除处理器外的资源记录, 如拥有的I/O设备, 打开的文件列表.
* **家族关系**: 关于父进程, 子进程的信息.
* **链接字**: 根据进程所处的现行状态, 进程相应的PCB加入到不同的队列中, PCB链接字指出该进程所在队列中下一个PCB的首地址.

### 线程

#### 线程的引入

进程的不足:
* 一个进程在某一时刻只能执行一项任务.
* 进程在执行过程中如果被阻塞, 则整个进程都会被挂起, 即使进程中有不依赖于等待事件的工作, 也不能继续执行.

> 需要一种新的实体:
> * 实体之间可以并发执行
> * 实体之间共享地址空间

注意到, 传统操作系统中, 进程包含了两个概念: **资源拥有者**和**可执行单元**. 现代操作系统中, 将资源拥有者称作进程, 将可执行单元称作线程.

线程, 就是将资源和计算分离, 提高了并发效率.
* 减少了进程切换的开销.
* 提高了进程内的并发程度.
* 同一进程内的线程共享资源.

#### 线程的实现

##### 用户级线程

**用户级线程**: 线程在用户空间, 通过相应的库来模拟线程, 不需要或仅需极少的内核支持.
* 上下文切换比较快: 不需要更改页表.

**用户级线程库的主要功能**:
* 创建和销毁线程.
* 线程之间传递消息和数据.
* 调度线程执行.
* 保存和恢复线程的上下文.

> 用户级线程库: POSIX Pthreads, Mach C-threads, Java Threads.
> 
> **POSIX Pthreads**:
> * 用于线程创建和同步的 POSIX 标准 API(IEEE 1003.1c).
> * 可在**用户级**或**内核级**实现.
> * API 规定了线程库的行文, 但不限定实现方法.
> * 类 Unix 操作系统中很常见: Solaris, Linux, MacOS X.

优点 | 缺点
:-- | :--
1. 线程切换与内核无关<br/>2. 线程的调度由应用决定, 容易优化<br/>3. 可运行在任何操作系统上, 只需要线程库的支持 | 1. 很多系统调用会引起阻塞, 内核会因此阻塞整个进程<br/>2. 内核只能将处理器分配给整个进程, 无法实现一个进程中多个线程的并行执行

##### 内核级线程

> 支持内核线程的操作系统称为**多线程内核**.

**内核级线程**: 线程在内核空间. 内核有多个分身, 每一个分身都可以用来处理一个任务. 这用来处理非同步事件很有效, 内核可以对每一个非同步事件产生一个分身来进行处理.

> 典型实现:
> * Windows 2000/XP
> * OS/2
> * Linux
> * Solaris
> * Tru64 UNIX
> * Mac OS X

优点 | 缺点
:-- | :--
1. 内核可在多个处理器上调度一个进程的多个线程实现同步并行执行<br/>2. 阻塞发生在线程级别<br/>3. 内核中的一些处理机制可以使用多线程实现. | 1. 一个进程中的线程切换需要内核参与, 线程的切换涉及到两个模式的切换<br/>2. 降低了线程切换的效率

##### 混合的线程实现方式

**混合实现**: 使用内核级线程, 然后将用户级线程与某些或全部内核线程多路复用起来, 形成混合的线程实现方式.
* 程序设计师可以决定有多少个内核级线程和多少个用户级线程彼此多路复用. 这一模型带来了很大的灵活性.
* 内核只识别内核级线程, 并对其进行调度. 其中一些内核级线程会被多个用户线程多路复用.

###### 线程模型

有些系统同时支持了用户线程和内核线程, 由此产生了不同的多线程模型, 即实现用户级线程和内核级线程的连接方式.

**Many-to-One**: 将多个用户级线程映射到一个内核级线程, 线程管理在用户空间完成.
* 优点: 效率高.
* 缺点: 当一个线程在使用内核服务时被阻塞, 则整个进程都被阻塞; 不能运行在多个处理器上.

**One-to-One**: 将每一个用户级线程映射到一个内核级线程.
* 优点: 当一个线程被阻塞后, 另一个线程能够继续执行, 所以并发能力较强.
* 缺点: 每创建一个用户级线程都需要创建一个内核级线程与之对应, 这样创建线程的开销较大, 会影响到应用程序的性能.

**Many-to-Many**: 将 $n$ 个用户级线程映射到 $m$ 个内核级线程, 要求 $m \le n$: 在Many-to-One模型和One-to-One模型折中, 克服了Many-to-One模型并发度不高的缺点, 又克服了One-to-One模型一个用户进程占用太多内核级线程的缺点, 还拥有了这两种模型的优点.

#### 线程安全

称一个对象为**线程安全**, 是指多个线程访问它时, 若不考虑这些线程在运行时环境下的调度和交替执行, 也不需要进行额外的同步, 或者调用方进行任何其他操作, 调用这个对象的行为都可以得到正确的结果.

> 可重入不一定线程安全, 线程安全不一定可重入

## 同步与互斥

### 临界资源与临界区

* **临界资源**: 我们将一次仅允许一个进程访问的资源称为临界资源.
* **临界区**: 每一个进程中访问临界资源的那段代码称为临界区.

### 进程的同步与互斥

* **进程互斥(间接制约)**:
  * 两个或两个以上的进程, 不能同时进入关于同一组共享变量的临界区域, 否则可能发生与时间有关的错误, 这种现象称为进程互斥.
  * 进程互斥是进程间发生的一种间接性作用, 一般是程序不期望的现象.
* **进程同步(直接制约)**:
  * 系统中各进程之间能有效地共享资源和相互合作, 从而使程序的执行具有可再现性的过程称为进程同步.
  * 进程同步是进程间一种刻意安排的直接制约关系, 即为了完成同一任务的各个进程之间, 因需要而协调它们的工作, 从而相互等待, 相互交换信息.

> 互斥管理应满足的条件:
> * 没有进程在临界区时, 想进入临界区的进程可进入.
> * 任何两个进程都不能同时进入临界区.
> * 当一个进程运行在它的临界区外面时, 不能防碍其他进程进入临界区.
> * 任何一个进程进入临界区的要求应该在有限时间内得到满足.

### 基于忙等待的互斥实现

#### 软件方法

##### Dekker算法 - 两个进程的互斥问题

```pascal
// - P - begin - //
pturn = TRUE
WHILE qturn :
    IF turn == 1 :
        pturn = FALSE
        WHILE turn == 1 :
            CONTINUE
        pturn = TRUE

// Critical Region //

turn = 1
pturn = FALSE
// - P -  end  - //
// ------------- //
// - Q - begin - //
qturn = TRUE
WHILE pturn :
    IF turn == 0 :
        qturn = FALSE
        WHILE turn == 0 :
            CONTINUE
        qturn = TRUE

// Critical Region //

turn = 0
qturn = FALSE
// - Q -  end  - //
```

##### Peterson算法 - 两个进程的互斥问题

```pascal
VAR turn
VAR interested[2]

ENTER_REGION (i) :
    other = 1 - i
    interested[i] = TRUE
    turn = process
    WHILE turn == process && interested[other] :
        CONTINUE

LEAVE_REGION (i) :
    interested[i] = FALSE
```

##### Lamport面包店算法 - N个进程的互斥问题

###### 面包店的排队原理

顾客进入面包店后, 首先抓取一个号码, 然后按照号码从小到大的次序依次进入面包店购买面包, 这里假设:
1. 面包店按照从小到大的次序发放号码, 且两个或两个以上的顾客有可能得到相同的号码;
2. 若多个顾客抓到相同的号码, 则按照顾客名字的字典序排序.

###### Lamport面包店算法

计算机系统中, 顾客相当于进程, 每一个进程都有唯一的标识.

基本思想: 设置发号器, 按从小到大的次序发放号码. 进程进入临界区前先抓取一个号码, 然后按号码从小到大的次序依次进入临界区. 若多个进程抓到相同的号码, 则按照进程的标识依次进入.

```pascal
VAR choosing[N] = [FALSE, ..., FALSE]
VAR number[N] = [0, ..., 0]
ENTER_REGION (i) :
    WHILE TRUE :
        choosing[i] = TRUE
        number[i] = 1 + MAX_IN_LIST(number)
        choosing[i] = FALSE
        FOR j = 0 TO N - 1 :
            WHILE choosing[j] :
                CONTINUE
            WHILE number[j] != 0 :
                IF number[j] < number[i] :
                    CONTINUE
                ELSE IF number[j] == number[i] && j < i :
                    CONTINUE
                ELSE :
                    BREAK
        
        // Critical Region //

        number[i] = 0

        // NON-Critical Region //

```

#### 硬件方法

##### 中断屏蔽

使用开关中断的指令:
1. 屏蔽中断, 进入临界区;
2. 离开临界区, 开启中断.

这种做法的优点很显然: 简单. 缺点则有:
1. 不适用于多核系统, 往往带来很大的性能损失.
2. 单处理器下, 很多日常任务是靠中断机制来触发的, 如果屏蔽中断, 则会影响系统效率.
3. 用户进程中禁用中断是危险操作.

##### Test-and-Set指令

TS(Test-and-Set)是一种不可中断的原语(指令), 它会写值到某个内存位置并传回旧值. 其语义为:

```pascal
TEST_AND_SET (&lock) :
    init = lock
    lock = TRUE
    EXIT init
```

###### 自旋锁Spinlocks

利用TS原语提供的互斥支持, 可以通过以下方法进行互斥控制:

```pascal
ACQUIRE (&lock) :
    WHILE TEST_AND_SET(lock) :
        CONTINUE

RELEASE (&lock) :
    lock = 0
```

##### Swap指令

Swap指令与TS原语一样, 是不会被中断的原子指令, 其功能是交换两个字的内容. 其语义为:

```pascal
SWAP (&i, &j) :
    tmp = i
    i = j
    j = tmp
```

使用Swap指令实现进程互斥的描述如下:

```pascal
k = TRUE
use = FALSE
WHILE !k :
    SWAP (use, k)
// Critical Region //
user = FALSE
// NON-Critical Region //
```

#### 忙等待算法的问题

上述算法都使用了忙等待的方法来实现互斥控制:
1. 忙等: 浪费CPU时间.
2. 优先级反转: 低优先级进程先进入临界区, 高优先级进程忙等待.

### 基于信号量的互斥实现

#### 信号量

信号量是一种新的变量类型 `Semaphore` , 只能通过初始化和标准原语 `P/V` 来访问, 作为OS核心代码执行, 不受进程调度打断.

`P/V` 原语的语义为:

```pascal
P (S) :
    WHILE S <= 0 :
        CONTINUE
    S = S - 1
V (S) :
    S = S + 1
```

> 语义上出现了忙等, 但实际上是利用了阻塞机制.

应用 | 描述
:-: | :--
互斥 | 可使用初值为1的信号量来实现进程间的互斥: 一个进程进入临界区前执行 `P` 操作, 退出临界区后执行 `V` 操作.
有限并发 | 可使用初值为 `c` 的信号量来实现进程间的有限并发.
同步 | 可使用初值为 `0` 的信号量来实现进程间的同步.

![]({{ '/img/OS-P-V-Semaphore-Synchronization.svg' | prepend: site.baseurl}})

##### 屏障Barrier的PV信号量实现

![]({{ '/img/OS-Process-Synchronization-Barrier.svg' | prepend: site.baseurl}})

```pascal
VAR n                   // number of processes/threads
VAR count = 0
SEMAPHORE mutex = 1     // protect `count`
SEMAPHORE barrier = 0

// ... //

P(mutex)
count = count + 1
V(mutex)

IF count == n :         // the last process/thread
    V(barrier)
P(barrier)
V(barrier)
```

#### 信号量集

##### AND型信号量集

**基本思想**: 一次性分配全部所需共享资源给进程, 待进程使用完后, 再一次性释放.

`SP/SV` 原语的语义为:

```pascal
SP (S1, S2, ..., Sn) :
    WHILE TRUE :
        IF S1 >= 1 && S2 >= 1 && ... && Sn >= 1 :
            S1 = S1 - 1
            S2 = S2 - 1
            ...
            Sn = Sn - 1
            BREAK
SV (S1, S2, ..., Sn) :
    S1 = S1 + 1
    S2 = S2 + 1
    ...
    Sn = Sn + 1
```

##### 一般信号量集

**基本思想**: 在AND型信号量集的基础上进行扩充. 进程对信号量 `Si` 的测试值为 `ti`, 占用值为 `di`.

```pascal
SP (S1, t1, d1, S2, t2, d2, ..., Sn, tn, dn) :
    WHILE TRUE :
        IF S1 >= t1 && S2 >= t2 && ... && Sn >= tn :
            S1 = S1 - d1
            S2 = S2 - d2
            ...
            Sn = Sn - dn
            BREAK
SV (S1, d1, S2, d2, ..., Sn, dn) :
    S1 = S1 + d1
    S2 = S2 + d2
    ...
    Sn = Sn + dn
```

常用机制:
* `SP(S, d, d)` : 表示每次申请 `d` 个资源, 当资源数少于 `d` 时, 便不予分配.
* `SP(S, 1, 1)` : 表示互斥信号量.
* `SP(S, 1, 0)` : 可控开关.
  * 当 `S >= 1` 时, 允许多个进程/线程进入临界区.
  * 当 `S == 0` 时, 禁止任何进程/线程进入临界区.

#### `P/V` 操作的优缺点

优点 | 缺点
:-- | :--
表达简单, 且可以解决任何同步互斥问题 | 安全性不足, 操作不当会导致死锁, 难以描述复杂的同步互斥问题.

### 基于管程(Monitor)的同步互斥实现

> 管程是一种高级同步原语.
> * 管程可以通过函数库形式实现.
> * 管程比信号量更容易控制.

**管程**: 将分散的临界区集中起来, 为每个可共享对象设计一个专门机构, 即管程, 来统一管理各进程对该资源的访问.

一个管程是由过程, 变量和数据结构等组成的一个集合, 它们组成一个特殊的模块或者软件包.
* 局部于该管程的共享数据: 资源状态
* 局部于该管程的若干过程: 数据操作

管程机制下, 互斥是指: 任何时刻, 管程中只能由一个活跃进程.

> 管程是一种语言概念, 由编译器负责实现互斥.

#### 管程的实现

目标: 为每一个共享资源设立一个管程, 对共享资源及其操作进行封装, 从而简化对共享资源的互斥访问.
* **局部控制变量(临界资源)**: 一组局部于管程的控制变量
* **初始化代码**: 对控制变量进行初始化的代码
* **操作原语(互斥)**: 对控制变量和临界资源进行操作的一组原语过程(程序代码), 是访问该管程的唯一途径.
* **条件变量(同步)**: 每个独立的条件变量是和进程需要等待的某种原因相联系的, 当定义一个条件变量 `x` 时, 系统就建立一个相应的等待队列.
  * `wait(x)` 将调用者进程放入 `x` 的等待队列中.
  * `signal(x)` 唤醒 `x` 等待队列中的一个进程.

> 当进入管程的进程因资源被占用等原因而不能继续运行时, 须令其等待. 为此, 在管程内部可以说明和使用一种特殊类型的变量 - **条件变量**:
> 
> > 每个条件变量表示一种等待原因, 并不去具体数值 - 每个原因对应一个队列.
> 
> 条件变量与信号量的区别:
> * 条件变量的值不可增减, 信号量的值可增减
>   * `wait` 操作一定阻塞当前进程, 但 `P` 操作只有当信号量值小于等于 `0` 时才被阻塞.
>   * 若没有等待进程, `signal` 将无效, 而 `V` 操作增加了信号量的值, 不会丢失其效果.
> * 访问条件变量需要持有管程的锁.

考虑这样的场景:
1. 当一个进入管程的进程执行等待操作时, 它应当释放管程的互斥权.
2. 当后面进入管程的进程执行唤醒操作时, 管程中便存在**两个同时处于活跃状态**的进程.

* Hoare管程(阻塞式条件变量): 执行 `signal` 的进程等待, 直到被唤醒的进程退出管程, 或等待另一个条件.
* Mesa管程(非阻塞式条件变量): 被唤醒的进程等待, 直到执行 `signal` 的进程退出管程或者等待另一个条件.
* Hansen管程: 执行 `signal` 的进程立即退出管程, 即 `signal` 须为管程的最后一个操作.

##### Hoare管程

* **入口等待队列**: 因为管程是互斥的, 所以当一个进程试图进入一个已被占用的管程时, 它应当在管程的入口处等待, 因而在管程的入口处有一个进程等待队列, 称为入口等待队列.
* **紧急等待队列**: 如果进程P唤醒进程Q, 则P进入紧急等待队列, Q继续执行. 如果Q又唤醒了进程R, 则Q也进入紧急等待队列, R继续执行... 紧急等待队列的调度优先级应**高于**入口等待队列.

**Hoare管程的同步原语**:
* `wait(x)` : 若紧急等待队列非空, 则唤醒其队首; 否则释放管程的互斥权. 执行此操作的进程排入 `x` 入口等待队列的队尾.
* `signal(x)` : 若入口等待队列为空, 则相当于空操作, 执行此操作的进程继续执行; 否则唤醒其队首, 执行此操作的进程排入紧急等待队列的队尾.

### 进程通信(IPC)的主要方法

* **低级通信**: 只能传递状态和整数值(控制信息), 包括进程互斥和通信所采用的信号量和管程机制.
  * 信息量小: 效率低, 每次通信传递的信息量固定, 若传递较多信息则需要进行多次通信.
  * 编程复杂: 用户直接实现通信细节, 编程复杂, 容易出错.
* **高级通信**: 适用于分布式系统, 基于共享内存的多处理器系统, 单处理器系统, 能够传送任意数量的数据, 可以解决进程的同步问题和通信问题, 主要包括三类: 管道, 共享内存, 消息系统.

#### (无名)管道

* **半双工**: 数据只能单向流动; 需要双方通信时, 需要建立起两个管道.
* **只能用于亲缘进程**.
* **构成独立文件系统**: 管道对于管道两端的进程而言, 就是一个文件, 但它不是普通的文件, 它不属于某种文件系统, 而是自立门户, 单独构成一种文件系统, 并且只存在于内存中.
* **数据读写**: 一个进程向管道中写的内容被关到的另一端进程读出. 每次都将写的数据添加在管道缓冲区的末尾, 且每次都从管道缓冲区的头部读取数据.

#### 有名管道(FIFO)

* 无名管道只能用于亲缘进程, 在有名管道提出后, 该限制得到克服.
* FIFO提供一个路径名与之关联, 以FIFO形式存在于文件系统中. 这样, 即便与创建FIFO的进程不存在亲缘关系, 只要能访问该路径就可以通过FIFO进行通信.
* FIFO严格遵循先进先出原则, 总是将写的数据添加到队列尾部, 且总是从队头读取数据.

#### 消息传递

消息传递的通信原语: `send(dst, &msg)` 与 `receive(src, &msg)`.
* 调用方式:
  * 阻塞调用
  * 非阻塞调用
* 实现中需要解决的问题:
  * 信息丢失, 延迟问题(TCP)
  * 编址问题: Mailbox

#### 共享内存

**共享内存**是最有用, 最高效的IPC形式.
* 两个不同进程A, B共享内存的意义是: 同一块物理内存被映射到进程A, B各自的进程地址空间.
* 当多个进程共享同一块内存区域, 为保证共享内存块不被同时写, 则需要同步机制约束.
* 进程在共享内存时, 保持共享区域, 直到通信完毕.

![]({{ '/img/OS-Memory-Sharing.svg' | prepend: site.baseurl}})

### 经典进程同步与互斥问题

#### 生产者-消费者问题

```pascal
SEMAPHORE full = 0
SEMAPHORE empty = N
SEMAPHORE mutex = 1         // protect buffer

VAR buffer[N]
VAR buf_in = 0
VAR buf_out = 0

PRODUCER () :
    WHILE TRUE :
        next = // produce `next` //
        P(empty)
        P(mutex)
        buffer[buf_in] = next
        buf_in = (buf_in + 1) % N
        V(mutex)
        V(full)
CONSUMER () :
    WHILE TRUE :
        P(full)
        P(mutex)
        next = buffer[buf_out]
        buf_out = (buf_out + 1) % N
        V(mutex)
        V(empty)
        // consume `next` //
```

##### 扩展问题

设有一个可以装A, B两种物品的仓库, 其容量无限大, 但要求仓库中A, B两种物品的数量 $C_A$ 和 $C_B$ 满足下述不等式:

$$
-M\le C_A - C_B \le N\quad(M, N \in \mathbf{Z}^+)
$$

```pascal
SEMAPHORE mutex = 1
SEMAPHORE sem_a = N
SEMAPHORE sem_b = M

A () :
    WHILE TRUE :
        P(sem_a)
        P(mutex)
        // A --> warehouse //
        V(mutex)
        V(sem_b)
B () :
    WHILE TRUE :
        P(sem_b)
        P(mutex)
        // B --> warehouse //
        V(mutex)
        V(sem_a)
```

#### 读者-写者问题

> * 同一时刻只能有一个激活的写进程
> * 同一时刻可以有多个激活的读进程

##### 读者优先

```pascal
SEMAPHORE wmutex = 1
SEMAPHORE rcmutex = 1           // protect readcount

VAR readcount = 0

WRITER () :
    P(wmutex)
    // write //
    V(wmutex)
READER () :
    P(rcmutex)
    IF readcount == 0 :
        P(wmutex)
    readcount = readcount + 1
    V(rcmutex)
    // read //
    P(rcmutex)
    readcount = readcount - 1
    IF readcount == 0 :
        V(wmutex)
    V(rcmutex)
```

##### 读写公平

> 与读者优先写法的不同之处在代码中以 `<~~` 方式指出.

```pascal
SEMAPHORE rwmutex = 1           // <~~
SEMAPHORE wmutex = 1
SEMAPHORE rcmutex = 1           // protect readcount

VAR readcount = 0

WRITER () :
    P(rwmutex)                  // <~~
    P(wmutex)
    // write //
    V(wmutex)
    V(rwmutex)                  // <~~
READER () :
    P(rwmutex)                  // <~~
    P(rcmutex)
    IF readcount == 0 :
        P(wmutex)
    readcount = readcount + 1
    V(rcmutex)
    V(rwmutex)                  // <~~
    // read //
    P(rcmutex)
    readcount = readcount - 1
    IF readcount == 0 :
        V(wmutex)
    V(rcmutex)
```

##### 写者优先

> 与读者优先写法的不同之处在代码中以 `<~~` 方式指出.

```pascal
SEMAPHORE wmutex = 1
SEMAPHORE rmutex = 1            // <~~
SEMAPHORE wpend = 1             // <~~
SEMAPHORE wcmutex = 1           // protect writecount
SEMAPHORE rcmutex = 1           // protect readcount

VAR writecount = 0              // <~~
VAR readcount = 0

WRITER () :
    P(wcmutex)                  // <~~
    IF writecount == 0 :        // <~~
        P(rmutex)               // <~~
    writecount = writecount + 1 // <~~
    V(wcmutex)                  // <~~
    P(wmutex)
    // write //
    V(wmutex)
    P(wcmutex)                  // <~~
    writecount = writecount - 1 // <~~
    IF writecount == 0 :        // <~~
        V(rmutex)               // <~~
    V(wcmutex)                  // <~~
READER () :
    P(wpend)                    // <~~
    P(rmutex)                   // <~~
    P(rcmutex)
    IF readcount == 0 :
        P(wmutex)
    readcount = readcount + 1
    V(rcmutex)
    V(rmutex)                   // <~~
    V(wpend)                    // <~~
    // read //
    P(rcmutex)
    readcount = readcount - 1
    IF readcount == 0 :
        V(wmutex)
    V(rcmutex)
```
#### 哲学家进餐问题

![]({{ '/img/OS-The-Dinning-Philosophers.svg' | prepend: site.baseurl}})

下面是几种解题思路:
* **破除资源互斥**: 至多允许四个哲学家同时尝试拿起筷子, 从而可以保证至少有一个哲学家能够进餐, 最终总会释放出他所使用过的两支筷子, 从而可使得更多的哲学家进餐.
* **破除循环等待**: 对哲学家进行编号, 奇数号先拿左再拿有; 偶数号相反.
* **破除保持等待**: 同时拿起两根筷子, 否则不拿起.

> 或者直接用 AND 型信号量集实现.

## 调度

### 调度类型

* **高级调度**: 又称为宏观调度, 作业调度. 从用户工作流程的角度, 一次性提交若干个作业, 对每一个作业进行调度. 这个时间跨度通常是分钟, 小时或天.
* **中级调度**: 又称为内外存交换. 从存储器资源角度, 将进程的部分或全部换出到外存, 将当前需要的部分换入到内存.
* **低级调度**: 又称为进程(或线程)调度, 微观调度. 从CPU资源的角度, 为进程分配处理器资源. 这个时间跨度通常是毫秒.
  * 非抢占式
  * 抢占式: 时间片原则? 优先权原则? 短作业(进程)优先?

### 调度性能准则

> 针对调度算法本身的准则:
> * **易于实现, 维护**:
>   * 算法复杂度, 实现结构, 代码量.
> * **执行开销比**:
>   * 算法本身执行时间, 算法本身占用的空间.
>   * 算法开销占调度后整体任务执行时间比例.

#### 面向用户

* **周转时间**: 作业从提交到完成所经历的时间. 包括: 收容队列中等待, CPU上执行, 就绪队列和阻塞队列中等待, 结果输出等待 - **批处理系统**.
  * 平均周转时间.
  * 带权平均周转时间.
  
  $$
  \begin{aligned}
      T_{\text{周转}}&=T_{\text{完成}}-T_{\text{提交}}\\
      T_{\text{带权周转}}&=\frac{T_{\text{周转}}}{T_{\text{执行}}}\\
      \overline{T_{\text{周转}}}&=\frac{\sum^N T_{\text{周转}}}{N}\\
      \overline{T_{\text{带权周转}}}&=\frac{\sum^N T_{\text{带权周转}}}{N}
  \end{aligned}
  $$
  
* **响应时间**: 用户输入一个请求到系统给出首次响应的时间 - **分时系统**.
* **截止时间**: 开始截止时间和完成截止时间 - **实时系统**.
  * 开始截止时间: 指作业开始执行的最晚时间.
  * 完成截止时间: 指作业执行完毕的最晚时间.
* **优先级**: 可以使关键任务达到更好的指标.
* **公平性**: 不因作业或进程本身的特性而使得上述指标过分恶化, 如长作业"饥饿".

#### 面向系统

* **吞吐量**: 单位时间内所完成的作业数, 与作业本身的特性和调度算法有关系 - **批处理系统**.
  
  $$
  W=\displaystyle\frac{N_{\text{process}}}{T}
  $$
  
* **处理器利用率** - **大中型主机**.
* **各种资源的均衡利用**: 如CPU繁忙的作业和I/O繁忙的作业搭配 - **大中型主机**.

### 批处理系统的调度算法

#### 先来先服务(FCFS)

* 按先后顺序调度.
  * 按照作业提交或进程变成就绪状态的**先后次序**, 分配CPU资源.
  * 当前作业或进程占用CPU, **直到执行完成或阻塞, 才让出CPU资源**.
  * 在作业或进程唤醒后, 并不立即恢复执行, 通常等到当前作业或进程让出CPU.
* 特点:
  * 有利于长作业, 不利于短作业.
  * 有利于CPU繁忙作业, 不利于I/O繁忙作业.

#### 最短作业优先(SJF)

* 对预计时间短的作业或进程优先分配CPU资源.
  * 后来的短作业**不抢占**CPU资源.
* 优点:
  * 比FCFS改善了平均周转时间和平均带权周转时间, 缩短了作业的等待时间.
  * 提高了系统的吞吐量.
* 缺点:
  * 对长作业非常不利, 可能长时间得不到执行.
  * 未能根据作业的紧迫程度来划分执行的优先级.
  * 难以准确估计作业或进程执行时间, 影响调度性能.

#### 最短剩余时间优先(SRTF)

对SJF进行改进, 改为**抢占式**.
* 新就绪进程比当前运行进程具有更短的完成时间, 系统抢占当前进程, 选择新就绪的进程执行.
* 缺点: 长作业"饥饿".

#### 最高响应比优先(HRRF)

> HRRF 事实上是 FCFS 与 SJF 的折中.

在每次选择作业投入运行时, 先计算后备作业队列中每个作业的响应比 $RP$:

$$
RP=1+\frac{T_{\text{已等待}}}{T_{预计运行}}
$$

然后选择其值最大的作业投入运行.

* 响应比的**计算时机**:
  * 每当调度一个作业运行时, 都要计算后备作业队列中每一个作业的响应比.
* 算法效果:
  * 短作业, 长时间等待的作业能获得高响应比 - 无"饥饿".
* 缺点:
  * 每次计算响应比, 都有一定的时间开销.

### 交互式系统的调度算法

#### 时间片轮转(Round Robin, RR)

* 时间片长度变化的影响:
  * 过长: 每一个进程都能在一个时间片内完成, 退化为FCFS.
  * 过短: 一次请求需要多个时间片才能处理完毕, 上下文切换次数增加, 响应时间长.
* 系统响应时间:
  
  $$
  T_{\text{响应}}=N_{\text{进程}} \times \tau_{\text{时间片}}
  $$
  
* 就绪进程数目: 数目越多, 时间片越小
* 系统处理能力: 应当使得用户输入能在一个时间片内处理完毕, 否则会导致响应时间, 平均周转时间和平均带权周转时间延长.

#### 优先级算法(Priority Scheduling)

平衡各进程对响应时间的要求, 适用于作业调度和进程调度, 可分为抢占式和非抢占式.

* **静态优先级**: 创建进程时就确定, 直到进程终止前都不改变:
  * 进程类型: 系统进程的优先级高.
  * 对资源的需求: 对CPU和内存需求少的进程, 优先级高.
  * 用户要求: 紧迫程度.
* **动态优先级**: 在创建进程时赋予优先级, 在进程运行时自动改变, 以便获得更好的调度性能.
  * 在就绪队列中, 等待时间越长优先级越高.
  * 进程每得到一个时间片后, 就降低其优先级.

> **优先级倒置**: 在某些涉及临界区的情景下, 高优先级进程会因低优先级进程而被延迟和阻塞. 可以通过:
> * 优先级**置顶**: 已进入临界区的低优先级进程临时获得最高优先级.
> * 优先级**继承**: 已进入临界区的低优先级进程, 继承临界区等待队列队头的优先级.
> 
> 等方法来解决.

#### 多级队列算法(Multiple-level Queue)

引入多个就绪队列, 通过各个队列的区别对待, 达到一个综合的调度目标:
* 根据作业或进程的性质或类型的不同, 将就绪队列再分为若干个子队列.
* 每个作业固定归入一个队列.

不同队列可以有不同的优先级, 时间片长度, 调度策略等. 在运行过程中还可以改变进程所在队列.

#### 多级反馈队列算法(Round Robin with Multiple Feedback)

> 时间片轮转算法和优先级算法的综合与发展.

* 为提高系统吞吐量和缩短平均周转时间而照顾短进程.
* 为获得较好的I/O设备利用率和缩短响应时间而照顾I/O型进程.
* 不必估计进程的执行时间, 动态调节.

**算法**:
1. 设置多个就绪队列, 分别赋予不同的优先级, 如逐级降低, 队列1的优先级最高. 每个队列执行的时间片长度不同, 规定优先级越低的时间片越长, 如逐级加倍.
2. 新进程进入内存后, 先投入队列1的末尾, 按时间片轮转算法调度.
3. 若队列1中的进程在一个时间片内未能执行完成, 则将其投入到队列2的末尾, 同样按时间片轮转算法调度. 以此类推, 直至到达最后一级队列, 按FCFS调度.
4. 仅当较高优先级的队列为空时, 才调度较低优先级队列中的进程执行. 若进程执行时有新进程进入较高优先级的队列, 则抢占执行新进程, 并把被抢占的进程投入到原队列的末尾.

![]({{ '/img/OS-Round-Robin-Multiple-Feedback.svg' | prepend: site.baseurl}})

### 实时系统的调度算法

> 实时系统是一种**时间起主导作用**的系统. 当外部的一种或多种物理设备给了计算机一个刺激, 计算机就必须在一个确定的时间范围内做出恰当的反应.

问题描述: 假设有一任务集 $S=\lbrace t_1, t_2, t_3, \dotsm, t_n \rbrace$ , 周期为 $T_1, T_2, T_3, \dotsm, T_n$ , 执行时间为 $C_1, C_2, C_3, \dotsm, C_n$ , 截至时间为 $D_1, D_2, D_3, \dotsm, D_n$ (通常 $D_i=T_i$ ), CPU的利用率用:

$$
U=\sum_{i=1}^n\frac{C_i}{T_i}
$$

表示.

#### 静态表调度

通过对所有周期性任务的分析预测, 事先确定一个固定的调度方案.
* 无任何计算, 按固定方案进行, 开销最小.
* 无灵活性, 只适用于固定任务的场景.

#### 单调速率调度(RMS)

RMS是单处理器系统下的**最优静态优先级调度算法**. 在该调度下, 任务集可调度的**充分条件**是:

$$
\begin{aligned}
    \sum_{i=1}^n\frac{C_i}{T_i}&\le n(\sqrt[n]{2}-1)\\
    \lim_{n\rightarrow\infty}n(\sqrt[n]{2}-1)&=\ln2\approx 0.693147\dotsm
\end{aligned}
$$

其特点是:
* 任务的周期越小, 优先级越高.
* 对于两个任务:
  * 若优先级不相同, 则优先调度优先级高的任务.
  * 否则, 随机选择一个来调度.

#### 最早截止期优先(EDF)

任务的绝对截止时间越早, 优先级越高.

#### 最低松弛度优先算法(LLF)

定义**松弛度** $L$ :

$$
L=T_{\text{截止}}-T_{\text{剩余运行}}-T_{\text{当前}}
$$

任务松弛程度越大的, 其紧急程度越高, 优先级越高.

LLF算法下, 任务级可调度的**充分必要条件**是:

$$
\sum_{i=1}^n\frac{C_i}{T_i}\le1
$$

### 多处理器系统调度

#### 非对称式多处理器系统(AMP)

> AMP: Asymmetric Multi-Processor, 指多处理器系统中, 各个处理器的地位不一致.

**主/从处理器**: 由主处理器管理一个公共就绪队列, 并分配进程给从处理器执行.
* 各个处理器有固定分工: OS系统, I/O处理, 应用.
* 潜在的不可靠性: 主处理器崩溃导致整个系统崩溃.

#### 对称式多处理器系统(SMP)

> SMP: Symmetric Multi-Processor, 指多处理器系统中, 各个处理器的地位相同.

按控制方式, SMP调度算法可以分为集中式控制和分布式控制.
* **集中式控制**: 静态与动态调度
  * 静态调度: 每个CPU设立一个就绪队列, 进程从开始执行到完成, 都在一个CPU上.
    * 优点: 调度算法开销小.
    * 缺点: 容易出现忙闲不均.
  * 动态调度: 为所有CPU设立一个公共就绪队列, 队首进程分配到空闲CPU上执行. 可防止系统中多个处理器忙闲不均.
* **分布式控制**: 自调度
  * 自调度:
    * 整个系统采用一个公共就绪队列, 每个处理器都可以从队列中选择适当进程来执行, 不需要专门的处理器来从事任务分配的工作.
    * 可采用单处理器的调度技术, 是最常用的算法, 实现时容易移植.
    * 面临的问题:
      * 队列同步开销: 各个处理器共享就绪队列.
      * 缓存更新开销: 被阻塞的进程重新运行时不一定仍在原有处理器上运行, Cache中的数据需要重置.
      * 线程协作开销: 由于合作中的几个线程没有同时运行而受阻, 被切换下来.

除上述调度方式之外, 还有**成组调度**和**专用处理器调度**:
* **成组调度**: 将一个进程中的一组线程, 每次分派时, 都分派到一组处理器上运行; 在剥夺处理器时, 也同时对这一组线程进行剥夺.
  * 优点:
    * 提高了线程执行的合作并发, 有利于减少阻塞和加快推进速度, 提高系统吞吐量.
    * 每次调度完成多个线程的分派, 减少了调度开销.
* **专用处理器调度**: 为进程中的每个线程都固定分配一个CPU, 直到该线程执行完成.
  * 只适用于CPU数量众多的高度并行系统, 单个CPU的利用率已经不太重要.

## 死锁及其处理方法

> 死锁:
> ```pascal
> // Thread A //
> P(mutex_a)            // <~~ 1: success
> P(mutex_b)            // <~~ 3: blocked
> // ...
> V(mutex_a)
> V(mutex_b)
> // Thread B //
> P(mutex_b)            // <~~ 2: success
> P(mutex_a)            // <~~ 4: blocked -- deadlock
> // ...
> V(mutex_b)
> V(mutex_a)
> ```

### 必要条件

1. **互斥条件**: 指进程对所分配到的资源进行排他性使用, 即在一段时间内某资源只能由一个进程占用. 若此时还有其它进程请求资源, 则请求者只能等待, 直至占有资源的进程用毕释放.
2. **请求和占有条件**: 指进程已经占有至少一个资源, 但又提出了新的资源请求, 而该资源被其他进程占有, 此时请求进程阻塞, 但又对自己已获得的其他资源保持不放.
3. **不可剥夺条件**: 指进程已获得的资源, 在未使用完之前, 不可被剥夺, 只能在使用完时由自己释放.
4. **环路等待条件**: 指在发生死锁时, 必然存在一个进程-资源环路, 即进程序列 $P_0, P_1, \dotsm, P_{n-1}$ 中 $P_i$ 等待的资源被 $P_{(i+1) \bmod n}$ 占有.

### 活锁与饥饿

* **活锁**: 是指任务或者执行者没有被阻塞, 由于某些条件没有被满足, 导致一直重复尝试/失败.
  * 与死锁的区别在于: 处于活锁的实体, 其状态在不断变化, 有可能自行解开; 死锁的实体, 其状态表现为等待, 无法解开.
* **饥饿**: 某些进程可能由于资源分配策略的不公平导致长时间等待. 当等待时间给进程推进和响应带来明显影响时, 称发生了进程饥饿.

### 处理方法

1. 死锁预防 - Prevention(Static)
2. 死锁避免 - Avoidance(Dynamic)
3. 死锁检测 - Detection

#### 死锁预防 - 打破必要条件

1. 打破互斥条件: 允许进程访问某些资源. 但**有一些资源是不允许被同时访问的, 这是资源的固有属性**.
2. 打破占有且申请条件: 可实行资源的预分配策略. 只有当系统能够满足当前进程的全部资源需求时, 才一次性将所申请的资源全部分配给该进程, 否则不分配任何资源.
   * 这种做法有缺点:
     * 多数情况下, 进程执行过程是**不可预测**的, 不可知道它需要的全部资源.
     * **资源利用率低**. 无论资源何时用到, 一个进程只有在占有所需的全部资源后才能执行. 即使有些资源最后才被用到一次, 但该进程在生存期间内却一直占有, 有极大的资源浪费.
     * **降低了进程的并发度**.
3. 打破不可剥夺条件: 即允许进程抢占资源. 当一个进程已占有某些资源, 它又申请了新的资源, 当不能立即被满足时, 就需要释放所占有的所有资源, 重新申请. 它所释放的资源可以分配给其他进程. 这就相当于该进程占有的资源被隐蔽地抢占了. 这样的方式实现起来困难, 会**降低系统的性能**.
4. 打破循环等待条件: 实行资源有序分配策略. 即把资源实现分类编号, 按号分配, 使进程在申请占用资源时不形成环路. 所有进程对资源的请求必须严格按照资源序号递增的顺序提出. 进程占用了小号资源, 才能申请大号资源, 避免环路. 这样的做法虽然基本保持了资源的利用率和吞吐量, 但有如下的缺点:
   * 限制了进程对资源的请求, 同时个系统中所有的资源合理编号也是一件困难事, 并**增加了系统开销**.
   * 为遵循按编号申请的次序, 暂不使用的资源也需要提前申请, 从而**增加了进程对资源的占用时间**.

#### 死锁避免

##### 安全序列

称一个进程序列 $P_1, P_2, \dotsm, P_n$ 是安全的, 是指: 若对于其中的任意一个进程 $P_i$ , 它需要的附加资源可以由系统中的当前可用资源, 与其之前所有进程 $P_1, P_2, \dotsm, P_{i-1}$ 占用的资源之和所满足.

若系统中不存在这样的安全序列, 则系统是不安全的.

##### 银行家算法

1. 当顾客对资金的最大需求不超过银行家现有资金时, 就可以接纳顾客.
2. 顾客可以分期贷款, 但贷款总数不可超过最大需求量.
3. 当银行家现有的资金不能满足顾客尚需的贷款数额时, 对顾客的贷款可推迟支付, 但总能使顾客在有限的时间内得到贷款.
4. 当顾客得到所需的全部资金后, 一定能在有限的时间内归还所有的资金.

设 $n$ 为进程数量, $m$ 为资源类型数量. 引入:
* $m$ 维向量 $Available$:
  * $Available_j=k$ 表示现有 $k$ 个 $R_j$ 类型的资源.
* $n\times m$ 矩阵 $Max$:
  * $Max_{i, j}=k$ 表示进程 $i$ 需要的 $R_j$ 类资源的最大数目为 $k$.
* $n\times m$ 矩阵 $Allocation$:
  * $Allocation_{i, j}=k$ 表示进程 $i$ 已获得 $R_j$ 类资源 $k$ 个.
* $n\times m$ 矩阵 $Need$:
  * $Need_{i, j}=k$ 表示进程 $i$ 还需要 $R_j$ 类资源 $k$ 个.

显然有:

$$
Need_{i, j}=Max_{i, j}-Allocation_{i, j}
$$

**银行家算法**:

设 $Request_i$ 为进程 $i$ 的请求向量:
1. 若 $Request_i\le Need_i$ 则转步骤2, 否则认为出错.
2. 若 $Request_i\le Available$ 则转步骤3, 否则进程 $i$ 必须等待.
3. 试修改数据结构:
   
   $$
   \begin{aligned}
       Available&:=Available-Request\\
       Allocation_i&:=Allocation_i+Request_i\\
       Need_i&:=Need_i-Request_i
   \end{aligned}
   $$

4. 系统执行**安全性算法**, 检查此次分配后是否处于安全状态. 若安全, 则正式将资源分配给进程 $i$ ; 否则, 本次分配作废, 上述数据结构恢复为原有状态, 进程 $i$ 等待.

**安全性算法**:
1. 设置两个向量:
   * $m$ 维向量 $Work$, 表示系统可提供给进程继续运行所需要的各类资源的数目. 安全性算法开始时, 初始化: $Work:=Available$.
   * $n$ 维向量 $Finish$, 表示系统是否有足够的资源分配给进程, 使之运行完成. 安全性算法开始时, 初始化: $Finish_i:=0$; 当有足够资源分配给进程 $i$ 时, 改 $Finish_i:=1$.
2. 从进程集合中找到一个能满足下述条件的进程:
   * $Finish_i=0$, $Need_i\le Work$. 若能找到, 则执行步骤3, 否则执行步骤4.
3. 当进程 $P_i$ 获得资源后, 可顺利执行, 直至完成, 并释放出分配给它的资源:
   
   $$
   \begin{aligned}
       Work&:=Work+Allocation\\
       Finish_i&:=1\\
   \end{aligned}
   $$

   随后转步骤2.
4. 若 $\forall i, Finish_i=1$ 则表示系统处于安全状态; 否则, 系统处于不安全状态.

银行家算法的特点是:
* 允许互斥, 部分分配和不可抢占, 提高资源利用率.
* 但要求事先说明最大资源需求, 这在实际应用中很困难.

#### 死锁检测

> 死锁检测方法, 主要是检查是否有循环等待.

##### 资源分配图(进程-资源图)

用有向图描述系统资源和进程的状态. 二元组 $G=<N,E>$ , 其中 $N$ 为结点集合, $N=P\cup R$ . 其中 $P$ 是进程集合, $R$ 是资源集合:

$$
\begin{aligned}
    P=\lbrace p_1, p_2, \dotsm, p_n\rbrace\\
    R=\lbrace r_1, r_2, \dotsm, r_m\rbrace
\end{aligned}
$$

$E$ 为有向边集合, $e\in E\Leftrightarrow e=<p_i, r_j>\lor e=<r_j, p_i>$ :
* $e=<p_i, r_j>$ 是请求边, 进程 $p_i$ 请求一个单位的 $r_j$ 型资源.
* $e=<r_j, p_i>$ 是分配边, 表示已为进程 $p_i$ 分配了一个单位的 $r_j$ 型资源.

资源分配图中, 圆形结点代表进程, 矩形结点代表一类资源, 矩形中的小圆代表一个资源.

![]({{ '/img/OS-Source-Allocation.svg' | prepend: site.baseurl}})

##### 资源分配图算法

资源分配图**化简算法**:
1. 对于进程 $P_i$ , 当进程 $P_i$ 有请求边时, 首先尝试将其请求边变成分配边.
2. 若能将其请求边都变成分配边, 则删去其所有的请求边和分配边, 使 $P_i$ 成为孤立点, 继续寻找下一个进程, 转步骤1, 直至遍历完所有进程.
3. 否则, 寻找下一个进程, 转步骤1, 直至遍历完所有进程.

**死锁定理**: 死锁的充要条件是其资源分配图化简后仍有非孤立点.

##### 死锁解除

> 死锁检测的最终目的.

1. **撤销进程**: 逐个杀死死锁进程, 直至有足够的资源位置.
2. **剥夺进程**: 挂起一些进程, 剥夺他们持有的资源, 待条件满足后, 再激活他们.