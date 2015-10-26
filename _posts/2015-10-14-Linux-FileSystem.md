---
layout: post
title:  "linux文件目录结构"
date:   2015-10-14 23:55:05
categories: linux
excerpt:  目录结构参照FHS2.3，鸟哥. 并简单介绍硬盘的结构
---
* 文件目录结构
{:toc}

## Linux 文件系统大览
  linux下的所有的目录都是按照一定的类别有规律的组织和命名，每个目录数据可以跨越不同的磁盘分区或者不同的磁盘设备，所有目录都可以按两个类别来分：静态的与动态的，或者可共享的与不可共享的。可共享一个用户储存的文件可以被其它用户查看。静态的文件包括binaries, libraries, documenttation和其它在超级管理员不更改的情况下处于不变的文件。

|        |Shareable      | unshareable|
|:---    |:------------  |:-----------|
|static  |/usr           |/etc        |
|        |/opt           |/boot       |
|variable|/var/mail      |/var/rum    |
|        |/var/spool/news|/var/lock   |

Linux 系统的所有目录是一个有层次的倒着的树状目录结构，"／"  根是所有目录的起点可以用   

	$ tree -L 1 /


|**/bin** | Essential command binaries 存放着所有用户都可以用的二进制命令.  
|**/boot** | Static files of the boot loader 内核及系统引导程序所需的文件目录.  
|**/dev** |  Device files.  
|**/etc** | Host-specific system configuration,配置文件的默认路径.   
|**/lib/lib64** | Essential shared libraries and kernel modules,库文件的目录.  
|**/lost+found** | 系统突然崩溃突然关机，系统产生的一些文件碎片在这里  fsck检查修复
|**/media** | Mount point for removeable media.   
|**/mnt** | Mount point for mounting a filesystem temporarily,文件系统的临时挂载点.
|**/opt** | Add-on application software packages，可选的第三方软件的安装目录.  
|**/proc** | Kernel and process information virtual filesystem,内核与进程信息的虚拟文件系统.  
|**/root** | Home directory for the root user (optional), 超级管理员的家目录.  
|**/home** | User home directories (optional)，普通用户的家目录.  
|**/sbin** | Essential system binaries,系统二进制命令文件.  
|**/srv** | Data for services provided by this system.  
|**/tmp** | Temporary files 临时文件存放目录.  
|**/usr** | Secondary hierarchy 第二层次结构, 存放系统程序以及用户安装程序的目录.  
|**/var** | Variable data 可变的数据.  

---

### 一些简单的补充

* /bin  存放着一些所有用户都可以用的二进制命令
  这个目录下面存放着的命令有：cat, chgrp, chmod, chown, cp, date, dd, df, dmesg, echo, false, hostaname, kill, ln, login, ls, mkdir, mknod, more, mount, mv, ps, pwd, rm, rmdir, sed, sh, stty, su, sync, true, umount, uname.

* /sbin 存放着只允许超级管理员才可以用的二进制命令，还可以存放在/usr/sbin /usr/local/sbin  
  sbin 包含了一些系统启动，修复命令。

* /boot 是linux 内核及系统引导程序所需的文件目录。安装系统分区时一般先分/boot 100－200M 可以用`du -sh /boot`来查看大小  

		du -sh /boot
		#39M     /boot

---

## /etc Host-specific system configuration
  这个层次结构包含程序的配置文件，它必须是静态的不允许执行的二进制文件, 如果相应的子系统被安装，那么下面的文件或者符号链接必须在/etc下面  

