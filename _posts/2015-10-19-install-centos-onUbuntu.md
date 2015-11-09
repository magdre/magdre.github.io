---
layout: post
title:  "在ubuntu下面安装centos"
date:   2015-10-19 18:06:05
category: ubuntu
tags: [ubuntu]
excerpt: "在ubuntu下安装vmware,虚拟机里centos的安装，及安装后的网络配置"
---



  之前在windows环境下在VMWARE里安装过一次CentOs,因自己在windows下搭建编程环境过于麻烦，昨日脑袋一发热便把window直接换成了Ubuntu，且在Ubuntu, 并安装VM用来搭建服务器测试环境。  


## 安装VMWARE

   有关在ubuntu下安装vmware的教程可以参见[How To Install VMware Workstation 11 On Ubuntu Linux](http://www.linuxtechi.com/install-vmware-workstation-11-on-ubuntu-linux/)下面主要列出关键步骤  


1. 从官网下载vmware 

		$ wget https://download3.vmware.com/software/wkst/file/VMware-Workstation-Full-11.0.0-2305329.x86_64.bundle

2. 在脚本包上设置执行命令

		$ chmod a+x VMware-Workstation-Full-11.0.0-2305329.x86_64.bundle

3. 执行脚本开始安装

		$ sudo ./VMware-Workstation-Full-11.0.0-2305329.x86_64.bundle

然后便是图形化界面的按照自己的意愿下一步下一步就OK，要注意记住HTTP port是443. 但是安装之后打开程序创建虚拟网卡时出错，一个解决办法是把vmnet.tar替换掉，链接之后附上。然后顺利进入vmware，接下来便可以安装centos了。


## 安装centos

新建虚拟机，载入ISO，开机。进入安装界面后前几项都按照默认既可。在which type of installation would you like里选择create  custom layout. 对磁盘进行分区
首先可以给/boot以及swap分区

|分区|大小|
|:----------|:--------|
| /boot 存放引导程序| 100-200M|
| swap 虚拟内存     | 物理内存<8G，SWAP就实际内存\*1.5,物理内存>8G，SWAP就 8G|

后面按不同的场景来划分利剩余的空间

|生产场景|对剩余空间的分配方案|
|:---------|:-----------|
|集群的节点分区方法 | 把剩余的数据都给根（/）|
|重要数据的业务：存储，数据库|根（/）给50-200G，剩余给/data 存放数据用|
|门户网站，大型企业| /  根 给50-200G，剩余的预留|

自己因为是测试环境，所以分区便把其它都给了根。校正，swap本来要给他1024的，结果只给了200  

![format-divers](/images/format-dives.jpg)  

格式代后便进入选包环节，选择minimal-customize now。 我们以最小化为仅选择四个包见下图。  

![format-divers](/images/format-divers1.jpg)  
![selection-package1](/images/selection-package1.jpg)  
![selection-package2](/images/selection-package2.jpg)  



## 配置网络 

在centos里配置网络,这里需要说明的是，**在centos里配置的网段必须与VM里的网段一样**。可以在edit > virtual network edit里面查看并配置因为我选择的是nat网络，所以这里只需要配置vmnet8既可配置见下图之后进入.  

![vmware-vmnet8-setting](/images/ubuntu-vmnet8-setting.jpg)  

然后在ubuntu,系统设置-网络里对vmnet8做进一步配置见下图。  

![ubuntu-vmnet8-setting](/images/vmware-vmnet8-setting.jpg)

进入centos里输入 

	vi /etc/sysconfig/network-scripts/ifcfg-eth0 

对centos网卡进行配置  

![centos-networking-setting](/images/centos-networking-setting.png)

之后便可以顺利上网了。







