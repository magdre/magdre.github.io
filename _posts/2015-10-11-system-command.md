---
layout: post
title:  "系统管理以及系统设置的命令."
date: 2015-10-11 15:08:58 
categories: linux
excerpt: "chkconfig inittab"

---

* content
{:toc}



## 系统设置命令

###  **chkconfig**

  updates and queries runlevel information for system services 更新与修改系统每个启动级别下的服务信息  

* *语法*:

		chkconfig [--list] [--type type][name]
		chkconfig --add name
		chkconfig --del name
		chkconfig --override name
		chkconfig [--level levels] [--type type] name <on|off|reset|resetpriorities>
		chkconfig [--level levels] [--type type] name

* *描述*:
  chkconfig 提供了一些简单的命令来管理/etc/rc[0-6].d 这些目录层次结构里的众多符号链接文件,  从而减轻了linux管理员的负担.  
  chkconfig 一共有五个功能: 增加与删除(--add, --del)一个服务; 列出(--list)当前启动的服务信息; 更改(on|off|reset|resetpriorities)当前启动服务的信息,如果不指定level那么默认管理的是2345这四个级别上的信息;以及单独查看特定服务的启动状况. 

* *参数*:
  `--level`  指定启动级别(0-6),如果想指定多个 可以 --level 35. 

  **Runlevel简单介绍:** 

  linux操作系统自从开始启动至启动完毕需要经历几个不同的阶段（用数字表示），这几个阶段就叫做runlevel，同样，当linux操作系统关闭时也要经历另外几个不同的runlevel， 

	$ cat /etc/inittab  #查看runlevel 的运行级别
	#Default runlevel. The runlevels used are:
	#  0 - halt (Do NOT set initdefault to this) 关机
	#  1 - Single user mode 用来找回root密码
	#  2 - Multiuser, without NFS (The same as 3, if you do not have networking)
	#  3 - Full multiuser mode 多用户文本模式(工作模式)
	#  4 - unused
	#  5 - X11
	#  6 - reboot (Do NOT set initdefault to this)

最后一行: `id:3:initdefault:` 的3便是默认的启动级别. 我们可以用下面命令临时切换启动级别

	$ init 6

系统默认会自动启动很多服务,为了节省开机时间，加快启动速度；节省资源开销； 减少安全隐患。我们只启动关键的几个服务(sshd,远程连接. rsyslog,系统守护程序通常会使用rsyslog将各种信息写到各系统日志文件中. netowrk.  crond,闹钟服务. sysstat,监测系统性能及效率的一组工具), 在centos里也可以用`setup` 选第四项system services来选择修改系统启动项。或者类似windows下的msconfig 输入`ntsysv` 也可以进入system services来个性系统启动项. 下面主要介绍用chkconfig来关闭管理启动服务. 

查看系统启动项

	$ chkconfig --list
	# abrt-ccpp       0:off   1:off   2:off   3:on    4:off   5:on    6:off
	# abrtd           0:off   1:off   2:off   3:on    4:off   5:on    6:off
	# acpid           0:off   1:off   2:on    3:on    4:on    5:on    6:off
	# atd             0:off   1:off   2:off   3:on    4:on    5:on    6:off
	# auditd          0:off   1:off   2:on    3:on    4:on    5:on    6:off
	…

先用awk把服务名称取出来并用grep把上述五个服务排除

	$ chkconfig --list|awk '{print $1}' |grep -Ev "sshd|rsyslog|network|crond|sysstat" >runlevel3on.txt

然后可以用三种方式来关闭这些服务

	$ for name in `cat runlevel3on.txt`;do chkconfig $name --level 3 off;done   #最喜欢这一种

	$ cat runlevel3on.txt |awk '{print "chkconfig " $1 " --level 3 off"}' |bash
	$ awk '{print "chkconfig " $1 " --level 3 off"}' runlevel3on.txt |bash

	$ cat runlevel3on.txt |sed 's#\(.*\)#chkconfig –-level 3 \1 off#g' |bash
	$ sed 's#\(.*\)#chkconfig –-level 3 \1 off#g' runlevel3on.txt |bash
	$ sed –r 's#(.*)#chkconfig –-level 3 \1 off#g' runlevel3on.txt |bash

 **chkconfig 关闭原理介绍**

用`ls /etc/rc.d/rc3.d` 可以看到每个服务名称前面都会添加 'K[0-9]{2}'或'S[0-9]{2}' 两种名字. 其中前面 `K` 的意思是当前启动级别下这个服务未启动. `S` 的意思是当前启动级别下这个服务开机启动, 后面两个数字分别代表启动顺序与关闭顺序, 一般来说先启动的后关闭.我们可以模拟一下.

