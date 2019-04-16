### DNS zone数据的4个部分
	1) 所有节点的权威数据
	2) 顶端节点的数据
	3) 描述所授权subzone的数据
	4) 用于访问subzone域名服务器的数据（胶水记录）
	
### zone数据表示为资源记录（RR），包含5字段
	NAME,TYPE,CLASS,TTL,RDATA
	
### DNS消息格式
	header,question,answer,authority,additional
	其中header，question在请求和问答消息中都有，且请求和应答中的header里的查询ID需要相同
	
### DNS for CDN
	递归服务器迭代查询访问到目标站点后，目标站点用CNAME字段给出推荐CDN的域名，递归服务器再进行CDN域名的解析，最后访问的实际是CDN服务器。
	
### DNS攻击
	1) 分组窃听与伪造应答
		攻击者窃听到递归服务器的查询请求，并在权威服务器应答前，伪造应答信息进行应答。
![](https://github.com/cckuailong/Interview/blob/master/%E8%AE%A1%E7%BD%91%E5%9F%BA%E7%A1%80%E6%80%BB%E7%BB%93/img/1.png)

	2) ID猜测与查询预测
		攻击者无法窃听，但可以根据受害者行为来猜测请求查询ID及受害者客户端UDP端口，从而伪造信息进行应答。
![](https://github.com/cckuailong/Interview/blob/master/%E8%AE%A1%E7%BD%91%E5%9F%BA%E7%A1%80%E6%80%BB%E7%BB%93/img/2.png)

	3) Kaminsky攻击
		a. 攻击者通过受操控客户端查询一个不存在的域名
		b. 递归服务器缓存没有命中，开始迭代查询（问权威服务器）
		c. 攻击者服务端通过ID猜测，发送大量伪造应答包，对缓存进行下毒。
![](https://github.com/cckuailong/Interview/blob/master/%E8%AE%A1%E7%BD%91%E5%9F%BA%E7%A1%80%E6%80%BB%E7%BB%93/img/3.png)

	4) 名字链
		a. 受控客户端发送攻击者域名查询
		b. 递归服务器访问攻击者服务器
		c. 攻击者服务器伪造应答：攻击者服务器域名-->被攻击者域名-->伪造的被攻击者IP
![](https://github.com/cckuailong/Interview/blob/master/%E8%AE%A1%E7%BD%91%E5%9F%BA%E7%A1%80%E6%80%BB%E7%BB%93/img/4.png)

	5) 可信服务器欺骗
		连上不可信网络（恶意wifi等）
![](https://github.com/cckuailong/Interview/blob/master/%E8%AE%A1%E7%BD%91%E5%9F%BA%E7%A1%80%E6%80%BB%E7%BB%93/img/5.png)

### DNSSEC
	1) KSK对zone中的ZSK签名
	2) ZSK对zone中的RR进行签名
	3) ZSK对subzone的KSK的DS进行签名，并将其放在当前zone
	4) 信任链：DNSKEY-->[DS-->DNSKEY]*-->RRset
	
### 密钥分离优点
	1) ZSK更新时，不需要父子zone交互
	2) KSK可以很长，安全性高，不需要经常使用，更新频率低
	3) ZSK相对较短，加密速度快，需要经常使用，更新频率较高
	
### NSEC
	对DNS否定存在的认证（证实dns不存在）
	域名空间呈环形
	右标签开始，向左排序，若域名，type，class，TTL(缺省值)都相同，按RDATA排序。
	问题：
		zone walking: 暴露zone数据
	解决：
		NSEC3：域名多次hash后排序
		
### 密钥滚动：
	1) 提前发布key：
		不需要签两次名，但是步骤增加，KSK更新更加复杂
	2) 同时发布key和sig：
		需要签两次名，步骤简单