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
|------|-------|-------|-------|
|Python标准库|BeautifulSoup(markup,'html.parser')|Python的内置标准库、执行熟读适中、文档容错能力强、速度快、文档容错能力强|Pyton2.7.3或3.2.2前的版本中文容错能力差，需要安装c语言库|
|LXML HTML解析器|BeautifulSoup(markup,'lxml')|速度快、文档容错能力强|需要安装c语言库|
|LXML XML解析器|BeautifulSoup(markup,'xml')|速度快、唯一支持XML的解析器|需要安装c语言库|
|html5lib|BeautifulSoup(markup,'html5lib')|提供最好的容错性、以浏览器的方式解析文档、生成HTML5格式的文档|速度慢、不依赖外部扩展|

### 准备工作
### 基本使用
```
from bs4 import BeautifulSoup

html = """
<html>
	<head>
		<meta charset="utf-8">
		<title>The Dormouse's story</title>
	</head>
	<body>
		<p class="title" name="dromouse"><b>The Dormouse's story</b></p>
		<p class="story">Once upon a time there were three little sisters;and htir names were
		<a href="http://example.com/elsie" class="sister" id="link1"><!--ELSIE--></a>
		<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
		<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
		and htey lived at the bottom of a well </P>
		<p class="story">.....</p>
"""
soup = BeautifulSoup(html,'lxml')
print(soup.prettify())
print(soup.title.string)
```

	<html>
	 <head>
	  <meta charset="utf-8"/>
	  <title>
	   The Dormouse's story
	  </title>
	 </head>
	 <body>
	  <p class="title" name="dromouse">
	   <b>
		The Dormouse's story
	   </b>
	  </p>
	  <p class="story">
	   Once upon a time there were three little sisters;and htir names were
	   <a class="sister" href="http://example.com/elsie" id="link1">
		<!--ELSIE-->
	   </a>
	   <a class="sister" href="http://example.com/lacie" id="link2">
		Lacie
	   </a>
	   and
	   <a class="sister" href="http://example.com/tillie" id="link3">
		Tillie
	   </a>
	   ;
			and htey lived at the bottom of a well
	  </p>
	  <p class="story">
	   .....
	  </p>
	 </body>
	</html>
	The Dormouse's story
	
### 节点选择器
```
html = """
<html>
	<head>
		<meta charset="utf-8">
		<title>The Dormouse's story</title>
	</head>
	<body>
		<p class="title" name="dromouse"><b>The Dormouse's story</b></p>
		<p class="story">Once upon a time there were three little sisters;and htir names were
		<a href="http://example.com/elsie" class="sister" id="link1"><!--ELSIE--></a>
		<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
		<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
		and htey lived at the bottom of a well </P>
		<p class="story">.....</p>
"""
from bs4 import BeautifulSoup
soup = BeautifulSoup(html,'lxml')
print(soup.title)
print(type(soup.title))
print(soup.title.string)
print(soup.head)
print(soup.p)
```

	<title>The Dormouse's story</title>
	<class 'bs4.element.Tag'>
	The Dormouse's story
	<head>
	<meta charset="utf-8"/>
	<title>The Dormouse's story</title>
	</head>
	<p class="title" name="dromouse"><b>The Dormouse's story</b></p>
	
### 提取信息
#### 获取名称
```
print(soup.title.name)
```

	title

#### 获取属性
```
print(soup.p.attrs)
print(soup.p.attrs['name'])
```

	{'class': ['title'], 'name': 'dromouse'}
	dromouse
	
```
print(soup.p['name'])
print(soup.p['class'])
```

	dromouse
	['title']

#### 获取内容
```
print(soup.p.string)
```

	The Dormouse's story
	
#### 嵌套选择
```
print(soup.head.title)
print(type(soup.head.title))
print(soup.head.title.string)
```

	<title>The Dormouse's story</title>
	<class 'bs4.element.Tag'>
	The Dormouse's story
	
