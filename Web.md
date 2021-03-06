### Apache 3种MPM（多进程处理模块）
	1) Prefork:	Apache启动之初，预先fork一些子进程，每个子进程中只有一个线程，只能处理一个请求
	  a) 优点：成熟稳定，兼容新老模块；不需要担心线程安全问题
	  b) 缺点：进程占用资源较多，不擅长处理高并发请求。
	2) Worker:	与Prefork类似，也预先fork了一些子进程（数量较少），每个子进程中创建一些线程。
	(P.S.)为什么不直接用多线程？
	考虑稳定性，如果一个线程异常挂了，会导致父进程连同其他正常的子线程都挂掉，因此不能全部使用多线程
	  a) 优点：占用内存少，高并发表现优秀
	  b) 缺点：需要考虑线程安全问题，keep-alive场景会造成资源浪费
	3) Event:	有一个专门的线程管理keep-alive类型的线程，当有接收到真实请求时，它会传递给服务线程，执行完毕后，允许其释放资源
	(P.S.)解决了Worker的什么问题？
	keep-alive场景下，没有请求发送的时候，线程会一直占用资源，造成资源浪费。
	
### API网关
	针对微服务API设计的服务器。
	这篇文章讲的很好 [谈谈微服务中的 API 网关（API Gateway）](https://www.cnblogs.com/savorboard/p/api-gateway.html)

### BOM弹窗
	1) alert("a")
	2) confirm("a")
	3) prompt("a", "b")
