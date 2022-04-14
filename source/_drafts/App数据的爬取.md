---
title: App数据的爬取
date: 2022-03-14 15:21:25
tags:
---
## 前言
## Charles抓包工具的使用
### 本节目标
### 准备工作
### 抓包原理
### 实战抓包
### 分析
### 重发
### 修改响应内容
### 模拟爬取
```
import requests

BASE_URL = 'https://app1.scrape.center/api/movie?offset={offset}&limit=10'

for i in range(0,10):
    offset = i*10
    url = BASE_URL.format(offset=offset)
    data = requests.get(url).json()
    print('data',data)
```

## mitmproxy 抓包工具的使用
### mitmproxy的功能
### 准备工作
### 抓包原理
### 基本使用
`mitmproxy`
`ipconfig`
### 总结
## mitmdump实时抓包处理
### 实例引入
```
def request(flow):
    flow.request.headers['User-Agent'] = 'MitmProxy'
    print(flow.request.headers)
```
### 日志输出
```
from mitmproxy import ctx

def request(flow):
    flow.request.headers['User-Agent'] = 'MitmProxy'
    ctx.log.info(str(flow.request.headers))
    ctx.log.warn(str(flow.request.headers))
    ctx.log.error(str(flow.request.headers))
```
### 请求
```
from mitmproxy import ctx

def request(flow):
    request = flow.request
    info = ctx.log.info
    info(request.url)
    info(str(request.headers))
    info(str(request.cookies))
    info(request.host)
    info(request.method)
    info(str(request.host))
    info(request.scheme)
```

```
def request(flow):
    url = 'https://www.baidu.com'
    flow.request.url = url
```
### 响应
```
from mitmproxy import ctx

def response(flow):
    response = flow.response
    info = ctx.log.info
    info(str(response.status_code))
    info(str(response.headers))
    info(str(response.cookies))
    info(str(response.text))
```

### 实战准备
### 抓取分析
```
def response(flow):
    print(flow.request.url)
    print(flow.response.text)
```
### 数据抓取
```
from mitmproxy import ctx
import json
def response(flow):
    url = "https://app5.scrape.center/api/movie"
    if flow.request.url.startswith(url):
        text = flow.response.text
        if not text:
            return
        data = json.loads(text)
        items = data.get('results')
        for item in items:
            ctx.log.info(str(item))
```
### 提取保存
```
from mitmproxy import ctx
import json
import os

OUTPUT_FOLDER = 'movies'
os.path.exists(OUTPUT_FOLDER) or os.makedirs(OUTPUT_FOLDER)

def response(flow):
    url = "https://app5.scrape.center/api/movie"
    if flow.request.url.startswith(url):
        text = flow.response.text
        if not text:
            return
        data = json.loads(text)
        items = data.get('results')
        for item in items:
            ctx.log.info(str(item))
            with open(f'{OUTPUT_FOLDER}/{item["name"]}.json','w',encoding='utf-8') as f:
                f.write(json.dumps(item,ensure_ascii=False,indent=2))
                ctx.log.info("save successfully")
```

### 总结

## Appium的使用
### 准备工作
### Appium启动APP
```
server = 'http://127.0.0.1:4723/wd/hub'
```

```
desired_cappbilities = {
  "platformName": "Android",
  "deviceName": "f2fa67ac",
  "appPackage": "com.goldze.mvvmhabit",
  "appActivity": ".ui.MainActivity",
  "noReset": True
}
```

```
from appium import webdriver

driver = webdriver.Remote(server,desired_cappbilities)
```

```
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.ui import WebDriverWait

wait = WebDriverWait(driver,30)
wait.until(EC.presence_of_all_elements_located((By.XPATH,"//android.support.v7.widget.RecyclerView/android.widget.LinearLayout")))
window_size = driver.get_window_size()
width,height = window_size.get('width'),window_size.get("height")
driver.swipe(width*0.5,height*0.8,width*0.5,height*0.2,1000)
```

