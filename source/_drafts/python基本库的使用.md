---
title: python爬虫基本库的使用
date: 2022-02-03 00:02:41
tags: python爬虫基本库的使用
categories: python爬虫的学习
---

## 前言
 - 爬虫文章均来自[《python3网络爬虫开发实战教程》](https://item.jd.com/12333540.html)，只为方便记录日常所学，以及方便观看。
 - 本章主要学习python爬虫的基本库，这些库基本已经实现了爬虫的一些功能，我们只要能够调用函数即可爬虫一些简单的页面。

## urllib的使用
首先介绍一个python库，叫做urllib库，利用它可以就可以实现HTTP请求的发送，而且不需要
关心HTTP协议本身甚至更底层的实现，我们要做的是制定请求的url，请求头，请求体等信息。
此外urllib还可以吧服务器返回的响应转化为Python对象，我们通过该对象便更方便地获取响应的
相关信息，如响应状态码，响应头，响应体等。
> 注意：在python2中，有urllib和urllib2两个库来实现HTTP请求的发送，而在Python3中，urllib2的库已经不存在了，统一为urllib。

首先，我们了解一下urllib库的使用方法，它是Python内置的HTTP请求库，也就是说不需要额外的安装，可直接使用。urllib库包含如下4个模块。
1. request: 这是最基本的HTTP请求模块，可以模拟请求的发送。就像在浏览器里输入网址后然后按下回车一样，只需要给库方法传入URL以及额外的参数，就可以模拟实现发送请求的过程了。
2. error: 异常处理模块。如果出现请求异常，那么我们可以捕获这些异常，然后进行重试或其他操作以保证程序运行不会意外中止。
3. parse: 一个工具模块。提供了许多url的处理方法，例如拆分、解析、合并等。
4. robotparser: 主要用来识别网站的robots.txt文件，然后判断哪些网站可以爬，哪些网站不可以，它其实用得比较少。

### 发送请求
使用urllib库的request模块，可以方便地发送请求并得到响应。我们先来看下它的具体用法。
#### urlopen
urllib.request模块提供了最基本的构造HTTP请求的方法，利用这个模块可以模拟浏览器的
请求发起过程，同时它具有处理授权验证（Authentication）、重定向（Redirection）、浏览器Cookie以及其他一些功能。

下面我们体会一下request模块的强大之处。这里以Python官网为例，我们把这个网页抓取下来：
```
import urllib.request

response = urllib.request.urlopen('http://www.python.org')
print(response.read().decode('utf-8'))
```
运行结果如下方：

	<!doctype html>
	<!--[if lt IE 7]>   <html class="no-js ie6 lt-ie7 lt-ie8 lt-ie9">   <![endif]-->
	<!--[if IE 7]>      <html class="no-js ie7 lt-ie8 lt-ie9">          <![endif]-->
	<!--[if IE 8]>      <html class="no-js ie8 lt-ie9">                 <![endif]-->
	<!--[if gt IE 8]><!--><html class="no-js" lang="en" dir="ltr">  <!--<![endif]-->

	<head>
		<meta charset="utf-8">
		<meta http-equiv="X-UA-Compatible" content="IE=edge">

		<link rel="prefetch" href="//ajax.googleapis.com/ajax/libs/jquery/1.8.2/jquery.min.js">
		<link rel="prefetch" href="//ajax.googleapis.com/ajax/libs/jqueryui/1.12.1/jquery-ui.min.js">

		<meta name="application-name" content="Python.org">
	.............

这里我们只用了两行代码，便完成了Python官网的爬取，输出了其网页的源代码。得到代码之后
，我们想要的链接、图片地址、文本信息不就都可以提取出来了吗？

接下来，看看返回的响应到底是什么。利用type方法输出响应的类型：
```
import urllib.request

response = urllib.request.urlopen('http://www.python.org')
print(type(response))
print(dir(response))
```
输出结果如下：

	<class 'http.client.HTTPResponse'>
	<class 'http.client.HTTPResponse'>
	['__abstractmethods__', '__class__', '__del__', '__delattr__', '__dict__', '__dir__', '__doc__', '__enter__', '__eq__', '__exit__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__next__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '_abc_impl', '_checkClosed', '_checkReadable', '_checkSeekable', '_checkWritable', '_check_close', '_close_conn', '_get_chunk_left', '_method', '_peek_chunked', '_read1_chunked', '_read_and_discard_trailer', '_read_next_chunk_size', '_read_status', '_readall_chunked', '_readinto_chunked', '_safe_read', '_safe_readinto', 'begin', 'chunk_left', 'chunked', 'close', 'closed', 'code', 'debuglevel', 'detach', 'fileno', 'flush', 'fp', 'getcode', 'getheader', 'getheaders', 'geturl', 'headers', 'info', 'isatty', 'isclosed', 'length', 'msg', 'peek', 'read', 'read1', 'readable', 'readinto', 'readinto1', 'readline', 'readlines', 'reason', 'seek', 'seekable', 'status', 'tell', 'truncate', 'url', 'version', 'will_close', 'writable', 'write', 'writelines']

可以看出，响应是一个HTTPResponse类型的对象，主要包含read、readinto、getheader、
getheads、fileno等方法，以及msg、version、status、reason、debuglevel、closed等属性。

例如，调用read方法可以得到相应的额网页内容、调用status属性可以得到响应结果的状态码（200代表请求成功，404代表网页未找到等）。

下面通过一个实例来看看：
```
import urllib.request
response = urllib.request.urlopen('http://www.python.org')
print(response.status)
print(response.getheaders())
print(response.getheader('Server'))
```
运行结果如下：

	200
	[('Connection', 'close'), ('Content-Length', '49696'), ('Server', 'nginx'), ('Content-Type', 'text/html; charset=utf-8'), ('X-Frame-Options', 'DENY'), ('Via', '1.1 vegur, 1.1 varnish, 1.1 varnish'), ('Accept-Ranges', 'bytes'), ('Date', 'Thu, 03 Feb 2022 15:24:58 GMT'), ('Age', '1123'), ('X-Served-By', 'cache-iad-kjyo7100026-IAD, cache-hkg17927-HKG'), ('X-Cache', 'HIT, HIT'), ('X-Cache-Hits', '2, 2830'), ('X-Timer', 'S1643901899.635229,VS0,VE0'), ('Vary', 'Cookie'), ('Strict-Transport-Security', 'max-age=63072000; includeSubDomains')]
	nginx

其中前两个输出分别是响应的状态码和响应的头信息；最后一个输出是调用getheader方法，并传入参数Server,获取了响应肉中Server的值，结果是nginx，意思为服务器是用Nainx搭建的。
利用最基本的urlopen方法，已经可以完成对简单网页的GET请求抓取。
如果想给连接传递一些阐述，又该怎么实现呢？首先看一下urlopen方法的api：
```
urllib.request.urlopen(url,data=None,[timeout,]*,cafile=None,capath=None,cadefault=False,context=None)
```
可以发现，除了第一个阐述用于传递URL外，我们还可以传递其他内容，例如data（附加数据）、timeout（超时时间）等。
接下来就详细说明一下urlopen方法中几个阐述的用法

#### data参数
data参数是可选的。在添加该参数时，需要是哦那个bytes方法将参数转化为字节流编码格式的内容，即bytes类型。另外，
如果传递了这个参数，那么它的请求方式就不再是GET，而是POST了。

下面用实例来看一下：
```
import urllib.parse
import urllib.request

data = bytes(urllib.parse.urlencode({'name':'germey'}),encoding='utf-8')
response = urllib.request.urlopen('http://www.httpbin.org/post',data=data)
print(response.read().decode('utf-8'))
```
这里我们传入了一个参数name，值是germey，需要将它转码成bytes类型。转码时采用了bytes方法，该方法的第一个参数是str(字符串)类型，因此用urllib.parse模块里的urlencode方法将字典参数转化为字符串；第二个参数用于指定编码格式，这里指定为utf-8。

此处我们请求的站点是`www.httpbin.org`,它可以提供HTTP请求测试。本次我们请求的额URL 为`http://www.httpbin.org/post`,这个链接可以用来测试
POST请求，能够输出请求的一些信息，其中就包含我们传递的data参数。

上面实例的运行结果如下：

	{
	  "args": {}, 
	  "data": "", 
	  "files": {}, 
	  "form": {
		"name": "germey"
	  }, 
	  "headers": {
		"Accept-Encoding": "identity", 
		"Content-Length": "11", 
		"Content-Type": "application/x-www-form-urlencoded", 
		"Host": "www.httpbin.org", 
		"User-Agent": "Python-urllib/3.7", 
		"X-Amzn-Trace-Id": "Root=1-61fbfe42-40d13fd96dac1f1602deed73"
	  }, 
	  "json": null, 
	  "origin": "39.181.204.56", 
	  "url": "http://www.httpbin.org/post"
	}
可以发现我们传递的参数出现在了form字段中，这表明是模拟表单提交，以POST方式传输数据。

#### timeout参数
timeout参数用于设置超时时间，单位为秒，意思是如果请求超出了设置的这个时间，还没有得到响应，就会抛出异常。如果不指定该参数，则会使用全局默认时间。这个参数支持HTTP、HTTPS、FTP请求。

下面用实例来看一下：、
```
import urllib.request

response = urllib.request.urlopen('https://www.httpbin.org/get',timeout=0.1)
print(response.read())
```
运行结果可能如下：
	
	........
	  File "C:\Program Files\WindowsApps\PythonSoftwareFoundation.Python.3.9_3.9.2800.0_x64__qbz5n2kfra8p0\lib\urllib\request.py", line 1389, in https_open
		return self.do_open(http.client.HTTPSConnection, req,
	  File "C:\Program Files\WindowsApps\PythonSoftwareFoundation.Python.3.9_3.9.2800.0_x64__qbz5n2kfra8p0\lib\urllib\request.py", line 1349, in do_open
		raise URLError(err)
	urllib.error.URLError: <urlopen error timed out>
	
这里我们设置超时时间为0.1秒。程序运行了0.1秒后，服务器依然没有响应，于是抛出了URLError异常。该异常属于urllib.error模块，错误原因是超时。

因此可以通过设置这个超时时间，实现当一个页面长时间未响应时，就跳过对它的抓取。此外，利用try except语句也可以实现，相关代码如下：
```
from urllib import request,error
import socket

try:
    response = request.urlopen('https://httpbin.org',timeout=0.1)
except error.URLError as e:
    if isinstance(e.reason,socket.timeout):
        print('TIME OUT')
```
这里我们请求了`https://www.httpbin.org/get`这个测试链接，设置超时时间为0.1，然后捕获到URLError这个异常，并判断异常类型是socket.timeout,意思是超时异常，因此得出确实是因为超时而报错的结论，最后打印输出了TIME OUT。

运行结果如下：
	
	TIME OUT
	
按照常理来说，0.1秒几乎不可能得到服务器响应，因此输出了TIME OUT的提示。
通过设置timeout参数超时处理，有时还是很有用的。
#### 其他参数
除了data参数和timeout参数，urlopen方法还有context参数，该参数必须是ssl.SSLContext类型，用来指定SSL的设置。
此外，cafile和capath这两个参数分别用来指定CA证书和其路径，这两个在请求HTTPS链接是会有用。
cadefaule参数现在已经弃用，其默认值为False。
至此，我们讲解了urlopen方法的用法，通过这个最基本的方法，就可以完成简单的请求和页面抓取。

#### Request
利用urlopen方法可以发起最基本的请求，但它那几个简单的参数并不足以构建一个完整的请求。如果需要往请求中加入Headers等信息，就得利用更强大的Requst类来构建请求了。

首先，我们用实例感受一下Request类的用法：
```
import urllib.request
request = urllib.request.Request('https://httpbin.org')
response = urllib.request.urlopen(request)
print(response.status,response.read().decode('utf-8'),sep="\n")
```
可以发现，我么依然是用urlopen方法来发送请求，只不过这次该方法的参数不在是URL，而是一个Request类型的对象。通过构造这个数据结构某一方面可以将请求独立成一个对象，另一方面可更加丰富和灵活地配置参数。

下面我们看一下可以通过怎样的参数来构造Request类，构造方法如下：
```
class urllib.request.Request(url,data=None,headers={},origin_req_host=None,unverifiable=False,method=None)
```
第一个参数url用来请求URL，这是必传参数，其他的都是可选参数。

第二个参数data如果要传数据，必须传bytes类型的，如果数据是字典，可以先用urllib.parse模块里的urlencode方法进行编码。

第三个参数headers是一个字典，这就是请求头，我们在构造请求时，既可以通过header是参数直接构造此项，也可以通过调用请求实例的add_header方法添加。
添加请求头最常见的方法就是通过修改User-Agent来伪装浏览器。默认的User-Agent是Python-urllib,我们可以通过，修改这个值来伪装浏览器。

第四个参数origin_req_host指的是请求方的host名称或者IP地址。

第五个参数unverifiable表示请求是否是无法验证的，默认取值是False，意思是用户没有足够的权限来接受这个请求的结果。例如，请求一个HTML文档中的照片，但没有自动抓取图像的权限，这是unverifiable的值就是True.

第六个参数method是一个字符串，用来指示请求使用的方法，例如GRT,POST,PUT等。

下面我们传入多个参数尝试构建Requset类：
```
from urllib import request,parse

url = 'https://www.httpbin.org/post'
headers = {
    'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.80 Safari/537.36 Edg/98.0.1108.43',
    'Host':'www.httpbin.org'
}
dict = {'name':'germey'}
data = bytes(parse.urlencode(dict),encoding = 'utf-8')
req = request.Request(url=url,data=data,headers=headers,origin_req_host='www.httpbin.org',method='POST')
response = request.urlopen(req)
print(response.read().decode('utf-8'))
```

这里我们通过4个参数构造了一个Reuqust类，其中的url即请求URL，headers中指定了User-Agent和Host，data用urlencode方法和bytes方法吧字典数据转成字节流格式。另外，指定了请求方式为POST。
运行结果如下：

	{
	  "args": {}, 
	  "data": "", 
	  "files": {}, 
	  "form": {
		"name": "germey"
	  }, 
	  "headers": {
		"Accept-Encoding": "identity", 
		"Content-Length": "11", 
		"Content-Type": "application/x-www-form-urlencoded", 
		"Host": "www.httpbin.org", 
		"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.80 Safari/537.36 Edg/98.0.1108.43", 
		"X-Amzn-Trace-Id": "Root=1-61ff57db-247af137696671ad3f2f0316"
	  }, 
	  "json": null, 
	  "origin": "36.28.50.254", 
	  "url": "https://www.httpbin.org/post"
	}

通过结果可以发现，我们成功设置了data、headers和method。
通过add_header方法天界headers的方式如下：
```
req = request.Request(url=url,data=data,method='POST')
req.add_header('User-Agent','Mozilla/5.0 (Windows NT 10.0; Win64; x64)')
```
有了Request类，我们就可以更方便地构建请求，并实现请求的发送啦。
#### 高级用法
我们已经构建请求了，那么对于一些更高级的操作（例如Cookie、代理设置等），又该怎么实现呢？
此时我们需要更强大的工具，于是Handler登场了。简而言之，Handler可以理解为各种处理器，有专门处理登录验证的，处理Cookie的、处理代理设置的。利用这Handler,我们几乎可以实现HTTP请求中所有功能。
首先介绍一下urllib.request模块里的BaseHandler类，这是其他所有Handler类的父类。它提供了最基本的方法，例如default_open、protocol_request等。

会有各种Handler子类继承BaseHandler类，接下来举几个子类的例子如下。

> HTTPDefaultErrorHandler用于处理HTTP响应错误，所有错误都会抛出HTTPError类型的错误。
> HTTPRedirectHandler用于重定向。
> HTTPCookieProcessor用于处理Cookie。
> ProxyHandler用于设置代理，代理默认为空。
> HTTPPasswordMgr用于管理密码，它维护着用户名密码的对照表。
> HTTPBasicAuthHandler用于管理认证，如果一个链接在打开时需要认证，那么可以用这个类来解决认证问题。

关于这些类如何使用，现在先不着急了解，后面会用实例演示。

另一个比较重要的类是OpenerDirector，我们可以称之为Opener。我们之前用过的urlopen方法，实际上就是urllib库为我们提供的一个Opener。

那么，为什么要引入Opener呢？因为需要实现更高级的功能。之前使用的Request类和urlopen类相当于类库已经封装好的极其常用的请求方法，利用这两个可以完成基本的请求，但是现在我们需要实现更高级的功能，就需要深入一层进行配置，使用更底层的实力来完成操作，所以这里就用到了Opener。

Opener类可以提供open方法，该方法返回的响应类型和urlopen方法如出一辙。那么，Opener类和Handler类有什么关系呢？简而言之就是，利用Handler类来构建Opener类

下面用几个实例来看看Handler类和Opener类的用法。
### 解析链接
#### urljoin

#### urlencode
```
from urllib.parse import urlencode

params = {
    'name':'germey',
    'age':25
}
base_url = "https://www.baidu.com?"
url = base_url+urlencode(params)
print(url)
```

	https://www.baidu.com?name=germey&age=25

#### parse_qs
```
from urllib.parse import parse_qs

query = 'name=germy&age=25'
print(parse_qs(query))
```

	{'name': ['germy'], 'age': ['25']}

#### parse_qsl
```
from urllib.parse import parse_qsl

query = 'name=germy&age=25'
print(parse_qsl(query))
```

	[('name', 'germy'), ('age', '25')]
	
#### quote
```
from urllib.parse import quote

keyword = "壁纸"
url = "https://www.baidu.com/s?wd="+quote(keyword)
print(url)
```

	https://www.baidu.com/s?wd=%E5%A3%81%E7%BA%B8
	
#### unquote
```
from urllib.parse import quote,unquote

keyword = "壁纸"
p = quote(keyword)
url = "https://www.baidu.com/s?wd="+p
print(url)
print(unquote(url))
```

	https://www.baidu.com/s?wd=%E5%A3%81%E7%BA%B8
	https://www.baidu.com/s?wd=壁纸
	
### 分析robots协议
```
from urllib.robotparser import RobotFileParser

rp = RobotFileParser()
rp.set_url('https://www.baidu.com/robots.txt')
rp.read()
print(rp.can_fetch('Baiduspider','https://www.baidu.com/'))
print(rp.can_fetch('Baiduspider','https://www.baidu.com/homepage/'))
print(rp.can_fetch('Googlebot','https://www.baidu.com/homepage/'))
```

	True
	True
	False

	
```
from urllib import request
res = request.urlopen('https://www.baidu.com/robots.txt')
print(res.read().decode('utf-8'))
```

	User-agent: Baiduspider
	Disallow: /baidu
	Disallow: /s?
	Disallow: /ulink?
	Disallow: /link?
	Disallow: /home/news/data/
	Disallow: /bh

	User-agent: Googlebot
	Disallow: /baidu
	Disallow: /s?
	Disallow: /shifen/
	Disallow: /homepage/
	Disallow: /cpro
	Disallow: /ulink?
	Disallow: /link?
	Disallow: /home/news/data/
	Disallow: /bh

## requests的使用
```
from urllib.robotparser import RobotFileParser
from urllib import request

response = request.urlopen('https://www.baidu.com/robots.txt')
rp = RobotFileParser()
rp.parse(response.read().decode('utf-8').split('\n'))
print(rp.can_fetch('Baiduspider','https://www.baidu.com/'))
print(rp.can_fetch('Baiduspider','https://www.baidu.com/homepage/'))
print(rp.can_fetch('Googlebot','https://www.baidu.com/homepage/'))
```

	<class 'requests.models.Response'>
	200
	<class 'str'>
	<!DOCTYPE html>
	<!--STATUS OK--><html> <head><meta http-equiv=content-type content=text/html;charse
	<RequestsCookieJar[<Cookie BDORZ=27315 for .baidu.com/>]>


```
import requests

r = requests.get('https://www.httpbin.org/get')
r = requests.post('https://www.httpbin.org/post')
r = requests.put('https://www.httpbin.org/put')
r = requests.delete('https://www.httpbin.org/delete')
r = requests.patch('https://www.httpbin.org/patch')
```

### GET请求
```
import requests
r = requests.get('https://www.httpbin.org/get')
print(r.text)

```

	{
	  "args": {}, 
	  "headers": {
		"Accept": "*/*", 
		"Accept-Encoding": "gzip, deflate", 
		"Host": "www.httpbin.org", 
		"User-Agent": "python-requests/2.27.1", 
		"X-Amzn-Trace-Id": "Root=1-61ffd810-2a681e25600cd3df072e03a4"
	  }, 
	  "origin": "36.28.50.254", 
	  "url": "https://www.httpbin.org/get"
	}
	
```
import requests

data = {
    'name':'germey',
    'age':25
}
r = requests.get('https://www.httpbin.org/get',params=data)
print(r.text)
```

	{
	  "args": {
		"age": "25", 
		"name": "germey"
	  }, 
	  "headers": {
		"Accept": "*/*", 
		"Accept-Encoding": "gzip, deflate", 
		"Host": "www.httpbin.org", 
		"User-Agent": "python-requests/2.27.1", 
		"X-Amzn-Trace-Id": "Root=1-61ffe7a5-6d4bbfc272b8d1de5896aa47"
	  }, 
	  "origin": "36.28.50.254", 
	  "url": "https://www.httpbin.org/get?name=germey&age=25"
	}

```
import requests

r = requests.get('https://www.httpbin.org/get')
print(type(r.text))
print(r.json())
print(type(r.json()))
```

	<class 'str'>
	{'args': {}, 'headers': {'Accept': '*/*', 'Accept-Encoding': 'gzip, deflate', 'Host': 'www.httpbin.org', 'User-Agent': 'python-requests/2.27.1', 'X-Amzn-Trace-Id': 'Root=1-61ffe8c0-29e010d739afac8a0e8dab53'}, 'origin': '36.28.50.254', 'url': 'https://www.httpbin.org/get'}
	<class 'dict'>
	
### 抓取网页
```
import requests
import re

r = requests.get('https://ssr1.scrape.center/')
pattern = re.compile('<h2.*?>(.*?)</h2>',re.S)
title = re.findall(pattern,r.text)
print(title)
```

	['霸王别姬 - Farewell My Concubine', '这个杀手不太冷 - Léon', '肖申克的救赎 - The Shawshank Redemption', '泰坦尼克号 - Titanic', '罗马假日 - Roman Holiday', '唐伯虎点秋香 - Flirting Scholar', '乱世佳人 - Gone with the Wind', '喜剧之王 - The King of Comedy', '楚门的世界 - The Truman Show', '狮子王 - The Lion King']

### 抓取二进制数据
```
import requests

r = requests.get('https://scrape.center/favicon.ico')
print(r.text)
print(r.content)
```
> r.text
	
	         �     (       @                         W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��X@��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��X@��W?��W?��W?��W?��W?��W?��W?��X@��R:��J1��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��J1��R:��X@��W?��W?��W?��W?��W?��X@��R:��r]������������������������������������������������������������������������������������������r]��R:��X@��W?��W?��W?��W?��ZB��K2��������������������������������������������������������������������������������������������������K2��ZB��W?��W?��W?��W?��ZB��K2��������������������������������������������������������������������������������������������������K2��ZB��W?��W?��W?��W?��ZB��K2��������������������������������������������������������������������������������������������������K2��ZB��W?��W?��W?��W?��ZB��K2��������������������������������������������������������������������������������������������������K2��ZB��W?��W?��W?��W?��ZB��K2��������������������������t_��{g��zf��zf��zf��zf��zf��zf��zf��zf��{g��t_��������������������������K2��ZB��W?��W?��W?��W?��ZB��K2����������������������t`��G-��Q8��O6��O6��O6��O6��O6��O6��O6��O6��Q8��G.��t_����������������������K2��ZB��W?��W?��W?��W?��ZB��K2����������������������{h��Q8��ZC��YA��YB��[D��[D��[D��[D��[D��[D��]F��T<��}j����������������������K2��ZB��W?��W?��W?��W?��ZB��K2����������������������zf��O6��YA��X@��T<��K2��K2��K2��K2��K2��K2��M4��C)��q\����������������������K2��ZB��W?��W?��W?��W?��ZB��K2����������������������zf��O6��YB��T;��fO����������������������������������������������������������K2��ZB��W?��W?��W?��W?��ZB��K2����������������������zf��O6��ZC��O6��|j����������������������������������������������������������K2��ZB��W?��W?��W?��W?��ZB��K2����������������������zf��O6��ZC��O6��{j����������������������������������������������������������K2��ZB��W?��W?��W?��W?��ZB��K2����������������������zf��O6��ZC��O6��|j����������������������������������������������������������K2��ZB��W?��W?��W?��W?��ZB��K2����������������������zf��O6��ZC��O6��|j����������������������������������������������������������K2��ZB��W?��W?��W?��W?��ZB��K2����������������������zf��O6��YA��U=��_I��}l��|j��|j��|j��|j��|j��|j��|j��|j��|j��|j��{j��m��fP��T<��X@��W?��W?��W?��W?��ZB��K2����������������������zf��O6��YA��W?��U=��O5��O6��O6��O6��O6��O6��O6��O6��O6��O6��O6��O6��N5��T;��X@��W?��W?��W?��W?��W?��ZB��K2����������������������{h��Q8��ZC��YA��YA��[C��ZC��ZC��ZC��ZC��ZC��ZC��ZC��ZC��ZC��ZC��ZC��[C��XA��W?��W?��W?��W?��W?��W?��ZB��K2����������������������t`��H-��Q8��O6��O6��O6��O6��O6��O6��O6��O6��O6��O6��O6��O6��O6��P7��O6��T<��X@��W?��W?��W?��W?��W?��ZB��K2��������������������������s`��{h��zf��zf��zf��zf��zf��zf��zf��zf��zf��zf��zf��zf��zf��yf��|i��dO��U<��X@��W?��W?��W?��W?��ZB��K2��������������������������������������������������������������������������������������������������K2��ZB��W?��W?��W?��W?��ZB��K2��������������������������������������������������������������������������������������������������K2��ZB��W?��W?��W?��W?��ZB��K2��������������������������������������������������������������������������������������������������K2��ZB��W?��W?��W?��W?��ZB��K2��������������������������������������������������������������������������������������������������K2��ZB��W?��W?��W?��W?��X@��R:��r^������������������������������������������������������������������������������������������r]��R:��X@��W?��W?��W?��W?��W?��X@��R:��J1��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��K2��J1��R:��X@��W?��W?��W?��W?��W?��W?��W?��X@��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��ZB��X@��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W?��W
	
> r.content	
	
	b'\x00\x00\x01\x00\x01\x00  \x00\x00\x01\x00 \x00\xa8\x10\x00\x00\x16\x00\x00\x00(\x00\x00\x00 \x00\x00\x00@\x00\x00\x00\x01\x00 \x00\x00\x00\x00\x00\x00\x10\x00\x00\x12\x0b\x00\x00\x12\x0b\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00W?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffX@\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffZB\xeb\xffX@\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffX@\xeb\xffR:\xea\xffJ1\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffK2\xea\xffJ1\xea\xffR:\xea\xffX@\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffX@\xeb\xffR:\xea\xffr]\xee\xff\x9f\x91\xf4\xff\x9a\x8a\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x9b\x8b\xf3\xff\x99\x8a\xf3\xff\x9f\x91\xf4\xffr]\xee\xffR:\xea\xffX@\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffZB\xeb\xffK2\xea\xff\x9a\x8c\xf3\xff\xff\xff\xff\xff\xfc\xfc\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xff\xfc\xfc\xff\xff\xff\xff\xff\xff\x9a\x8c\xf3\xffK2\xea\xffZB\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffW?\xeb\xffZB\xeb\xffK2\xea\xff\x99\x8a\xf3\xff\xff\xff\xff\xff\xfa\xf9\xfe\xff\xfc\xfc\xff\xff\xfc\xfc\xff\xff\xfc\xfc\xff\xff\xfc\xfc\xff\xff\xfc\xfc\xff\xff\xfc\xfc\xff\xff\xfc\xfc\xff\xff\xfc\xfc\xff\xff\xfc\xfc\xff\xff\xfc\xfc\xff\xff\xfc\xfc\xff\xff\xfc\xfc\xff\xff\xfc\xfc
	
```
import requests

r = requests.get('https://scrape.center/favicon.ico')
with open('favicon.ico','wb') as f:
    f.write(r.content)
```

### 添加请求头
```
import requests

header = {
    'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.80 Safari/537.36 Edg/98.0.1108.43'
}
r = requests.get('https://httpbin.org/get',headers=header)
print(r.text)
```

	{
	  "args": {}, 
	  "headers": {
		"Accept": "*/*", 
		"Accept-Encoding": "gzip, deflate", 
		"Host": "httpbin.org", 
		"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.80 Safari/537.36 Edg/98.0.1108.43", 
		"X-Amzn-Trace-Id": "Root=1-6200bc86-1d8d3be041e3ac734582cae3"
	  }, 
	  "origin": "36.28.30.92", 
	  "url": "https://httpbin.org/get"
	}

```
import requests

data = {'name':'germey','age':'25'}
r = requests.post('https://www.httpbin.org/post',data=data)
print(r.text)
```

	{
	  "args": {}, 
	  "data": "", 
	  "files": {}, 
	  "form": {
		"age": "25", 
		"name": "germey"
	  }, 
	  "headers": {
		"Accept": "*/*", 
		"Accept-Encoding": "gzip, deflate", 
		"Content-Length": "18", 
		"Content-Type": "application/x-www-form-urlencoded", 
		"Host": "www.httpbin.org", 
		"User-Agent": "python-requests/2.27.1", 
		"X-Amzn-Trace-Id": "Root=1-6200bd3d-623720004f80b8cb072e551d"
	  }, 
	  "json": null, 
	  "origin": "36.28.30.92", 
	  "url": "https://www.httpbin.org/post"
	}

### 响应
```
import requests

r = requests.post('https://www.httpbin.org/post')
print(type(r.status_code),r.status_code)
print(type(r.headers),r.headers)
print(type(r.cookies),r.cookies)
print(type(r.url),r.url)
print(type(r.history),r.history)
```

	<class 'int'> 200
	<class 'requests.structures.CaseInsensitiveDict'> {'Date': 'Mon, 07 Feb 2022 06:46:09 GMT', 'Content-Type': 'application/json', 'Content-Length': '405', 'Connection': 'keep-alive', 'Server': 'gunicorn/19.9.0', 'Access-Control-Allow-Origin': '*', 'Access-Control-Allow-Credentials': 'true'}
	<class 'requests.cookies.RequestsCookieJar'> <RequestsCookieJar[]>
	<class 'str'> https://www.httpbin.org/post
	<class 'list'> []

```
import requests

r = requests.get('https://ssr1.scrape.center/')
exit() if not r.status_code == requests.codes.ok else print('Request successfully')
```

### cookie设置
```
import requests
Cookies='_device_id=e9e3309ca2f53cde288fcd882275df1f; _octo=GH1.1.407237607.1641277350; user_session=l-6hmCWnicBdN8ViZz-pra5acK3owr5AVz-w_A0Qore_yaaH; __Host-user_session_same_site=l-6hmCWnicBdN8ViZz-pra5acK3owr5AVz-w_A0Qore_yaaH; logged_in=yes; dotcom_user=iiheng; has_recent_activity=1; color_mode={"color_mode":"auto","light_theme":{"name":"light","color_mode":"light"},"dark_theme":{"name":"dark","color_mode":"dark"}}; _gh_sess=b+r4XZ3MQ9Z80zBfBGpbshAOVPvpjyrRf9MGcUdtSMBINWBx6SQt0Q41J7jYuof+NrPEthD++o+JDBWNdOv3UOetNMIE1+/6+/Vo8pry5uu8qZRVQUeExHnZlylNZZSIVdN33AUmoydiT8QNnKuf7yflZR8fNpplEAVNGf82eXt0wyrWKrvW7AsJvJrFLzPc--oV0oVXYX21+2D+u3--HQ0wOunUOjJxlX4RaS5B6A=='
jar = requests.cookies.RequestsCookieJar()
for cookie in Cookies.split(';'):
    key,value = cookie.split('=',1)
    print(key,'=',value)
    jar.set(key,value)

header = {
    'User-agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.80 Safari/537.36 Edg/98.0.1108.43',
}
r = requests.get('https://github.com',headers = header,cookies = jar)
print(r.text)
```

### session维持
```
import requests

requests.get('https://www.httpbin.org/cookies/set/number/123456789')
r = requests.get('https://www.httpbin.org/cookies')
print(r.text)
```

	{
	  "cookies": {}
	}
	
```
import requests

s = requests.session()
s.get('https://www.httpbin.org/cookies/set/number/123456789')
r = s.get('https://www.httpbin.org/cookies')
print(r.text)
```

	{
	  "cookies": {
		"number": "123456789"
	  }
	}
	
### SSL证书验证
```
import requests

response = requests.get('https://ssr2.scrape.center/')
print(response.status_code)
```

	........requests.exceptions.SSLError: HTTPSConnectionPool(host='ssr2.scrape.center', port=443): Max retries exceeded with url: / (Caused by SSLError(SSLCertVerificationError(1, '[SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: self signed certificate (_ssl.c:1129)')))

```
import requests

response = requests.get('https://ssr2.scrape.center/',verify=False)
print(response.status_code)
```

	InsecureRequestWarning: Unverified HTTPS request is being made to host 'ssr2.scrape.center'. Adding certificate verification is strongly advised. See: https://urllib3.readthedocs.io/en/1.26.x/advanced-usage.html#ssl-warnings
	  warnings.warn(
	200

```
import requests
from requests.packages import urllib3

urllib3.disable_warnings()
response = requests.get('https://ssr2.scrape.center/',verify=False)
print(response.status_code)
```

```
import requests
import logging

logging.captureWarnings(True)
response = requests.get('https://ssr2.scrape.center/',verify=False)
print(response.status_code)
```
### 超时设置
```
import requests

r = requests.get('https://httpbin.org/get',timeout=0.1)
print(r.status_code)
```

	r = requests.get('https://httpbin.org/get',timeout=(2,5))
		
	r = requests.get('https://httpbin.org/get',timeout=None)
	
	r = requests.get('https://httpbin.org/get')
	
### 身份认证
```
import requests
from requests.auth import HTTPBasicAuth

r = requests.get('http://ssr3.scrape.center/',auth=HTTPBasicAuth('admin','admin'))
print(r.status_code)
```

```
import requests
from requests_oauthlib import OAuth1

url = 'https://api.twitter.com/1.1/account/verify_credentials.json'
auth = OAuth1('YOUR_APP_KEY','YOUR_APP_SECRET','USER_OAUTH_TOKEN','USER_OAUTH_TOKEN_SECRET')
requests.get(url,auth = auth)
```

### 代理设置
```
import requests

proxies = {
    'http':'http://10.10.10.10:1080',
    'https':'http://10.10.10.10:1080'
}
requests.get('https://httpbin.org/get',proxies=proxies)
```

### Prepared Request
```
from requests import Request,Session

url = 'https://www.httpbin.org/post'
data = {
    'name':'germey'
}
headers = {
    'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.80 Safari/537.36 Edg/98.0.1108.43'
}
s = Session()
req = Request('POST',url,data = data,headers = headers)
prepped = s.prepare_request(req)
r = s.send(prepped)
print(r.text)
```

	{
	  "args": {}, 
	  "data": "", 
	  "files": {}, 
	  "form": {
		"name": "germey"
	  }, 
	  "headers": {
		"Accept": "*/*", 
		"Accept-Encoding": "gzip, deflate", 
		"Content-Length": "11", 
		"Content-Type": "application/x-www-form-urlencoded", 
		"Host": "www.httpbin.org", 
		"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.80 Safari/537.36 Edg/98.0.1108.43", 
		"X-Amzn-Trace-Id": "Root=1-6200f12c-61139caf50e3f6d713578e40"
	  }, 
	  "json": null, 
	  "origin": "36.28.30.92", 
	  "url": "https://www.httpbin.org/post"
	}
	

	
## 正则表达式
### 实例引入

|模式|描述|
|------|----|
|`\w`|匹配字母、数字及下划线|
|`\W`|匹配不是字母、数字及下划线的字符|
|`\s`|匹配任意空白字符，等价于`[\t\n\r\f]`|
|`\S`|匹配任意非空字符|
|`\d`|匹配任意数字，等价于`[0-9]`|
|`\D`|匹配任意非数字的字符|
|`\A`|匹配字符串开头|
|`\Z`|匹配字符串结尾。如果存在换行，只匹配到换行前的结束字符串|
|`\z`|匹配字符串结尾。如果存在换行，同时还会匹配换行符|
|`\G`|匹配最后匹配完成的位置|
|`\n`|匹配一个换行符|
|`\t`|匹配一个制表符|
|`^`|匹配一行字符串开头|
|`$`|匹配一行字符串的结尾|
|`.`|匹配任意字符，除了换行符，当re.DOTALL标记被指定时，可以匹配包括换行符的任意字符|
|`[...]`|用来表示一组字符，单独列出，例如`[amk]`用来匹配a、m或k|
|`[^...]`|匹配不在`[]`中的字符，例如`[^abc]`匹配除了a,b,c之外的字符|
|`*`|匹配0个或多个表达式|
|`+`|匹配1个或多个表达式|
|`?`|匹配0个或1个前面的正则表达式定义的片段，非贪婪方式|
|`{}`|精准匹配n个前面的表达式|
|`{n,m}`|匹配n到m次有前面正则表达式定义的片段，贪婪方式|
|`a\|b`|匹配括号内的表达式，也表示一个组|

### match
```
import re
content = 'Hello 123 4567 World_this is a Regex Demo'
print(len(content))
result = re.match('Hello\s\d{3}\s\d{4}\s\w{10}',content)
print(result)
print(result.group())
print(result.span())
```

	41
	<re.Match object; span=(0, 25), match='Hello 123 4567 World_this'>
	Hello 123 4567 World_this
	(0, 25)

#### 匹配目标
```
import re
content = 'Hello 1234567 World_this is a Regex Demo'
result = re.match('^Hello\s(\d+)\s\w{5}',content)
print(result)
print(result.group())
print(result.group(1))
print(result.span())
```

	<re.Match object; span=(0, 19), match='Hello 1234567 World'>
	Hello 1234567 World
	1234567
	(0, 19)
	
#### 通用匹配
```
import re
content = 'Hello 123 4567 World_this is a Regex Demo'
result = re.match('^Hello.*Demo$',content)
print(result)
print(result.group())
print(result.span())
```

	<re.Match object; span=(0, 41), match='Hello 123 4567 World_this is a Regex Demo'>
	Hello 123 4567 World_this is a Regex Demo
	(0, 41)
	
#### 贪婪与非贪婪
```
import re
content = 'Hello 1234567 World_this is a Regex Demo'
result = re.match('^He.*(\d+).*Demo$',content)
print(result)
print(result.group(1))
```

	<re.Match object; span=(0, 40), match='Hello 1234567 World_this is a Regex Demo'>
	7
	
```
import re
content = 'Hello 1234567 World_this is a Regex Demo'
result = re.match('^He.*?(\d+).*Demo$',content)
print(result)
print(result.group(1))
```

	<re.Match object; span=(0, 40), match='Hello 1234567 World_this is a Regex Demo'>
	1234567

```
import re

content = 'http://weibo.com/comment/kEraCN'
result1 = re.match('http.*?comment/(.*?)',content)
result2 = re.match('http.*?comment/(.*)',content)
print('result1:',result1.group(1))
print('result2:',result2.group(1))
```

	result1: 
	result2: kEraCN

#### 修饰符
```
import re

content = """Hello 1234567 World_This
is a Regex Demo
"""
result = re.match('^Hello.*?(\d+).*?Demo$',content)
print(result.group(1))
```

	Traceback (most recent call last):
	  File "E:\pythonproject\scrapel\urlencodel.py", line 7, in <module>
		print(result.group(1))
	AttributeError: 'NoneType' object has no attribute 'group'

`result = re.match('^Hello.*?(\d+).*?Demo$',content,re.S)`

	1234567
	
|修饰符|描述|	
|----|-----|
|re.I|使匹配对大小写不敏感|
|re.L|实现本地化识别（locale-aware）匹配|
|re.M|多行匹配，影响`^`和`$`|
|re.S|是匹配内容包括换行符在内的所有字符|
|re.U|根据Unicode字符集解析字符。这个标志会影响`\w`、`\W`、`\b`和`\B`|
|re.X|该标志能够给予你更灵活的格式，以便将正则表达式书写的更易于理解|

#### 转义匹配
```
import re

content = '(百度)www.baidu.com'
result = re.match('\(百度\)www\.baidu\.com',content)
print(result)
```
	
	<re.Match object; span=(0, 17), match='(百度)www.baidu.com'>
	
### search
```
import re

content = 'Extra stings Hello 1234567 World_This is a Regex Demo Extra   stings'
result = re.match('Hello.*?(\d+).*?Demo',content)
print(result)
```

	None
		
	<re.Match object; span=(13, 53), match='Hello 1234567 World_This is a Regex Demo'>
	
```
html = """
<div id="songs-group">
    <h2 class="title">经典老歌</h2>
    <p class="introdution">
        经典老歌列表
    </p>
    <ul id="list" class="list-group">
        <li data-view="2">一路上有你</li>
        <li data-view="7">
            <a href='/2.mp3' signer="任贤齐">沧海一声笑</a>
        </li>
        <li data-view="4" class="active">
            <a href="/3.mp3" signer="齐秦">往事随风</a>
        </li>
        <li data-view="6"><a href="/4.mp3" signer="beyong">光辉岁月</a></li>
        <li data-view="5"><a href="/5.mp3" signer="陈慧琳">记事本</a></li>
        <li data-view="5">
            <a href="/6.mp3" signer="邓丽君">但愿人长久</a>
        </li>
    </ul>
</div>
"""
```
```
result = re.search('<li.*?active.*?signer="(.*?)">(.*?)</a>',html,re.S)
print(result.group(1),result.group(2))
```

	齐秦 往事随风

```
result = re.search('<li.*?signer="(.*?)">(.*?)</a>',html,re.S)
if result:
    print(result.group(1),result.group(2))
```

	任贤齐 沧海一声笑
	
```
result = re.search('<li.*?signer="(.*?)">(.*?)</a>',html)
if result:
    print(result.group(1),result.group(2))
```

	beyong 光辉岁月

### findall
```
results = re.findall('<li.*?href="(.*?)".*?signer="(.*?)">(.*?)</a>',html,re.S)

print(results)
print(type(results))
for result in results:
    print(result)
    print(result[0],result[1],result[2])
```

	[('/3.mp3', '齐秦', '往事随风'), ('/4.mp3', 'beyong', '光辉岁月'), ('/5.mp3', '陈慧琳', '记事本'), ('/6.mp3', '邓丽君', '但愿人长久')]
	<class 'list'>
	('/3.mp3', '齐秦', '往事随风')
	/3.mp3 齐秦 往事随风
	('/4.mp3', 'beyong', '光辉岁月')
	/4.mp3 beyong 光辉岁月
	('/5.mp3', '陈慧琳', '记事本')
	/5.mp3 陈慧琳 记事本
	('/6.mp3', '邓丽君', '但愿人长久')
	/6.mp3 邓丽君 但愿人长久

### sub
```
import re

content = '54asdw846Wszw5142fw'
content = re.sub('\d+','',content)
print(content)
```

	asdwWszwfw

```
results = re.findall('<li.*?>\s*?(<a.*?>)?(\w+)(</a>)?\s*?</li>',html,re.S)
for result in results:
    print(result[1])
```

	一路上有你
	沧海一声笑
	往事随风
	光辉岁月
	记事本
	但愿人长久

```
html = re.sub('<a.*?>|</a>','',html)
print(html)
results = re.findall('<li.*?>.*?(\S*?)\s*?</li>',html,re.S)
for result in results:
    print(result)
```

	<div id="songs-group">
		<h2 class="title">经典老歌</h2>
		<p class="introdution">
			经典老歌列表
		</p>
		<ul id="list" class="list-group">
			<li data-view="2">一路上有你</li>
			<li data-view="7">
				沧海一声笑
			</li>
			<li data-view="4" class="active">
				往事随风
			</li>
			<li data-view="6">光辉岁月</li>
			<li data-view="5">记事本</li>
			<li data-view="5">
				但愿人长久
			</li>
		</ul>
	</div>

	一路上有你
	沧海一声笑
	往事随风
	光辉岁月
	记事本
	但愿人长久

### complie
```
import re

content1 = '2019-12-15 12:00'
content2 = '2019-12-19 12:55'
content3 = '2019-12-24 06:30'
pattern = re.compile('\d{2}:\d{2}')
result1 = re.sub(pattern,'',content1)
result2 = re.sub(pattern,'',content2)
result3 = re.sub(pattern,'',content3)
print(result1,result2,result3)
```

	2019-12-15  2019-12-19  2019-12-24 

## httpx的使用
### 示例
```
import requests

url = "http://spa16.scrape.center/"
result = requests.get(url)
print(result.text)
```

	Traceback (most recent call last):
	requests.exceptions.ConnectionError: ('Connection aborted.', ConnectionResetError(10054, '远程主机强迫关闭了一个现有的连接。', None, 10054, None))
	
### 安装

	pip3 install httpx

	pip3 install 'httpx[http2]'
	
### 基本使用
```
import httpx

response = httpx.get('https://www.httpbin.org/get')
print(response.status_code)
print(response.headers)
print(response.text)
```

	200
	Headers({'date': 'Wed, 09 Feb 2022 11:48:54 GMT', 'content-type': 'application/json', 'content-length': '310', 'connection': 'keep-alive', 'server': 'gunicorn/19.9.0', 'access-control-allow-origin': '*', 'access-control-allow-credentials': 'true'})
	{
	  "args": {}, 
	  "headers": {
		"Accept": "*/*", 
		"Accept-Encoding": "gzip, deflate", 
		"Host": "www.httpbin.org", 
		"User-Agent": "python-httpx/0.22.0", 
		"X-Amzn-Trace-Id": "Root=1-6203aa26-3ebede6723446cf1278d5cd8"
	  }, 
	  "origin": "36.28.21.78", 
	  "url": "https://www.httpbin.org/get"
	}

```
import httpx

headers = {
    'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.80 Safari/537.36 Edg/98.0.1108.43'
}
response = httpx.get('https://www.httpbin.org/get',headers=headers)
print(response.text)
```

	{
	  "args": {}, 
	  "headers": {
		"Accept": "*/*", 
		"Accept-Encoding": "gzip, deflate", 
		"Host": "www.httpbin.org", 
		"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.80 Safari/537.36 Edg/98.0.1108.43", 
		"X-Amzn-Trace-Id": "Root=1-6203ae97-209a7da31e6b8eb137cf477e"
	  }, 
	  "origin": "36.28.21.78", 
	  "url": "https://www.httpbin.org/get"
	}

```
import httpx

response = httpx.get('https://spa16.scrape.center')
print(response.text)
```

		raise mapped_exc(message) from exc
	httpx.ReadError: [WinError 10054] 远程主机强迫关闭了一个现有的连接。
	
```
import httpx

client = httpx.Client(http2=True)
response = client.get('https://spa16.scrape.center')
print(response.text)
```

	<!DOCTYPE html><html lang=en><head><meta charset=utf-8><meta http-equiv=X-UA-Compatible content="IE=edge"><meta name=viewport content="width=device-width,initial-scale=1"><meta name=referrer content=no-referrer><link rel=icon href=/favicon.ico><title>Scrape | Book</title><link href=/css/chunk-50522e84.e4e1dae6.css rel=prefetch><link href=/css/chunk-f52d396c.4f574d24.css rel=prefetch><link href=/js/chunk-50522e84.6b3e24aa.js rel=prefetch><link href=/js/chunk-f52d396c.f8f41620.js rel=prefetch><link href=/css/app.ea9d802a.css rel=preload as=style><link href=/js/app.b93891e2.js rel=preload as=script><link href=/js/chunk-vendors.a02ff921.js rel=preload as=script><link href=/css/app.ea9d802a.css rel=stylesheet></head><body><noscript><strong>We're sorry but portal doesn't work properly without JavaScript enabled. Please enable it to continue.</strong></noscript><div id=app></div><script src=/js/chunk-vendors.a02ff921.js></script><script src=/js/app.b93891e2.js></script></body></html>

```
import httpx

r = httpx.get('https://www.httpbin.org/get',params={'name':'germey'})
r = httpx.post('https://www.httpbin.org/post',data={'name':'germey'})
r = httpx.put('https://www.httpbin.org/put')
r = httpx.delete('https://www.httpbin.org/delete')
r = httpx.patch('https://www.httpbin.org/patch')
```

### client对象
```
import httpx

with httpx.Client() as client:
    response = client.get('https://www.httpbin.org/get')
    print(response)
```

	<Response [200 OK]>

```
import httpx

client = httpx.Client()
try:
    response = client.get('https://www.httpbin.org/get')
finally:
    client.close()
```
```
import httpx

headers = {
    'User-Agent':'my-app/0.0.1'
}
with httpx.Client(headers=headers) as client:
    response = client.get('https://www.httpbin.org/get')
    print(response.text)
```

	"User-Agent": "my-app/0.0.1", 

### 支持HTTP/2.0
```
import httpx

client = httpx.Client(http2=True)
response = client.get('https://www.httpbin.org/get')
print(response.text)
print(response.http_version)
```
### 支持异步请求
```
import httpx
import asyncio

async def fetch(url):
    async with httpx.AsyncClient(http2=True) as client:
        response = await client.get(url)
        print(response.text)

if __name__ == '__main__':
    asyncio.get_event_loop().run_until_complete(fetch('https://www.httpbin.org/get'))
```

## 基础爬虫案例实战
### 准备工作
我们需要先做好如下准备工作：
- 安装好python3，最低为3.6版本，并能成功运行Python3程序
- 了解Python多线程的基本原理
- 了解Python HTTO请求库requests的基本用法
- 了解正则表达式的用法和Python中正则表达式库re的基本用法
以上内容在前面的章节中多有讲解，如果尚未准备好，建议先熟悉一下这些内容
### 爬取目标
### 爬取列表页
```
import requests
import logging
import re
from urllib.parse import urljoin

logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(levelname)s:%(message)s')
BASE_URL = 'https://ssr1.scrape.center'
TOTAL_PAGE = 10
```

```
def scrape_page(url):
    logging.info('scraping %s...',url)
    try:
        response = requests.get(url)
        if response.status_code == 200:
            return response.text
        logging.error('get invalid status code %s while scraping %s',
                      response.status_code,url)
    except requests.RequestException:
        logging.error('error occurred while scraping %s',url,exc_info=True)
```

```
def scrape_index(page):
    index_url = f'{BASE_URL}/page/{page}'
    return scrape_page(index_url)
```

```
def parse_index(html):
    pattern = re.compile('<a.*?href="(.*?)".*?class="name">')
    items = re.findall(pattern,html)
    if not items:
        return []
    for item in items:
        detail_url = urljoin(BASE_URL,item)
        logging.info('get detail url %s',detail_url)
        yield detail_url
```

```
def main():
    for page in range(1,TOTAL_PAGE+1):
        index_html = scrape_index(page)
        detail_url = parse_index(index_html)
        logging.info('detail urls %s',list(detail_url))

if __name__ == "__main__":
    main()
```

	2022-02-10 17:12:21,521 - INFO:scraping https://ssr1.scrape.center/page/1...
	2022-02-10 17:12:22,130 - INFO:get detail url https://ssr1.scrape.center/detail/1
	2022-02-10 17:12:22,130 - INFO:get detail url https://ssr1.scrape.center/detail/2
	2022-02-10 17:12:22,130 - INFO:get detail url https://ssr1.scrape.center/detail/3
	2022-02-10 17:12:22,130 - INFO:get detail url https://ssr1.scrape.center/detail/4
	2022-02-10 17:12:22,130 - INFO:get detail url https://ssr1.scrape.center/detail/5
	2022-02-10 17:12:22,130 - INFO:get detail url https://ssr1.scrape.center/detail/6
	2022-02-10 17:12:22,130 - INFO:get detail url https://ssr1.scrape.center/detail/7
	2022-02-10 17:12:22,130 - INFO:get detail url https://ssr1.scrape.center/detail/8
	2022-02-10 17:12:22,130 - INFO:get detail url https://ssr1.scrape.center/detail/9
	2022-02-10 17:12:22,130 - INFO:get detail url https://ssr1.scrape.center/detail/10
	2022-02-10 17:12:22,130 - INFO:detail urls ['https://ssr1.scrape.center/detail/1', 'https://ssr1.scrape.center/detail/2', 'https://ssr1.scrape.center/detail/3', 'https://ssr1.scrape.center/detail/4', 'https://ssr1.scrape.center/detail/5', 'https://ssr1.scrape.center/detail/6', 'https://ssr1.scrape.center/detail/7', 'https://ssr1.scrape.center/detail/8', 'https://ssr1.scrape.center/detail/9', 'https://ssr1.scrape.center/detail/10']

```
def scrape_detail(url):
    return scrape_page(url)
```

```
def parse_detail(html):
    cover_pattern = re.compile('<div.*?item.*?<a.*?<img.*?src="(.*?)".*?cover.*?</a>',re.S)
    name_pattern = re.compile('<h2.*?>(.*?)</h2>',re.S)
    categories_pattern = re.compile('<button.*?category.*?<span>(.*?)</span>.*?</button>',re.S)
    published_at_pattern = re.compile('(\d{4}-\d{2}-\d{2})\s?上映',re.S)
    drama_pattern = re.compile('<div.*?drama.*?>.*?<p.*?>(.*?)</p>',re.S)
    score_pattern = re.compile('<p.*?score.*?>(.*?)</p>',re.S)
    html = str(html)
    cover = re.search(cover_pattern,html).group(1).strip() if re.search(cover_pattern,html) else None
    name = re.search(name_pattern,html).group(1).strip() if re.search(name_pattern,html) else None
    categories = re.findall(categories_pattern,html) if re.findall(categories_pattern,html) else []
    published_at = re.search(published_at_pattern,html).group(1).strip() if re.search(published_at_pattern,html) else None
    drama = re.search(drama_pattern,html).group(1).strip() if re.search(drama_pattern,html) else None
    score = float(re.search(score_pattern,html).group(1).strip()) if re.search(score_pattern,html) else None
    return {
        'cover':cover,
        'name':name,
        'categories':categories,
        'published-at':published_at,
        'drama':drama,
        'score':score
    }
```

```
def main():
    for page in range(1,TOTAL_PAGE+1):
        index_html = scrape_index(page)
        detail_urls = parse_index(index_html)
        for detail_url in detail_urls:
            detail_html = scrape_detail(detail_url)
            data = parse_detail(detail_html)
            logging.info('get detail data %s',data)
```

	2022-02-10 17:18:38,270 - INFO:scraping https://ssr1.scrape.center/page/1...
	2022-02-10 17:18:39,333 - INFO:get detail url https://ssr1.scrape.center/detail/1
	2022-02-10 17:18:39,333 - INFO:scraping https://ssr1.scrape.center/detail/1...
	2022-02-10 17:18:41,200 - INFO:get detail data {'cover': 'https://p0.meituan.net/movie/ce4da3e03e655b5b88ed31b5cd7896cf62472.jpg@464w_644h_1e_1c', 'name': '霸王别姬 - Farewell My Concubine', 'categories': ['剧情', '爱情'], 'published-at': '1993-07-26', 'drama': '影片借一出《霸王别姬》的京戏，牵扯出三个人之间一段随时代风云变幻的爱恨情仇。段小楼（张丰毅 饰）与程蝶衣（张国荣 饰）是一对打小一起长大的师兄弟，两人一个演生，一个饰旦，一向配合天衣无缝，尤其一出《霸王别姬》，更是誉满京城，为此，两人约定合演一辈子《霸王别姬》。但两人对戏剧与人生关系的理解有本质不同，段小楼深知戏非人生，程蝶衣则是人戏不分。段小楼在认为该成家立业之时迎娶了名妓菊仙（巩俐 饰），致使程蝶衣认定菊仙是可耻的第三者，使段小楼做了叛徒，自此，三人围绕一出《霸王别姬》生出的爱恨情仇战开始随着时代风云的变迁不断升级，终酿成悲剧。', 'score': 9.5}
	
	
```
import json
from os import makedirs
from os.path import exists

RESULTS_DIR = 'result'
exists(RESULTS_DIR) or makedirs(RESULTS_DIR)
def save_data(data):
    name = data.get('name')
    data_path = f'{RESULTS_DIR}/{name}.json'
    json.dump(data,open(data_path,'w',encoding='utf-8'),ensure_ascii=False,indent=2)
```

```
def main():
	for page in range(1,TOTAL_PAGE+1):
		index_html = scrape_index(page)
		detial_urls = parse_index(index_html)
		logging.info('all_detail_urls:%s',detial_urls)
    for detial_url in detial_urls:
        detial_html = scrape_detail(detial_url)
        data = parse_detail(detial_html)
        logging.info('get detail data %s',data)
        save_data(data)
        logging.info('data saved successfully')
```


	2022-02-10 17:22:07,869 - INFO:get detail url https://ssr1.scrape.center/detail/92
	2022-02-10 17:22:07,869 - INFO:scraping https://ssr1.scrape.center/detail/92...
	2022-02-10 17:22:08,509 - INFO:scrape https://ssr1.scrape.center/detail/81 successfully
	2022-02-10 17:22:08,509 - INFO:get detail data {'cover': 'https://p0.meituan.net/movie/4c41068ef7608c1d4fbfbe6016e589f7204391.jpg@464w_644h_1e_1c', 'name': '活着 - To Live', 'categories': ['剧情', '家庭', '历史'], 'published-at': '1994-05-17', 'drama': '影片讲述了曾经的纨绔子弟福贵输光家产后，与家人自力更生，在苦难中坚持“活着”的故事。富少福贵（葛优 饰）嗜赌成性，妻子家珍（巩俐 饰）屡劝无果后带着女儿凤霞离开了他，当夜，福贵输光所有家产。这气死了他的父亲，福贵也被迫变卖母亲首饰，租了间破屋和母亲过活。一年后，家珍手拉小凤霞（张路 饰）怀抱刚出世的儿子有庆（董飞 饰）回到家中，福贵开始靠演皮影戏过起安份守己的日子。但好景不常，内战时期，福贵被国民党抓去当劳工，一番辗转终回到家乡与一家人团圆后却发现凤霞因病变成哑巴，而在后来的大跃进运动和文化大革命中，逆境也一直如影相随。', 'score': 9.0}
	2022-02-10 17:22:08,509 - INFO:data saved successfully

```
def main(page):
    index_html = scrape_index(page)
    detial_urls = parse_index(index_html)
    logging.info('all_detail_urls:%s',detial_urls)
    for detial_url in detial_urls:
        detial_html = scrape_detail(detial_url)
        data = parse_detail(detial_html)
        logging.info('get detail data %s',data)
        save_data(data)
        logging.info('data saved successfully')

if __name__ == "__main__":
    pool = multiprocessing.Pool()
    pages = range(1,TOTAL_PAGE+1)
    pool.map(main,pages)
    pool.close()
    pool.join()
```
