---
title: XPath的使用
date: 2022-04-10 23:52:24
tags: 网页数据解析提取、xpath的使用
categories: 网页数据解析提取
---

# XPath
> 注：来源于崔庆才<<python3爬虫实战开发2>>本人学习并分享，略有补充。

本文主要分为三个部分，首先是将网页内容转为XPath解析对象，然后通过该对象进行节点的选择以及文本和属性的提取。

## 安装lxml库
在开始之前，安装lxml库，并能够运行。
```
pip install lxml
```

## XPath解析对象
我们可以通过`etree.HTML()`或`etree.parse()`这两个类来初始化XPath解析对象。
### `etree.HTML()`
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
print(html)
print(etree.tostring(html).decode("utf-8"))
```
先是导入了lxml库中etree模块，然后调用HTML这个类进行初始化，接下来就可以调用对象的方法来选取其中的内容了。
这里打印了html的类型，并且调用了etree模块的tostring方法返回了html的内容，但返回的内容是bytes类型的，就用decode将其转化为string类型

### `etree.parse()`
> __test.html__
```
<div>
    <ul>
        <li class="item-0"><a href="link1.html">first item</a></li>
        <li class="item-1"><a href="link2.html">second item</a></li>
        <li class="item-inactive"><a href="link3.html">third item</a></li>
        <li class="item-1"><a href="link4.html">fourth item</a></li>
        <li class="item-0"><a href="link5.html">fifth item</a>
    </ul>
</div>
```
> __mian.py__
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
print(type(html))
print(etree.tostring(html).decode("utf-8"))
```
以上我们先创建了一个test.html文件，然后在main.py中利用etree.parse模块来初始化对象，其他的与上方一致
> __注意__ ：这里需要填入两个参数，一个是文件来源——可以是路径也可以是url，另一个是解析方式，这里选择etree.HTMLParser()

## 选择节点（重点）
节点选择的通用规则有如下，在练习之前可以先浏览一遍，然后对照着这些规则来写例子。

|选择规则|描述|
|----|-----|
|`nodename`|选取此节点的所有子节点|
|`/`|从当前节点选取直接子节点|
|`//`|从当前节点选取子孙节点|
|`.`|选取当前节点|
|`..`|选取当前节点的父节点|
|`@`|选取属性|

> 接下来我们尝试选择所有节点、子节点和父节点这三类
### 选择所有节点
```
from lxml import etree

html = etree.parse('http://003edcjcgedhi.jumpkj.chuairan.com/index.html',etree.HTMLParser())
result = html.xpath("//*")
print(type(result))
print(type(result[0]))
print(result)
```


> 这里我们选择了所有子孙节点，返回是一个列表，并且列表中的每一个元素的类型与最原始的xpath对象一样

```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath("//li")
print(type(result))
print(type(result[0]))
print(result)
```

> 这里我们选择了所有为li的节点，并同样地打印了结果的类型

### 选择子节点
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath("//li/a")
print(result)
```

	[<Element a at 0x1acaaacf9c0>, <Element a at 0x1acaaacfa00>, <Element a at 0x1acaaacfa40>, <Element a at 0x1acaaacfa80>, <Element a at 0x1acaaacfac0>]

这里我们直接在选择了`li`节点的基础上，选择了子节点a

> __注意__ ：`/`与 `//`的区别，前者是选择了子节点，后者选择的是子孙节点，下面我们通过两个例子来体验一下这两个的区别。

__使用`//`__ :
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath("//ul//a")
print(result)
```
运行结果

	[<Element a at 0x251beb5f680>, <Element a at 0x251beb5f6c0>, <Element a at 0x251beb5f700>, <Element a at 0x251beb5f740>, <Element a at 0x251beb5f780>]
	
__使用`/`__ :

```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath("//ul/a")
print(result)
```

运行结果

	[]
	
第一个例子使用了`//`,选出来是有内容的，而第二个就只有一个空的列表，因为`ul`和`a`这两个节点中间还有一个`li`节点。

### 选择父节点
在一个节点下，使用`..`就可回到父节点，下面我们来尝试一下a节点回到li节点:

```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath("//ul//a/..")
print(result)
```

运行结果：

	[<Element li at 0x26c4872f880>, <Element li at 0x26c4872f8c0>, <Element li at 0x26c4872f900>, <Element li at 0x26c4872f940>, <Element li at 0x26c4872f980>]
	
这里我们已经从`a`节点回到了它的父节点`li`上面，当然还有一种方法，可以用`parent::*`来替换`..`来达到选择父节点的效果，
例子如下：

```
result = html.xpath("//ul//a/parent::*")
```
	
选择效果与上面的`html.xpath("//ul//a/..")`一样。


### 属性匹配
节点的选择可以通过属性来进行筛选，最后得到自己想要的节点。
#### 单属性单值
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath("//li[@class='item-0']")
print(result)
```

运行结果为：

	[<Element li at 0x20772090680>, <Element li at 0x207720906c0>]

这里我们选择了`class`为item-0的li元素

#### 单属性多值
```
from lxml import etree

