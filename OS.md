### 进程与线程是什么及不同
	进程：封装了运行的程序，是系统资源调度和分配的基本单位，是操作系统级别的并发
	线程：是进程的子任务，是CPU调度的基本单位，是进程内部的并发

	不同：
	1) 一个程序运行至少一个进程，一个进程至少一个线程
	2) 进程拥有独立的内存单元，同一进程中的线程共享进程中的内存
	3) 线程创建，撤销和切换时，开销都远远小于进程

### 进程间通信的几种方式
	1) 管道：用于有血缘关系的进程通信（父子，兄弟）
	2) 命名管道（FIFO）：无关进程也可通信
	3) 消息队列：有写权限的进程向消息队列中写一个消息，又读权限的进程读取消息
	4) 信号量：用于互斥和同步（PV）
	5) 共享内存：多个进程共享一片内存区域，需要信号量来进行互斥同步操作
	6) socket：不同网络间的进程通信

### 线程同步互斥的方法
	1) 临界区：同一进程中线程对同一资源访问
	2) 互斥量：不同进程中线程对同一资源访问
	3) 事件：线程间通过触发事件来进行同步
	4) 信号量：信号量允许多个线程访问同一资源（P:申请资源 S：释放资源）

### 什么是死锁？发生条件？避免方法？
	死锁：p1,p2,r1,r2，p1占用r1，p2占用r2，这时，p1想占用r2，p2想占用r1，两者都进入阻塞状态，产生死锁

	条件：
	1) 互斥：非共享资源
	2) 占有并等待：占有一个资源，等待另一个正被占有的资源
	3) 非抢占
	4) 循环等待：形成进程环，后一个等待占用前一个的资源

	避免：
	1) 打破互斥：共享资源
	2) 打破占有并等待：资源预分配
	3) 打破非抢占：抢占
	4) 打破循环等待：资源有序分配策略

### 进程状态
	1) 就绪状态：已获得除处理机外的其他资源
	2) 运行状态：正在运行
	3) 阻塞状态：进程等待某种条件
![progress](images/OS_1.jpg)

### 内存分段，分页不同
	分段：根据用户需要，分为代码段，数据段，堆栈段等，长度可变，易产生外部碎片
	分页：根据系统管理需要，将内存分为固定大小的片段（4kb），易产生内部碎片

	不同：
	1) 分段用户需要，分页系统需要
	2) 分段长度可变，分页长度固定
	3) 分段提供二位地址空间，分页一维
	4) 分段产生外部碎片，分页产生内部碎片（内：分配了，没用了  外：空间太小，不够分配）

### 操作系统中进程调度策略
	1) FCFS（先来先服务）
	2) 时间短优先
	3) 优先级
	4) 时间片轮转
	5) 多级队列：根据时间等策略，将进程分级，但是不能移动分级队列
	6) 多级反馈队列：可根据情况，移动进程到不同队列

### 虚拟内存页面置换算法
	1) FIFO
	2) 最近最少使用
	3) 使用次数最少
	4) 最优置换

### 颠簸
	置换出一个页，立即再次需要这个页，如此往复

	解决办法：
	1) 改代码
	2) 加内存
	3) 关闭其他进程

### 局部性原理
	1) 时间：最近被访问的页不久会被再次访问
	2) 空间：被访问的页周围更容易被访问

### 系统运行级别
	1) 运行级别0：系统停机状态，系统默认运行级别不能设为0，否则不能正常启动 
	2) 运行级别1：单用户工作状态，root权限，用于系统维护，禁止远程登陆 
	3) 运行级别2：多用户状态(没有NFS) 
	4) 运行级别3：完全的多用户状态(有NFS)，登陆后进入控制台命令行模式 
	5) 运行级别4：系统未使用，保留 
	6) 运行级别5：X11控制台，登陆后进入图形GUI模式 
	7) 运行级别6：系统正常关闭并重启，默认运行级别不能设为6，否则不能正常启动