### 关联选择
#### 子节点和子孙节点
```
html = """
<html>
	<head>
		<meta charset="utf-8">
		<title>The Dormouse's story</title>
	</head>
	<body>
		<p class="story">
		Once upon a time there were three little sisters;and htir names were
		<a href="http://example.com/elsie" class="sister" id="link1"><span>ELSIE</span></a>
		<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
		<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
		and htey lived at the bottom of a well.
		</P>
		<p class="story">.....</p>
"""
from bs4 import BeautifulSoup
soup = BeautifulSoup(html,'lxml')
print(soup.p.contents)
```

	['\n\t\tOnce upon a time there were three little sisters;and htir names were\n\t\t', <a class="sister" href="http://example.com/elsie" id="link1"><span>ELSIE</span></a>, '\n', <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>, ' and\n\t\t', <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>, ';\n\t\tand htey lived at the bottom of a well.\n\t\t']
	
```
from bs4 import BeautifulSoup
soup = BeautifulSoup(html,'lxml')
print(soup.p.children)
for i,child in enumerate(soup.p.children):
    print(i,child)
```

	<list_iterator object at 0x0000023BE8130190>
	0 
			Once upon a time there were three little sisters;and htir names were
			
	1 <a class="sister" href="http://example.com/elsie" id="link1"><span>ELSIE</span></a>
	2 

	3 <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>
	4  and
			
	5 <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>
	6 ;
			and htey lived at the bottom of a well.

```
from bs4 import BeautifulSoup
soup = BeautifulSoup(html,'lxml')
print(soup.p.descendants)
for i,child in enumerate(soup.p.descendants):
    print(i,child)
```

	0 
			Once upon a time there were three little sisters;and htir names were
			
	1 <a class="sister" href="http://example.com/elsie" id="link1"><span>ELSIE</span></a>
	2 <span>ELSIE</span>
	3 ELSIE
	4 

	5 <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>
	6 Lacie
	7  and
			
	8 <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>
	9 Tillie
	10 ;
			and htey lived at the bottom of a well.
			
#### 父节点和祖先节点
```
print(soup.a.parent)
```

	<p class="story">
			Once upon a time there were three little sisters;and htir names were
			<a class="sister" href="http://example.com/elsie" id="link1"><span>ELSIE</span></a>
	<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a> and
			<a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>;
			and htey lived at the bottom of a well.
			</p>
			
