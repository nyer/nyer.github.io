---
layout: default
title: 在ubuntu下利用iptables端口转发本地80端口访问tomcat服务
---

众所周知，在eclipse下面启动tomcat时候，如果端口设置为80端口的话，会提示没有权限，这是因为linux默认在1024以下端口开启服务需要root权限。这给程序的开发带来了不少麻烦，因为程序的运行一般不会在以root身份运行。针对这样的情况，可以通过iptables的设置来转发访问80端口的流量到tomcat服务端口，比如8080,这样可以达到访问本地80端口来访问tomcat服务的目的，减少了开发的不便。注意，以下的命令得以root身份运行。

首先查看iptables的端口转发功能是否开启。打开`/etc/sysctl.conf`，查找行`net.ipv4.ip_forward`是否等于1,如果不是将其改为1，然后保存。

然后运行以下命令：
	iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-ports 8080
	iptables -t nat -A OUTPUT -d localhost -p tcp --dport 80 -j REDIRECT --to-ports 8080
然后运行`iptables -t nat -L -v`来查看转发项是否已经添加，以下是我的输出：
	Chain PREROUTING (policy ACCEPT 4492 packets, 810K bytes)
	 pkts bytes target     prot opt in     out     source               destination
		0     0 REDIRECT   tcp  --  any    any     anywhere             anywhere             tcp dpt:http redir ports 8080

	Chain INPUT (policy ACCEPT 4491 packets, 810K bytes)
	 pkts bytes target     prot opt in     out     source               destination

	Chain OUTPUT (policy ACCEPT 1210 packets, 79335 bytes)
	 pkts bytes target     prot opt in     out     source               destination
		1    60 REDIRECT   tcp  --  any    any     anywhere             localhost            tcp dpt:http redir ports 8080

	Chain POSTROUTING (policy ACCEPT 1211 packets, 79395 bytes)
	 pkts bytes target     prot opt in     out     source               destination 

这时候，如果8080端口开启了tomcat服务，那么可以通过80端口访问tomcat服务了。但是注意，上述的操作会在下次重启后失效，如果要保证上述的操作持久，可以运行以下命令：
	iptables-save > /etc/iptables.up.rules # 如iptables.up.rule不存在，可以手动建一个
然后再编辑`/etc/network/interfaces`文件，添加以下2行：
	pre-up iptables-restore < /etc/iptables.up.rules #系统启动时候恢复`/etc/iptables.up.rules`到系统转发表
	post-down iptables-save > /etc/iptables.up.rules #系统关闭时保存系统转发表到`/etc/iptables.up.rules` 

如果要删除以上的转发，可以运行`iptables -t nat -X`清空用户添加的记录，然后运行`iptables-save > /etc/iptables.up.rules`。