```
from appium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.ui import WebDriverWait
server = 'http://127.0.0.1:4723/wd/hub'

desired_cappbilities = {
  "platformName": "Android",
  "deviceName": "f2fa67ac",
  "appPackage": "com.goldze.mvvmhabit",
  "appActivity": ".ui.MainActivity",
  "noReset": True
}

driver = webdriver.Remote(server,desired_cappbilities)
wait = WebDriverWait(driver,30)
wait.until(EC.presence_of_all_elements_located((By.XPATH,"//android.support.v7.widget.RecyclerView/android.widget.LinearLayout")))
window_size = driver.get_window_size()
width,height = window_size.get('width'),window_size.get("height")
driver.swipe(width*0.5,height*0.8,width*0.5,height*0.2,1000)
```

### Appium 的相关API
#### 初始化
```
from appium import webdriver

server = 'http://127.0.0.1:4723/wd/hub'
desired_capabilities = {
  'platformName':'Android',
  'deviceName':'f2fa67ac',
  'appPackage':'com.goldze.mvvmhabit',
  'appActivity':'ui.MainActivity',
  'noReset':True
}
driver = webdriver.Remote(server,desired_capabilities)
```

#### 查找节点
`el = self.driver.find_element_by_android_uiautomator('new UiSelector().description("Animatio")')`

#### 点击屏幕
`tap(self,postion,duration=None)`

```
driver.tap([(100,20),(100,60),(100,100)],500)
```

```
button = find_element_by_id('<package>:id/<id>')
button.click
```

#### 屏幕滑动
`scroll(self,origin_el,destination_el)`

`driver.scroll(el1,el2)`

`swipe(self,start_x,start_y,end_x,end_y,duration=None)`

```
driver.swipe(100,100,100,400,5000)
```

`flick(self,start_x,start_y,end_x,end_y)`

```
driver.flick(100,1000,100,100)
```
#### 拖动
`drag_and_drop(self,origin_el,destination_el)`

```
driver.drag_and_drop(el1,el2)
```

#### 文本输入
`el = find_element_by_id('<package>:id/cjk')`
`el.set_text('Hello')`

#### 动作链
`el = self.driver.find_element_by_accessibility_id('Animation')`
`action = TouchAction(self.driver)`
`action.tap(el).perform()`

```
els = driver.find_element(By.CLASS_NAME,'listView')
a1 = TouchAction()
a1.press(els[0]).move_to(x=10,y=10).move_to(x=10,y=-75).move_to(x=10,y=-600).release()
a2 = TouchAction()
a2.press(els[1]).move_to(x=10,y=10).move_to(x=10,y=-300).move_to(x=10,y=-600).release()
```

### 基于Appium的APP爬取实战
#### 准备工作
`pip install loguru`
#### 思路分析
#### 基本实现
```
from appium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.ui import WebDriverWait
from selenium.common.exceptions import NoSuchElementException
from loguru import logger
import os
import json

SERVER = "http://127.0.0.1:4723/wd/hub"

DESURED_CAPABILITIES = {
    "platformName": "Android",
    "appium:deviceName": "f2fa67ac",
    "appium:appPackage": "com.goldze.mvvmhabit",
    "appium:appActivity": ".ui.MainActivity",
    "appium:noReset": True
}
PACKAGE_NAME = DESURED_CAPABILITIES['appium:appPackage']
TOTAL_NUMBER = 100
```
```
driver = webdriver.Remote(SERVER,DESURED_CAPABILITIES)
wait = WebDriverWait(driver,30)
window_size = driver.get_window_size()
window_width,windiw_height = window_size.get('width'),window_size.get('height')
```

```
def scrape_index():
  items = wait.until(EC.presence_of_all_elements_located((By.XPATH,f'//android.widget.LinearLayout[@resource-id="{PACKAGE_NAME}:id/item"]')))
  return items
```

```
def scrape_detail(element):
  logger.debug(f"scraping {element}")
  element.click()
  wait.until(EC.presence_of_element_located((By.ID,f"{PACKAGE_NAME}:id/detail")))
  title = wait.until(EC.presence_of_element_located((By.ID,f"{PACKAGE_NAME}:id/title"))).get_attribute('text')
  categories = wait.until(EC.presence_of_element_located((By.ID,f"{PACKAGE_NAME}:id/categories_value"))).get_attribute('text')
  score = wait.until(EC.presence_of_element_located((By.ID,f"{PACKAGE_NAME}:id/score_value"))).get_attribute('text')
  minute = wait.until(EC.presence_of_element_located((By.ID,f"{PACKAGE_NAME}:id/minute_value"))).get_attribute('text')
  published_at = wait.until(EC.presence_of_element_located((By.ID,f"{PACKAGE_NAME}:id/published_at_value"))).get_attribute('text')
  drama = wait.until(EC.presence_of_element_located((By.ID,f"{PACKAGE_NAME}:id/drama_value"))).get_attribute('text')
  return {
    'title':title,
    'categories':categories,
    'score':score,
    'minute':minute,
    'publish_at':published_at,
    'drama':drama
  }
```

