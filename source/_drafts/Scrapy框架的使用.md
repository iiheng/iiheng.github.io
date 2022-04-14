---
title: Scrapy框架的使用
date: 2022-03-22 16:39:16
tags:
---
## 前言
## Scrapy 框架介绍
### 简介
### 架构
### 数据流
### 项目结构
`pip install scrapy`
`scrapy startproject news`
`scrapy genspider sina news.sina.com.cn`
## Scrapy入门
### 本节目标
### 准备工作
### 创建项目
```
scrapy startproject scrapytutorial
```

### 创建Spider
`cd scrapytutorial`
`scrapy genspider quotes`

```
class QuotesSpider(scrapy.Spider):
    name = 'quotes'
    allowed_domains = ['quotes.toscrape.com']
    start_urls = ['http://quotes.toscrape.com/']

    def parse(self, response):
		pass
```

### 创建Item
```
class QuoteItem(scrapy.Item):
    text = scrapy.Field()
    author = scrapy.Field()
    tags = scrapy.Field()
```

### 解析Response
```
    def parse(self, response):
        quotes = response.css('.quote')
        for quote in quotes:
            item['text'] = quote.css('.text::text').extract_first()
            item['author'] = quote.css('.author::text').extract_first()
            item['tags'] = quote.css('.tags .tag::text').extract_first()
```

### 使用Item
```
import scrapy
from ..items import QuoteItem

class QuotesSpider(scrapy.Spider):
    name = 'quotes'
    allowed_domains = ['quotes.toscrape.com']
    start_urls = ['http://quotes.toscrape.com/']

    def parse(self, response):
        quotes = response.css('.quote')
        for quote in quotes:
            item = QuoteItem()
            item['text'] = quote.css('.text::text').extract_first()
            item['author'] = quote.css('.author::text').extract_first()
            item['tags'] = quote.css('.tags .tag::text').extract_first()
            yield item
```

### 后续Request
```
next = response.css('.pager .next a::attr("href")').extract_first()
        url = response.urljoin(next)
        yield scrapy.Request(url=url,callback=self.parse)
```

```
import scrapy
from ..items import QuoteItem


class QuotesSpider(scrapy.Spider):
    name = 'quotes'
    allowed_domains = ['quotes.toscrape.com']
    start_urls = ['http://quotes.toscrape.com/']

    def parse(self, response):
        quotes = response.css('.quote')
        for quote in quotes:
            item = QuoteItem()
            item['text'] = quote.css('.text::text').extract_first()
            item['author'] = quote.css('.author::text').extract_first()
            item['tags'] = quote.css('.tags .tag::text').extract_first()
            yield item

        next = response.css('.pager .next a::attr("href")').extract_first()
        url = response.urljoin(next)
        yield scrapy.Request(url=url,callback=self.parse)
```
### 运行
`scrapy crawl quotes`

### 保存到文件
`scrapy crawl quotes -o quotes.json` 保存为json文件
`scrapy crawl quotes -o quotes.jl` 保存为jsonline
`scrapy crawl quotes -o quotes.csv` 保存为xml文件
`scrapy crawl quotes -o quotes.xml` 保存为pickle文件
`scrapy crawl quotes -o quotes.marshal` 保存为marshal文件
`scrapy crawl quotes -o ftp://user:pass@ftp.example.com/path.to.quotes.csv` ftp远程输出

### 使用Item Pipeline
```
from scrapy.exceptions import DropItem

class TextPipeline(object):
    def __init__(self):
        self.limit = 50
    def process_item(self,item,spider):
        if item['text']:
            if len(item['text']) > self.limit:
                item['text'] = item['text'][0:self.limit].rstrip() + '...'
                return item
            else:
                return DropItem('Missing Text')
```
```
class MongoDBPipeline(object):
    def __init__(self,connection_string,database):
        self.connection_string = connection_string
        self.database = database

    @classmethod
    def from_crawler(cls,crawler):
        return cls(
            connection_string=crawler.settings.get('MONGODB_CONNECTION_STRING'),
            database=crawler.settings.get('MONGODB_DATABASE')
        )

    def open_spider(self,spider):
        self.client = pymongo.MongoClient(self.connection_string)
        self.db = self.client[self.database]

    def process_item(self,item,spider):
        name = item.__class__.__name__
        self.db[name].insert(dict(item))
        return item

    def close_spider(self,spider):
        self.client.close()
```

