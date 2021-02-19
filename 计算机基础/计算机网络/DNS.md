## DNS

#### 简介

- Domain Name System 
- 根据域名查出 IP 地址，可以把它想象成一本巨大的电话本
- 比如访问域名 www.baidu.com，首先要通过 DNS 查出它的IP地址是 14.215.177.38



#### 查询过程

- 要返回一个 IP 地址，DNS 的查询过程非常复杂，分成多个步骤
- 可以在命令行中使用 dig 命令显示整个查询过程

```
dig www.baidu.com
```

```
; <<>> DiG 9.10.6 <<>> www.baidu.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 41184
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;www.baidu.com.			IN	A

;; ANSWER SECTION:
www.baidu.com.		30	IN	CNAME	www.a.shifen.com.
www.a.shifen.com.	30	IN	A	14.215.177.38
www.a.shifen.com.	30	IN	A	14.215.177.39

;; Query time: 10 msec
;; SERVER: 192.168.31.1#53(192.168.31.1)
;; WHEN: Thu Feb 18 12:07:36 CST 2021
;; MSG SIZE  rcvd: 104
```

- 第一段是查询参数和统计

```
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 28445
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1
```

- 第二段是查询内容

```
;; QUESTION SECTION:
;www.baidu.com.			IN	A
```

- 第三段是 DNS 服务器的答复

```
;; ANSWER SECTION:
www.baidu.com.		30	IN	CNAME	www.a.shifen.com.
www.a.shifen.com.	30	IN	A	14.215.177.38
www.a.shifen.com.	30	IN	A	14.215.177.39
```

- 第四段显示所查询地址的 NS 记录（Name Server），即哪些服务器负责管理所查询地址的 DNS 记录
- 第五段是上面四个域名服务器的IP地址，这是随着前一段一起返回的
- 第六段是DNS服务器的一些传输信息

```
;; Query time: 10 msec
;; SERVER: 192.168.31.1#53(192.168.31.1)
;; WHEN: Thu Feb 18 11:10:46 CST 2021
;; MSG SIZE  rcvd: 104
```



#### DNS 服务器

- DNS服务器的IP地址有可能是动态的，每次上网时由网关分配，叫做 DHCP 机制
- 也有可能是事先指定的固定地址，Linux系统里面，DNS 服务器的 IP 地址保存在 /etc/resolv.conf 文件



#### 域名层级

- DNS 服务器通过分级查询知道每个域名的IP地址
- www.baidu.com 真正的域名是 www.baidu.com.root，简写为 www.baidu.com. / www.baidu.com，根域名 .root 对于所有域名都是一样的，平时省略
- 根域名的下一级，叫做"顶级域名"（top-level domain，缩写为TLD），比如 .com、.net
- 顶级域名的下一级叫做"次级域名"（second-level domain，缩写为SLD），比如 www.baidu.com 里面的 .baidu，这一级域名是用户可以注册的
- 次级域名的下一级是主机名（host），比如 www.baidu.com 里面的 www，又称为"三级域名"，这是用户在自己的域里面为服务器分配的名称，是用户可以任意分配的
- 总结
  - 主机名.次级域名.顶级域名.根域名
  - host.sld.tld.root



#### 根域名服务器

- "根域名服务器"的NS记录和IP地址一般是不会变化的，内置在 DNS 服务器里面
- 每一级域名都有自己的 NS 记录，NS 记录指向该级域名的域名服务器，这些服务器知道下一级域名的各种记录
- 所谓"分级查询"，就是从根域名开始，依次查询每一级域名的NS记录，直到查到最终的 IP 地址，过程大致如下
  - 从"根域名服务器"查到"顶级域名服务器"的 NS 记录和 A 记录（IP地址）
  - 从"顶级域名服务器"查到"次级域名服务器"的 NS 记录和 A 记录（IP地址）
  - 从"次级域名服务器"查出"主机名"的 IP 地址



#### 分级查询

- dig 命令的 +trace 参数可以显示DNS的整个分级查询过程

