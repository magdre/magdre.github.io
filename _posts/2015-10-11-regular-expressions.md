---
layout: post
title: "Regular Expressions"
category: python
tags: [linux, python]
excerpt: "全面介绍正则表达式的语法.以及在python下的一些用法,并对python中的re中的函数做简单介绍"
---

 正则表达式是为处理大量字符串而定义的方法。
 正则表达式分基本正则表达式（Basic Regular Expressions,BERs）和扩展正则表达式（Extended Regular Expressions,EREs）. EREs基本包含BERs，而且还包含更多功能. EREs现在已经被应用于 Apache, PERL, PHP, python. 以及VI，emac,grep awk和sed...ERES现在基本已成流行，也是本文主要关注的重点   

## 通用的定义

  本文基本都会用下面几个概念：**literal, metacharacter, target string, escape sequence, search expression**. 下面来介绍下他们的定义

1. literal:  
  literal 我们用来匹配或者用来搜索的可以是任意字符. 在表达式中写什么便会匹配到什么. 简单来说literal 便是你确切想要查找的内容

2. metacharacter:  
  metacharacter 便是一个或者多个不同于literal用法的拥有特殊意义的特殊字符. 例如 ^ (circumflex or caret) 便是一个metacharacter

3. target string:   
  target string 便是我们想要查找或者匹配到的内容

4. search expression:  
  也叫regular expression.  这是我们需要查到或者匹配内容的样式

5. escape sequence:   
  如果想把metacharacter当做literal来用便需要用到escape sequenc. 就是需要用 \ (backslash)把metacharacter 转义为普通的literal. 例如如果我们想要匹配 ^ 便要写成：`\^`  

  本文都会用正则表达式来在下面两个字符串中匹配   

>字符串1   Mozilla/4.0 (compatible; MSIE 5.0; Windows NT; DigExt)   
>字符串2   Mozilla/4.75 \[en\](X11;U;Linux2.2.16-22 i586)   

---

 一些简单的例子

