---
title: python网页数据的解析提取
date: 2022-02-12 14:18:30
tags: python网页数据的解析提取
categories: python爬虫的学习
---
## 前言
- 爬虫文章均来自《python3网络爬虫开发实战教程》，只为方便记录日常所学，以及方便观看。

## XPath的使用
### XPath概览
### XPath常用规则
|表达式|描述|
|----|-----|
|`nodename`|选取此节点的所有子节点|
|`/`|从当前节点选取直接子节点|
|`//`|从当前节点选取子孙节点|
|`.`|选取当前节点|
|`..`|选取当前节点的父节点|
|`@`|选取属性|
### 准备工作
`pip3 install lxml`
### 实例引入
```
from lxml import etree

text = """
<div>
    <ul>
        <li class="item-0"><a href="link1.html">first item</a></li>
        <li class="item-1"><a href="link2.html">second item</a></li>
        <li class="item-inactive"><a href="link3.html">third item</a></li>
        <li class="item-1"><a href="link4.html">fourth item</a></li>
        <li class="item-0"><a href="link5.html">fifth item</a>
    </ul>
</div>
"""
html = etree.HTML(text)
result = etree.tostring(html)
print(result.decode('utf-8'))
```

	<html><body><div>
		<ul>
			<li class="item-0"><a href="link1.html">first item</a></li>
			<li class="item-1"><a href="link2.html">second item</a></li>
			<li class="item-inactive"><a href="link3.html">third item</a></li>
			<li class="item-1"><a href="link4.html">fourth item</a></li>
			<li class="item-0"><a href="link5.html">fifth item</a>
		</li></ul>
	</div>
	</body></html>
	
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = etree.tostring(html)
print(result.decode('utf-8'))
```

	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN" "http://www.w3.org/TR/REC-html40/loose.dtd">
	<html><body><div>&#13;
		<ul>&#13;
			<li class="item-0"><a href="link1.html">first item</a></li>&#13;
			<li class="item-1"><a href="link2.html">second item</a></li>&#13;
			<li class="item-inactive"><a href="link3.html">third item</a></li>&#13;
			<li class="item-1"><a href="link4.html">fourth item</a></li>&#13;
			<li class="item-0"><a href="link5.html">fifth item</a>&#13;
		</li></ul>&#13;
	</div></body></html>

### 所有节点
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath('//*')
print(result)
```

	[<Element html at 0x1f47048cd00>, <Element body at 0x1f47048ce40>, <Element div at 0x1f47048ce80>, <Element ul at 0x1f47048cec0>, <Element li at 0x1f47048cf00>, <Element a at 0x1f47048cf80>, <Element li at 0x1f47048cfc0>, <Element a at 0x1f470494040>, <Element li at 0x1f470494080>, <Element a at 0x1f47048cf40>, <Element li at 0x1f4704940c0>, <Element a at 0x1f470494100>, <Element li at 0x1f470494140>, <Element a at 0x1f470494180>]

```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath('//li/a')
print(result)
```

	[<Element a at 0x1cd5369bec0>, <Element a at 0x1cd5369bf00>, <Element a at 0x1cd5369bf40>, <Element a at 0x1cd5369bf80>, <Element a at 0x1cd5369bfc0>]
	
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath('//ul//a')
print(result)
```

```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath('//ul/a')
print(result)
```

	[]
	
### 父节点
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath('//a[@href="link4.html"]/../@class')
print(result)
```

	['item-1']
	
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath('//a[@href="link4.html"]/parent::*/@class')
print(result)
```

### 属性匹配
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath('//li[@class="item-0"]')
print(result)
```

	[<Element li at 0x1c41a04cdc0>, <Element li at 0x1c41a04ce00>]

### 文本获取
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath('//li[@class="item-0"]/text()')
print(result)
```

	['\r\n    ']

```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath('//li[@class="item-0"]/a/text()')
print(result)
```

	['first item', 'fifth item']

```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath('//li[@class="item-0"]//text()')
print(result)
```

	['first item', 'fifth item', '\r\n    ']

### 属性获取
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath('//li/a/@href')
print(result)
```

	['link1.html', 'link2.html', 'link3.html', 'link4.html', 'link5.html']

### 属性多值匹配
```
from lxml import etree

text = """
<li class="li li-first"><a htef="link.html">first item</a></li>
"""
html = etree.HTML(text)
result = html.xpath('//li[@class="li"]/a/text()')
print(result)
```

	[]

