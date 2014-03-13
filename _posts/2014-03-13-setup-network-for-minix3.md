---
layout: default
title: setup networking for minix3 in virtualbox
---
Firstly, following is my system:
	Ubuntu 13.10 amd686, VirtualBox 4.3.8, minix3.2.0

After following steps in http://wiki.minix3.org/Releases/3.2.0/UsersGuide/RunningMinixOnVirtualBox, I found networking does not work. With many tries, I setuped it successfully.

Login the system as root, type netconf comamnd. After selecting the default Ehternet car, I chose to configure network manully. Following is my typing:
	Hostname [%nameserver]:[blank]
	IP address [10.0.0.1]: 10.0.2.15
	Netmask [255.255.255.0]: [blank]
	Gateway: 10.0.2.2
	Primary DNS Server: [blank]
	Secondary DNS Server: [blank]

then modify the /etc/resolv.conf as following:
	nameserver 8.8.8.8
	nameserver 8.8.4.4

And finally, reboot the system, and Everything is ok :).

