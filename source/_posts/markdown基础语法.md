---
title: markdown基础语法
date: 2022-01-29 01:47:53
tags: 笔记，查询专用
---


# 标题语法

要创建标题，请在单词或短语前面添加井号（`#`）。`#`的数量代表了标题的级别。列如，添加三个`#`表示创建一个三级标题（`<h3>`）(列如：`### Heading leavel 3`)

1. ## 语句：
```
# Heading level 1
## Heading level 2
### Heading level 3
```

2. ## 效果：
> 效果请自行打出

# 段落语法

要创建段落，使用空白行将一行或多行文本进行分隔。

1. ## 语句：
```
我创建了一个段落。
```
2. ## 效果：
> 我创建了一个段落。  

# 换行语法
在一行的末尾添加两个或多个空格，然后按回车键，即可创建一个换行（直接回车换行也是可以的）。

1. ## 语句：
This is the first line.
And this is the second line.
2. ## 效果：
>This is the first line.
>And this is the second line.

# 强调语法
在需要强调的前后加上`*`或`_`。
> 一个`*`代表斜体
> 两个`*`代表粗体
> 三个`*`代表粗体和斜体

1. ## 语句：
```
*这是斜体*
**这是粗体**
***这是斜体加粗体***
```
2. ## 效果：
> *这是斜体*
> **这是粗体**
> ***这是斜体加粗体***

# 引用语法

要创建块引用，请在段落前添加一个`>`符号。

1. ## 语句：
```
> 引用的格式，记得在`>`后面空一格，与`#`类似。
```
2. ## 效果：
> 引用的格式，记得在`>`后面空一格，与`#`类似。

# 列表语法

1. ## 有序列表：
在每个列表项前添加数字并紧跟一个英文句点。数字不必按数学顺序排列，但列表应当以数字1起始。
	1. ### 语句：
```
		1. first
		2. second
		3. third
		------
		1. first
		1. second
		1. third
		------
		1. first
		5. second
		8. third
```
	2. ### 效果：
	>1. first
	>2. second
	>3. third
		-----
	>1. first
	>1. second
	>1. third
		-----
	>1. first
	>5. second
	>8. third

2. ## 无序列表：
要创建无序列表，请在每个列表项前面添加破折号 `-`、星号 `*` 或加号 `+` 。缩进一个或多个列表项可创建嵌套列表。（建议使用`-`）
	1. ### 语句：
```
	- First item
	- Second item
	- Third item
	-----
	* First item
	* Second item
	* Third item
	-----
	+ First item
	+ Second item
	+ Third item
```
	2. ### 效果：
	> - First item
	> - Second item
	> - Third item
		------
	> * First item
	> * Second item
	> * Third item
		------
	> + First item
	> + Second item
	> + Third item

# 分隔线语法

要创建分隔线，请在单独一行上使用三个或多个星号 `***`、破折号 `---` 或下划线 `___` ，并且不能包含其他内容。

1. ## 语句：
```
***

---

_________________
```
2. ## 效果：
***

---

_________________

# 链接语法

- 链接文本放在中括号内，链接地址放在后面的括号中，链接的title可选。
超链接MarkDown语法代码：`[超链接显示名]（超链接地址 "超链接title"）`
对应的HTML代码：`<a href="超链接地址" title="超链接title">超链接显示名</a>`

- 使用尖括号可以很方便地将URL或者email地址变成可点击的链接
1. ## 语句：
```
这是一个链接：[毅恒-个人博客](https://blog.iheng.xyz "回到主页")
<http://blog.iheng.xyz>
<fake@example.com>
```
2. ## 效果：
> 这是一个链接：[毅恒-个人博客](https://blog.iheng.xyz "回到主页")
> <http://blog.iheng.xyz>
> <fake@example.com>
> > 还有更多效果请前往官方文档查看：[MarkDown官方文档](https://markdown.com.cn/basic-syntax/links.html "https://markdown.com.cn/basic-syntax/links.html")

# 图片语法

- 要添加图像，请使用感叹号 (!), 然后在方括号增加替代文本，图片链接放在圆括号里，括号里的链接后可以增加一个可选的图片标题文本。
	- 插入图片Markdown语法代码：`![图片alt](图片链接 "图片title")。`
	- 可跳转的图片：`[![图片alt](图片链接 "图片title")](跳转链接)。`
	- 对应的HTML代码：`<img src="图片链接" alt="图片alt" title="图片title">`
- 建议使用图床
1. ## 语句：
```
![这是星空图](https://s3.bmp.ovh/imgs/2022/01/258c9ee82e95528b.jpg "星空图")
[![这是星空图](https://s3.bmp.ovh/imgs/2022/01/258c9ee82e95528b.jpg "星空图")](https://s3.bmp.ovh/imgs/2022/01/258c9ee82e95528b.jpg)
```
2. ## 效果：
![这是星空图](https://s3.bmp.ovh/imgs/2022/01/258c9ee82e95528b.jpg "星空图")
[![这是星空图](https://s3.bmp.ovh/imgs/2022/01/258c9ee82e95528b.jpg "星空图")](https://s3.bmp.ovh/imgs/2022/01/258c9ee82e95528b.jpg)

# 转义字符语法
- 要显示原本用于格式化MarkDown文档的字符，请在字符前面添加反斜杠字符`\`
- 可转义的字符：
```
\ ` * _ {} [] () # + - . ! |。
```
- 自动转义字符(作为HTML实体将不会被转义，其他情况下自动转换)：
```
& <
```
	>具体参考：[官方文档](https://markdown.com.cn/basic-syntax/escaping-characters.html)

1. ## 语句：
> & 在MarkDown中&被转为`&amp`;,然后再HTML中就显示为&
> \`&copy;\``&copy;` 在MarkDown中不会被转义成`&amp;copy`,在MarkDown中依旧表示为'&'。
> 详情关注[Markdown 转义字符语法](https://markdown.com.cn/basic-syntax/escaping-characters.html)


	对比以下两个的不同
		- Without the backslash, this would be a bullet in an unordered list.
		\- Without the backslash, this would be a bullet in an unordered list.

2. ## 效果：
- Without the backslash, this would be a bullet in an unordered list.
\- Without the backslash, this would be a bullet in an unordered list.

# 结语
主要使用的语法有十个，半小时左右就可以学完。这篇文章主要是用来查询，不建议用本篇
文章学习，如若学习，请前往[markdown官方教程](https://markdown.com.cn/)。