text = """
<li class='li li-first'><a href"link.html">first item</a></li>
"""
html = etree.HTML(text)
result = html.xpath("//li[@class='li']")
print(result)
```

运行结果为：

	[]
	
这里我们看到运行结果是个空列表，说明在`class`多个值时，并不能选择到这个节点，所以我们将用`contains`方法，运行代码如下：

```
from lxml import etree

text = """
<li class='li li-first'><a href"link.html">first item</a></li>
"""
html = etree.HTML(text)
result = html.xpath("//li[contains(@class,'li')]")
print(result)
```

运行结果为：

	[<Element li at 0x189c79d0580>]

我们看到结果并非是一个空列表，说明是已经选择出来了，`contains`方法的一个参数传入属性名称，第二个传入属性值。

#### 多属性多值
```
from lxml import etree

text = """
<li class='li li-first' name="item"><a href"link.html">first item</a></li>
"""
html = etree.HTML(text)
result = html.xpath("//li[contains(@class,'li') and @name='item']")
print(result)
```

这里我们通过`class`与`name`两个属性来选择`li`节点，这两个属性通过`and`运算符来连接，还有许多运算符将列在下方表格中。

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
当我们匹配属性得到一个节点列表时，但我们只需要其中的一个或者多个时，可采取下方措施来选择自己需要的节点。

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

运行结果为：

	['first item']
	['fifth item']
	['first item', 'second item']
	['third item']

以上通过向中括号船体数字以及`last`、`positon`这两个方法选择到了相应的节点。XPath提供了100多种方法，部分方法呈现在下方。

------
>  节点集函数 
  
   last() 返回当前上下文中的最后一个节点的位置号数。 
   position() 返回当前节点的位置的数字,位于第多少个。
   count(node-set) 返回节点集node-set中的节点数。
   id(mark) 根据在DTD中声明为ID类型的标识符选择元素,返回一个节点集。
   name() 返回节点名称。
   local-name() 返回不带名称空间的节点名称。
   namespace-uri() 返回名称空间。

>  字符串函数 
  
   string(object) 把节点集、数字、布尔值等转化成字串并返回。 
   format-number(num) 把数字转化成字串并返回。 
   concat(string1,string2...) 合并多个字串并返回。
   starts-with(string1,string2) 如果字串string1开头带string2的所有字符则返回true,否则返回false。
   contains(string1,string2) 如果字串string1包含string2的所有字符则返回true,否则返回false。
   substring(string,number1,number2) 取string中从位置number1开始,number2长的子串,number2可省略。
   substring-before(string1,string2) 取string1在string2第一次出现位置之前的子串。
   substring-after(string,string) 取string1在string2第一次出现位置之后的子串。
   string-length(string) 返回string的长度数字。
   normalize-space(string) 清除string头尾的空白字符并且把连续的空白字符替换为一个再返回。
   translate(string1,string2,string3) 假如string1中的字符在string2中有出现,那么替换为string3对应string2的同一位置的字符,假如string3这个位置取不到字符则删除string1的该字符。

>  布尔函数 
   
    boolean(object) 非0和NaN的数字/非空节点/长度大于0的字串返回true,非基本类型的转换有时无法估计。
    not(boolean)   对布尔值取反。 
    true() 返回true。
    false() 返回false。
    lang(string) 如果上下文节点的lang属性和string相同则返回true。

>  数字函数

   number(object) 使对象转化成数字,布尔值true为1,false为0;节点集首先转换成字符串,字符串转换成数字或者NaN。
   sum(node-set) 对节点集node-set中的所有节点应用number()函数后返回和。 
   floor(number) 返回不大于数字number的最大整数。
   ceiling(number) 返回不小于数字number的最小整数。
   round(number) 返回和数字number的四舍五入结果。

------
### 节点轴选择
想必还比较陌生，但大家在选择父节点时已经用过一次，那就是`parent`方法，下面将介绍其他方法：

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
```

以上的格式都是方法加上`::`最后再加上要选择的节点。
就拿第一个来举例，第一个先是调用了`ancestor`轴，即祖先节点，两个`:`之后跟着`*`。相当于除了`li`节点，其他节点都被选上了。
其他几个相继是：获取`div`祖先节点、`attribute`轴获取当前节点所有属性、`desecndant`获取所有子孙节点

## 获取内容
### 文本获取
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath("//li[@class='item-0']/a/text()")
print(result)
```

运行结果为：

	['first item', 'fifth item']
	
上面先是选择了a节点，再提取了a节点内部的文本内容

### 属性获取
```
from lxml import etree

html = etree.parse('./test.html',etree.HTMLParser())
result = html.xpath("//li[@class='item-0']/a/@href")
print(result)
```

运行结果为：

	['link1.html', 'link5.html']

上面选择了a节点，并提取了节点中的`href`的属性值