---
layout: post
title:  "linux问题小总结"
date:   2015-10-14 18:06:05
category: linux
tags: [command]
excerpt: 本文会重点涉及：chkconfig cat ls tail ln find wc tar cut grep egrep 

---

1. 已知oldboy.txt里面内容为：**inet addr:10.0.0.8 Bcast:10.0.0.255 Mask:255.255.255.0** .现在只想打印出下面内容：　10.0.0.8 10.0.0.255 255.255.255.0。

		$ sed -nr 's#^.*dr:(.*) Bc.*:(.*) M.*k:(.*)#\1 \2 \3#gp' oldboy.txt
		$ grep -Eo '[0-9.]+' oldboy.txt|xargs -n3
		$ awk -F "[: ]" '{print $3, $5, $7}' oldboy.txt
		$ ifconfig eth0|sed -rn '2s#^.*:([0-9.]+).*:([0-9.]+).*:([0-9.]+)$#\1 \2 \3#gp'


2. 上使用awk取passwd文件的第10行到20行的第三列重定向到/tmp/oldboy/test.txt文件里。

		$ awk -F ':' ‘{if(NR>10 && NR <21) print $3}’ /etc/passwd >/tmp/oldboy/test.txt

3. 禁止使用rm，并使该效果永久生效。

		$ echo alias rm='echo "Do note use rm command"' >>/etc/profile
		$ . /etc/profile

4. 请用awk,grep,sed,head,tail分别取出passwd文件中的第2-5行

		$ awk ‘{if(NR<6 && NR>1 ) pirnt $0}’passwd
		$ head -5 passwd | tail -4
		$ sed –n ‘2,5p’passwd
		$ grep ‘bin:x’-A4 passwd

5. 使用命令调换passwd文件里root位置和/bin/bash位置？即调换所有的第一列和最后一列位置调换？
例：默认：root:x:0:0:root:/root:/bin/bash. 修改后：/bin/bash:x:0:0:root:/root:root  

		$ sed -r 's#([^:]+)(:.*:)(/.*$)#\3 \2 \1 #g' /etc/passwd

6. 把/data目录及其子目录下所有以扩展名.txt结尾的文件中包含oldgirl的字符串全部替换为oldboy。

		$ Find /data –type f –name “*.txt” |xargs sed ‘s#oldboy#oldgirl#g’
		$ sed -i ‘s#oldboy#oldgirl#g’ `find /data –name *.txt`



2. 如何过滤出已知当前目录下oldboy中的所有一级目录(提示:不包含oldboy目录下面目录的子目录及隐藏目录，即只能是一级目录)？  
* 方法一：

			$ ls -l |grep ^d
			drwxr-xr-x  2 sandow sandow 4096 Oct 13 06:13 Desktop
			drwxr-xr-x  2 sandow sandow 4096 Oct 11 07:41 Documents
			drwxr-xr-x  2 sandow sandow 4096 Oct  9 19:30 Downloads
			
			$ ls -F|grep /$
			Desktop/
			Documents/
			Downloads/			
* 方法二：

			$ find . -maxdepth 1 -type d ! -name "."
* 方法三:
	
			$ tree -Ldi 1
			$ ls -l|sed -n '/^d/p'
			$ ls -l|awk '/^d/'
	

3. 一个目录中有很多文件（ls -l 查看时好多屏），想用一条命令最快速度查看到最近更新的文件。如何看？  

		$ ls -lrt

4. 在配置apache时 执行了./configure --prefix=/application/apache2.2.17 来编译apche，在make 
install 完成后，希望用户访问 apache 路径更简单，需要给/application/apache2.2.17 目录做一个软链接/application/apache，使得内部开发或管理人员通过/application/apache 就可以访问到 apache 的安装目录/application/apache2.2.17 下的内容，请你给出实现的命令。（提示：apache 为一个 web 服务） 

		$ ln –s /application/apache2.2.17 /application/apache
 
5. 已知 apache 服务的访问日志按天记录在服务器本地目录/app/logs 下，由于磁盘空间紧张，现在要求只能保留最近 7 天的访问日志！请问如何解决？ 请给出解决办法或配置或处理命令。（提示：可以从 apache 服务配置上着手，也可以从生成出来的日志上着手。）
		
		for n in `seq 15`
		do
			date -s "2015/10/$n"
			touch access_www_`(date +%F)`.log
		done
		

		$ find /app/logs -type f -name "access*.log" -mtime +7 exec rm -f {} \;
		$ find /app/logs -type f -name "access*.log" -mtime +7 |xargs rm -f

6. 调试系统服务时，希望能实时查看/var/log/messages 系统日志的更新,如何做？  

		$ for n in `seq 19999`;do echo $n >>/var/log/messages;usleep 5000;done
		$ tail -f /var/log/messages

7. 打印轻量级 web 服务的配置文件 nginx.conf 内容的行号及内容，该如何做？
	
		$ cat -n nginx.conf
		$ less -N nginx.conf
		$ awk '{print NR.$0}' nginx.conf
		$ sed = nginx.conf| sed 'N;s/\n/ /'
		$ grep -n ".*" nginx.conf

8. 装完 Centos 系统后，希望网络文件共享服务 NFS，仅在 3 级别上开机自启动，该如何做？

		$ chkconfig NFS –-level 3 on

12. /etc/目录为 linux 系统的默认的配置文件及服务启动命令的目录   
  a.请用 tar 打包/etc 整个目录（打包及压缩）   
  b.请用 tar 打包/etc 整个目录（打包及压缩，但需要排除/etc/services 文件）   
  c.请把 a 点命令的压缩包，解压到/tmp 指定目录下（最好只用 tar 命令实现）  
 
		$ tar -zcvf a.tar /etc
		$ find /etc/ ! -name /etc/services exec tar -cvf a.tar {} \;
		$ cd /tmp && tar –xvf a.tar 


14. 如何查看/etc/services 文件内容有多少行？

		wc -l /etc/services

15. 过滤出/etc/services 文件包含 3306 或 1521 两数据库端口的行的内容。
 
		$ grep -E "3306|1521" /etc/services

16. echo 'i am oldboy myqq is 31333741' >>oldboy.txt取出oldboy, 31333741

```
awk "{print $3, $6}" oldboy.txt
cut -d " " -f3,6 oldboy.txt
cut -c 6-11, 20- oldboy.txt
```






