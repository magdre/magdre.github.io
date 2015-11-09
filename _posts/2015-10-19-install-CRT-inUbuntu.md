---
layout: post
title:  "在ubuntu下面安装PAC Manager"
date:   2015-10-19 12:06:05
category: ubuntu
tags: [ubuntu]
excerpt: "在ubuntu下本来也终端的，但是如果需要远程连接到其它机子，一个还行，但是如果连多个，那就麻烦了，在windows下有crt在这里想说CRT做的太垃圾了，没发发屏，而在ubuntu下有PAC manager,比crt好用的多。"
---

## 安装PAC Manager

近日开始放弃WIN10转而用Ubuntu，原因很简单，因为自己在搭建环境的时候在windows下相当不顺手，而且总出错，每个包都需要自己一个个下一个个安装，累了，真的累了。果断换成Ubuntu,一行命令便可搞定所有问题。 上一次说到在Ubuntu里面安装了VMware但是如果用Terminal输入命令`ssh -p22 sandow@10.0.0.19` 这样连接虽然是完全OK的，但每天都这样输也是个麻烦事，如果用alias做了别名后又不能连别的IP，而且还不能批量管理多个虚拟机，百度搜到有一个Ubuntu下的CRT，但是官网已经不支持下载更何况要怎么破解呢。后来发现了一个替代CRT的程序，那就是PAC Manager 下面便记录下是如何安装并且使用之的。  

首先去[PAC Manager官网](http://sourceforge.net/projects/pacmanager/)下载安装

	sudo dpkg -i pac-*.deb

发现提示错误，提示信息显示依赖关系未被配置，

	(正在读取数据库 ... 系统当前共安装有 199158 个文件和目录。)
	正准备解包 pac-4.5.5.5-all.deb  ...
	正在将 pac (4.5.5.5) 解包到 (4.5.3.2) 上 ...
	dpkg: 依赖关系问题使得 pac 的配置工作不能继续：
	 pac 依赖于 libgtk2-unique-perl；然而：
	  未安装软件包 libgtk2-unique-perl。

	dpkg: 处理软件包 pac (--install)时出错：
	 依赖关系问题 - 仍未被配置
	正在处理用于 man-db (2.7.0.2-5) 的触发器 ...
	正在处理用于 gnome-menus (3.10.1-0ubuntu5) 的触发器 ...
	正在处理用于 desktop-file-utils (0.22-1ubuntu3) 的触发器 ...
	正在处理用于 bamfdaemon (0.5.1+15.04.20150202-0ubuntu1) 的触发器 ...
	Rebuilding /usr/share/applications/bamf-2.index...
	正在处理用于 mime-support (3.58ubuntu1) 的触发器 ...
	在处理时有错误发生：
	 pac

这个怎么整呢，可能是还有很多支持包未安装，

	sudo apt-get -f install

将会看到下面信息：

	将会安装下列额外的软件包：
	  libgtk2-unique-perl libunique-1.0-0
	下列【新】软件包将被安装：
	  libgtk2-unique-perl libunique-1.0-0

这时便可以修复依赖关系。但如果出现错误完法可能需要清理下包了

	sudo apt-get -f clean

然后再执行上面的命令，完美解决问题，等安装完成后便输入`pac`便可以进入PAC Manager界面了。之后若PAC用的顺手再来谈PAC的的心得。

## 问题
1. 字体挤在一起
这里需要把字体调成ubuntu mono, options >> terminal optins 下调。
PAC用着十分友好，可以分屏，可以同时在不同窗口输命令，还是不错的。在左下角的PCC