```
OUTPUT_FOLDER = 'movie'
os.path.exists(OUTPUT_FOLDER) or os.makedirs(OUTPUT_FOLDER)
def save_data(element_data):
  with open(f"{OUTPUT_FOLDER}/{element_data.get('title')}.json",'w',encoding='utf-8') as f:
    f.write(json.dumps(element_data,ensure_ascii=False,indent=2))
    logger.debug(f'save as file {element_data.get("title")}.json')
scraped_titles = []
```

```
def get_element_title(element):
  try:
    element_title = element.find_element(By.ID,f'{PACKAGE_NAME}:id/tv_title').get_attribute('text')
    return element_title
  except NoSuchElementException:
    return None
```

```
def main():
  while len(scraped_titles)<TOTAL_NUMBER:
    elements = scrape_index()
    for element in elements:
      element_title = get_element_title(element)
      if not element_title or element_title in scraped_titles:
        continue
      element_location = element.location
      element_y = element_location.get('y')
      if element_y / windiw_height > 0.8:
        logger.debug('scroll up')
        scroll_up()
      element_data = scrape_detail(element)
      logger.debug(f'scraped data{element_data}')
      save_data(element_data)
          

if __name__ == '__main__':
  main()
```

```
from appium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.ui import WebDriverWait
from selenium.common.exceptions import NoSuchElementException
from loguru import logger
import os
import json
# 配置
SERVER = "http://127.0.0.1:4723/wd/hub"
DESURED_CAPABILITIES = {
    "platformName": "Android",
    "appium:deviceName": "f2fa67ac",
    "appium:appPackage": "com.goldze.mvvmhabit",
    "appium:appActivity": ".ui.MainActivity",
    "appium:noReset": True
}
PACKAGE_NAME = DESURED_CAPABILITIES['appium:appPackage']
TOTAL_NUMBER = 100 # 总电影数

driver = webdriver.Remote(SERVER,DESURED_CAPABILITIES)
wait = WebDriverWait(driver,30)
window_size = driver.get_window_size()
window_width,window_height = window_size.get('width'),window_size.get('height')

# 得到主页加载的各个点击框
def scrape_index():
    items = wait.until(EC.presence_of_all_elements_located(
        (By.XPATH, f'//android.widget.LinearLayout[@resource-id="{PACKAGE_NAME}:id/item"]')))
    return items

# 解析内容
def scrape_detail(element):
    logger.debug(f'scraping {element}')
    element.click()
    wait.until(EC.presence_of_element_located(
        (By.ID, f'{PACKAGE_NAME}:id/detail')))
    title = wait.until(EC.presence_of_element_located(
        (By.ID, f'{PACKAGE_NAME}:id/title'))).get_attribute('text')
    categories = wait.until(EC.presence_of_element_located(
        (By.ID, f'{PACKAGE_NAME}:id/categories_value'))).get_attribute('text')
    score = wait.until(EC.presence_of_element_located(
        (By.ID, f'{PACKAGE_NAME}:id/score_value'))).get_attribute('text')
    minute = wait.until(EC.presence_of_element_located(
        (By.ID, f'{PACKAGE_NAME}:id/minute_value'))).get_attribute('text')
    published_at = wait.until(EC.presence_of_element_located(
        (By.ID, f'{PACKAGE_NAME}:id/published_at_value'))).get_attribute('text')
    drama = wait.until(EC.presence_of_element_located(
        (By.ID, f'{PACKAGE_NAME}:id/drama_value'))).get_attribute('text')
    driver.back()
    return {
        'title': title,
        'categories': categories,
        'score': score,
        'minute': minute,
        'published_at': published_at,
        'drama': drama
    }

# 滑动主页面
def scroll_up():
  driver.swipe(window_width*0.5,window_height*0.8,window_width*0.5,window_height**0.5,1000)

# 判断是否重复
def get_element_title(element):
  try:
    element_title = element.find_element(By.ID,f'{PACKAGE_NAME}:id/tv_title').get_attribute('text')
    return element_title
  except NoSuchElementException:
    return None

OUTPUT_FOLDER = 'movie'
os.path.exists(OUTPUT_FOLDER) or os.makedirs(OUTPUT_FOLDER)

# 保存数据
def save_data(element_data):
  with open(f"{OUTPUT_FOLDER}/{element_data.get('title')}.json",'w',encoding='utf-8') as f:
    f.write(json.dumps(element_data,ensure_ascii=False,indent=2))
    logger.debug(f'save as file {element_data.get("title")}.json')
scraped_titles = []

def main():
    while len(scraped_titles) < TOTAL_NUMBER:
        elements = scrape_index()
        for element in elements:
            element_title = get_element_title(element)
            if not element_title or element_title in scraped_titles:
                continue
            element_location = element.location
            element_y = element_location.get('y')
            if element_y / window_height > 0.8:
                logger.debug(f'scroll up')
                scroll_up()
            element_data = scrape_detail(element)
            scraped_titles.append(element_title)
            logger.debug(f'scraped data {element_data}')
            save_data(element_data)


if __name__ == '__main__':
  main()
```

