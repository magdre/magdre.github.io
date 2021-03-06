---
title:  "linux目录管理命令"
layout: post
category: linux
tags: [command, linux]
excerpt: "主要介绍linux目录管理的基本命令。ls mkdir rmdir cd mv rm pwd tree cp find "
---

#  ls, 列出目录下的内容

**描述**：list directory contents, 列出目录里的内容的信息，默认是列出当前目录的信息，按字母排序，    


**参数**：    

* -a, --all do not ignore entries starting with(.)  显示隐藏文件
* -l use a long listing format 使用长格式显示
* -A, --almost-all do not list implied . and ..  不要列出（.与..)
* -F, --classify append indicator (one of */=>@\|) to entries 用特殊字符来把文件分类。
* -r, --reverse	reverse order while sorting 反向排序
* -R, --recursive list subdirectories recursively 递归显示子内容
* -t  sort by modification time   按照修改时候排序
* -p append / indicator to directories   在目录后面加（/）

**语法**：

	ls [OPTION]... [FILE]...

**举例**

```
ls -ltr s*
	#列出目前工作目录下所胡名名称是s开头的文件，越新的排越后面   
ls -lR /bin
	#将/bin目录以下所有目录及文件详细资料列出   
ls -AF
	#列出目前工作目录下所有文件及目录，且不同符号标记
```

目录名称后加”/”,可执行档后加“*” 以c开始的为字符设备文件, 以b开头的为卖设备文件(磁盘). 以s开头的为套接口文件


#  mkdir, 创建一个目录

**描述**：Make directories如果该目录不存在，创建一个目录，    

**参数**： 

* -p, --parents    no error if existing, make parent directories as needed  
* -m, --mode=MODE  set  file  mode  (as in chmod), not a=rwx - umask  

**语法**:  

	mkdir [OPTION]... DIRECTORY...

**举例**
	mkdir -m 664 /data
		#在根目录下创建一个data文件。 并且给文件添加权限
	mkdir -p /sandow/word 
		#递归创建多个目录，先创建sandow文件然后再创建word文件
	mkdir a b c 
		#在当前目录下创建多个文件a b c 
	mkdir -p 1/2/3 1/3/5


# rmdir 删除一个空目录

**描述**：remove empty directories 删除空目录，   
**语法**:  

	rmdir [OPTION]... DIRECTORY...   

**参数**：-p, --parents   


#  cd,更改当前工作目录

**描述**：Change the current directory to DIR.更改当前工作目录，默认目录为家目录   
**语法**:  

	cd [-L|-P] [dir]   

**参数**：