```
dig +trace www.baidu.com
```

- 最先回复的根域名服务器将被缓存，以后只向这台服务器发请求，其他域名同理

```
; <<>> DiG 9.10.6 <<>> +trace www.baidu.com
;; global options: +cmd
.			1993	IN	NS	l.root-servers.net.
.			1993	IN	NS	c.root-servers.net.
.			1993	IN	NS	d.root-servers.net.
.			1993	IN	NS	b.root-servers.net.
.			1993	IN	NS	a.root-servers.net.
.			1993	IN	NS	f.root-servers.net.
.			1993	IN	NS	j.root-servers.net.
.			1993	IN	NS	g.root-servers.net.
.			1993	IN	NS	e.root-servers.net.
.			1993	IN	NS	k.root-servers.net.
.			1993	IN	NS	h.root-servers.net.
.			1993	IN	NS	i.root-servers.net.
.			1993	IN	NS	m.root-servers.net.
;; Received 239 bytes from 192.168.31.1#53(192.168.31.1) in 16 ms

com.			172800	IN	NS	a.gtld-servers.net.
com.			172800	IN	NS	b.gtld-servers.net.
com.			172800	IN	NS	c.gtld-servers.net.
com.			172800	IN	NS	d.gtld-servers.net.
com.			172800	IN	NS	e.gtld-servers.net.
com.			172800	IN	NS	f.gtld-servers.net.
com.			172800	IN	NS	g.gtld-servers.net.
com.			172800	IN	NS	h.gtld-servers.net.
com.			172800	IN	NS	i.gtld-servers.net.
com.			172800	IN	NS	j.gtld-servers.net.
com.			172800	IN	NS	k.gtld-servers.net.
com.			172800	IN	NS	l.gtld-servers.net.
com.			172800	IN	NS	m.gtld-servers.net.
com.			86400	IN	DS	30909 8 2 E2D3C916F6DEEAC73294E8268FB5885044A833FC5459588F4A9184CF C41A5766
com.			86400	IN	RRSIG	DS 8 1 86400 20210302210000 20210217200000 42351 . RLzPfn8l2VPhw3ARzvoUN4DiCabbYboFUHrYETL0m1Pu6xDLMylJhfRF iePDM+oqdbWmQdKNuwriqywwyl/qn//XArA9fa9kVScM1UAnIaY4Xp7Z 07L63iadhJnQfx7ClzPf8z5Eb8gQxNxhJ6D/lgYaTBrGfoxnji6qM+uh YThfXFPjdJY66kJ9kUdLDsrH96SVLi1Cq9AYkbFoQ0PDPjQMD3P+g10l 4IhiGnpdHL6Kq3bPHJlugi9BLcOxeGPPhY/s/iriev9ETei3Mz9jOE9o e/0h99S3mq1VQ2H6GA2Xh+xAG5tWsXs/ue3eGfsnq147i5xCUdNfgxI1 qh3D4w==
;; Received 1173 bytes from 192.203.230.10#53(e.root-servers.net) in 313 ms

baidu.com.		172800	IN	NS	ns2.baidu.com.
baidu.com.		172800	IN	NS	ns3.baidu.com.
baidu.com.		172800	IN	NS	ns4.baidu.com.
baidu.com.		172800	IN	NS	ns1.baidu.com.
baidu.com.		172800	IN	NS	ns7.baidu.com.
CK0POJMG874LJREF7EFN8430QVIT8BSM.com. 86400 IN NSEC3 1 1 0 - CK0Q1GIN43N1ARRC9OSM6QPQR81H5M9A  NS SOA RRSIG DNSKEY NSEC3PARAM
CK0POJMG874LJREF7EFN8430QVIT8BSM.com. 86400 IN RRSIG NSEC3 8 2 86400 20210221054057 20210214043057 58540 com. UXd7aJao94p+1Czk8pFmnvcYMuauRA7J4YTmqXFHm+kjJDuAslBRtPW3 cvJRc5RSbl2NZ8AB2gAgV5NOXGj7S/a/pDYWkl6SMxDMDamRcwR8KeFH NIKkfkNsQ5dORu1JmucmDa8jr+8EukFbigK98iBtfix8dYbdI6qqqpno CQ16Z7zpdinU2I5BGNx4gsw3wU2L/cR0rGlcPFdIVLRc+g==
HPVUSBDNI26UDNIV6R0SV14GC3KGR4JP.com. 86400 IN NSEC3 1 1 0 - HPVVN3Q5E5GOQP2QFE2LEM4SVB9C0SJ6  NS DS RRSIG
HPVUSBDNI26UDNIV6R0SV14GC3KGR4JP.com. 86400 IN RRSIG NSEC3 8 2 86400 20210221071750 20210214060750 58540 com. rKRvJTxgzPKruA77ymuU1nUp9+aW8TbMKBCcwnDifhX4l3asn1o8fme0 C+pT92d3IEt6EIfrxR3gVa4MzPGRJV+hrUhtpRv/BmDVfcLv2CY8oWcq RYfT3Iu8xWVVCLoWqYAPgCV4I+1gL5fxukBTGMPUUutIPP22GyNL8gGC Jjezu682UU5ZuYXqmdC096MPXQIznRBrtS9470pNqL3/2Q==
;; Received 761 bytes from 192.54.112.30#53(h.gtld-servers.net) in 260 ms

www.baidu.com.		1200	IN	CNAME	www.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns1.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns5.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns3.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns2.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns4.a.shifen.com.
;; Received 239 bytes from 112.80.248.64#53(ns3.baidu.com) in 65 ms
```