|表达式  |           匹配到的内容                     |   注释           |
|:------:|:----------------------------------        |:--------------:  |
|m       | 只有在字符串一中会在compatible里查找到m     | literal         |
|5 \\[   | 只有在字符串二中从4.7*5 [*en 匹配到 **5 [** | escape sequence |
|a/4     | 只会在字符串一中从Mozilla/4中匹配到**a/4**  | literal          |

---

### metacharacters
  在RE中经常会用到很多特殊字符，每个字符在不同的位置拥有不同的意思，下面简单介绍几个常用的metacharacters

一些基本的元字符及各自的意思

|元字符|特殊意义|例子|
|:---:|:---|:----|
|**[ ]**  | 匹配在方括号里面的任意1个字符   | `[12]` 便只会匹配1或者2, [0-9] 便会匹配任意一个数字|
| **-** | 在方括号里面dash的意思是range separator | [0-9]` 相当于[0123456789] |
| **^** | 在放括號里caret是取反,不在中括号里是最开头 | `[^Ff]` 匹配除了f(大写与小写)的任意字符。`^Moz` 以Woz开头字符会从 'Mozilla' 中找到 |
|**$**  | 只匹配结尾是literal的字符 |`fox$` 便会从 'silver fox' 中找到|
|**.**  | 在这个位置的任意字符      |`ton.` 便会从 'tonneau' 中找到**tons** , 但不会从'wanton' 里找到|
|**?**  | 匹配前面的一个字符出现0次或者1次 | `colou?r` 便会找到**color**和**colour**| 
| * | 匹配前面的一个字符出现0次或者多次| `tre\*` (请忽略\ )便会找到 `tr`, `tre`, `tree` 。|
|**+**  | 匹配前面的一个字符出现1次或者多次||
| {n}   | 匹配前面的字符或者出现n次 |`[1-9]{3}-[0-9]{4}` 便用找到像123-4567形式的数字|
|{n,m}  | 匹配前面的字符至少出现n到m次|| 
| {n,m}? ,+?, *?, ??| 让{n,m}, *, +, ?非貪婪匹配(non-greed)|字符串'aaaaa', a{3,4}将会匹配５个a，　而a{3,5}?便只会匹配３个a |
| ()    |子表达式| 可以向后引用|
| 竖线  |可以匹配前面的字符或者后面的字符 | |  


gr(a|e)y`可以找到 'gray' 或 'grey'

 举例  

>字符串1   Mozilla/4.0 (compatible; MSIE 5.0; Windows NT; DigExt)   
>字符串2   Mozilla/4.75 \[en\](X11;U;Linux2.2.16-22 i586)   

|表达式         |           字符串一                |   字符串二                    |
|:------:       |:---------------------------------|:--------------               |
|[^A-M]in       | 会从Windows里找到 **Win**         | 因为排队A－M所以不会匹配到Linux |
|[a-z]\)$       | 会从 DigiExt)里找到**t）**        | 因为结尾是数字所以不会匹配到   |
|.in            | 会从 Windows 里打到 **Win**       | 会从Linux里找到**Lin**        |
|\(.\*l          | 会匹配到**(compatibl**            | "("后面没有l所以不会匹配到     |
|[Xx][0-9a-z]{2}| 无匹配虽然有小x但是后面t并没有重复两次| 会找到X11                   |
|^([L-Z]in)     | 没有以L-Z开头的字符所以无匹配      | Lin不在字符串的开头无匹配       |



`((4\.[0-3])\|(2\.[0-3]))` 可以在字符串1中找到4.0，在字符串2中找到2.2


### POSIX Character Class Definitions

  POSIX 1003.2第2.8.3.2(6) 定义了一些字符集来表示特定的范围, 虽然看起来很奇怪但是呢也有一些优点的. 下面的内容引用LC_CTYPE POSIX定义

* \[:digit:] 相当于\[0-9]
* \[:alnum:] 相当于\[0-9a-zA-Z]
* \[:alpha:] 相当于\[a-zA-Z]
* \[:blank:] 空格或者制表符
* \[:xdigit:] 十六进制符相当于\[0-9a-fA-F]
* \[:punct:] 标点符号 . , " ' ? ! ; : # $ % & ( ) * + - / < > = @ [ ] \ ^ _ { } \| ~
* \[:print:] 任何可打印的字符
* \[:space:] 任何空格(space, tab, NL, FF, VT, CR). 很多时候用 \s.
* \[:graph:] 排除空格 (SPACE, TAB). Many system abbreviate as \W.
* \[:upper:] 相当于\[A-Z]
* \[:lower:] 相当于\[a-z]
* \[:cntrl:] 控制字符 NL CR LF TAB VT FF NUL SOH STX EXT EOT ENQ ACK SO SI DLE DC1 DC2 DC3 DC4 NAK SYN ETB CAN EM SUB ESC IS1 IS2 IS3 IS4 DEL.


### 一些扩展和缩写

  有些程序（ruby, python, js, php）支持扩展和缩写正则表达式, 这些扩展将在下面列出，一般来说这些扩展都是由PERL（perl compatible regular Expressions） 定义。

1. Character Class Abbreviations
* \d 匹配数字，(equivalent of POSIX \[:digit:])
* \D 匹配任意非数字字符(equivalent of POSIX \[^\[:digit:]])
* \s 匹配空格字符(space, tab etc.). (equivalent of POSIX \[:space:] EXCEPT VT is not recognized)
* \S 匹配任何非空格字符(space, tab). (equivalent of POSIX \[^\[:space:]])
* \w 匹配任何数字与字母（大小）字符(equivalent of POSIX \[:alnum:])
* \W 匹配任何非数字与字母（大小）字符 (equivalent of POSIX \[^\[:alnum:]])

2. Positional Abbreviations
* \b 单词边界，`\bxx`匹配以xx开始的词，`xx\b`匹配以xx结束的词.  `\bton\b` 可以找到ton但是找不到tons, `\bton`就会找到 tons.
* \B 非单词边界，匹配任意不以xx开始或者结束的词. `\Bton\B`会找到wantons找不到tons, `ton\B`会找到 wantons 和 tons.


![regular expression](/images/regular-expression.png)

---

## python 中的re包，
　python里的re使用方法, 后面的语法是结合complie使用pos是position的缩写意思是从什么位置开始匹配，什么位置结束。

* **re.compile(pattern, flags=0)**   先把pattern编译然后再用正则表达式的方法调用  

```python
import re

>>> prog = re.compile(pattern)
>>> result = prog.match(string)
```

相当于

```python
>>> result = re.match(pattern, string)
```

* **re.search(pattern, string, flags=0) , regex.search(string[, pos[, endpos]])**
 扫描整个字符串找到第一个regular expression pattern 匹配到的位置然后返回符合pattern的match object,　如果未找到便返回　None .

* **re.match(pattern, string, flags=0)　　regex.match(string[, pos[, endpos]])**   
  如果在这个string的开始匹配到字符，那么就返回相应的match object,　匹配不到便会返回　None  

```python
>>> m=re.search("a\w+","bcdfa\na1b2c3")
>>> n=re.match("a\w+","bcdfa\na1b2c3")
>>> m.group()
'a1b2c3'
>>> print(n)
None
```


* **re.split(pattern, string, maxsplit=0, flags=0)，　regex.split(string, maxsplit=0)**  
  用能够匹配到的字符来分隔字符串，maxsplit默认是最大分隔，指定数字后为最大分隔数

```python
>>> re.split('\W+', 'Words, words, words.')
['Words', 'words', 'words', '']
>>> re.split('(\W+)', 'Words, words, words.')
['Words', ', ', 'words', ', ', 'words', '.', '']
>>> re.split('\W+', 'Words, words, words.', 1)
['Words', 'words, words.']
>>> re.split('[a-f]+', '0a3B9', flags=re.IGNORECASE)
['0', '3', '9']
```

如果有字符串的形状找到一个或者多个分隔符，那么在结果中便会以一个空字符开始，结尾有字符也是一样的

```python
>>> re.split('(\W+)', '...words, words...')
['', '...', 'words', ', ', 'words', '...', '']
```

* **re.findall(pattern, string, flags=0), regex.findall(string[, pos[, endpos]])**  
  用列表的方式返回匹配到的字符，

* **re.finditer(pattern, string, flags=0), regex.finditer(string[, pos[, endpos]])** 
  用迭代的方式返回匹配到的字符，

* **re.sub(pattern, repl, string, count=0, flags=0), regex.sub(repl, string, count=0)**
  把用样式匹配到的字符换成repl。如果没有匹配到字符便返回原string。　repl可以是字符，也可以是函数，当repl是一个字符串时，可以使用\id或\g\<id\>、\g\<name\>引用分组，但不能使用编号0。 

```python
def dashrepl(matchobj):
	if matchobj.group(0) == '-': 
		return ' '
	else: 
		return '-'
>>> re.sub('-{1,2}', dashrepl, 'pro----gram-files')
'pro--gram files'
>>> re.sub(r'\sAND\s', ' & ', 'Baked Beans And Spam', flags=re.IGNORECASE)
'Baked Beans & Spam'
```

* **re.subn(pattern, repl, string, count=0, flags=0)**
  与sub用法一亲但是返回一个数组(new_string, number_of_subs_made).

* **re.escape(string)**
Escape all the characters in pattern except ASCII letters, numbers and '_'. This is useful if you want to match an arbitrary literal string that may have regular expression metacharacters in it.



### Match Objects
  match object提供了很多方法与属性

* **match.expand(template)** 将匹配到的分组代入template中然后返回。template中可以使用\id或\g\<id\>、\g\<name\>引用分组，但不能使用编号0。\id与\g\<id\>是等价的；但\10将被认为是第10个分组，如果你想表达\1之后是字符'0'，只能使用\g\<1\>0。


* **match.group([group1, ...])** 获得一个或多个分组截获的字符串；指定多个参数时将以元组形式返回。如果里面无参数默认为０，返回所有匹配的子符串

```python
>>> m = re.match(r"(\w+) (\w+)", "Isaac Newton, physicist")
>>> m.group(0)       # The entire match
'Isaac Newton'
>>> m.group(1)       # The first parenthesized subgroup.
'Isaac'
>>> m.group(2)       # The second parenthesized subgroup.
'Newton'
>>> m.group(1, 2)    # Multiple arguments give us a tuple.
('Isaac', 'Newton')
```

如果使用(?P\<name\>...)表达式，那么便会吧每个子组用name来命名，

```python
>>> m = re.match(r"(?P<first_name>\w+) (?P<last_name>\w+)", "Malcolm Reynolds")
>>> m.group('first_name')
'Malcolm'
>>> m.group('last_name')
'Reynolds'
```

这里也可以用数字来提取

```python
>>> m.group(1)
'Malcolm'
>>> m.group(2)
'Reynolds'
```

* **match.groups(default=None)** 将所有匹配到的子组用tuple 的方式返回，

```python
>>> m = re.match(r"(\d+)\.(\d+)", "24.1632")
>>> m.groups()
('24', '1632')
```

* **match.groupdict(default=None)**  将所有匹配到的子组用dic　的方式返回，key 是子组的名字.

```python
>>> m = re.match(r"(?P<first_name>\w+) (?P<last_name>\w+)", "Malcolm Reynolds")
>>> m.groupdict()
{'first_name': 'Malcolm', 'last_name': 'Reynolds'}
```

## re in linux
在linux中使用re请参见另一篇文章 linux三剑客

[1]: http://www.zytrax.com/tech/web/regex.htm#intro







