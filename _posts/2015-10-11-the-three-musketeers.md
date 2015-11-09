---
layout: post
title:  "linux 三剑客"
category: linux
tags: [command, linux]
excerpt: "本文主要介绍linux系统中三个非常重要的命令grep, sed, awk。指在用最短的内容，抓住 此三个命令的主要用法及功能"
---




# Linux the three musketeers
linux中三剑客分别是grep, sed, awk. 他们都支持正则表达式，在文本处理上非常高效。现在就此三个命令做简单介绍

[toc]

## grep, egrep, fgrep
egrep, fgrep 是grep的扩展，其主要功能便是打印符合样式的行

其控制打印的行数的参数有
- -A NUM, --after-context=NUM	Print NUM lines of trailing context after matching lines.
- -B NUM, --before-context=NUM	Print NUM lines of leading context before matching lines.
- -C NUM, --context=NUM	Print NUM lines of output context.

也就是说A 打印匹配到样式及其后几行，B 打印匹配到样式及其前几行，C 便是匹配到样式的上下文下面仅以A举例，BC，同理

```
$ grep -A2 "sandow" /etc/passwd
sandow:x:500:500::/home/sandow:/bin/bash
oldboy:x:501:501::/home/oldboy:/bin/bash
oldgirl:x:502:502::/home/oldgirl:/bin/bash
```

控制匹配结果的参数有：
- -i ,--ignore-case.  Ignore case distinctions 顾名思义，便是忽略大小写
- -v, --inver-match.  Invert the sense of matching, to select non-matching lines 反向匹配
- -E, --extended-regexp. 扩展正则表达式，相当于egrep
-  -F, --fixed-strings, --fixed-regexp, 把范本样式视为固定字符串的列表，相当于fgrep
- -o, --only-matching 只显示匹配到的那部分

控制输出结果的参数：
- -c, --count， 仅统计匹配到的结果，匹配到两个就会返回2
- -n, --line-number ,  显示匹配到行的行数
-  --color=auto 把匹配到的显示高亮显示

```
$ ifconfig eth0
eth0      Link encap:Ethernet  HWaddr 00:0C:29:1E:00:6F  
          inet addr:10.0.0.17  Bcast:10.0.0.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fe1e:6f/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:2410 errors:0 dropped:0 overruns:0 frame:0
          TX packets:649 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:229147 (223.7 KiB)  TX bytes:135613 (132.4 KiB)

$ ifconfig eth0|grep Bcast|egrep -o "[0-9.]*"
10.0.0.17
10.0.0.255
255.255.255.0
```

grep 也可以指定目录，它会搜索目录下所有文件里的内容并输出匹配符合样式的行

## sed stream editor for filtering and transforming text
sed 批量的编辑文本，sed 擅长处理行。sed 可以删除(delete), 改变（change）, 添加(insert), 合并，交换文件中的数据行，或读入其它文件的数据到文件中，也可以替换(substuite) 它他其中的字符串。是一个非交互式的效率非常高的命令

###sed 参数

- -n, --quiet, --silent 只打印匹配到的行
- -e script, 执行后面的命令
- -f script-file, 执行后面文件中的命令

语法` sed -e 'command1' -e 'command2' filename` 注意这个单引号并不是一成不变的，当在shell里需要引用变量时便可以用双引号

例如，删除sed.txt文件中第1到10行并且把magdre改为sandow 便可以这样
```
$ sed -e '1,10d' -e 's/magdre/sandow/g'  sed.txt
```


### 位址
sed 的编辑指令分为位址(address)和指令 。 位址负责定位需要编辑的内容，指令负责编辑。其语法格式为 `[address1 [,address2]][function[argument]]`

当没有位址时便会对所有文本处理，一个位址时位会处理该位置定位到的行，两个位址时便会处理从位址1到位址2的行数。当然如果使用正则表达式便是匹配字符的行咯。

|位址中的指令|描述||位址中的指令|描述|
|:---:|:----||:---:|:----|
|$ | 最后一行 | | first~step |从first开始步长为setp的行|
|/regexp/|用正则来匹配行||0,addr2|匹配到前addr2行|
|addr1,+n|从addr1开始后n行||addr1,~n|这个不重要|

看起来很复杂吧，先来几个例子
```
$ echo -e "hello\nmy name is sandow\nmy bolg is magdre.github.io"  >test.txt

$ sed -n '$p' test.txt
my bolg is magdre.github.io

$ sed -n '/sandow/p' test.txt 
my name is sandow
```

### 指令
上面看到很多d , p, g, s 等这些都是什么意思呢，他们都需要函数，那么接下来就来说说他们到底代表的什么意思

|指令|描述||指令|描述|
|:--:|:---||:--:|:---|
|=   |打印行数|| r  | 从文件中记取数据|
|a\ |在当前行下追加数据||i\ | 在当前行上追加数据|
|c\ |把选定行改为新文本|| d  |删除所选择的行|
|s  |替换指定字符||   q | 退出sed编辑 |
|n |读取下一个输入行，用下一个命令处理新的行||N|这个百度去吧|
|h| 复制pattern space到内存缓存区||H|追加到缓存区|
|g|获取缓存区内容替换pattern space||G|追加到 pattern space后|
|p|打印当前pattern space||P|打印模版块的第一行|
|: label|建立参照位址||b label |将指令跳到参照位址|
|w|写入文件|||
当需要对一个位址执行多个指令时需要用{}来括起来
```
addr{
commend1
commend2
}
```
来来来，例子给你形象说明
```
$ cat test.txt
hello
my name is sandow
my bolg is magdre.github.io

$ sed '2a I am a gentleman' test.txt
hello
my name is sandow
I am a gentleman
my bolg is magdre.github.io

$ sed -e '2c\I am a gentleman' -e '1i\stay hungry stay foolish' test.txt
stay hungry stay foolish
hello
I am a gentleman
my bolg is magdre.github.io

```
这里有一个"!" 其功能很特别需要在这里说下，如下例
```
[root@centOs ~]# sed '/sandow/!d' test.txt 
my name is sandow
```
这时会删除除匹配到的那行外的所有行


**替换字符举例**
 `s/regexp/replacement/[flag]`  用正则表达式来匹配，匹配到后用replacement来做替换 在replacement里可以用\1到\9来输出前面regexp里的子表达式匹配到的内容。 另在replacement 里可以用 ‘&’ 来代表前面所匹配到的内容。我们用flag来控制如何替换
 - g 代表替换所有匹配到的内容
 - num 当为数字为是从第几个匹配到的内容开始替换
 - p  标准输出
 - w filename 替换后输入名为filename的文件中
 - 空 仅替换第一个匹配到的内容
```
$ ifconfig eth0 | sed -nr '2s/.*:(.*) .*:(.*) .*:(.*)/\1 \2 \3/gp'
10.0.0.17  10.0.0.255  255.255.255.0

$ ifconfig eth0 | sed -nr '/Bcast/s/.*:(.*) .*:(.*) .*:(.*)/\1 \2 \3/gp'
10.0.0.17  10.0.0.255  255.255.255.0

```
**变形**
说实话这个不怎么用，所以只附上其英文解释
`y/source/dest/`   Transliterate  the  characters  in  the  pattern space  which appear in source to the corresponding  character  in dest.

### sed 的小技巧
` sed -n '$=' test.txt`    计算行数
` ifconfig eth0 |sed 2q` 显示前两行内容
` sed '$!d' ` 或 ` sed -n '$p'  显示最后一行内容



## gawk - pattern scanning and processing language
awk这就是一门轻量级语言，也没必要去认真研习，awk能解决的python都可以搞定，所以现在仅简单介绍下便好。