```
print(soup.a.parents)
print(list(enumerate(soup.a.parents)))
```

	<generator object PageElement.parents at 0x0000012F49E69900>
	[(0, <p class="story">
			Once upon a time there were three little sisters;and htir names were
			<a class="sister" href="http://example.com/elsie" id="link1"><span>ELSIE</span></a>
	<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a> and
			<a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>;
			and htey lived at the bottom of a well.
			</p>), (1, <body>
	<p class="story">
			Once upon a time there were three little sisters;and htir names were
			<a class="sister" href="http://example.com/elsie" id="link1"><span>ELSIE</span></a>
	<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a> and
			<a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>;
			.......
			
#### 兄弟节点
```
print('Next Sibling',soup.a.next_sibling)
print('Prev Sibiling',soup.a.previous_sibling)
print('Next Siblings',list(enumerate(soup.a.next_siblings)))
print('Prev Siblings',list(enumerate(soup.a.previous_siblings)))
```

	Next Sibling 
			Hello
			
	Prev Sibiling 
			Once upon a time there were three little sisters;and htir names were
			
	Next Siblings [(0, '\n\t\tHello\n\t\t'), (1, <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>), (2, ' and\n\t\t'), (3, <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>), (4, ';\n\t\tand htey lived at the bottom of a well.\n\t\t')]
	Prev Siblings [(0, '\n\t\tOnce upon a time there were three little sisters;and htir names were\n\t\t')]

#### 提取信息
```
html = """
<html>
	<head>
		<meta charset="utf-8">
		<title>The Dormouse's story</title>
	</head>
	<body>
		<p class="story">
		Once upon a time there were three little sisters;and htir names were
		<a href="http://example.com/elsie" class="sister" id="link1"><span>ELSIE</span></a><a href="http://example.com/lacie" class="sister" id="link2">Lacie</a>
		</P>
"""
from bs4 import BeautifulSoup
soup = BeautifulSoup(html,'lxml')
print('Next Sibling:')
print(type(soup.a.next_sibling))
print(soup.a.next_sibling)
print(soup.a.next_sibling.string)
print('Parent:')
print(type(soup.a.parents))
print(list(soup.a.parents)[0])
print(list(soup.a.parents)[0].attrs['class'])
```

	Next Sibling:
	<class 'bs4.element.Tag'>
	<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>
	Lacie
	Parent:
	<class 'generator'>
	<p class="story">
			Once upon a time there were three little sisters;and htir names were
			<a class="sister" href="http://example.com/elsie" id="link1"><span>ELSIE</span></a><a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>
	</p>
	['story']
	
### 方法选择器


#### find_all
`find_all(name,attrs,recursive,text,**kwargs)`
#### name
```
html="""
<div class="panel">
			<div class="panel-heading">
				<h4>Hello</h4>
			</div>
			<div class="panel-body">
				<ul class="list" id="list-1" name="elements">
					<li class="element">Foo</li>
					<li class="element">Bar</li>
					<li class="element">Jar</li>
				</ul>
				<ul class="list list-small" id="list-2">
					<li class="element">Foo</li>
					<li class="element">Bar</li>
				</ul>
			</div>
		</div>
"""
from bs4 import BeautifulSoup
soup = BeautifulSoup(html,'lxml')
print(soup.find_all(name='ul'))
print(type(soup.find_all(name='ul')[0]))
```

	[<ul class="list" id="list-1">
	<li class="element">Foo</li>
	<li class="element">Bar</li>
	<li class="element">Jar</li>
	</ul>, <ul class="list list-small" id="list-2">
	<li class="element">Foo</li>
	<li class="element">Bar</li>
	</ul>]
	<class 'bs4.element.Tag'>

```
for ul in soup.find_all(name='ul'):
    print(ul.find_all(name="li"))
    for li in ul.find_all(name="li"):
        print(li.string)
```

	[<li class="element">Foo</li>, <li class="element">Bar</li>, <li class="element">Jar</li>]
	Foo
	Bar
	Jar
	[<li class="element">Foo</li>, <li class="element">Bar</li>]
	Foo
	Bar
	
#### attrs
```
html="""
<div class="panel">
			<div class="panel-heading">
				<h4>Hello</h4>
			</div>
			<div class="panel-body">
				<ul class="list" id="list-1" name="elements">
					<li class="element">Foo</li>
					<li class="element">Bar</li>
					<li class="element">Jar</li>
				</ul>
				<ul class="list list-small" id="list-2">
					<li class="element">Foo</li>
					<li class="element">Bar</li>
				</ul>
			</div>
		</div>
"""
from bs4 import BeautifulSoup
soup = BeautifulSoup(html,'lxml')
print(soup.find_all(attrs={'id':'list-1'}))
print(soup.find_all(attrs={'name':'elements'}))
```

	[<ul class="list" id="list-1" name="elements">
	<li class="element">Foo</li>
	<li class="element">Bar</li>
	<li class="element">Jar</li>
	</ul>]
	[<ul class="list" id="list-1" name="elements">
	<li class="element">Foo</li>
	<li class="element">Bar</li>
	<li class="element">Jar</li>
	</ul>]
	
```
print(soup.find_all(id="list-1"))
print(soup.find_all(class_="element"))
```

	[<ul class="list" id="list-1" name="elements">
	<li class="element">Foo</li>
	<li class="element">Bar</li>
	<li class="element">Jar</li>
	</ul>]
	[<li class="element">Foo</li>, <li class="element">Bar</li>, <li class="element">Jar</li>, <li class="element">Foo</li>, <li class="element">Bar</li>]

#### text
```
import re
html = """
<div class="panel">
    <div class="panel-body">
        <a>Hello, this is a link</a>
        <a>Hello, this is a link,too</a>
    </div>
</div>
"""
from bs4 import BeautifulSoup
soup = BeautifulSoup(html,'lxml')
print(soup.find_all(text=re.compile('link')))
```

	['Hello, this is a link', 'Hello, this is a link,too']

#### find
```
html="""
<div class="panel">
			<div class="panel-heading">
				<h4>Hello</h4>
			</div>
			<div class="panel-body">
				<ul class="list" id="list-1" name="elements">
					<li class="element">Foo</li>
					<li class="element">Bar</li>
					<li class="element">Jar</li>
				</ul>
				<ul class="list list-small" id="list-2">
					<li class="element">Foo</li>
					<li class="element">Bar</li>
				</ul>
			</div>
		</div>
"""
from bs4 import BeautifulSoup
soup = BeautifulSoup(html,'lxml')
print(soup.find(name='ul'))
print(type(soup.find(name="ul")))
print(soup.find(class_='list'))
```

	<ul class="list" id="list-1" name="elements">
	<li class="element">Foo</li>
	<li class="element">Bar</li>
	<li class="element">Jar</li>
	</ul>
	<class 'bs4.element.Tag'>
	<ul class="list" id="list-1" name="elements">
	<li class="element">Foo</li>
	<li class="element">Bar</li>
	<li class="element">Jar</li>
	</ul>

### CSS选择器
```
html="""
<div class="panel">
			<div class="panel-heading">
				<h4>Hello</h4>
			</div>
			<div class="panel-body">
				<ul class="list" id="list-1" name="elements">
					<li class="element">Foo</li>
					<li class="element">Bar</li>
					<li class="element">Jar</li>
				</ul>
				<ul class="list list-small" id="list-2">
					<li class="element">Foo</li>
					<li class="element">Bar</li>
				</ul>
			</div>
		</div>
"""
from bs4 import BeautifulSoup
soup = BeautifulSoup(html,'lxml')
print(soup.select('.panel .panel-heading'))
print(soup.select("ul li"))
print(soup.select('#list-2 .element'))
print(type(soup.select('ul')[0]))
```

	[<div class="panel-heading">
	<h4>Hello</h4>
	</div>]
	[<li class="element">Foo</li>, <li class="element">Bar</li>, <li class="element">Jar</li>, <li class="element">Foo</li>, <li class="element">Bar</li>]
	[<li class="element">Foo</li>, <li class="element">Bar</li>]
	<class 'bs4.element.Tag'>
	
#### 嵌套选择
```
from bs4 import BeautifulSoup
soup = BeautifulSoup(html,'lxml')
for ul in soup.select('ul'):
    print(ul.select('li'))
```

	[<li class="element">Foo</li>, <li class="element">Bar</li>, <li class="element">Jar</li>]
	[<li class="element">Foo</li>, <li class="element">Bar</li>]
	
#### 获取属性
```
from bs4 import BeautifulSoup
soup = BeautifulSoup(html,'lxml')
for ul in soup.select('ul'):
    print(ul['id'])
    print(ul.attrs['id'])
```

	list-1
	list-1
	list-2
	list-2
	
#### 获取文本
```
from bs4 import BeautifulSoup
soup = BeautifulSoup(html,'lxml')
for li in soup.select('li'):
    print('Get Text:',li.get_text())
    print('String:',li.string)
```

	Get Text: Foo
	String: Foo
	Get Text: Bar
	String: Bar
	Get Text: Jar
	String: Jar
	Get Text: Foo
	String: Foo
	Get Text: Bar
	String: Bar

### 总结

## pyquery的使用
### 准备工作
`pip3 install pyquery`
### 初始化
在用pyquery库解析HTML文本的时候，需要先将其初始化为一个PyQuery对象。
初始化方式有很多种，例如直接传入字符串、传入URL、传入文件名，等等。下面我们详细介绍一下这些方式
#### 字符串初始化
```
html = """
<div>
    <ul>
        <li class="item-0">first item</a></li>
        <li class="item-1"><a href="link2.html">second item</a></li>
        <li class="item-inactive"><a href="link3.html">third item</a></li>
        <li class="item-1"><a href="link4.html">fourth item</a></li>
        <li class="item-0"><a href="link5.html">fifth item</a></li>
    </ul>
</div>
"""
from pyquery import PyQuery as pq
doc = pq(html)
print(doc('li'))
```

	<li class="item-0">first item</li>
	<li class="item-1"><a href="link2.html">second item</a></li>
	<li class="item-inactive"><a href="link3.html">third item</a></li>
	<li class="item-1"><a href="link4.html">fourth item</a></li>
	<li class="item-0"><a href="link5.html">fifth item</a></li>
	
#### URL初始化
```
from pyquery import PyQuery as pq
doc = pq(url="https://blog.iheng.xyz")
print(doc('title'))
```

	<title>毅恒-个人博客</title>
	
#### 文件初始化
```
from pyquery import PyQuery as pq
doc = pq(filename='test.html')
print(doc('li'))
```

	<li class="item-0"><a href="link1.html">first item</a></li>
	<li class="item-1"><a href="link2.html">second item</a></li>
	<li class="item-inactive"><a href="link3.html">third item</a></li>
	<li class="item-1"><a href="link4.html">fourth item</a></li>
	<li class="item-0"><a href="link5.html">fifth item</a></li>
	
### 基本CSS选择器
```
html = """
<div id="container">
    <ul class="list">
        <li class="item-0">first item</a></li>
        <li class="item-1 active"><a href="link2.html">second item</a></li>
        <li class="item-0 active"><a href="link3.html">third item</a></li>
        <li class="item-1"><a href="link4.html">fourth item</a></li>
        <li class="item-0"><a href="link5.html">fifth item</a></li>
    </ul>
</div>
"""
from pyquery import PyQuery as pq
doc = pq(html)
print(doc('#container .list li'))
print(type(doc('#container .list li')))
```

	<li class="item-0">first item</li>
			<li class="item-1"><a href="link2.html">second item</a></li>
			<li class="item-inactive"><a href="link3.html">third item</a></li>
			<li class="item-1"><a href="link4.html">fourth item</a></li>
			<li class="item-0"><a href="link5.html">fifth item</a></li>
		
	<class 'pyquery.pyquery.PyQuery'>
	
```
for item in doc('#container .list li').items():
    print(item.text())
```

	first item
	second item
	third item
	fourth item
	fifth item

### 查找节点
#### 子节点
```
from pyquery import PyQuery as pq
doc = pq(html)
items = doc('.list')
print(type(items))
print(items)
lis = items.find('li')
print(type(lis))
print(lis)
```

	<class 'pyquery.pyquery.PyQuery'>
	<ul class="list">
			<li class="item-0">first item</li>
			<li class="item-1"><a href="link2.html">second item</a></li>
			<li class="item-inactive"><a href="link3.html">third item</a></li>
			<li class="item-1"><a href="link4.html">fourth item</a></li>
			<li class="item-0"><a href="link5.html">fifth item</a></li>
		</ul>

	<class 'pyquery.pyquery.PyQuery'>
	<li class="item-0">first item</li>
	<li class="item-1"><a href="link2.html">second item</a></li>
	<li class="item-inactive"><a href="link3.html">third item</a></li>
	<li class="item-1"><a href="link4.html">fourth item</a></li>
	<li class="item-0"><a href="link5.html">fifth item</a></li>
			
```
from pyquery import PyQuery as pq
doc = pq(html)
items = doc('.list')
lis = items.children()
print(type(lis))
print(lis)
```

	<class 'pyquery.pyquery.PyQuery'>
	<li class="item-0">first item</li>
	<li class="item-1"><a href="link2.html">second item</a></li>
	<li class="item-inactive"><a href="link3.html">third item</a></li>
	<li class="item-1"><a href="link4.html">fourth item</a></li>
	<li class="item-0"><a href="link5.html">fifth item</a></li>
			
```
lis = items.children('.active')
print(lis)
```

	<class 'pyquery.pyquery.PyQuery'>
	<li class="item-1 active"><a href="link2.html">second item</a></li>
	<li class="item-0 active"><a href="link3.html">third item</a></li>

#### 父节点
```
html = """
<div id="container">
    <ul class="list">
        <li class="item-0">first item</a></li>
        <li class="item-1 active"><a href="link2.html">second item</a></li>
        <li class="item-0 active"><a href="link3.html">third item</a></li>
        <li class="item-1"><a href="link4.html">fourth item</a></li>
        <li class="item-0"><a href="link5.html">fifth item</a></li>
    </ul>
</div>
"""
from pyquery import PyQuery as pq
doc = pq(html)
items = doc('.list')
container = items.parent()
print(type(container))
print(container)
```

	<class 'pyquery.pyquery.PyQuery'>
	<div id="container">
		<ul class="list">
			<li class="item-0">first item</li>
			<li class="item-1 active"><a href="link2.html">second item</a></li>
			<li class="item-0 active"><a href="link3.html">third item</a></li>
			<li class="item-1"><a href="link4.html">fourth item</a></li>
			<li class="item-0"><a href="link5.html">fifth item</a></li>
		</ul>
	</div>
	
```
from pyquery import PyQuery as pq
doc = pq(html)
items = doc('.list')
container = items.parents()
print(type(container))
print(container)
```

	<html><body><div class="wrap">
		<div id="container">
			<ul class="list">
				<li class="item-0">first item</li>
				<li class="item-1 active"><a href="link2.html">second item</a></li>
				<li class="item-0 active"><a href="link3.html">third item</a></li>
				<li class="item-1"><a href="link4.html">fourth item</a></li>
				<li class="item-0"><a href="link5.html">fifth item</a></li>
			</ul>
		</div>
	</div>
	</body></html><body><div class="wrap">
		<div id="container">
			<ul class="list">
				<li class="item-0">first item</li>
				<li class="item-1 active"><a href="link2.html">second item</a></li>
				<li class="item-0 active"><a href="link3.html">third item</a></li>
				<li class="item-1"><a href="link4.html">fourth item</a></li>
				<li class="item-0"><a href="link5.html">fifth item</a></li>
			</ul>
		</div>
	</div>
	</body><div class="wrap">
		<div id="container">
			<ul class="list">
				<li class="item-0">first item</li>
				<li class="item-1 active"><a href="link2.html">second item</a></li>
				<li class="item-0 active"><a href="link3.html">third item</a></li>
				<li class="item-1"><a href="link4.html">fourth item</a></li>
				<li class="item-0"><a href="link5.html">fifth item</a></li>
			</ul>
		</div>
	</div>
	<div id="container">
			<ul class="list">
				<li class="item-0">first item</li>
				<li class="item-1 active"><a href="link2.html">second item</a></li>
				<li class="item-0 active"><a href="link3.html">third item</a></li>
				<li class="item-1"><a href="link4.html">fourth item</a></li>
				<li class="item-0"><a href="link5.html">fifth item</a></li>
			</ul>
		</div>

```
from pyquery import PyQuery as pq
doc = pq(html)
items = doc('.list')
container = items.parents(".wrap")
print(type(container))
print(container)
```

	<div class="wrap">
		<div id="container">
			<ul class="list">
				<li class="item-0">first item</li>
				<li class="item-1 active"><a href="link2.html">second item</a></li>
				<li class="item-0 active"><a href="link3.html">third item</a></li>
				<li class="item-1"><a href="link4.html">fourth item</a></li>
				<li class="item-0"><a href="link5.html">fifth item</a></li>
			</ul>
		</div>
	</div>
	
#### 兄弟节点
```
from pyquery import PyQuery as pq
doc = pq(html)
li = doc('.list .item-0.active')
print(li.siblings())
```

	<li class="item-1 active"><a href="link2.html">second item</a></li>
	<li class="item-0">first item</li>
	<li class="item-1"><a href="link4.html">fourth item</a></li>
	<li class="item-0"><a href="link5.html">fifth item</a></li>
	
```
from pyquery import PyQuery as pq
doc = pq(html)
li = doc('.list .item-0.active')
print(li.siblings(".active"))
```

	<li class="item-1 active"><a href="link2.html">second item</a></li>
	
### 遍历节点
```
from pyquery import PyQuery as pq
doc = pq(html)
li = doc('.item-0.active')
print(li)
print(str(li))
```

	<li class="item-0 active"><a href="link3.html">third item</a></li>
	<li class="item-0 active"><a href="link3.html">third item</a></li>
	
```
from pyquery import PyQuery as pq
doc = pq(html)
lis = doc('li').items()
print(type(lis))
for item in lis:
    print(item,type(item))
```

	<class 'generator'>
	<li class="item-0">first item</li>
	 <class 'pyquery.pyquery.PyQuery'>
	<li class="item-1 active"><a href="link2.html">second item</a></li>
	 <class 'pyquery.pyquery.PyQuery'>
	<li class="item-0 active"><a href="link3.html">third item</a></li>
	 <class 'pyquery.pyquery.PyQuery'>
	<li class="item-1"><a href="link4.html">fourth item</a></li>
	 <class 'pyquery.pyquery.PyQuery'>
	<li class="item-0"><a href="link5.html">fifth item</a></li>
	 <class 'pyquery.pyquery.PyQuery'>
	 
#### 获取信息
##### 获取属性
```
from pyquery import PyQuery as pq
doc = pq(html)
a = doc('.item-0.active a')
print(a,type(a))
print(a.attr('href'))
```

	<a href="link3.html">third item</a> <class 'pyquery.pyquery.PyQuery'>
	link3.html
	
```
print(a.attr.href)
```

	link3.html
	
```
a = doc('a')
print(a,type(a))
print(a.attr('href'))
print(a.attr.href)
```

	<a href="link2.html">second item</a><a href="link3.html">third item</a><a href="link4.html">fourth item</a><a href="link5.html">fifth item</a> <class 'pyquery.pyquery.PyQuery'>
	link2.html
	link2.html
	
```
a = doc('a')
for item in a.items():
    print(item.attr('href'))
```

	link2.html
	link3.html
	link4.html
	link5.html
	
##### 获取文本
```
html = """
<div class="wrap">
    <div id="container">
        <ul class="list">
        <li class="item-0">first item</a></li>
        <li class="item-1 active"><a href="link2.html">second item</a></li>
        <li class="item-0 active"><a href="link3.html">third item</a></li>
        <li class="item-1"><a href="link4.html">fourth item</a></li>
        <li class="item-0"><a href="link5.html">fifth item</a></li>
        </ul>   
    </div>
</div>
"""
from pyquery import PyQuery as pq
doc = pq(html)
a = doc('.item-0.active a')
print(a)
print(a.text())
```

	<a href="link3.html">third item</a>
	third item
	
```
from pyquery import PyQuery as pq
doc = pq(html)
li = doc('.item-0.active')
print(li)
print(li.html())
```
			
	<a href="link3.html">third item</a>
	
```
from pyquery import PyQuery as pq
doc = pq(html)
li = doc('li')
print(li.html())
print(li.text())
print(type(li.text()))
```

	first item
	first item second item third item fourth item fifth item
	<class 'str'>
	
### 节点操作
#### addClass 和 removeClass
```
html = """
<div class="wrap">
    <div id="container">
        <ul class="list">
        <li class="item-0">first item</a></li>
        <li class="item-1 active"><a href="link2.html">second item</a></li>
        <li class="item-0 active"><a href="link3.html">third item</a></li>
        <li class="item-1"><a href="link4.html">fourth item</a></li>
        <li class="item-0"><a href="link5.html">fifth item</a></li>
        </ul>   
    </div>
</div>
"""
from pyquery import PyQuery as pq
doc = pq(html)
li = doc('.item-0.active')
print(li)
li.remove_class('active')
print(li)
li.add_class('active')
print(li)
```

	<li class="item-0 active"><a href="link3.html">third item</a></li>
	<li class="item-0"><a href="link3.html">third item</a></li>
	<li class="item-0 active"><a href="link3.html">third item</a></li>
	
#### attr、text和html
```
html='''
<ul class=“list”>
    <li class="item-0 active"><a href="link3.html"><span class="bold">third item</span></a></li>
</ul>
'''
from pyquery import PyQuery as pq
doc = pq(html)
li = doc('.item-0.active')
print(li)
li.attr('name','link')
print(li)
li.text('changed item')
print(li)
li.html('<span>changed item</span>')
print(li)
```

	<li class="item-0 active"><a href="link3.html"><span class="bold">third item</span></a></li>
	<li class="item-0 active" name="link"><a href="link3.html"><span class="bold">third item</span></a></li>
	<li class="item-0 active" name="link">changed item</li
	<li class="item-0 active" name="link"><span>changed item</span></li>
	
#### remove
```
html = """
<div class="wrap">
    Htllo,world
    <p>This is a paragraph</p>
</div>
"""
from pyquery import PyQuery as pq
doc = pq(html)
wrap = doc('.wrap')
print(wrap.text())
```

	Htllo,world
	This is a paragraph
	
```
wrap.find('p').remove()
print(wrap.text())
```

### 伪类选择器
```
html = """
<div id="container">
    <ul class="list">
        <li class="item-0">first item</a></li>
        <li class="item-1 active"><a href="link2.html">second item</a></li>
        <li class="item-0 active"><a href="link3.html">third item</a></li>
        <li class="item-1"><a href="link4.html">fourth item</a></li>
        <li class="item-0"><a href="link5.html">fifth item</a></li>
    </ul>
</div>
"""
from pyquery import PyQuery as pq
doc = pq(html)
li = doc('li:first-child')
print(li)
li = doc('li:last-child')
print(li)
```

	<li class="item-0">first item</li>
	<li class="item-0"><a href="link5.html">fifth item</a></li>

### 总结

## parsel的使用
### 介绍
### 准备工作
`pip3 install parsel`
### 初始化
```
html = """
<div id="container">
    <ul class="list">
        <li class="item-0">first item</a></li>
        <li class="item-1 active"><a href="link2.html">second item</a></li>
        <li class="item-0 active"><a href="link3.html">third item</a></li>
        <li class="item-1"><a href="link4.html">fourth item</a></li>
        <li class="item-0"><a href="link5.html">fifth item</a></li>
    </ul>
</div>
"""
```

```
from parsel import Selector
selector = Selector(text=html)
```

```
items = selector.css('.item-0')
print(len(items),type(items),items)
item2 = selector.xpath('//li[contains(@class,"item-0")]')
print(len(item2),type(items),item2)
```

	3 <class 'parsel.selector.SelectorList'> [<Selector xpath="descendant-or-self::*[@class and contains(concat(' ', normalize-space(@class), ' '), ' item-0 ')]" data='<li class="item-0">first item</li>'>, <Selector xpath="descendant-or-self::*[@class and contains(concat(' ', normalize-space(@class), ' '), ' item-0 ')]" data='<li class="item-0 active"><a href="li...'>, <Selector xpath="descendant-or-self::*[@class and contains(concat(' ', normalize-space(@class), ' '), ' item-0 ')]" data='<li class="item-0"><a href="link5.htm...'>]
	3 <class 'parsel.selector.SelectorList'> [<Selector xpath='//li[contains(@class,"item-0")]' data='<li class="item-0">first item</li>'>, <Selector xpath='//li[contains(@class,"item-0")]' data='<li class="item-0 active"><a href="li...'>, <Selector xpath='//li[contains(@class,"item-0")]' data='<li class="item-0"><a href="link5.htm...'>]
### 提取文本
```
from parsel import Selector
selector = Selector(text=html)
items = selector.css('.item-0')
for item in items:
    text = item.xpath('.//text()').get()
    print(text)
```

	first item
	third item
	fifth item

```
result = selector.xpath('//li[contains(@class,"item-0")]//text()').get()
print(result)
```

	first item

```
result = selector.xpath('//li[contains(@class,"item-0")]//text()').getall()
print(result)
```

	['first item', 'third item', 'fifth item']

```
result = selector.css('.item-0 *::text').getall()
print(result)
```

```
from parsel import Selector
selector = Selector(text=html)
result = selector.css('.item-0.active a::attr(href)').get()
print(result)
result = selector.xpath('//li[contains(@class,"item-0") and contains(@class,"active")]/a/@href').get()
print(result)
```

	link3.html
	link3.html
	
### 正则提取
```
from parsel import Selector
selector = Selector(text=html)
result = selector.css('.item-0').re('link.*')
print(result)
```

	['link3.html">third item</a></li>', 'link5.html">fifth item</a></li>']
	
```
from parsel import Selector
selector = Selector(text=html)
result = selector.css('.item-0 *::text').re('.*item')
print(result)
```

	['first item', 'third item', 'fifth item']
	
```
from parsel import Selector
selector = Selector(text=html)
result = selector.css('.item-0').re_first('<span class="bold">(.*?)</span>')
print(result)
```

	third item

### 总结
