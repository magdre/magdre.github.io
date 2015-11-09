---
layout: post
title:  "优化系统"
date:   2015-10-19 18:06:05
category: linux
excerpt: "SELinux,iptables,SSH,系统字符集,文件描述符,服务器时间同步"
---

* content
{:toc}

---

本文对系统做出视频优化， 针对刚入门linux的人来说了解其如何配置是必要的，之后在需要的时候也可适当配置。

---

## SELinux

SELinux（Security-enhanced linux）是美国国家安全局（nsa）对于强制访问控制的实现，是linux历史上最杰出的新安全子系统。 但因为控制太严格，生产环境不用它，使用其他的安全手段。现在有关配置文件放在 /etc/selinux/config 下

	$ cat /etc/selinux/config 

	# This file controls the state of SELinux on the system.
	# SELINUX= can take one of these three values:
	#     enforcing - SELinux security policy is enforced.
	#     permissive - SELinux prints warnings instead of enforcing.
	#     disabled - No SELinux policy is loaded.
	SELINUX=disabled
	# SELINUXTYPE= can take one of these two values:
	#     targeted - Targeted processes are protected,
	#     mls - Multi Level Security protection.
	SELINUXTYPE=targeted 


由此可以看到SELinux 用三个值， enforcing， permissive， disabled。
* enforcing 就是如果你违反了策略，你就无法继续操作下去。
* Permissive 就是selinux 有效， 但即使你违反了策略的也会让你继续操作，但是把你违反的内容记录下来。
* disabled 就是关闭selinux 

SELINUXTYPE 主要有两大类，
* targeted 是红帽
*