## Airtest 的使用
### Airtest 介绍
### AirtestIDE 体验
### Airtest 的图像识别与自动化控制
### Airtest 的相关API
`def init_device(platform="Android",uuid=None,**kwargs)`

```
from airtest.core.api import *

device = init_device('Android')
print(device)
```

	<airtest.core.android.android.Android object at 0x0000022199778460>

```
from airtest.core.api import *
from airtest.core.android import Android
import logging

logging.getLogger("airtest").setLevel(logging.WARNING)

device:Android = init_device('Android')
is_locked = device.is_locked()
print(f"id_locked:{is_locked}")
if is_locked:
    device.unlock()

device.wake()

app_list = device.list_app()
print(f"app_list:{app_list}")

uuid = device.uuid
print(f'uuid:{uuid}')

display_info = device.get_display_info()
print(f'display_info:{display_info}')

resolution = device.get_render_resolution()
print(f"resolution:{resolution}")

ip_address = device.get_ip_address()
print(f"ip address{ip_address}")

top_activity = device.get_top_activity()
print(f"top_activity:{top_activity}")

is_keyborder_shown = device.is_keyboard_shown()
print(f"is_keyboard_shown:{is_keyborder_shown}")
```

#### 获取当前设备
```
def device():
	return G.DEVICE
```

#### 获取所有设备
```
from airtest.core.api import *
from airtest.core.android import Android

init_device()

print(G.DEVICE_LIST)
uri = 'Android://127.0.0.1:5037/emulate-5554'
device:Android = connect_device(uri)
print(G.DEVICE_LIST)
```

#### 执行命令行
```
@logwrap
def shell(cmd):
	return G.DEVICE.shell(cmd)
```

```
from airtest.core.api import *
from airtest.core.android import Android

init_device("Android")

result = shell('cat /proc/meminfo')
print(result)
```

#### 启动和停止APP
```
@logwrap
def start_app(package):
	return G.DEVICE.start_app(package)
```
```
@logwrap
def stop_app(package):
	return G.DEVICE.stop_app(package)
```

```
from airtest.core.api import *

init_device("Android")

package = 'com.tencent.mm'
start_app(package)
sleep(20)
stop_app(package)
```

#### 安装和卸载App
```

```

#### 截图
`def snapshot(filename=None,msg="",qulity=ST.SNAPSHOT_QUALITY)`
```
from airtest.core.api import *

init_device('Android')

package = 'com.tencent.mm'
start_app(package)
sleep(3)
snapshot('weixin.png',quality=30)
```
#### 唤醒和回到首页
```
@logwrap
def wake():
	G.DEVICE.wake()
	
@logwrap
def home():
	G.DEVICE.home()
```

#### 点击屏幕
```
from airtest.core.api import *

init_device('Android')
touch(Template('tpl.png'))
```

```
from airtest.core.api import *

device = init_device('Android')
home()
touch((400,600),times=2)
```

#### 滑动
```
@logwrap
def swipe(v1,v2=None,vector=None,**kwargs)
```

```
from airtest.core.api import *

device = init_device('Android')
home()
swipe((900,300),(200,300))
```