在/etc/init.d 下面创建一个oldboy文件,并且在里面输入下面信息(注意前面两井号是我为了区分命令与输出的符号加内容的时候要去掉),注意这里脚本开头必须要有包含chkconfig 管理的代码. 然后给这个文件一个执行权限. 

	$ cat oldboy 
	# #chkconfig:  2345 91 61
	# echo welcome to oldboy training
 
然后使用命令把他启动并且添加到chkconfig中

	$ /etc/init.d/oldboy start && chkconfig --add oldboy
	$ chkconfig |grep oldboy
	# oldboy          0:off   1:off   2:on    3:on    4:on    5:on    6:off

这时便可以看到chkconfig 便可以管理oldboy这个服务了. 这时我们可以看到在 /etc/rc.d/rc3.d 里面有一个符号链接文件 'S91oldboy' 如果把3:on 改为 3:off 这时便会看到的文件便是 'K61oldboy' . 其它rc[0-6].d 也是同样的道理. 由此我们可以看到 `chkconfig --level 3 oldboy on` 的实质便是

	rm -f /etc/rc.d/rc3.d/K61sshd
	ln -s /etc/init.d/sshd /etc/rc.d/re3.d/S91sshd

或者

	mv /etc/rc.d/rc3.d/K61sshd /etc/rc.d/rc3.d/S91sshd


## stat: display file or file system status

描述： 查看文件或者系统的信息即查看inode的所有信息

	$ stat a.txt 
	  File: "a.txt"
	  Size: 4476      	Blocks: 16         IO Block: 4096   普通文件
	Device: 803h/2051d	Inode: 131566      Links: 1
	Access: (0645/-rw-r--r-x)  Uid: (  501/  sandow)   Gid: (  501/  sandow)
	Access: 2015-10-20 11:03:01.912088501 +0800
	Modify: 2015-10-20 14:43:58.882564527 +0800
	Change: 2015-10-20 14:43:58.882564527 +0800

语法：

	stat [OPTION]... FILE...

参数：
-L, --dereference      #follow links
       -f, --file-system
              display file system status instead of file status

       -c  --format=FORMAT
              use the specified FORMAT instead of the default; output  a  new‐
              line after each use of FORMAT

       --printf=FORMAT
              like  --format, but interpret backslash escapes, and do not out‐
              put a mandatory trailing newline; if you want a newline, include
              \n in FORMAT

       -t, --terse
              print the information in terse form

       --help display this help and exit

       --version
              output version information and exit

       The valid format sequences for files (without --file-system):

       %a     access rights in octal

       %A     access rights in human readable form

       %b     number of blocks allocated (see %B)

       %B     the size in bytes of each block reported by %b

       %C     SELinux security context string

       %d     device number in decimal

       %D     device number in hex

       %f     raw mode in hex

       %F     file type

       %g     group ID of owner

       %G     group name of owner

       %h     number of hard links

       %i     inode number

       %m     mount point

       %n     file name

       %N     quoted file name with dereference if symbolic link

       %o     optimal I/O transfer size hint

       %s     total size, in bytes

       %t     major  device  type  in  hex, for character/block device special
              files

       %T     minor device type in hex,  for  character/block  device  special
              files

       %u     user ID of owner

       %U     user name of owner

       %w     time of file birth, human-readable; - if unknown

       %W     time of file birth, seconds since Epoch; 0 if unknown

       %x     time of last access, human-readable

       %X     time of last access, seconds since Epoch

       %y     time of last data modification, human-readable

       %Y     time of last data modification, seconds since Epoch

       %z     time of last status change, human-readable

       %Z     time of last status change, seconds since Epoch

       Valid format sequences for file systems:

       %a     free blocks available to non-superuser

       %b     total data blocks in file system

       %c     total file nodes in file system

       %d     free file nodes in file system

       %f     free blocks in file system

       %i     file system ID in hex

       %l     maximum length of filenames

       %n     file name

       %s     block size (for faster transfers)

       %S     fundamental block size (for block counts)

       %t     file system type in hex

       %T     file system type in human readable form

       NOTE: your shell may have its own version of stat, which usually super‐
       sedes the version described here.  Please refer to your  shell's  docu‐
       mentation for details about the options it supports.

AUTHOR
       Written by Michael Meskes.

REPORTING BUGS
       GNU coreutils online help: <http://www.gnu.org/software/coreutils/>
       Report stat translation bugs to <http://translationproject.org/team/>

COPYRIGHT
       Copyright  ©  2014  Free Software Foundation, Inc.  License GPLv3+: GNU
       GPL version 3 or later <http://gnu.org/licenses/gpl.html>.
       This is free software: you are free  to  change  and  redistribute  it.
       There is NO WARRANTY, to the extent permitted by law.

SEE ALSO
       stat(2)

       Full documentation at: <http://www.gnu.org/software/coreutils/stat>
       or available locally via: info '(coreutils) stat invocation'




date +%F
date +%y-%m-$d\ %H:%M:%S

date +%F\ %T