```
from lxml import etree

text = """
<li class="li li-first"><a htef="link.html">first item</a></li>
"""
html = etree.HTML(text)
result = html.xpath('//li[contains(@class,"li")]/a/text()')
print(result)
```
	
	['first item']
	
### 多属性匹配
```
from lxml import etree

text = """
<li class="li li-first" name="item"><a htef="link.html">first item</a></li>
<li class="li li-first"><a htef="link.html">second item</a></li>
"""
html = etree.HTML(text)
result = html.xpath('//li[contains(@class,"li") and @name="item"]/a/text()')
print(result)
```

	['first item']
	
|运算符|描述|实例|返回值|
|----|----|-----|-----|
|or|或|age=19 or age=20|如果age是19或者20则返回true。如果age是其他则返回false|
|and|与|age>19 and age<21|如果age是20，则返回true。如果age是其他则返回false|
|mod|计算除法的余数|5 mod 2|1|
|`|`|计算两个节点集|`//book|//cd`|返回所有拥有book和cd元素的节点集|
|`+`|加法|6 + 4|10|
|`-`|减法|6 - 4|2|
|`*`|乘法|6 * 4|24|
|div|除法|8 div 4|2|
|`=`|等于|age = 19|如果age是19，则返回true。如果age是20，则返回false|
|`!=`|不等于|age != 19|如果age不是19，则返回true。如果age是19则返回false|
|`<`|小于|age < 19|如果age小于19，则返回true。如果age大于等于19则返回false|
|`<=`|小于或等于|age <= 19|如果age小于等于19，则返回true。如果age大于19则返回false|
|`>`|大于|age > 19|如果age大于19，则返回true，如果age小于等于19则返回false|
|`>=`|大于或等于|age >= 19|如果age大于等于19.则返回true，如果age小于19则返回false|

### 按序选择
```
from lxml import etree

text = """
<div>
    <ul>
        <li class="item-0"><a href="link1.html">first item</a></li>
        <li class="item-1"><a href="link2.html">second item</a></li>
        <li class="item-inactive"><a href="link3.html">third item</a></li>
        <li class="item-1"><a href="link4.html">fourth item</a></li>
        <li class="item-0"><a href="link5.html">fifth item</a>
    </ul>
</div>
"""
html = etree.HTML(text)
result = html.xpath('//li[1]/a/text()')
print(result)
result = html.xpath('//li[last()]/a/text()')
print(result)
result = html.xpath('//li[position()<3]/a/text()')
print(result)
result = html.xpath('//li[last()-2]/a/text()')
print(result)
```

	['first item']
	['fifth item']
	['first item', 'second item']
	['third item']

### 节点轴选择
```
from lxml import etree

text = """
<div>
    <ul>
        <li class="item-0"><a href="link1.html"><span>first item</span></a></li>
        <li class="item-1"><a href="link2.html">second item</a></li>
        <li class="item-inactive"><a href="link3.html">third item</a></li>
        <li class="item-1"><a href="link4.html">fourth item</a></li>
        <li class="item-0"><a href="link5.html">fifth item</a>
    </ul>
</div>
"""
html = etree.HTML(text)
result = html.xpath('//li[1]/ancestor::*')
print(result)
result = html.xpath('//li[1]/ancestor::div')
print(result)
result = html.xpath('//li[1]/attribute::*')
print(result)
result = html.xpath('//li[1]/child::a[@href="link1.html"]')
print(result)
result = html.xpath('//li[1]/descendant::span')
print(result)
result = html.xpath('//li[1]/following::*[2]')
print(result)
result = html.xpath('//li[1]/following-sibling::*')
print(result)
```

	[<Element html at 0x2017ad0de40>, <Element body at 0x2017b08dd40>, <Element div at 0x2017b08dd80>, <Element ul at 0x2017b08ddc0>]
	[<Element div at 0x2017b08dd80>]
	['item-0']
	[<Element a at 0x2017b08ddc0>]
	[<Element span at 0x2017b08dd80>]
	[<Element a at 0x2017b08dd40>]
	[<Element li at 0x2017b08ddc0>, <Element li at 0x2017b08dc80>, <Element li at 0x2017b08de40>, <Element li at 0x2017b08de80>]

### 总结
## Beautiful Soup的使用
### Beautiful Soup的简介
### 解析器
|解析器|使用方法|优势|劣势|
|---|----|----|---|----|