```
ITEM_PIPELINES = {
    'scrapytutorial.pipelines.TextPipeline':300,
    'scrapytutorial.pipelines.MongoDBPipeline':400
}
MONGODB_CONNECTION_STRING = 'localhost'
MONGODB_DATABASE = 'scrapytutorial'
```

### 总结
## Selector 的使用
### 直接使用
```
from scrapy import Selector

body = '<html><head><title>Hello World</title></head><body></body></html>'
selector = Selector(text=body)
title = selector.xpath('//title/text()').extract_first()
print(title)
```
### Scrapy Shell
`scrapy shell https://doc.scrapy.org/en/latest/_static/selectors-sample1.html`
### Xpath 选择器
`>>> scrapy shell https://doc.scrapy.org/en/latest/_static/selectors-sample1.html`
`>>> result`
`>>> type(result)`
`>>> result.extract()`
`>>> response.xpath('//a/@href').extract()`
`>>> response.xpath('//a/text()').extract()`
`>>> response.xpath('//a/text()').extract()[0]`
`>>> response.xpath('//a/text()').extract_first()`
`>>> response.xpath('//a[href="image1.html"]/text()').extract_first()`
`>>> response.xpath('//a[href="image1.html"]/text()').extract_first('default image')`
### CSS选择器
`>>> response.css('a')`
`>>> response.css('a').extract()`
`>>> response.css('a[href="image1.html"] img').extract()`
`>>> response.css('a[href="image1.html"]::text').extract_first()`
`>>> response.css('a[href="image1.html"] img::attr(src)').extract()`
`>>> response.xpath('//a').css('img').xpath('@src').extract()`