|File                     |Description                     |
|:-------------------------|:-----------------------------------------------|
|csh.login |  Systemwide initialization file for C shell logins|
|**exports**   |  NFS filesystem access control list |
|**fstab**     |  Static information about filesystems开机自动挂载文件 |
|**ftpusers**  |  FTP daemon user access control list |
|**gateways**  |  File which lists gateways for routed |
|gettydefs |  Speed and terminal settings used by getty|
|**group**     |  User group file |
|host.conf |  Resolver configuration file |
|**hosts**     |  Static information about host names |
|hosts.allow |  Host access file for TCP wrappers |
|hosts.deny  |  Host access file for TCP wrappers |
|hosts.equiv |  List of trusted hosts for rlogin, rsh, rcp |
|hosts.lpd   |  List of trusted hosts for lpd |
|inetd.conf  |  Configuration file for inetd |
|**inittab**     |  Configuration file for init |
|**issue**       |  Pre-login message and identification file |
|ld.so.conf  |  List of extra directories to search for shared libraries |
|**motd**        |  Post-login message of the day file 文件|
|**mtab**        |  Dynamic information about filesystems |
|mtools.conf |  Configuration file for mtools |
|**networks**    |  Static information about network names |
|**passwd**      |  The password file |
|printcap    |  The lpd printer capability database |
|**profile** |  Systemwide initialization file for sh shell logins 全局的环境变量配置文件|
|protocols   |  IP protocol listing |
|resolv.conf |  Resolver configuration file |
|rpc         |  RPC protocol listing |
|**securetty**   |  TTY access control for root login |
|services    |  Port names for network services |
|**shells**      |  Pathnames of valid login shells |
|syslog.conf |  Configuration file for syslogd |
|**rc.local** | 用于存放开机自启动程序命令的文件。|
|basrc        | 对所有用户生效的全局变量     |


---

## The /var Hierarchy

  /var　下面存放着可变的数据文件，他们包括spool directories，administrative and logging data, transient and temporary files. /var下面的文件有些是可共享的比如/var/mail, /var/cache/man,/var/cache/fonts, /var/spool/news,有些是不可共享的：/var/log, /var/lock, /var/run.   

　　/var is specified here in order to make it possible to mount /usr read-only. Everything that once went into /usr that is written to during system operation (as opposed to installation and software maintenance) must be in /var.  
  　If /var cannot be made a separate partition, it is often preferable to move /var out of the root partition and into the /usr partition. (This is sometimes done to reduce the size of the root partition or when space runs low in the root partition.) However, /var must not be linked to /usr because this makes separation of /usr and /var　more difficult and is likely to create a naming conflict. Instead, link /var to /usr/var.  
　　Applications must generally not add directories to the top level of /var. Such directories should only be added if　they have some system-wide implication, and in consultation with the FHS mailing list.  

