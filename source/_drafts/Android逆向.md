---
title: Android逆向
date: 2022-03-18 15:36:58
tags:
---
## 前言
## jadx的使用
### jadx的简介
### 准备工作
### jadx的命令
### jadx-gui的使用方法
#### 启动和反编译
#### 保存为Gradle项目
#### 文本搜索
#### 查找方法的生命
#### 查找用力
#### 反混淆
#### 设置选项
#### 日志查看
#### 常见问题

## JEB的使用
### JEB的简介
### 准备工作
### 实战
### 模拟
```
import hashlib
import time
import base64
import requests

INDEX_URL = 'https://app5.scrape.center/api/movie?limit={limit}&offset={offset}&token={token}'
MAX_PAGE = 10
LIMIT = 10

def get_token(args):
    timestamp = str(int(time.time()))
    args.append(timestamp)
    sign = hashlib.sha1(','.join(args).encode('utf-8')).hexdigest()
    print(','.join([sign,timestamp]).encode('utf-8'))
    return base64.b64decode(','.join([sign,timestamp]).encode('utf-8')).decode('utf-8')

for i in range(MAX_PAGE):
    offset = i * LIMIT
    token = get_token(args=['/api/movie'])
    index_url = INDEX_URL.format(limit=LIMIT,offset=offset,token=token)
    response = requests.get(index_url)
    print('response',response.json())
```
### 总结
## Xposed框架的使用
### Xposed 框架的简介
### 准备工作
### 