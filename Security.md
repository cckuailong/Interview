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

### mysql 休眠函数

### xss 触发事件

### substr

### arp欺骗及防御

### ssrf

### 本地文件包含，函数，写文件

### 宽字节注入

### 哈希长度扩展攻击

### python2,3区别