|Directory             |Description                                |
|:---------------------|:------------------------------------------|
|cache                 |Application cache data 应用程序缓存数据。这些数据是在本地生成的一个耗时的I/O或计算结果。应用程序必须能够再生或恢复数据。缓存的文件可以被删除而不导致数据丢失。|
|lib                   |Variable state information 系统正常运行时要改变的文件.|
|local                 |Variable data for /usr/local|
|lock                  |Lock files|
|log                   |Log files and directories 各种程序的Log文件，特别是login   (/var/log/wtmp log所有到系统的登录和注销) 和syslog (/var/log/messages 里存储所有核心和系统程序信息. /var/log 里的文件经常不确定地增长，应该定期清除.|
|log/lastlog           |record of last login of each user|
|log/messages          |system messages from syslogd 日志信息，按周自动轮询|
|log/wtmp              |record of all logins and logouts|
|log/secure            |记录登陆系统存取信息的文件，不管认证成功还是认证失败都会记录|
|opt                   |Variable data for /opt|
|run                   |Data relevant to running processes保存到下次引导前有效的关于系统的信息文件.例如， /var/run/utmp 包含当前登录的用户的信息.|
|spool                 |Application spool data|
|tmp                   |Temporary files preserved between system reboots|

---

### log　文件下的文件详细介绍

|Directory             |Description                                                  |
|:---------------------|:------------------------------------------------------------|
|**messages**          |包括整体系统信息，其中也包含系统启动期间的日志。此外mail, cron, daemon, kern和auth等内容也记录在messages日志中。|
|**dmesg**             |包含内核缓冲信息（kernel ring buffer）。在系统启动时，会在屏幕上显示许多与硬件有关的信息。可以用dmesg查看它们。|
|auth.log              |包含系统授权信息，包括用户登录和使用的权限机制等。|
|boot.log              |包含系统启动时的日志。|
|daemon.log            |包含各种系统后台守护进程日志信息。|
|dpkg.log              |包括安装或dpkg命令清除软件包的日志。|
|**kern.log**          |包含内核产生的日志，有助于在定制内核时解决问题。|
|lastlog               |记录所有用户的最近信息。这不是一个ASCII文件，因此需要用lastlog命令查看内容|
|user.log              |记录所有等级用户信息的日志。|
|alternatives.log      |更新替代信息都记录在这个文件中。|
|btmp                  |记录所有失败登录信息。使用last命令可以查看btmp文件。例如，”last -f /var/log/btmp \| more“。|
|anaconda.log          |在安装Linux时，所有安装信息都储存在这个文件中。
|**secure**            |包含验证和授权方面信息。例如，sshd会将所有信息记录（其中包括失败登录）在这里。
|wtmp ,utmp            |包含登录信息。使用wtmp可以找出谁正在登陆进入系统，谁使用命令显示这个文件或信息等。|
|faillog               |包含用户登录失败信息。此外，错误登录命令也会记录在本文件中。|

---

##  /dev : Devices and special files
The following devices must exist under /dev.

|Directory             |Description                                                  |
|:---------------------|:------------------------------------------------------------|
|null                  |All data written to this device is discarded. A read from this device will return an EOF condition.|
|zero                  |This device is a source of zeroed out data. All data written to this device is discarded. A read from this device will return as many bytes containing the value zero as was requested.|
|tty                   |This device is a synonym for the controlling terminal of a process. Once this device is opened, all reads and writes will behave as if the actual controlling terminal device had been opened.|

### 普及硬盘的基础知识


　　　 在linux下面每个设备都被当成一个文件来对待，因硬盘接口的不同对应于/dev下面的文件名也不同下面表格可以拿来参考对照

|设备　                |设备在linux内的文件名            |
|:---------------------|:-------------------------------|
|IDE硬盘　　　　　　　　|/dev/hd[a-d]                     |
|scsi, stat, usb硬盘   |/dev/sd[a-p]                     |
|U盘　　　　　　        |/dev/sd[a-p]                     |
|软驱                   |/dev/fd[0-1]                    |
|CD ROM                 |/dev/cdrom                     |

　　硬盘由许多盘片，机械手臂，磁头与主轴马达所组成，实际的数据都写在盘片上，　而读写主要通过机械手臂可伸展让读取头在盘片上面进行读写操作. 盘片上，以圆心以放射状的方式分割出最小的存储单位叫扇区(sector)，每个扇区512bytes,扇区所组成的圆叫磁道（track)，在所胡盘片上面的同一个磁道可以组成柱面(cylinder)，柱面也是一般我们分割硬盘的最小单位了。硬盘大小计算方式 head * cylinder * sector * 512 bytes  
　　　　
    每个硬盘的第一扇区都非常重要，主要记录了两个重要的信息：主引导分区(master boot record, MBR)，可以安装引导加载程序的地方，有446bytes。　分区表（partiion table），记录了整块硬盘分区的状态有64bytes,因为只有６４bytes所以只能存放４个分区，这四个分区被称为主(primary)分区或者扩展(extended)分区,　而扩展分区本身不不能被拿来格式化，可以在扩展分区上继续做分区，这些分区被称为逻辑(logical）分区。在linux系统中对应的文件名前四个预留给主分区或者扩展分区，逻辑分区必须从５开始，如图  
![device partition](/images/deviec-partition.png)  
这里p1对应linux文件中的 /dev/hda1。 p2对应 /dev/hda2。 l1　对应 /dev/hda5。 l2　对应 /dev/hda6 等。

　　在/dev里面的设备，系统里的设备默认情况是无法访问的，需要进行挂载才可访问，比如如果需要访问/dev/sda1,就需要用一个入口与之关联,比如关联到／mnt 便需要用mount命令把sda1挂载到/mnt  

	mount -t ext4 /dev/sda1 /mnt

  这里/mnt被挂载的目录就叫挂载点可以用 `df -h` 来查看系统的分区情况,可以是通过配置/etc/fstab实现开机自动挂载  


	df -h
	#Filesystem      Size  Used Avail Use% Mounted on
	#/dev/sda3       6.6G  1.7G  4.6G  28% /
	#tmpfs           491M     0  491M   0% /dev/shm
	#/dev/sda1       190M   40M  141M  22% /boot

如果需要取消挂载，便可以用 `umount` 命令. 这里需要退出到/mnt后才能操作

	umount /mnt

/proc     内核与进程信息的的虚拟文件系统
	 /proc/meminfo
	 /proc/cpuinfo

         /proc/loadavg
         内核：/proc/sys/net/ipv4/ip_forward对应/etc/sysctl.conf里的配置。


/var/spool/crom/root  定时任务的配置文件
/var/spool/clientmpqueue 

/proc/sys/net/ipv4

