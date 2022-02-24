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