#### 放大缩小
```
@logwrap
def pinch(in_or_out="in",center=None,percent=0.5)
```

```
from airtest.core.api import *

device = init_device('Android')
home()
pinch(in_or_out='out',center=(300,300),percent=0.4)
```

#### 键盘事件
`def keyevent(keyname,**kwargs)`
`keyevent('HOME')`
#### 输入内容
```
@logwrap
def text(text,enter=True,**kwargs)
```
### 基于Poco的UI组件自动化控制
```
from poco.drivers.android.uiautomation import AndroidUiautomationPoco
poco = AndroidUiautomationPoco(use_airtest_input=True, screenshot_each_action=False)
```

```
def __call__(self,name=None,**kw):
	if not name and len(kw) ==0:
		warnings.warn("........")
	return UIObjectProxy(self,name,**kw)
```

```
poco("VMOS Pro").click()
```

```
poco(text="搜索或输入网址").wait_for_appearance(10)
```

```
poco(text="跟着总书记上好这门课").swipe([0,-0.1])
```

```
# -*- encoding=utf8 -*-
__author__ = "DELL"

from airtest.core.api import *

auto_setup(__file__)
keyevent("HOME")
from poco.drivers.android.uiautomation import AndroidUiautomationPoco
poco = AndroidUiautomationPoco(use_airtest_input=True, screenshot_each_action=False)
poco("android.widget.FrameLayout").offspring("android:id/content").offspring("com.miui.home:id/workspace").offspring("百度")[0].offspring("com.miui.home:id/icon_icon").click()
poco(text="搜索或输入网址").wait_for_appearance(10)
poco(text="跟着总书记上好这门课").swipe([0,-0.1])
keyevent("HOME")
```

### 基于Airtest的APP爬取实战
```
from airtest.core.api import *
from poco.drivers.android.uiautomation import AndroidUiautomationPoco
from loguru import logger
import json
import os

poco = AndroidUiautomationPoco(
    use_airtest_input=True, screenshot_each_action=False)
window_width, window_height = poco.get_screen_size()

PACKAGE_NAME = 'com.goldze.mvvmhabit'
TOTAL_NUMBER = 100
init_device("Android")
stop_app(PACKAGE_NAME)
start_app(PACKAGE_NAME)

scraped_titles = []

OUTPUT_FOLDER = 'movie'
os.path.exists(OUTPUT_FOLDER) or os.makedirs(OUTPUT_FOLDER)


def save_data(element_data):
    with open(f'{OUTPUT_FOLDER}/{element_data.get("title")}.json', 'w', encoding='utf-8') as f:
        f.write(json.dumps(element_data, ensure_ascii=False, indent=2))
        logger.debug(f'saved as file {element_data.get("title")}.json')


def scrape_index():
    elements = poco(f'{PACKAGE_NAME}:id/item')
    elements.wait_for_appearance()
    return elements


def scrape_detail(element):
    element.click()
    panel = poco(f'{PACKAGE_NAME}:id/content')
    panel.wait_for_appearance(5)
    title = poco(f'{PACKAGE_NAME}:id/title').attr('text')
    categories = poco(f'{PACKAGE_NAME}:id/categories_value').attr('text')
    score = poco(f'{PACKAGE_NAME}:id/score_value').attr('text')
    published_at = poco(f'{PACKAGE_NAME}:id/published_at_value').attr('text')
    drama = poco(f'{PACKAGE_NAME}:id/drama_value').attr('text')
    keyevent('BACK')
    return {
        'title': title,
        'categories': categories,
        'score': score,
        'published_at': published_at,
        'drama': drama
    }


def scroll_up():
    swipe((window_width * 0.5, window_height * 0.8),
          vector=[0, -0.5], duration=1)


def main():
    while len(scraped_titles) < TOTAL_NUMBER:
        elements = scrape_index()
        for element in elements:
            element_title = element.offspring(f'{PACKAGE_NAME}:id/tv_title')
            if not element_title.exists():
                continue
            title = element_title.attr('text')
            logger.debug(f'get title {title}')
            if title in scraped_titles:
                continue
            _, element_y = element.get_position()
            if element_y > 0.7:
                scroll_up()
            element_data = scrape_detail(element)
            logger.debug(f'scraped data {element_data}')
            save_data(element_data)
            scraped_titles.append(title)


if __name__ == '__main__':
    main()
```