#### DNS 记录类型

- 域名与 IP 之间的对应关系，称为"记录"（record）

- 根据使用场景，"记录"可以分成不同的类型（type）

- 常见的 DNS 记录类型

  - A：地址记录（Address），返回域名指向的IP地址

  - NS：域名服务器记录（Name Server），返回保存下一级域名信息的服务器地址。该记录只能设置为域名，不能设置为IP地址
  - MX：邮件记录（Mail eXchange），返回接收电子邮件的服务器地址
  - CNAME：规范名称记录（Canonical Name），返回另一个域名，即当前查询的域名是另一个域名的跳转
  - PTR：逆向查询记录（Pointer Record），只用于从IP地址查询域名

- 一般来说，为了服务的安全可靠，至少应该有两条 NS 记录，而 A 记录和 MX 记录也可以有多条，这样就提供了服务的冗余性，防止出现单点失败

- CNAME 记录

  - 主要用于域名的内部跳转，为服务器配置提供灵活性，用户感知不到
  - 比如，facebook.github.io 这个域名就只有一个 CNAME 记录

  ```
  $ dig facebook.github.io
  
  ...
  
  ;; ANSWER SECTION:
  facebook.github.io. 3370    IN  CNAME   github.map.fastly.net.
  github.map.fastly.net.  600 IN  A   103.245.222.133
  ```

  - facebook.github.io 的 CNAME 记录指向 github.map.fastly.net。也就是说，用户查询 facebook.github.io 的时候，实际上返回的是 github.map.fastly.net的 IP地址，好处是，变更服务器 IP 地址的时候，只要修改 github.map.fastly.net 这个域名就可以了，用户的 facebook.github.io 域名不用修改
  - 由于 CNAME 记录就是一个替换，所以域名一旦设置 CNAME 记录以后，就不能再设置其他记录了，这是为了防止产生冲突
    - 比如，ab.com 指向 ba.com ，而两个域名各有自己的 MX 记录，如果两者不一致，就会产生问题
  - 由于顶级域名通常要设置 MX 记录，所以一般不允许用户对顶级域名设置 CNAME 记录

- PTR 记录

  - 用于从IP地址反查域名
  - dig 命令的 -x 参数用于查询 PTR 记录

- dig`命令可以查看指定的记录类型

```

```

