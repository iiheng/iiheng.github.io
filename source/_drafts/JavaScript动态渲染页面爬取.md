---
title: JavaScript动态渲染页面爬取
date: 2022-02-23 14:56:16
tags: JavaScript动态渲染页面爬取
categories: python爬虫的学习
---
## 前言
## Selenium的使用
[selenium文档](https://selenium-python.readthedocs.io/)
### 准备工作
### 基本用法
```
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.wait import WebDriverWait

browser = webdriver.Chrome()
try:
    browser.get('https://www.baidu.com')
    input = browser.find_element_by_id('kw')
    input.send_keys('Python')
    input.send_keys(Keys.ENTER)
    wait = WebDriverWait(browser,10)
    wait.until(EC.presence_of_element_located((By.ID,'content_left')))
    print(browser.current_url)
    print(browser.get_cookie())
    print(browser.page_source)
finally:
    browser.close()
```

	https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&rsv_idx=1&tn=baidu&wd=Python&fenlei=256&rsv_pq=a4ba16a800006f38&rsv_t=bf09nMdMAx3qgm57o9bM8ukF%2BIiQSYUJKm2Hxuq6I61AM2UDAKvGMEotjX0&rqlang=cn&rsv_dl=tb&rsv_enter=1&rsv_sug3=6&rsv_sug2=0&rsv_btype=i&inputT=113&rsv_sug4=114&rsv_jmp=fail
	[{'domain': '.www.baidu.com', 'expiry': 1677143189, 'httpOnly': False, 'name': 'channel', 'path': '/', 'secure': False, 'value': 'baidusearch'}, {'domain': '.baidu.com', 'expiry': 1645610789, 'httpOnly': False, 'name': 'BA_HECTOR', 'path': '/', 'secure': False, )
	.......
	
### 初始化浏览器对象
```
from selenium import webdriver

browser = webdriver.Chrome()
browser = webdriver.Firefox()
browser = webdriver.Edge()
browser = webdriver.Safari()
```

### 访问页面
```
import time
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('https://www.taobao.com')
print(browser.page_source)
time.sleep(5)
browser.close()
```

### 查找节点
#### 单个节点

```
import time
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('https://www.taobao.com')
print(browser.page_source)
imput_first = browser.find_element_by_id('q')
input_second = browser.find_element_by_css_selector('#q')
input_third = browser.find_element_by_xpath('//*[@id="q]')
print(imput_first,input_second,input_third)
browser.close()
```

	<selenium.webdriver.remote.webelement.WebElement (session="96000c1f8154f2985765950cfbb264a1", element="123bc1ce-4896-4a65-92b6-7446b4f4affd")>
	<selenium.webdriver.remote.webelement.WebElement (session="96000c1f8154f2985765950cfbb264a1", element="123bc1ce-4896-4a65-92b6-7446b4f4affd")>
	<selenium.webdriver.remote.webelement.WebElement (session="96000c1f8154f2985765950cfbb264a1", element="123bc1ce-4896-4a65-92b6-7446b4f4affd")>


```
from selenium import webdriver
from selenium.webdriver.common.by import By

browser = webdriver.Chrome()
browser.get("https://www.taobao.com")
input = browser.find_element(By.ID,'q')
print(input)
browser.close()
```

#### 多个节点
```
from selenium import webdriver
from selenium.webdriver.common.by import By

browser = webdriver.Chrome()
browser.get('https://www.taobao.com')
lis = browser.find_elements(By.CSS_SELECTOR,'.service-bd li')
print(lis)
browser.close()
```

### 节点交互
```
import time
from selenium import webdriver
from selenium.webdriver.common.by import By

browser = webdriver.Chrome()
browser.get('https://www.taobao.com')
input = browser.find_element(By.ID,'q')
print(type(input))
input.send_keys('iphone')
time.sleep(1)
input.clear()
input.send_keys('iPad')
button = browser.find_element(By.CLASS_NAME,'btn-search')
button.click()
```

### 动作链
```
from selenium import webdriver
from selenium.webdriver import ActionChains
from selenium.webdriver.common.by import By

browser = webdriver.Chrome()
url = 'https://www.runoob.com/try/try.php?filename=jqueryui-api-droppable'
browser.get(url)
browser.switch_to.frame('iframeResult')
source = browser.find_element(By.CSS_SELECTOR,'#draggable')
target = browser.find_element(By.CSS_SELECTOR,'#droppable')
actions = ActionChains(browser)
actions.drag_and_drop(source,target)
actions.perform()
```

### 运行JavaScript
```
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('https://www.zhihu.com/explore')
browser.execute_script('window.scrollTo(0,document.body.scrollHeight)')
browser.execute_script('alert("To Button")')
```

### 获取节点信息
#### 获取属性
```
from selenium import webdriver
from selenium.webdriver.common.by import By

browser = webdriver.Chrome()
url = 'https://spa2.scrape.center/'
browser.get(url)
logo = browser.find_element(By.CLASS_NAME,'logo-image')
print(logo)
print(logo.get_attribute('src'))
```

	<selenium.webdriver.remote.webelement.WebElement (session="add169cdc98c9f7e661450254c5edc59", element="e6923709-4eb9-4ac6-9c27-b082df01b309")>
	https://spa2.scrape.center/img/logo.a508a8f0.png)
	
#### 获取文本值
```
from selenium import webdriver
from selenium.webdriver.common.by import By

browser = webdriver.Chrome()
url = 'https://spa2.scrape.center/'
browser.get(url)
input = browser.find_element(By.CLASS_NAME,'logo-title')
print(input.text)
```

	Scrape
	
#### 获取ID、位置、标签名、和大小
```
from selenium import webdriver
from selenium.webdriver.common.by import By

browser = webdriver.Chrome()
url = 'https://spa2.scrape.center/'
browser.get(url)
input = browser.find_element(By.CLASS_NAME,'logo-title')
print(input.id)
print(input.location)
print(input.tag_name)
print(input.size)
```

#### 切换Frame
```
import time
from selenium import webdriver
from selenium.common.exceptions import NoSuchElementException
from selenium.webdriver.common.by import By

browser = webdriver.Chrome()
url = "http://www.runoob.com/try/try.php?filename=jqueryui-api-droppable"
browser.get(url)
browser.switch_to.frame('iframeResult')
try:
    logo = browser.find_element(By.CLASS_NAME,'logo')
except NoSuchElementException:
    print('NO LOGO')
browser.switch_to.parent_frame()
logo = browser.find_element(By.CLASS_NAME,'logo')
print(logo)
print(logo.text)
```

	NO LOGO
	<selenium.webdriver.remote.webelement.WebElement (session="b7b7043f32618a9180273bd6c50645b2", element="6aea1b0d-cd18-4829-9d84-6fbe7b2ec12e")>

### 延时等待
#### 隐式等待
```
from selenium import webdriver
from selenium.webdriver.common.by import By

browser = webdriver.Chrome()
browser.implicitly_wait(10)
browser.get('https://spa2.scrape.center/')
input = browser.find_element(By.CLASS_NAME,'logo-name')
print(input)
```

#### 显式等待
```
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

browser = webdriver.Chrome()
browser.get('http://www.taobao.com/')
wait = WebDriverWait(browser,10)
input = wait.until(EC.presence_of_element_located((By.ID,'q')))
button = wait.until(EC.element_to_be_clickable((By.CSS_SELECTOR,'.btn-search')))
print(input,button)
```

	<selenium.webdriver.remote.webelement.WebElement (session="018dae729d57adb69657474133fe310c", element="24cb68cb-0d65-4706-a152-3e8619458b67")> <selenium.webdriver.remote.webelement.WebElement (session="018dae729d57adb69657474133fe310c", element="3afa6c9f-ed76-4018-8ecb-aa880bf3370d")>

|等待节点|含义|
|-----|-----|
|`title_is`|标题是某内容|
|`title_contains`|标题包含某内容|
|`presence_of_element_located`|节点出现，参数为节点的定位元组，如（By.ID,'p'）|
|`visibility_of_element_located`|节点可见，参数为节点的定位元组|
|`visibility_of()`|可见，参数为节点对象|
|`visibility_of_all_elements_located`|可见，参数为节点对象|
|`presence_of_all_elements_located`|所有节点都出现|
|`text_to_be_present_in_element`|某个节点的文本值中包含某文字|
|`text_to_be_present_in_element_value`|某节点值中包含某文字|
|`frame_to_be_available_and_switch_to_it`|加载并切换|
|`invisibility_of_element_located`|节点不可见|
|`element_to_be_clickable`|按钮可点击|
|`staleness_of`|判断一个检点是否分在DOM树中，可知页面是否已经刷新|
|`element_to_be_selected`|节点可选择，参数为节点对象|
|`element_located_to_be_selected`|节点可选择，参数为节点的定位元组|
|`element_selection_state_to_be`|参数为节点对象以及状态，相等返回True,否则返回False|
|`element_located_selection_state_to_be`|参数为定位元组以及船台，相等返回True，否则返回False|
|`alert_is_present`|是否出现警告提示框|
### 前进和后退
```
import time
from selenium import webdriver

browser = webdriver.Chrome()
browser.get("https://www.baidu.com/")
browser.get("https://www.taobao.com/")
browser.get("https://www.python.org/")
browser.back()
time.sleep(1)
browser.forward()
time.sleep(1)
browser.close()
```

### Cookie
```
from selenium import webdriver

browser = webdriver.Chrome()
browser.get("https://www.zhihu.com/explore")
print(browser.get_cookies())
browser.add_cookie({'name':'name','domain':'www.zhihu.com','value':'germey'})
print(browser.get_cookies())
browser.delete_all_cookies()
print(browser.get_cookies())
```

	[{'domain': '.zhihu.com', 'httpOnly': False, 'name': 'Hm_lpvt_98beee57fd2ef70ccdd5ca52b9740c49', 'path': '/', 'secure': False, 'value': '1645673843'}, {'domain': 'www.zhihu.com', 'httpOnly': False, 'name': 'JOID', 'path': '/', 'secure': False, 'value': 'UVoUCkollG4TtETwRS8H_UyZyXlTEu1XLeAOpR5d8yBIzxCyMVVieHu3RvtFC_MDXUaKnkybncsBx543H_gXaKU='}, {'domain': 'www.zhihu.com', 'httpOnly': False, 'name': 'osd', 'path': '/', 'secure': False, 'value': 'Ul8WAE8mkWwZsUf1RyUC_kmbw3xQF-9dKOMLpxRY8CVKxRWxNFdofXiyRPFACPYBV0OJm06RmMgExZQyHP0VYqA='}, {'domain': '.zhihu.com', 'expiry': 1708745840, 'httpOnly': False, 'name': '_zap', 'path': '/', 'secure': False, 'value': '1a39dfeb-3d7c-4b5f-a1fd-ab1a960f1740'}, {'domain': '.zhihu.com', 'httpOnly': False, 'name': '_xsrf', 'path': '/', 'secure': False, 'value': '42d7bbea-58c2-4ff4-a39f-12c9b7ed9a4d'}, {'domain': 'www.zhihu.com', 'httpOnly': False, 'name': 'SESSIONID', 'path': '/', 'secure': False, 'value': '6z16VKVSzfMSsVCw5cGQUho2ULzQe1e9EslSwIfXnKX'}, {'domain': '.zhihu.com', 'expiry': 1677209843, 'httpOnly': False, 'name': 'Hm_lvt_98beee57fd2ef70ccdd5ca52b9740c49', 'path': '/', 'secure': False, 'value': '1645673843'}, {'domain': 'www.zhihu.com', 'httpOnly': False, 'name': 'KLBRSID', 'path': '/', 'secure': False, 'value': 'e42bab774ac0012482937540873c03cf|1645673842|1645673840'}, {'domain': '.zhihu.com', 'expiry': 1740281840, 'httpOnly': False, 'name': 'd_c0', 'path': '/', 'secure': False, 'value': '"AIDfxCqHihSPTnlJjcpy4gxA626ZnczgZ-U=|1645673840"'}]
	[{'domain': '.www.zhihu.com', 'httpOnly': False, 'name': 'name', 'path': '/', 'secure': True, 'value': 'germey'}, {'domain': '.zhihu.com', 'httpOnly': False, 'name': 'Hm_lpvt_98beee57fd2ef70ccdd5ca52b9740c49', 'path': '/', 'secure': False, 'value': '1645673843'}, {'domain': 'www.zhihu.com', 'httpOnly': False, 'name': 'JOID', 'path': '/', 'secure': False, 'value': 'UVoUCkollG4TtETwRS8H_UyZyXlTEu1XLeAOpR5d8yBIzxCyMVVieHu3RvtFC_MDXUaKnkybncsBx543H_gXaKU='}, {'domain': 'www.zhihu.com', 'httpOnly': False, 'name': 'osd', 'path': '/', 'secure': False, 'value': 'Ul8WAE8mkWwZsUf1RyUC_kmbw3xQF-9dKOMLpxRY8CVKxRWxNFdofXiyRPFACPYBV0OJm06RmMgExZQyHP0VYqA='}, {'domain': '.zhihu.com', 'expiry': 1708745840, 'httpOnly': False, 'name': '_zap', 'path': '/', 'secure': False, 'value': '1a39dfeb-3d7c-4b5f-a1fd-ab1a960f1740'}, {'domain': '.zhihu.com', 'httpOnly': False, 'name': '_xsrf', 'path': '/', 'secure': False, 'value': '42d7bbea-58c2-4ff4-a39f-12c9b7ed9a4d'}, {'domain': 'www.zhihu.com', 'httpOnly': False, 'name': 'SESSIONID', 'path': '/', 'secure': False, 'value': '6z16VKVSzfMSsVCw5cGQUho2ULzQe1e9EslSwIfXnKX'}, {'domain': '.zhihu.com', 'expiry': 1677209843, 'httpOnly': False, 'name': 'Hm_lvt_98beee57fd2ef70ccdd5ca52b9740c49', 'path': '/', 'secure': False, 'value': '1645673843'}, {'domain': 'www.zhihu.com', 'httpOnly': False, 'name': 'KLBRSID', 'path': '/', 'secure': False, 'value': 'e42bab774ac0012482937540873c03cf|1645673842|1645673840'}, {'domain': '.zhihu.com', 'expiry': 1740281840, 'httpOnly': False, 'name': 'd_c0', 'path': '/', 'secure': False, 'value': '"AIDfxCqHihSPTnlJjcpy4gxA626ZnczgZ-U=|1645673840"'}]
	[]
	
### 选项卡管理
```
import time
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('https://www.baidu.com')
browser.execute_script('window.open()')
print(browser.window_handles)
browser.switch_to.window(browser.window_handles[1])
browser.get('https://www.taobao.com')
time.sleep(1)
browser.switch_to.window(browser.window_handles[0])
browser.get('https://python.org')
```

	['CDwindow-34558DE7FEC4345F7357E755837010A4', 'CDwindow-4A6FB10DE31B0D9D1D0DDFCE938BCE6B']
	
### 异常处理
```
from selenium import webdriver
from selenium.webdriver.common.by import By

browser = webdriver.Chrome()
browser.get('https://www.baidu.com')
browser.find_element(By.ID,"hello")
```

	File "E:\pythonproject\scrapel\parsehtmll.py", line 6, in <module>
		browser.find_element(By.ID,"hello")

```
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.common.exceptions import TimeoutException,NoSuchElementException

browser = webdriver.Chrome()
try:
    browser.get('https://www.baidu.com')
except TimeoutException:
    print('Time Out')
try:
    browser.find_element(By.ID,"hello")
except NoSuchElementException:
    print("No Element")
finally:
    browser.close()
```

	No Element
	
### 反屏蔽
```
from selenium import webdriver
from selenium.webdriver import ChromeOptions

option = ChromeOptions()
option.add_experimental_option('excludeSwitches',['enable-automation'])
option.add_experimental_option('useAutomationExtension',False)
browser = webdriver.Chrome(options=option)
browser.execute_cdp_cmd('Page.addScriptToEvaluateOnNewDocument',{
    'source':'Object.defineProperty(navigator,"webdriver",{get:()=>undefined})'
})
browser.get("https://antispider1.scrape.center/")
```

### 无头模式
```
from selenium import webdriver
from selenium.webdriver import ChromeOptions

option = ChromeOptions()
option.add_argument("--headless")
browser = webdriver.Chrome(options=option)
browser.set_window_size(1366,768)
browser.get("https://www.baidu.com")
browser.get_screenshot_as_file("preview.png")
```

## Splash的使用
### 功能介绍
### 准备工作
### 实例引入
```
function main(splash, args)
  assert(splash:go(args.url))
  assert(splash:wait(0.5))
  return {
    html = splash:html(),
    png = splash:png(),
    har = splash:har(),
  }
end
```

### Splash Lua脚本
#### 入口及返回值
```
function main(splash, args)
  splash:go("https://www.baidu.com")
  splash:wait(0.5)
  local title = splash:evaljs("document.title")
  return {title=title}
end
```

```
function main(splash, args)
  return {hello="world"}
end
```

```
function main(splash, args)
  return "hello"
end
```

### 异步处理
```
function main(splash, args)
  local example_urls = {"www.baidu.com","www.taobao.com","www.zhihu.com"}
  local urls = args.urls or example_urls
  local results = {}
  for index,url in ipairs(urls) do
    local ok,reason = splash:go("https://"..url)
    if ok then
      splash:wait(2)
      results[url] = splash:png()
   end
    return results
end
```

### splash 对象的属性
#### args 属性
```
function main(splash, args)
  local url = args.url
end
```
```
function main(splash)
  local url = splash.args.url
  return url
end
```

#### js_enabled属性
```
function main(splash, args)
  splash:go("https://www.baidu.com")
  splash.js_enabled = false
  local title = splash:evaljs("document.title")
  return title
end
```

	{
		"error": 400,
		"type": "ScriptError",
		"description": "Error happened while executing Lua script",
		"info": {
			"type": "JS_ERROR",
			"js_error_message": null,
			"message": "[string \"function main(splash, args)\r...\"]:4: unknown JS error: None",
			"splash_method": "evaljs",
			"source": "[string \"function main(splash, args)\r...\"]",
			"line_number": 4,
			"error": "unknown JS error: None"
		}
	}
	
#### resource_timeout属性
```
function main(splash,args)
  splash.resource_time = 0.1
  assert(splash:go("https://www.taobao.com"))
  return splash:png()
end
```

#### images_enabled属性
```
function main(splash, args)
  splash.images_enabled = false
  assert(splash:go("https://www.jd.com"))
  return {png=splash:png()}
end
```

#### plugins_enabled 属性
`splash.plugins_enabled = true/false`

#### scroll_position 属性
```
function main(splash, args)
  assert(splash:go("https://www.taobao.com"))
  splash.scroll_position = {y=400}
  return {png = splash:png()}
end
```

`splash.scroll_position = {x=100,y=400}`

### splash 对象的方法
#### go方法
`ok,reason = splash:go(url,baseurl=nil,headers=nil,http_method="GET",body=nil,formdata=nil)`

> url：请求	URL
> baseurl：资源加载的相对路径，是可选参数，默认为空。
> headers：请求头，是可选参数，默认为空
> http_method：请求方法，是可选参数，默认为GET，同时支持POST
> body：http_method为POST时的表单数据，使用的Content-type为application/x-www-form-urlencoded,是可选参数，默认为空。

```
function main(splash, args)
  local ok,reason = splash:go{"http://www.httpbin.org/post",http_method="POST",body="name=Germey"}
  if ok then
    return splash:html()
  end
end
```

	<html><head></head><body><pre style="word-wrap: break-word; white-space: pre-wrap;">{
	  "args": {}, 
	  "data": "", 
	  "files": {}, 
	  "form": {
		"name": "Germey"
	  }, 
	  "headers": {
		"Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8", 
		"Accept-Encoding": "gzip, deflate", 
		"Accept-Language": "en,*", 
		"Content-Length": "11", 
		"Content-Type": "application/x-www-form-urlencoded", 
		"Host": "www.httpbin.org", 
		"Origin": "null", 
		"User-Agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/602.1 (KHTML, like Gecko) splash Version/10.0 Safari/602.1", 
		"X-Amzn-Trace-Id": "Root=1-621876cb-7523fb9c08fb0c6360fc3e69"
	  }, 
	  "json": null, 
	  "origin": "115.200.184.159", 
	  "url": "http://www.httpbin.org/post"
	}
	</pre></body></html>
	
#### wait方法
ok,reason = splash:wait{time,cancel_on_redirect=false,cancel_on_error=true}

> time：等待的时间，单位为秒
> candel\_on\_edirect：如果发生了重定向就停止等待，并返回重定向结果，是可选参数，默认为false。
> cancel_on\_error：如果页面加载错误就停止等待，是可选参数，默认为false。

```
function main(splash, args)
  splash:go("https://www.taobao.com")
  splash:wait(2)
  return {html=splash:html()}
end
```

#### jsfunc 方法
```
function main(splash, args)
  local get_div_count = splash:jsfunc(
  [[
    function(){
    var body = document.body;
    var divs = body.getElementsByTagName('div');
    return divs.length;
  }
    ]]
  )
  splash:go('https://www.baidu.com')
  return ("There are %s DIVs"):format(get_div_count())
end
```

#### evaljs 方法
`result = splash:evaljs(js)`
`local title = splash:evaljs("document.title")`

#### runjs 方法
```
function main(splash, args)
  splash:go("https://www.baidu.com")
  splash:runjs("foo = function(){return 'bar'}")
  local result = splash:evaljs("foo()")
  return result
end
```

#### html 方法
```
function main(splash, args)
  splash:go("https://www.httpbin.org/get")
  return splash:html()
end
```

	<html><head></head><body><pre style="word-wrap: break-word; white-space: pre-wrap;">{
	  "args": {}, 
	  "headers": {
		"Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8", 
		"Accept-Encoding": "gzip, deflate", 
		"Accept-Language": "en,*", 
		"Host": "www.httpbin.org", 
		"User-Agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/602.1 (KHTML, like Gecko) splash Version/10.0 Safari/602.1", 
		"X-Amzn-Trace-Id": "Root=1-62187bcb-7f1b70e270cf21a3547ce93b"
	  }, 
	  "origin": "115.200.184.159", 
	  "url": "https://www.httpbin.org/get"
	}
	</pre></body></html>
	
#### png 方法
```
function main(splash, args)
  splash:go("https://www.taobao.com")
  return splash:png()
end
```

#### jpeg 方法
```
function main(splash, args)
  splash:go("https://www.taobao.com")
  return splash:jpeg()
end
```

#### har 方法
```
function main(splash, args)
  splash:go("http://www.taobao.com")
  return splash:har()
end
```
#### url 方法
```
function main(splash, args)
  splash:go("http://www.taobao.com")
  return splash:url()
end
```

	Splash Response: "https://www.taobao.com/"
	
#### set_user\_agent 方法
```
function main(splash, args)
  splash:set_user_agent('splash')
  splash:go("http://www.httpbin.org/get")
  return splash:html()
end
```

	<html><head></head><body><pre style="word-wrap: break-word; white-space: pre-wrap;">{
	  "args": {}, 
	  "headers": {
		"Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8", 
		"Accept-Encoding": "gzip, deflate", 
		"Accept-Language": "en,*", 
		"Host": "www.httpbin.org", 
		"User-Agent": "splash", 
		"X-Amzn-Trace-Id": "Root=1-62187dd3-15871842270435fe111405bc"
	  }, 
	  "origin": "115.200.184.159", 
	  "url": "http://www.httpbin.org/get"
	}
	</pre></body></html>
	
#### select 方法
```
function main(splash, args)
  splash:go("https://www.baidu.com/")
  input = splash:select("#kw")
  input:send_text('Splash')
  splash:wait(5)
  return splash:png()
end
```

#### select_all 方法
```
function main(splash, args)
  local treat = require("treat")
  assert(splash:go("https://quotes.toscrape.com/"))
  assert(splash:wait(1))
  local texts = splash:select_all('.quote .text')
  local results = {}
  for index,text in ipairs(texts) do
    results[index] = text.node.innerHTML
  end
  return treat.as_array(result)
end
```

#### mouse_click 方法
```
function main(splash, args)
  splash:go("https://www.baidu.com/")
  input = splash:select("#kw")
  input:send_text("Splash")
  splash:wait(3)
  submit = splash:select("#su")
  submit:mouse_click()
  splash:wait(5)
  return splash:png()
end
```

### 调用Splash提供的API
#### render.html

`curl http://localhost:8050/render.html?url=https://www.baidu.com`

```
import requests
url = "http://localhost:8050/render.html?url=https://www.baidu.com"
response = requests.get(url)
print(response.text)
```

```
import requests
url = "http://localhost:8050/render.html?url=https://www.baidu.com&amp;wait=5"
response = requests.get(url)
print(response.text)
```

#### render.png
`curl http://127.0.0.1:8050/render.png?url=https://www.taobao.com&wait=5&width=1000&heigh=700`

```
import requests
url = "http://127.0.0.1:8050/render.png?url=https://www.taobao.com&wait=5&width=1000&heigh=700"
response = requests.get(url)
with open('taobao.png',"wb") as f:
    f.write(response.content)
```

#### render.jpeg
和 render.png类似，多了一个quality参数
#### render.har
`curl http://localhost:8050/render.har?url=https://www.jd.com&wait=5`

#### render.json
`curl http://127.0.0.1:8050/render.json?url=https://www.httpbin.org`

#### execute
```
function main(splash)
  return "hello"
end
```

`http://127.0.0.1:8050/execute?lua_source=%0Afunction%20main%28splash%29%0A%20%20return%20%22hello%22%0Aend%0A`

	hello

```
import requests
from urllib.parse import quote

lua = """
function main(splash)
  return "hello"
end
"""

url = 'http://127.0.0.1:8050/execute?lua_source='+quote(lua)
print(url)
response = requests.get(url)
print(response.text)
```

	hello
	
```
import requests
from urllib.parse import quote

lua = """
function main(splash)
  local treat = require("treat")
  local response = splash:http_get("http://www.httpbin.org/get")
  return {html=treat.as_string(response.body),
  url=response.url,
  status=response.status
  
  }
end
"""

url = 'http://127.0.0.1:8050/execute?lua_source='+quote(lua)
response = requests.get(url)
print(response.text)
```

	{"html": "{\n  \"args\": {}, \n  \"headers\": {\n    \"Accept-Encoding\": \"gzip, deflate\", \n    \"Accept-Language\": \"en,*\", \n    \"Host\": \"www.httpbin.org\", \n    \"User-Agent\": \"Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/602.1 (KHTML, like Gecko) splash Version/10.0 Safari/602.1\", \n    \"X-Amzn-Trace-Id\": \"Root=1-621888fc-42301bdf74504e4935cb47c8\"\n  }, \n  \"origin\": \"115.200.184.159\", \n  \"url\": \"http://www.httpbin.org/get\"\n}\n", "status": 200, "url": "http://www.httpbin.org/get"}
	
## Pyppeteer的使用
`pip install pyppeteer`

## Playwright 的使用
### Playwright 的特点
### 安装
`pip3 install playwright`

`playwriht install`

### 基本使用
```
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    for browser_type in [p.chromium,p.firefox,p.webkit]:
        browser = browser_type.launch(headless=False)
        page = browser.new_page()
        page.goto('https://www.baidu.com')
        page.screenshot(path=f"screenshot-{browser_type.name}.png")
        print(page.title())
        browser.close()
```

	百度一下，你就知道
	百度一下，你就知道
	百度一下，你就知道

```
import asyncio
from playwright.async_api import async_playwright

async def main():
    async with async_playwright() as p:
        for browser_type in [p.chromium,p.firefox,p.webkit]:
            browser = await browser_type.launch()
            page = await browser.new_page()
            await page.goto("https://www.baidu.com")
            await page.screenshot(path=f"screanshot-{browser_type.name}.png")
            print(await page.title())
            await browser.close()

asyncio.run(main())
```

### 代码生成
`playwright codegen --help`
`playwright codegen -o script.py -b firefox`

```
from playwright.sync_api import Playwright, sync_playwright


def run(playwright: Playwright) -> None:
    browser = playwright.firefox.launch(headless=False)
    context = browser.new_context()

    # Open new page
    page = context.new_page()

    # Go to https://www.baidu.com/
    page.goto("https://www.baidu.com/")

    # Click input[name="wd"]
    page.locator("input[name=\"wd\"]").click()

    # Fill input[name="wd"]
    page.locator("input[name=\"wd\"]").fill("nba")

    # Click text=百度一下
    # with page.expect_navigation(url="https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&rsv_idx=1&tn=baidu&wd=nba&fenlei=256&oq=nba&rsv_pq=ffb6b183001d0938&rsv_t=fd9fsJTHk%2FU4ZduczDjkGBkjhlEWcqKXGq%2FTklb3qSVxaNnXX6578DZBn6Q&rqlang=cn&rsv_enter=0&rsv_dl=tb&rsv_btype=t"):
    with page.expect_navigation():
        page.locator("text=百度一下").click()

    # Close page
    page.close()

    # ---------------------
    context.close()
    browser.close()


with sync_playwright() as playwright:
    run(playwright)

```

### 支持移动端浏览器
```
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    iphone_12_pro_max = p.devices['iPhone 12 Pro Max']
    browser = p.webkit.launch(headless=False)
    context = browser.new_context(**iphone_12_pro_max,locale='zh-CN')
    page = context.new_page()
    page.goto("https://www.whatismybrowser.com/")
    page.wait_for_load_state(state="networkidle")
    page.screenshot(path="browser-iphone.png")
    browser.close()
```

### 选择器
#### 文本选择
`page.click("text=Log in")`

#### CSS选择器
`page.click("button")`
`page.click("#nav-bar .contact-us-item")`
`page.click("[data-test=login-button]")`
`page.click("[aria-label="Sign in"]")`
#### CSS选择器+文本值
`page.click("[article:has-text('Playwright')]")`
`page.click("#nav-bar :text('Contact us')")`
#### CSS选择器+节点关系
`page.click(".item-description:has(.item-promo-banner)")`
`page.click("input:right-of(:text("Username"))")`

#### XPath
`page.click("xpath=//button")`
### 常用操作方法
#### 事件监听
```
from playwright.sync_api import sync_playwright

def on_response(response):
    print(f"Status{response.status}:{response.url}")

with sync_playwright() as p:
    browser = p.chromium.launch(headless=False)
    page = browser.new_page()
    page.on("response",on_response)
    page.goto("https://spa6.scrape.center/")
    page.wait_for_load_state("networkidle")
    browser.close()
```

	Status200:https://spa6.scrape.center/
	Status200:https://spa6.scrape.center/css/app.ea9d802a.css
	Status200:https://spa6.scrape.center/js/app.5ef0d454.js
	Status200:https://spa6.scrape.center/css/chunk-19c920f8.2a6496e0.css
	Status200:https://spa6.scrape.center/css/chunk-2f73b8f3.5b462e16.css
	Status200:https://spa6.scrape.center/js/chunk-vendors.77daf991.js
	Status200:https://spa6.scrape.center/js/chunk-19c920f8.c3a1129d.js
	Status200:https://spa6.scrape.center/js/chunk-2f73b8f3.8f2fc3cd.js
	Status200:https://spa6.scrape.center/js/chunk-4dec7ef0.e4c2b130.js
	Status200:https://spa6.scrape.center/js/chunk-4dec7ef0.e4c2b130.js
	
```
from playwright.sync_api import sync_playwright

def on_response(response):
    if '/api/movie' in response.url and response.status ==200:
        print(response.json())

with sync_playwright() as p:
    browser = p.chromium.launch(headless=False)
    page = browser.new_page()
    page.on("response",on_response)
    page.goto("https://spa6.scrape.center/")
    page.wait_for_load_state("networkidle")
    browser.close()
```

	{'count': 100, 'results': [{'id': 1, 'name': '霸王别姬', 'alias': 'Farewell My Concubine', 'cover': 'https://p0.meituan.net/movie/ce4da3e03e655b5b88ed31b5cd7896cf62472.jpg@464w_644h_1e_1c', 'categories': ['剧情', '爱情'], 'published_at': '1993-07-26', 'minute': 171, 'score': 9.5, 'regions': ['中国内地', '中国香港']}, {'id': 2, 'name': '这个杀手不太冷', 'alias': 'Léon', 'cover': 'https://p1.meituan.net/movie/6bea9af4524dfbd0b668eaa7e187c3df767253.jpg@464w_644h_1e_1c', 'categories': ['剧情', '动作', '犯罪'], 'published_at': '1994-09-14', 'minute': 110, 'score': 9.5, 'regions': ['法国']}, {'id': 3, 'name': '肖申克的救赎', 'alias': 'The Shawshank Redemption', 'cover': 'https://p0.meituan.net/movie/283292171619cdfd5b240c8fd093f1eb255670.jpg@464w_644h_1e_1c', 'categories': ['剧情', '犯罪'], 'published_at': '1994-09-10', 'minute': 142, 'score': 9.5, 'regions': ['美国']}, {'id': 4, 'name': '泰坦尼克号', 'alias': 'Titanic', 'cover': 'https://p1.meituan.net/movie/b607fba7513e7f15eab170aac1e1400d878112.jpg@464w_644h_1e_1c', 'categories': ['剧情', '爱情', '灾难'], 'published_at': '1998-04-03', 'minute': 194, 'score': 9.5, 'regions': ['美国']}, {'id': 5, 'name': '罗马假日', 'alias': 'Roman Holiday', 'cover': 'https://p0.meituan.net/movie/289f98ceaa8a0ae737d3dc01cd05ab052213631.jpg@464w_644h_1e_1c', 'categories': ['剧情', '喜剧', '爱情'], 'published_at': '1953-08-20', 'minute': 118, 'score': 9.5, 'regions': ['美国']}, {'id': 6, 'name': '唐伯虎点秋香', 'alias': 'Flirting Scholar', 'cover': 'https://p0.meituan.net/movie/da64660f82b98cdc1b8a3804e69609e041108.jpg@464w_644h_1e_1c', 'categories': ['喜剧', '爱情', '古装'], 'published_at': '1993-07-01', 'minute': 102, 
	
#### 获取页面源代码
```
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page()
    page.goto("https://spa6.scrape.center/")
    page.wait_for_load_state("networkidle")
    html = page.content()
    print(html)
    browser.close()
```

#### 页面点击
`page.click(selector,**kwargs)`

> `click_count`:点击次数，默认为1
> `timeout`:等待找到要点击的节点的超时事件（单位为秒），默认是30
> `position`:需要传入一个字典，带有x属性和y属性，代表点击位置相对节点左上角的偏移量
> `force`:及时按钮设置了不可点击，也要强制点击，默认为False

click 方法的内部执行逻辑如下：
> 找到与selector匹配的节点，如果没有找到，就一只等待直到超时，超时时间timeout参数设置
> 检查匹配到的节点是否存在可操作性，等待检查结果，如果某个按钮设置了不可点击，就等该按钮变成可点击的时候再去点击，除非通过force参数设置了跳过可操作性检查的步骤，才会强制点击。
> 如果有需要，就滚动一下页面，是需要点击的节点呈现出来
> 调用Page对象的mouse方法，点击节点的中心位置，如果指定了position参数，就点击参数指定的位置。

#### 文本输入
`page.fill(selector,value,**kwargs)`

#### 获取节点属性
`page.get_attribute(selector,name,**kwargs)`

```
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page()
    page.goto("https://spa6.scrape.center/")
    page.wait_for_load_state("networkidle")
    href = page.get_attribute('a.name','href')
    print(href)
    browser.close()
```

	/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIx

#### 获取多个节点
```
import time

from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=False)
    page = browser.new_page()
    page.goto("https://spa6.scrape.center/")
    page.wait_for_load_state("networkidle")
    elements = page.query_selector_all('a.name')
    for element in elements:
        print(element.get_attribute('href'))
        print(element.text_content())
    browser.close()
```

	/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIx
	霸王别姬 - Farewell My Concubine
	/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIy
	这个杀手不太冷 - Léon
	/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIz
	肖申克的救赎 - The Shawshank Redemption
	/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI0
	泰坦尼克号 - Titanic
	/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI1
	罗马假日 - Roman Holiday
	/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI2
	唐伯虎点秋香 - Flirting Scholar
	
#### 获取单个节点
```
import time

from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=False)
    page = browser.new_page()
    page.goto("https://spa6.scrape.center/")
    page.wait_for_load_state("networkidle")
    element = page.query_selector('a.name')
    print(element.get_attribute("href"))
    print(element.text_content())
    browser.close()
```

	/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIx
	霸王别姬 - Farewell My Concubine
	
#### 网络挟持
```
import re
import time

from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=False)
    page = browser.new_page()

    def cancel_requesr(route,request):
        route.abort()

    page.route(re.compile(r"(\.png)|(\.jpg)"),cancel_requesr)
    page.goto("https://spa6.scrape.center")
    page.wait_for_load_state("networkidle")
    page.screenshot(path='no_picture.png')
    browser.close()
```

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Hack Response</title>
</head>
<body>
<h1>Hack Response</h1>
</body>
</html>
```

```
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=False)
    page = browser.new_page()

    def modify_response(route,requests):
        route.fulfill(path="./custom_response.html")

    page.route('/',modify_response)
    page.goto('https://spa6.scrape.center')
    time.sleep(10)
    browser.close()
```

## Selenium 爬取实战
### 准备工作
### 爬取目标
### 爬取列表页
```
from selenium import webdriver
from selenium.common.exceptions import TimeoutException
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.wait import WebDriverWait
import logging

logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(levelname)s:%(message)s')

INDEX_URL = "https://spa2.scrape.center/page/{page}"
TIME_OUT = 10
TOTAL_PAGE = 10

browser = webdriver.Chrome()
wait = WebDriverWait(browser,TIME_OUT)
```

```
def scrape_page(url,condition,locator):
    logging.info("scraping %s",url)
    try:
        browser.get(url)
        wait.until(condition(locator))
    except TimeoutException:
        logging.error("error occurred while scraping %s",url,exc_info=True)

def scrape_index(page):
    url = INDEX_URL.format(page=page)
    scrape_page(url,condition=EC.visibility_of_all_elements_located,locator=(By.CSS_SELECTOR,"#index .item"))
```

```
from urllib.parse import urljoin

def parse_index():
    elements = browser.find_elements(By.CSS_SELECTOR,"#index .item .name")
    for element in elements:
        href = element.get_attribute('href')
        yield urljoin(INDEX_URL,href)
```

```
def main():
    try:
        for page in range(0,TOTAL_PAGE+1):
            scrape_index(page)
            detail_urls = parse_index()
            logging.info("detail urls %s",list(detail_urls))
    finally:
        browser.close()

if __name__=="__main__":
    main()
```

	2022-02-26 15:27:15,001 - INFO:scraping https://spa2.scrape.center/page/0
	2022-02-26 15:27:17,911 - INFO:detail urls ['https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIx', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIy', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIz', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI0', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI1', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI2', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI3', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI4', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI5', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIxMA==']
	2022-02-26 15:27:17,911 - INFO:scraping https://spa2.scrape.center/page/1
	2022-02-26 15:27:19,952 - INFO:detail urls ['https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIx', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIy', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIz', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI0', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI1', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI2', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI3', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI4', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWI5', 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIxMA==']
	2022-02-26 15:27:19,952 - INFO:scraping https://spa2.scrape.center/page/2
	......
	
### 爬取详情页
```
def scrape_detail(url):
    scrape_page(url,condition=EC.visibility_of_all_elements_located,locator=(By.TAG_NAME,"h2"))
```

```
def parse_detail():
    url = browser.current_url
    name = browser.find_element(By.TAG_NAME,"h2").text
    categoires = [element.text for element in browser.find_elements(By.CSS_SELECTOR,".categories button span")]
    cover = browser.find_element(By.CSS_SELECTOR,".cover").get_attribute("src")
    score = browser.find_element(By.CSS_SELECTOR,".score").text
    drama = browser.find_element(By.CSS_SELECTOR,".drama p").text
    return {
        'url':url,
        'name':name,
        'categories':categoires,
        'cover':cover,
        'score':score,
        'drama':drama
    }
```
```
def main():
    try:
        for page in range(0,TOTAL_PAGE+1):
            scrape_index(page)
            detail_urls = parse_index()
            for detail_url in list(detail_urls):
                logging.info("get detail url %s",detail_url)
                scrape_detail(detail_url)
                detail_data = parse_detail()
                logging.info("detail data %s",detail_data)
    finally:
        browser.close()
```

	2022-02-26 15:50:34,910 - INFO:scraping https://spa2.scrape.center/page/0
	2022-02-26 15:50:38,338 - INFO:get detail url https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIx
	2022-02-26 15:50:38,338 - INFO:scraping https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIx
	2022-02-26 15:50:43,220 - INFO:detail data {'url': 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIx', 'name': '霸王别姬 - Farewell My Concubine', 'categories': ['剧情', '爱情'], 'cover': 'https://p0.meituan.net/movie/ce4da3e03e655b5b88ed31b5cd7896cf62472.jpg@464w_644h_1e_1c', 'score': '9.5', 'drama': '影片借一出《霸王别姬》的京戏，牵扯出三个人之间一段随时代风云变幻的爱恨情仇。段小楼（张丰毅 饰）与程蝶衣（张国荣 饰）是一对打小一起长大的师兄弟，两人一个演生，一个饰旦，一向配合天衣无缝，尤其一出《霸王别姬》，更是誉满京城，为此，两人约定合演一辈子《霸王别姬》。但两人对戏剧与人生关系的理解有本质不同，段小楼深知戏非人生，程蝶衣则是人戏不分。段小楼在认为该成家立业之时迎娶了名妓菊仙（巩俐 饰），致使程蝶衣认定菊仙是可耻的第三者，使段小楼做了叛徒，自此，三人围绕一出《霸王别姬》生出的爱恨情仇战开始随着时代风云的变迁不断升级，终酿成悲剧。'}
	2022-02-26 15:50:43,221 - INFO:get detail url https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIy
	2022-02-26 15:50:43,221 - INFO:scraping https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIy
	2022-02-26 15:50:50,437 - INFO:detail data {'url': 'https://spa2.scrape.center/detail/ZWYzNCN0ZXVxMGJ0dWEjKC01N3cxcTVvNS0takA5OHh5Z2ltbHlmeHMqLSFpLTAtbWIy', 'name': '这个杀手不太冷 - Léon', 'categories': ['剧情', '动作', '犯罪'], 'cover': 'https://p1.meituan.net/movie/6bea9af4524dfbd0b668eaa7e187c3df767253.jpg@464w_644h_1e_1c', 'score': '9.5', 'drama': '里昂（让·雷诺 饰）是名孤独的职业杀手，受人雇佣。一天，邻居家小姑娘马蒂尔德（纳塔丽·波特曼 饰）敲开他的房门，要求在他那里暂避杀身之祸。原来邻居家的主人是警方缉毒组的眼线，只因贪污了一小包毒品而遭恶警（加里·奥德曼 饰）杀害全家的惩罚。马蒂尔德 得到里昂的留救，幸免于难，并留在里昂那里。里昂教小女孩使枪，她教里昂法文，两人关系日趋亲密，相处融洽。 女孩想着去报仇，反倒被抓，里昂及时赶到，将女孩救回。混杂着哀怨情仇的正邪之战渐次升级，更大的冲突在所难免……'}
	
### 数据存储
```
def save_data(data):
    name = data.get('name')
    data_path = f'{RESULT_DIR}/{name}.json'
    json.dump(data,open(data_path,'w',encoding='utf-8'),ensure_ascii=False,indent=2)
```

### 设置无头模式
```
options = webdriver.ChromeOptions()
options.add_argument("--headless")
browser = webdriver.Chrome(options=options)
```

## Playwright 爬取实战
### 爬取目标
本节要爬取的目标和7.5节的一样，还是电影网站https://spa2.scrape.center/。
### 本节工作
本节要完成的工作也和7.5节的一样

> 遍历每一页列表页，获取每部电影详情页的URL
> 爬取每部电影的详情页，提取电影的名称、评分、类别、封面、简介等信息
> 将爬取的数据保存为JSON文件

### 准备工作
依旧是先做一些准备工作：


## CSS 位置偏移反扒案例分析与爬取实战
### 案例
```
from selenium import webdriver
from pyquery import PyQuery as pq
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.wait import WebDriverWait


browser = webdriver.Chrome()
browser.get("https://antispider3.scrape.center/")
WebDriverWait(browser,10).until(EC.presence_of_element_located((By.CSS_SELECTOR,".item")))
html = browser.page_source
doc = pq(html)
names = doc('.item .name')
for name in names.items():
    print(name.text())
browser.close()
```

	清 白 家 风
	宠 篇 的 上 下 老 法 ） 终 结 妃 册 （
	己 知 为 ） 册 （ 士 二 全
	那 些 年 ， 我 们 一 起 追 的 女 孩
	非 我 倾 城 （ 全 三 册 ）
	明 朝 那 些 事 儿
	我 和 你 笑 的 忘 书
	集 全 王 小 一 卷 第 波
	动 怦 然 心
	龙枪编年史（全3册）
	册 （ 龙 枪 传 三 ） 全 奇
	黎 明 之 街
	其 知 学 示 心 理 及 启 认
	银河帝国2：基地与帝国
	银 河 地 基 ： 帝 国
	小 学 教 材 全 解 - 四 年 级 语 文 下
	越界言论（第3卷）
	
```
from selenium import webdriver
from pyquery import PyQuery as pq
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.wait import WebDriverWait
import re

def parse_name(name_html):
    chars = name_html('.char')
    items = []
    for char in chars.items():
        items.append({
            'text':char.text().strip(),
            "left":int(re.search('(\d+)px',char.attr('style')).group(1))
        })
    items = sorted(items,key=lambda x:x['left'],reverse=False)
    return ''.join([item.get('text') for item in items])

browser = webdriver.Chrome()
browser.get("https://antispider3.scrape.center/")
WebDriverWait(browser,10).until(EC.presence_of_element_located((By.CSS_SELECTOR,".item")))
html = browser.page_source
doc = pq(html)
names = doc('.item .name')
for name_html in names.items():
    name = parse_name(name_html)
    print(name)
browser.close()
```

	<h3 data-v-7f1a77ef="" class="m-b-sm name"><span data-v-7f1a77ef="" class="char" style="left: 0px;">
						  明
						</span><span data-v-7f1a77ef="" class="char" style="left: 16px;">
						  朝
						</span><span data-v-7f1a77ef="" class="char" style="left: 32px;">
						  那
						</span><span data-v-7f1a77ef="" class="char" style="left: 48px;">
						  些
						</span><span data-v-7f1a77ef="" class="char" style="left: 64px;">
						  事
						</span><span data-v-7f1a77ef="" class="char" style="left: 80px;">
						  儿
						</span></h3>

	[{'text': '明', 'left': 0}, {'text': '朝', 'left': 16}, {'text': '那', 'left': 32}, {'text': '些', 'left': 48}, {'text': '事', 'left': 64}, {'text': '儿', 'left': 80}]

	清白家风
	法老的宠妃终结篇（上下册）
	士为知己（全二册）
	那些年，我们一起追的女孩
	非我倾城（全三册）
	明朝那些事儿
	我和你的笑忘书
	王小波全集第一卷
	怦然心动

	龙枪传奇（全三册）
	黎明之街
	认知心理学及其启示

	银河帝国：基地
	小学教材全解-四年级语文下
		
	
```
def parse_name(name_html):
    has_whole = name_html(".whole")
    if has_whole:
        return name_html.text()
    else:
        chars = name_html('.char')
        items = []
        for char in chars.items():
            items.append({
                'text':char.text().strip(),
                "left":int(re.search('(\d+)px',char.attr('style')).group(1))
            })
        items = sorted(items,key=lambda x:x['left'],reverse=False)
        return ''.join([item.get('text') for item in items])
```

	Wonder
	清白家风
	法老的宠妃终结篇（上下册）
	士为知己（全二册）
	那些年，我们一起追的女孩
	非我倾城（全三册）
	明朝那些事儿
	我和你的笑忘书
	王小波全集第一卷
	怦然心动
	龙枪编年史（全3册）
	龙枪传奇（全三册）
	黎明之街
	认知心理学及其启示
	银河帝国2：基地与帝国
	银河帝国：基地
	小学教材全解-四年级语文下
	越界言论（第3卷）
	
## 字体反爬案例分析与爬取实战
### 案例分析
```
from selenium import webdriver
from pyquery import PyQuery as pq
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.wait import WebDriverWait

browser = webdriver.Chrome()
browser.get("https://antispider4.scrape.center")
WebDriverWait(browser,10).until(EC.presence_of_element_located((By.CSS_SELECTOR,'.item')))
html = browser.page_source
doc = pq(html)
items = doc('.item')
for item in items.items():
    name = item(".name").text()
    categories = [o.text() for o in item('.categories button').items()]
    score = item('.score').text()
    print(f"name:{name},categories:{categories},score:{score}")

browser.close()
```

### 案例分析
```
import re
import requests
url = "https://antispider4.scrape.center/css/app.654ba59e.css"

response = requests.get(url)
pattern = re.compile('.icon-(.*?):before\{content:"(.*?)"\}')
results = re.findall(pattern,response.text)
icon_map = {item[0]:item[1] for item in results}
```

	{'afii10081': '\\43F', 'afii10079': '\\43D', 'afii10076': '\\43A', 'afii10072': '\\436', 'afii10084': '\\442', 'afii10077': '\\43B', 'afii10067': '\\432', 'afii10074': '\\438', 'afii10075': '\\439', 'afii10068': '\\433', 'afii10066': '\\431', 'afii10069': '\\434', 'afii10073': '\\437', 'afii10094': '\\44C', 'afii10088': '\\446', 'afii10089': '\\447', 'afii10097': '\\44F', 'afii10078': '\\43C', 'afii10100': '\\453', 'afii10101': '\\454', 'afii10095': '\\44D', 'afii10091': '\\449', 'afii10090': '\\448', 'afii10092': '\\44A', 'afii10093': '\\44B', 'afii10096': '\\44E', 'afii10086': '\\444', 'afii10145': '\\40F', 'afii10193': '\\45F', 'afii61352': '\\2116', 'afii10098': '\\491', 'afii10058': '\\409', 'afii10107': '\\45A', 'afii10106': '\\459', 'afii10059': '\\40A'}
	
```
print(icon_map['789'])
print(icon_map['437'])
```

	9
	3
	
```
from selenium import webdriver
from pyquery import PyQuery as pq
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.wait import WebDriverWait
import re
import requests
url = "https://antispider4.scrape.center/css/app.654ba59e.css"

response = requests.get(url)
pattern = re.compile('.icon-(.*?):before\{content:"(.*?)"\}')
results = re.findall(pattern,response.text)
icon_map = {item[0]:item[1] for item in results}

def parse_score(item):
    elements =item(".icon")
    icon_values = []
    for element in elements.items():
        class_name = (element.attr('class'))
        icon_key = re.search('icon-(\d+)',class_name).group(1)
        icon_value = icon_map.get(icon_key)
        icon_values.append(icon_value)
    return ''.join(icon_values)


browser = webdriver.Chrome()
browser.get("https://antispider4.scrape.center/")
WebDriverWait(browser,10).until(EC.presence_of_element_located((By.CSS_SELECTOR,'.item')))
html = browser.page_source
doc = pq(html)
items = doc(".item")
for item in items.items():
    name = item('.name').text()
    categories = [o.text() for o in item('.categories button').items()]
    score = parse_score(item)
    print(f"name:{name},categories:{categories},score:{score}")
browser.close()
```

	name:霸王别姬 - Farewell My Concubine,categories:['剧情', '爱情'],score:9.5
	name:这个杀手不太冷 - Léon,categories:['剧情', '动作', '犯罪'],score:9.5
	name:肖申克的救赎 - The Shawshank Redemption,categories:['剧情', '犯罪'],score:9.5
	name:泰坦尼克号 - Titanic,categories:['剧情', '爱情', '灾难'],score:9.5
	name:罗马假日 - Roman Holiday,categories:['剧情', '喜剧', '爱情'],score:9.5
	name:唐伯虎点秋香 - Flirting Scholar,categories:['喜剧', '爱情', '古装'],score:9.5
	name:乱世佳人 - Gone with the Wind,categories:['剧情', '爱情', '历史', '战争'],score:9.5
	name:喜剧之王 - The King of Comedy,categories:['剧情', '喜剧', '爱情'],score:9.5
	name:楚门的世界 - The Truman Show,categories:['剧情', '科幻'],score:9.0
	name:狮子王 - The Lion King,categories:['动画', '歌舞', '冒险'],score:9.0
	