* -L force symbolic links to be followed: resolve symbolic links in DIR after processing instances of `..'
* -P use the physical directory structure without following symbolic links: resolve symbolic links in DIR before processing instances

**举例**

	cd /
		#切换到根目录
	cd ~
		#切换到home 目录
	cd ../..
		#跳到目前目录的上上两层


# mv,移动或者更名

**命令描述**：Move (rename) files移动目录，或者更改文件名字   
**命令语法:**  

	mv [OPTION]... [-T] SOURCE DEST
	mv [OPTION]... SOURCE... DIRECTORY
	mv [OPTION]... -t DIRECTORY SOURCE...

**命令参数：**   

* -f, --force do not prompt before overwriting   
* -i, --interactive rompt before overwrite   

**举例**

	mv aaa bbb
		#将文件aaa更名为bbb
	mv /data /tmp/
		#把文件夹data移动到tmp下
	mv /data/ /tmp/
		#把文件夹data里的内容移动到tmp下
	mv /usr/strudent/ .
		#把student下的所有文件移动到当前目录下


#  rm,删除文件或者目录

**命令描述**：Remove the files or directories删除文件或者目录，取消链接   
**命令语法**:  
	
	rm [OPTION]... FILE...   

**命令参数**：   

* -r, -R, --recursive  remove directories and their contents recursively   
* -f, --force ignore nonexistent files, never prompt   
* -i	prompt before every removal   

**举例**

	rm –f test.txt
		#强制删除test.txt不做提示   
	rm -r homework
		#删除文件夹homework

#  pwd 打印当前工作目录

**命令描述**：Print name of current/working directory 打印当前工作目录的全路径   
**命令语法**:  

	pwd [OPTION]...

**命令参数**：   

* -L, --logical use PWD from environment, even if it contains symlinks
* -P, --physical avoid all symlinks


# tree 树形图展示文件内容

**命令描述**：List contents of directories in a tree-like format 用树状图列出目录下包含的所有文件   
**命令语法**:  

	tree [OPTION...] [directory ...]

**命令参数**：   

* -L level Max display depth of the directory tree.
* -d List directories only.
* -i

**举例**

	tree .
		.
		|-- 1
		|   |-- 2
		|   |   `-- 3
		|   `-- 3
		|       `-- 5
		`-- VMwareTools-9.9.3-2759765.tar.gz
		5 directories, 1 file

# cp 复制文件或目录


**命令描述**：Copy files and directories复制一个或者多个文件或目录到目标目录   
**命令语法**:  

	cp [OPTION]... [-T] SOURCE DEST
	cp [OPTION]... SOURCE... DIRECTORY
	cp [OPTION]... -t DIRECTORY SOURCE...

**命令参数**：   

* -a, --archive	same as -dR --preserve=all  在复制目录时使用。保留文件属性。
* -d same as --no-dereference --preserve=links   保留链接
* -f, --force  if  an existing destination file cannot be opened, remove it and try again (redundant if the -n  option is used)   
* -i, --interactive	prompt before overwrite (overrides a previous -n option)  
* -p  same as --preserve=mode,ownership,timestamps   
* -R, -r, --recursive   copy directories recursively  
* -l, --link 	link files instead of copying  
* -u, --update copy  only when the SOURCE file is newer than the destination   file or when the destination file  is missing  

如果目标文件下面有重名的文件要想复制而不显示提示可以用下面两种方法实现

	cp -r test /newtest/file1
		将file复制到目录/newtest下并改名为file1
	touch test.txt tmp/test.txt
	\cp test.txt tmp/test.txt
	/bin/cp test.txt tmp/test.txt


# find 

**命令描述**：search for files按目录树结构的方式查找文件   
   
**命令语法**:  

	find [-H] [-L] [-P] [-D debugopts] [-Olevel] [path...] [expression]

**命令参数**：   

-ntime
-daystart
	Measure  times  (for  -amin,  -atime,  -cmin, -ctime, -mmin, and -mtime) from the beginning of today rather than  from  24  hours ago.   This  option only affects tests which appear later on the command line.

+7  7天前
-7  近7天
7   第7天

* -type  指定文件类型（有下面几种）

|符号|代表文件类型|
|:-:|:---------------------------------|
| b |     block (buffered) special     |
| c |     character (unbuffered) special|
| d |     directory                   |
| p |     named pipe (FIFO)          |
| f |     regular file             |
| l |     symbolic  link;            |
| s |     socket                   |

* ACTIONS

  | -delete       | 删除文件，如果无法删除会显示错误|
  | -exec command {} \; 反查找到的文件用command来执行。|

exec 它的终止是以（;）为标志的，考虑到系统中分号会有不同的意义，所以要在前面加（\）
{} 是find找到的内容。

**举例**

	find . -type f -exec ls -l {} ;
		-rw-r--r--. 1 501 games 9533 Aug 23 06:47 ./SOURCES.txt
		-rw-r--r--. 1 501 games 1 Aug 23 06:47 ./not-zip-safe
		-rw-r--r--. 1 501 games 68 Aug 23 06:47 ./entry_points.txt
		-rw-r--r--. 1 501 games 1961 Aug 23 06:47 ./PKG-INFO	
	find /etc -type f -name "passwd" -exec grep "root" {} \;
		root:x:0:0:root:/root:/bin/bash
		operator:x:11:0:operator:/root:/sbin/nologin
		#找到名这passwd的文件然后执行grep 查看这些文件中是否存在一个root用户
	find /data –type f|grep –v 8|xargs rm -f

	find ./ -type f -o -name "*.txt"
		#-o或者，相当于or,-a并且，相当于and



# tar 打包或解包 
**描述** tar 命令用来打包，解包。但是tar仅是打包而并非压缩，所以打包后可以用gzip来压缩
**语法***

```
tar [OPTION...] [FILE]...
```

**参数**
- -A, 向已存在的包内增加文件
- -c --create, 打包， 新建立一个备份包
- -x --extract,解包， 从已有的备份包内还原文件
- -z --compress, 打包时进行压缩
- -v --verbose, 显示过程
- -r 添加文件到已经压缩的文件
- -p 保持原来属性
- -t 查看压缩包内容
- --exclude --exclude-from,排除符合样式的文件
- -f 指定备份文件
- -j 支持bzip2解压文件
- -j (bzip) -z(gzip) 压缩    -f 指定文件

**举例**

```
tar zcvf /tmp/etc.tar.gz ./etc  
```
把当前路径下面的etc打包到/tmp/etc.tar.gz

tar tf etc.tar.gz   查看包的内容


把etc.tar.gz解压到当前目录

```
tar zxvf /tmp/etc.tar.gz
```

排除oldboy下面的2并打包.

```
tar zcvf oldboy.tar.gz ./oldboy --exclude=oldboy/2
```


tar zcvf oldboy_$(date +%F).tar.gz ./oldboy

最简单的记忆方法

```
压缩： tar -zcvf filename.tar.gz src
查询： tar -ztvf filename.tar.gz
解压缩： tar -zxvf filename.tar.gz dic
```

# date 显示或修改时间 
**描述** date命令主要用于显示指定样式的指定日期
**参数**
- -d ，显示指定字符串的日期与时间，字符串必须用双引号

```
$ date -d "20151010" +%F
2015-10-10
```
-d 指定日期的样式可以有多种，计算方式便是在对应的位上执行加减算法
"+1 day" 
"-1 day"
"+1 month"
"+1 year"

日期格式列表

%H 24小时制时间(0-23)
%I 12小时制时间(01-12)
%M 分钟(00-59)
%p 显示AM或PM
%S 显示秒(00-59)
%T 显示时间(hh:mm:ss,10:58:32)
%X 10时58分44秒
%a 星期简称
%A 星期全称
%d 天数(01-31)
%x,%D 日期 (2015年11月09日,11/09/15)
%F 日期 2015-11-09






















date -%F -d "+9day" 比当前天多9天
date -%F -d "-9day" 比当前天少9天

