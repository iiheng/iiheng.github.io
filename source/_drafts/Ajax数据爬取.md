---
title: Ajax数据爬取
date: 2022-02-21 15:40:23
tags: Ajax数据爬取
categories: python爬虫的学习
---

## 前言
## 什么是Ajax
### 实例引入
### 基本原理
### 总结

## Ajax分析方法
## Ajax分析与爬取实战
### 准备工作
### 爬取目标
### 初步探索
```
import requests

url = 'https://spa1.scrape.center'
html = requests.get(url).text
print(html)
```

	<!DOCTYPE html><html lang=en><head><meta charset=utf-8><meta http-equiv=X-UA-Compatible content="IE=edge"><meta name=viewport content="width=device-width,initial-scale=1"><link rel=icon href=/favicon.ico><title>Scrape | Movie</title><link href=/css/chunk-700f70e1.1126d090.css rel=prefetch><link href=/css/chunk-d1db5eda.0ff76b36.css rel=prefetch><link href=/js/chunk-700f70e1.0548e2b4.js rel=prefetch><link href=/js/chunk-d1db5eda.b564504d.js rel=prefetch><link href=/css/app.ea9d802a.css rel=preload as=style><link href=/js/app.17b3aaa5.js rel=preload as=script><link href=/js/chunk-vendors.683ca77c.js rel=preload as=script><link href=/css/app.ea9d802a.css rel=stylesheet></head><body><noscript><strong>We're sorry but portal doesn't work properly without JavaScript enabled. Please enable it to continue.</strong></noscript><div id=app></div><script src=/js/chunk-vendors.683ca77c.js></script><script src=/js/app.17b3aaa5.js></script></body></html>
	
### 爬取列表页
```
import requests
import logging

logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(levelname)s:%(message)s')

INDEX_URL = f'https://spa1.scrape.center/api/movie/?limit={limit}&offset={offset}'
```

```
def scrape_api(url):
    logging.info('scraping %s...',url)
    try:
        response = requests.get(url)
        if response.status_code == 200:
            return response.json()
        logging.error("get invalid status code %s while scraping %s",response.status_code,url)
    except requests.RequestException:
        logging.error('error occurred while scraping %s',url,exc_info=True)
```

```
LIMIT = 10
def scrape_index(page):
    url = INDEX_URL.format(limit=LIMIT,offset=LIMIT*(page-1))
    return scrape_api(url)
```

```
DETAIL_URL = "https://spa1.scrape.center/api/movie/{id}/"
def scrape_detail(id):
    url = DETAIL_URL.format(id=id)
    return scrape_api(url)
```

```
TOTAL_PAGE = 10

def main():
    for page in range(1,TOTAL_PAGE+1):
        index_data = scrape_index(page)
        for item in index_data['results']:
            id = item.get('id')
            detail_data = scrape_detail(id)
            logging.info('detail data %s',detail_data)

if __name__=="__main__":
    main()
```

	2022-02-22 16:29:15,198 - INFO:scraping https://spa1.scrape.center/api/movie/?limit=10&offset=0...
	2022-02-22 16:29:16,179 - INFO:scraping https://spa1.scrape.center/api/movie/1/...
	2022-02-22 16:29:16,723 - INFO:detail data {'id': 1, 'name': '霸王别姬', 'alias': 'Farewell My Concubine', 'cover': 'https://p0.meituan.net/movie/ce4da3e03e655b5b88ed31b5cd7896cf62472.jpg@464w_644h_1e_1c', 'categories': ['剧情', '爱情'], 'regions': ['中国内地', '中国香港'], 'actors': [{'name': '张国荣', 'role': '程蝶衣', 'image': 'https://p0.meituan.net/movie/5de69a492dcbd3f4b014503d4e95d46c28837.jpg@128w_170h_1e_1c'}, {'name': '张丰毅', 'role': '段小楼', 'image': 'https://p0.meituan.net/movie/35e74707f69da838d7ba3422b8f6579840705.jpg@128w_170h_1e_1c'}, {'name': '巩俐', 'role': '菊仙', 'image': 'https://p1.meituan.net/moviemachine/b650dcb00c40356934a275515217850f191104.jpg@128w_170h_1e_1c'}, {'name': '吕齐', 'role': '关师傅', 'image': 'https://p1.meituan.net/movie/30e12a78b5e61916edb1e33ce6fec19b34794.jpg@128w_170h_1e_1c'}, {'name': '英达', 'role': '戏园老板', 'image': 'https://p0.meituan.net/movie/cdedec80554d04a6139dc5270f5e7b5b35118

### 保存数据
```
import pymongo
client = pymongo.MongoClient(MONGO_CONNECTION_STRING)
db = client['moveis']
collections = db['movies']

def save_data(data):
    collections.update_one({
        'name':data.get('name')
    },{
        '$set':data
    },upsert=True
    )
```

```
def main():
    for page in range(1,TOTAL_PAGE+1):
        index_data = scrape_index(page)
        for item in index_data['results']:
            id = item.get('id')
            detail_data = scrape_detail(id)
            logging.info('detail data %s',detail_data)
            save_data(detail_data)
            logging.info('data save successfully')
```