### 正则匹配
`>>> response.xpath('//a/text()').re('Name:\s(.*)')`
`>>> response.xpath('//a/text()').re('(.*?):\s(.*)')`
`>>> response.xpath('//a/text()').re_first('(.*?):\s(.*)')`
`>>> response.xpath('//a/text()').re_first('Name:\s(.*)')`
`>>> response.re((.*))`
### Spider 的使用
```
import scrapy


class HttpbinSpider(scrapy.Spider):
    name = 'httpbin'
    allowed_domains = ['www.httpbin.org']
    start_urls = ['https://www.httpbin.org/get']

    def parse(self, response):
        print('url',response.url)
        print('request',response.request)
        print('status',response.status)
        print('headers',response.headers)
        print('text',response.text)
        print('meta',response.meta)
```

	2022-03-23 00:07:50 [scrapy.core.engine] DEBUG: Crawled (200) <GET https://www.httpbin.org
	/get> (referer: None)
	url https://www.httpbin.org/get
	request <GET https://www.httpbin.org/get>
	status 200
	headers {b'Content-Length': [b'415'], b'Date': [b'Tue, 22 Mar 2022 16:07:49 GMT'], b'Conte
	nt-Type': [b'application/json'], b'Server': [b'gunicorn/19.9.0'], b'Access-Control-Allow-O
	rigin': [b'*'], b'Access-Control-Allow-Credentials': [b'true']}
	text {
	  "args": {},
	  "headers": {
		"Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
		"Accept-Encoding": "gzip, deflate",
		"Accept-Language": "en",
		"Host": "www.httpbin.org",
		"User-Agent": "Scrapy/2.6.1 (+https://scrapy.org)",
		"X-Amzn-Trace-Id": "Root=1-6239f455-3658007e72b6df797f3d7673"
	  },
	  "origin": "36.28.10.76",
	  "url": "https://www.httpbin.org/get"
	}

	meta {'download_timeout': 180.0, 'download_slot': 'www.httpbin.org', 'download_latency': 0
	.23965787887573242}
	2022-03-23 00:07:50 [scrapy.core.engine] INFO: Closing spider (finished)

    def start_requests(self):
        for url in self.start_urls:
            yield Request(url,dont_filter=True)
			
```
import scrapy
from scrapy import Request


class HttpbinSpider(scrapy.Spider):
    name = 'httpbin'
    allowed_domains = ['www.httpbin.org']
    start_urls = 'https://www.httpbin.org/get'
    headers ={
        'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.74 Safari/537.36 Edg/99.0.1150.46',
    }
    cookies = {
        'name':'iheng',
        'age':25
    }
    def start_requests(self):
        for offset in range(5):
            url = self.start_urls+f"?offset={offset}"
            yield Request(url,headers=self.headers,cookies=self.cookies,callback=self.parse_response,
                          meta={'offset':offset})

    def parse_response(self,response):
        print('url',response.url)
        print('request',response.requset)
        print('status',response.status)
        print('headers',response.headers)
        print('text',response.text)
        print('meta',response.meta)
```

	url https://www.httpbin.org/get?offset=3
	request <GET https://www.httpbin.org/get?offset=3>
	status 200
	headers {b'Content-Length': [b'579'], b'Date': [b'Tue, 22 Mar 2022 16:21:15 GMT'], b'Content-Type': [b'application/j
	son'], b'Server': [b'gunicorn/19.9.0'], b'Access-Control-Allow-Origin': [b'*'], b'Access-Control-Allow-Credentials':
	 [b'true']}
	text {
	  "args": {
		"offset": "3"
	  },
	  "headers": {
		"Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
		"Accept-Encoding": "gzip, deflate",
		"Accept-Language": "en",
		"Cookie": "name=iheng; age=25",
		"Host": "www.httpbin.org",
		"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844
	.74 Safari/537.36 Edg/99.0.1150.46",
		"X-Amzn-Trace-Id": "Root=1-6239f77b-6077f64712a1a65315fa326b"
	  },
	  "origin": "36.28.10.76",
	  "url": "https://www.httpbin.org/get?offset=3"
	}

	meta {'offset': 3, 'download_timeout': 180.0, 'download_slot': 'www.httpbin.org', 'download_latency': 1.048996448516
	8457}
	
```
import scrapy
from scrapy.http import JsonRequest,FormRequest

class HttpbinSpider(scrapy.Spider):
    name = 'httpbin'
    allowed_domains = ['www.httpbin.org']
    start_urls = 'https://www.httpbin.org/post'
    data = {'name':'iheng','age':'25'}
    def start_requests(self):
        yield FormRequest(self.start_urls,callback=self.parse_response,formdata=self.data)
        yield JsonRequest(self.start_urls,callback=self.parse_response,data=self.data)

    def parse_response(self,response):
        print('text',response.text)
```

### Request和Response
#### Request
#### Response
#### 总结
## Downloader Middleware 的使用
### 使用说明
### 核心方法
### 项目实战
```
import scrapy

class HttpbinSpider(scrapy.Spider):
    name = 'httpbin'
    allowed_domains = ['www.httpbin.org']
    start_urls = ['http://www.httpbin.org/get']

    def parse(self, response):
        print(response.text)
```
```
import random

class RandomUserAgentMiddleware(object):
    def __init__(self):
        self.user_agents = [
            'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.74 Safari/537.36 Edg/99.0.1150.46',
            'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.102 Safari/537.36',
            'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:97.0) Gecko/20100101 Firefox/97.0'
        ]
    def process_request(self,request,spider):
        request.headers['User-Agent'] = random.choice(self.user_agents)
```

```
class ProxyMiddleware(object):
    def process_request(self,request,spider):
        request.meta['proxy'] = 'http://127.0.0.1:7890'
```
```
DOWNLOADER_MIDDLEWARES = {
   'scrapydownloadermiddlewaredemo.middlewares.RandomUserAgentMiddleware': 543,
   'scrapydownloadermiddlewaredemo.middlewares.ProxyMiddleware': 544,
}
```

```
class ProxyMiddleware(object):
    def process_request(self,request,spider):
        request.meta['proxy'] = 'http://127.0.0.1:7890'
        return request
```

```
class ProxyMiddleware(object):
    def process_request(self,request,spider):
        return HtmlResponse(
            url=request.url,
            status=200,
            encoding='utf-8',
            body='Test Downloader Middleware'
        )
```

	Test Downloader Middleware

```
class ChangeResponseMiddleware(object):
    def process_response(self,request,response,spider):
        response.status = 201
        return response
```

```
print('Status Code',response.status)
```

```
DOWNLOADER_MIDDLEWARES = {
   'scrapydownloadermiddlewaredemo.middlewares.RandomUserAgentMiddleware': 543,
   'scrapydownloadermiddlewaredemo.middlewares.ChangeResponseMiddleware': 544,
}
```

	Status Code 201

### 总结
## Spider Middleware 的使用
### 使用说明
### 核心方法
### 实战
```
import scrapy
from scrapy import Request

from ..items import DemoItem


class HttpbinSpider(scrapy.Spider):
    name = 'httpbin'
    allowed_domains = ['www.httpbin.org']
    start_url = 'http://www.httpbin.org/get'
    def start_requests(self):
        for i in range(5):
            url = f"{self.start_url}?query={i}"
            yield Request(url,callback=self.parse)
    def parse(self, response):
		print(response.text)
```

```
import scrapy

class DemoItem(scrapy.Item):
    origin = scrapy.Field()
    headers = scrapy.Field()
    args = scrapy.Field()
    url = scrapy.Field()
```

```
def parse(self, response):
	item = DemoItem(**response.json())
	yield item
```

	{'args': {'query': '1'},
	 'headers': {'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
				 'Accept-Encoding': 'gzip, deflate',
				 'Accept-Language': 'en',
				 'Host': 'www.httpbin.org',
				 'User-Agent': 'Scrapy/2.6.1 (+https://scrapy.org)',
				 'X-Amzn-Trace-Id': 'Root=1-623ac48a-3a4689a53422e79c5f649f9d'},
	 'origin': '178.79.136.219',
	 'url': 'http://www.httpbin.org/get?query=1'}

```
class CustomizeMiddleware(object):

    def process_start_requests(self,start_request,spider):
        for request in start_request:
            url = request.url
            url += '&name=iheng'
            request = request.replace(url=url)
            yield request
```

```
SPIDER_MIDDLEWARES = {
   'scrapyspidermiddlewaredemo.middlewares.CustomizeMiddleware': 543,
}
```

	2022-03-24 15:09:48 [scrapy.core.scraper] DEBUG: Scraped from <201 http://www.httpbin.org/get?query=3&name=iheng>
	{'args': {'name': 'iheng', 'query': '3'},
	 'headers': {'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
				 'Accept-Encoding': 'gzip, deflate',
				 'Accept-Language': 'en',
				 'Host': 'www.httpbin.org',
				 'User-Agent': 'Scrapy/2.6.1 (+https://scrapy.org)',
				 'X-Amzn-Trace-Id': 'Root=1-623c193c-4b1823b65001c14977a32b21'},
	 'origin': None,
	 'url': 'http://www.httpbin.org/get?query=3&name=iheng'}
	status: 201

#### HttpErrorMiddleware
#### OffsiteMiddleware
#### UrlLengthMiddleware
### 总结
## Item Pipeline 的使用
### 核心方法
#### process_item(item,spider)
#### open_spider(self,spider)
#### close_spider(spider)
#### form_crawler(cls,crawler)
### 本节目标
### 实战
`scrapy startproject scrapyitempipelinedemo`
`scrapy genspider scrape ssr1.scrape.center`
```
import scrapy
from scrapy import Request

class ScrapeSpider(scrapy.Spider):
    name = 'scrape'
    allowed_domains = ['ssr1.scrape.center']
    base_url = 'https://ssr1.scrape.center'
    max_page = 10
    def start_requests(self):
        for i in range(1,self.max_page+1):
            url = f"{self.base_url}/page/{i}"
            yield Request(url,callback=self.parse_index)

    def parse_index(self,response):
        print(response)
```
```
# Define here the models for your scraped items
#
# See documentation in:
# https://docs.scrapy.org/en/latest/topics/items.html

from scrapy import Item, Field


class MovieItem(Item):
    mongodb_collection_name = 'movies'

    name = Field()
    categories = Field()
    drama = Field()
    score = Field()
    directors = Field()
    actors = Field()
```

```
def parse_detail(self, response):
	item = MovieItem()
	item['name'] = response.xpath('//div[contains(@class, "item")]//h2/text()').extract_first()
	item['categories'] = response.xpath('//button[contains(@class, "category")]/span/text()').extract()
	item['score'] = response.css('.score::text').re_first('[\d\.]+')
	item['drama'] = response.css('.drama p::text').extract_first().strip()
	item['directors'] = []
	directors = response.xpath('//div[contains(@class, "directors")]//div[contains(@class, "director")]')
	for director in directors:
		director_image = director.xpath('.//img[@class="image"]/@src').extract_first()
		director_name = director.xpath('.//p[contains(@class, "name")]/text()').extract_first()
		item['directors'].append({
			'name': director_name,
			'image': director_image
		})
	item['actors'] = []
	actors = response.css('.actors .actor')
	for actor in actors:
		actor_image = actor.css('.actor .image::attr(src)').extract_first()
		actor_name = actor.css('.actor .name::text').extract_first()
		item['actors'].append({
			'name': actor_name,
			'image': actor_image
		})
	yield item
```
#### MongiDB
```
import pymongo

class MongoDBPipeline(object):

    @classmethod
    def from_crawler(cls, crawler):
        cls.connection_string = crawler.settings.get('MONGODB_CONNECTION_STRING')
        cls.database = crawler.settings.get('MONGODB_DATABASE')
        cls.collection = crawler.settings.get('MONGODB_COLLECTION')
        return cls()

    def open_spider(self, spider):
        self.client = pymongo.MongoClient(self.connection_string)
        self.db = self.client[self.database]

    def process_item(self, item, spider):
        self.db[self.collection].update_one({
            'name': item['name']
        }, {
            '$set': dict(item)
        }, True)
        return item

    def close_spider(self, spider):
        self.client.close()
```

```
MONGODB_CONNECTION_STRING = 'mongodb://localhost:27017'
MONGODB_DATABASE = 'movies'
MONGODB_COLLECTION = 'movies'
```

```
class ElasticsearchPipeline(object):

    @classmethod
    def from_crawler(cls, crawler):
        cls.connection_string = crawler.settings.get('ELASTICSEARCH_CONNECTION_STRING')
        cls.index = crawler.settings.get('ELASTICSEARCH_INDEX')
        return cls()

    def open_spider(self, spider):
        self.conn = Elasticsearch([self.connection_string])
        if not self.conn.indices.exists(self.index):
            self.conn.indices.create(index=self.index)

    def process_item(self, item, spider):
        self.conn.index(index=self.index, body=dict(item), id=hash(item['name']))
        return item

    def close_spider(self, spider):
        self.conn.transport.close()
```

```
ELASTICSEARCH_CONNECTION_STRING = 'http://127.0.0.1:9200'
ELASTICSEARCH_INDEX = 'movies'
```

#### Image Pipeline
```
IMAGES_STORE = './images'
```

```
from scrapy import Request
from scrapy.exceptions import DropItem
from scrapy.pipelines.images import ImagesPipeline


class ImagePipeline(ImagesPipeline):
    def file_path(self, request, response=None, info=None):
        movie = request.meta['movie']
        type = request.meta['type']
        name = request.meta['name']
        file_name = f'{movie}/{type}/{name}.jpg'
        return file_name

    def item_completed(self, results, item, info):
        image_paths = [x['path'] for ok, x in results if ok]
        if not image_paths:
            raise DropItem('Image Downloaded Failed')
        return item

    def get_media_requests(self, item, info):
        for director in item['directors']:
            director_name = director['name']
            director_image = director['image']
            yield Request(director_image, meta={
                'name': director_name,
                'type': 'director',
                'movie': item['name']
            })

        for actor in item['actors']:
            actor_name = actor['name']
            actor_image = actor['image']
            yield Request(actor_image, meta={
                'name': actor_name,
                'type': 'actor',
                'movie': item['name']
            })
```

```
ITEM_PIPELINES = {
    'scrapyitempipelinedemo.pipelines.ImagePipeline': 300,
    'scrapyitempipelinedemo.pipelines.MongoDBPipeline': 301,
    # 'scrapyitempipelinedemo.pipelines.ElasticsearchPipeline': 302,
}
```

### 总结

## Extension的使用
### extension 介绍
### 准备工作
`pip install flask requests loguru`
### 实战
```
from flask import Flask,request,jsonify
from loguru import logger

app = Flask(__name__)

@app.route('/notify',methods=['post'])
def receive():
    post_data = request.get_json()
    event = post_data.get('event')
    data = post_data.get('data')
    logger.debug(f"received event {event},data {data}")
    return jsonify(status='success')

if __name__=="__main__":
    app.run(debug=True,host='0.0.0.0',port=5000)
```

`python3 server.py`

```
import requests
from scrapy import signals

NOTIFICATION_URL = 'http://127.0.0.1:5000/notify'

class NotificationExtension(object):
    @classmethod
    def from_crawler(cls,crawler):
        ext = cls()
        crawler.signals.connect(ext.spider_opened,signal=signals.spider_opened)
        crawler.signals.connect(ext.spider_closed,signal=signals.spider_closed)
        crawler.signals.connect(ext.item_scraped,signal=signals.item_scraped)
        return ext
    def spider_opened(self,spider):
        requests.post(NOTIFICATION_URL,json={
            'event':'SPIDER_OPENED',
            'data':{'spider_name':spider.name}
        })

    def spider_closed(self,spider):
        requests.post(NOTIFICATION_URL,json={
            'event':'SPIDER_CLOSED',
            'data':{'spider_name':spider.name}
        })

    def item_scraped(self,item,spider):
        requests.post(NOTIFICATION_URL,json={
            'event':'ITEM_SCRAPED',
            'data':{'spider_name':spider.name,'item':dict(item)}
        })
```

```
EXTENSIONS = {
    'scrapytutorial.extensions.NotificationExtension':100
}
```

## Scrapy对接Selenium
### 本节目标
### 准备工作
### 对接原理
### 实战对接
`scrapy startproject scrapyseleniumdemo`

```
import scrapy
from scrapy import Field


class ScrapyseleniumdemoItem(scrapy.Item):
    name = Field()
    tags = Field()
    score = Field()
    cover = Field()
    price = Field()
```

```
import scrapy
from scrapy import Request

class BookSpider(scrapy.Spider):
    name = 'book'
    allowed_domains = ['spa5.scrape.center']
    base_url = 'http://spa5.scrape.center'

    def start_requests(self):
        start_url = f"{self.base_url}/page/1"
        yield Request(start_url,callback=self.parse_index)
```

```
def parse_index(self,response):
	items = response.css('.item')
	for item in items:
		href = item.css('.top a::attr(href)').extract_first()
		detail_url = response.urljson(href)
		yield Request(detail_url,callback=self.parse_detail,priority=2)

	match = re.search(r"page/(\d+)",response.url)
	if not match:return
	page = int(match.group(1))+1
	next_url = f"{self.base_url}/page/{page}"
	yield  Request(next_url,callback=self.parse_index)
```

```
def parse_detail(self,response):
	name = response.css('.name::text').extract_first()
	tags = response.css('.tags button span::text').extract()
	score = response.css('.score::text').extract_first()
	price = response.css('.price span::text').extract_first()
	cover = response.css('.cover::attr(src)').extract_first()
	item = BookItem(name=name,tags=tags,score=score,price=price,cover=cover)
	yield item
```

```
class SeleniumMiddleware(object):

    def process_request(self,request,spider):
        url = request.url
        browser = webdriver.Chrome()
        browser.get(url)
        time.sleep(5)
        html = browser.page_source
        browser.close()
        return HtmlResponse(url=request.url,body=html,request=request,encoding='utf-8',status=200)
```

### 对接优化
`class SeleniumMiddleware(object):`

```
DOWNLOADER_MIDDLEWARES = {
   'gerapy_selenium.downloadermiddlewares.SeleniumMiddleware': 543,
}

COUNCURRENT_REQUESTS = 6
```

`yield SeleniumRequest(start_url,callback=self.parse_index,wait_for='.item .name')`

## Scrapy 对接 Splash

### 准备工作
### 对接原理
### 对接实战
`scrapy startproject scrapysplashdemo`
`scrapy genspider book spa5.scrape.center`
```
import scrapy
from scrapy import Field

class ScrapysplashdemoItem(scrapy.Item):
    name = Field()
    tags = Field()
    score = Field()
    cover = Field()
    price = Field()
```

## Scrapy 规则化爬虫
### CrawlSpider
### LinkExtractor
### Item Loaders
#### Indentity
#### TakeFirst
#### Join
#### Compose
#### MapCompose
#### SelectJmes
### 本节目标
### 实战
`scrapy startproject scrapyyuniversaldemo`
`scrapy genspider -l`

	Available templates:
	  basic
	  crawl
	  csvfeed
	  xmlfeed

`scrapy genspider -t crawl movie ssr1.scrape.center`

	import scrapy
	from scrapy.linkextractors import LinkExtractor
	from scrapy.spiders import CrawlSpider, Rule

	class MovieSpider(CrawlSpider):
		name = 'movie'
		allowed_domains = ['ssr1.scrape.center']
		start_urls = ['http://ssr1.scrape.center/']

		rules = (
			Rule(LinkExtractor(allow=r'Items/'), callback='parse_item', follow=True),
		)

		def parse_item(self, response):
			item = {}
			#item['domain_id'] = response.xpath('//input[@id="sid"]/@value').get()
			#item['name'] = response.xpath('//div[@id="name"]').get()
			#item['description'] = response.xpath('//div[@id="description"]').get()
			return item


```
rules = (
        Rule(LinkExtractor(restrict_css='.item .name'), callback='parse_item', follow=True),
    )
```

	2022-03-26 15:59:46 [scrapy.core.engine] DEBUG: Crawled (200) <GET https://ssr1.scrape.center/detail/1> (referer: https://ssr1.scrape.cen
	ter/)
	https://ssr1.scrape.center/detail/3
	https://ssr1.scrape.center/detail/7
	https://ssr1.scrape.center/detail/2
	https://ssr1.scrape.center/detail/1

```
def parse_item(self, response):
	item = MovieItem()
	item['name'] = response.css('.item h2::text').extract_first()
	item['categories'] = response.css('.categories button span::text').extract()
	item['cover'] = response.css(".cover::attr(src)").extract_first()
	item['published_at'] = response.css('.info span::text').re_first('(\d{4}-\d{2}-\d{2})\s?上映')
	item['score'] = response.xpath('//p[contains(@class,"score")]/text()').extract_first().strip()
	item['drama'] = response.xpath('//div[contains(@class,"drama")]/p/text()').extract_first().strip()
	yield item
```

```
def parse_item(self, response):
	loader = MovieItemLoader(item=MovieItem(),response=response)
	loader.add_css('name','.item h2::text')
	loader.add_css('categories','.categories button span::text')
	loader.add_css('cover','.cover::attr(src)')
	loader.add_css('published_at','.info span::text',re='(\d{4}-\d{2}-\d{2})\s?上映')
	loader.add_xpath('score','//p[contains(@class,"score")]/text()')
	loader.add_xpath('drama','//div[contains(@class,"drama")]/p/text()')
	yield loader.load_item()
```

```
from scrapy.loader import ItemLoader
from scrapy.loader.processors import Identity, TakeFirst, Compose

class MovieItemLoader(ItemLoader):
    default_output_processor = TakeFirst()
    categories_out = Identity()
    score_out = Compose(TakeFirst(),str.strip)
    drama_out = Compose(TakeFirst(),str.strip)
```
### 配置抽取
```
{
  "spider": "universal",
  "type": "电影",
  "home": "https://ssr1.scrape.center",
  "settings": {
    "USER_AGENT": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/99.0.4844.74 Safari/537.36 Edg/99.0.1150.46"
  },
  "start_urls": ["https://ssr1.scrape.center"],
  "allowed_domains": ["ssr1.scrape.center"],
  "rules": [{
    "link_extractor": {
      "restrict_css": ".item .name"
    },
    "follow": true,
    "callback": "parse_detail"
  },{
    "link_extractor": {
      "restrict_css": ".next"
    },
    "follow": true
  }]
}
```

```
from os.path import realpath,dirname,join
import json

def get_config(name):
    path = join(dirname(realpath(__file__)),'configs',f'{name}.json')
    with open(path,'r',encoding='utf-8') as f:
        return json.loads(f.read())
```

```
from scrapy.utils.project import get_project_settings
from scrapyyuniversaldemo.utils import get_config
from scrapy.crawler import CrawlerProcess
import argparse

parser = argparse.ArgumentParser(description='Universal Spider')
parser.add_argument('name',help='name of spider to run')
args = parser.parse_args()
name = args.name

def run():
    config = get_config(name)
    spider = config.get('spider','universal')
    project_settings = get_project_settings()
    settings = dict(project_settings.copy())
    settings.update(config.get('settings'))
    process = CrawlerProcess(settings)
    process.crawl(spider,**{'name':name})
    process.start()

if __name__ == '__main__':
    run()
```

```
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
from scrapy.utils.conf import get_config


class UniversalSpider(CrawlSpider):
    name = 'universal'

    def __init__(self,name,*args,**kwargs):
        config = get_config()
        self.config = config
        self.start_urls = config.get('start_urls')
        self.allowed_domains = config.get('ALLOWED_DOMAINS')
        rules = []
        for rule_kwargs in config.get('rules'):
            link_extractor = LinkExtractor(**rule_kwargs.get('link_extractor'))
            rule_kwargs['link_extractor'] = link_extractor
            rule = Rule(**rule_kwargs)
            rules.append(rule)
        self.rules = rules
        super(UniversalSpider,self).__init__(*args,**kwargs)
```

```
def parse_detail(self,response):
	item = self.config.get('item')
	if item:
		cls = getattr(items,item.get('class'))()
		loader = getattr(loaders,item.get('loader'))(cls,response=response)
		for key,value in item.get('attrs').items():
			for extractor in value:
				if extractor.get('method') == "xpath":
					loader.add_xpath(key,extractor.get('arg'),**{'re':extractor.get('re')})
				if extractor.get('method') == "css":
					loader.add_css(key,extractor.get('arg'),**{"re":extractor.get("re")})
				if extractor.get("method") == "value":
					loader.add_value(key,extractor.get("args"),**{"re":extractor.get("re")})
		yield loader.load_item()
```
### 总结
## Scrapy 实战
### 本节目标
### 准备
### 分析
### 实战
#### 主逻辑实现
```
import scrapy
from scrapy import Field


class BookItem(scrapy.Item):
    authors = Field()
    catalog = Field()
    comments = Field()
    cover = Field()
    id = Field()
    introduction = Field()
    isbn = Field()
    name = Field()
    page_number = Field()
    price = Field()
    published_at = Field()
    publisher = Field()
    score = Field()
    tags = Field()
    translators = Field()
```

```
import scrapy
from scrapy import Request

class BookSpider(scrapy.Spider):
    name = 'book'
    allowed_domains = ['antispider7.scrape.center']
    base_url = 'https://antispider7.scrape.center'
    max_page = 512

    def start_requests(self):
        for page in range(1,self.max_page+1):
            url = f"{self.base_url}/api/book/?limit=18&offset={(page-1)*18}"
            yield Request(url,callback=self.parse_index)
    def parse_index(self,response):
        print(response)
```
                                                                                   
`scrapy crawl book`
#### 模拟登录
