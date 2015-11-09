---
layout: post
title:  "python基础"
date:   2015-10-19 18:06:05
tags: [checkio]
category: python
excerpt: "本文主要指在利用checkio网站的问题来学习python的基础概念 list, dict, 等。。"
---



1. 在一个列表中去除出现次数为1的数字。给定例子有[1,2,3,1,3], [3,4,5,7,4,7].

方法一

```python
def checkio(data):
	data1 = []
	for element in data:
		if data.count(element) > 1:
			data1.append(element)
	return data1
```

方法二

```python
def checkio(data):
	return [x for x in data if data.count(x) > 1]
```

方法三

```python
	checkio = lambda data: [x for x in data if data.count(x)>1]
```

2. 找出列表中位数, （如果长度为奇数那么就是中间那个数字，如果是偶数便是中间两个的平均数）


