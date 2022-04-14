---
title: JavaScrip 逆向爬虫
date: 2022-03-10 17:03:58
tags:
---
## 前言
## 网站加密和混淆技术简介
### 网站数据防护方案
#### URL/API 参数加密
#### JavaScript 压缩、混淆和加密
### URL/API 参数加密
### JavaScript 压缩
### JavaScript混淆
`npm init`
`npm i -D javascript-obfuscator`

```
const code=`
let x = '1' +1
console.log('x',x)
`

const options = {
	compact: false,
	controFlowFlattening: true
}

const obfuscator = require('javascript-obfuscator')
function obfuscate(code,options){
	return obfuscator.obfuscate(code,options).getObfuscatedCode()
}
console.log(obfuscate(code,options))
```

`node main.js`
#### 代码压缩
```
const code=`
let hello = '1'+1
console.log('hello',hello)
`

const options = {
	compact: false
}
```


#### 变量名混淆
```
const code=`
let hello = '1'+1
console.log('hello',hello)
`

const options = {
	compact: true,
	identifierNameGenerator:'mangled'
}
```


```
const code=`
let hello = '1'+1
console.log('hello',hello)
`

const options = {
	identifiersPrefix:'germey'
}
```

```
const code = `
var $ = function(id){
	return document.getElementById(id);
};
`
const options = {
	renameGlobals: true
}
```

#### 字符串混淆
```
const code = `
var $ = function(id){
	return document.getElementById(id);
};
`
const options = {
	stringArray:true,
	rotateStringArray: true,
	stringArrayEncoding: ['base64'],//'base64'或'rc4'或'false'
	stringArrayThreshold: 1,
}
```

```
const code = `
var a = 'hello world'
`
const options = {
	compact: false,
	unicodeEscapeSequence: true,
	}
```
	var a = '\x68\x65\x6c\x6c\x6f\x20\x77\x6f\x72\x6c\x64';
	
#### 代码自我保护
```
const code = `
var a = 'hello world'
alert(a)
`
const options = {
	selfDefending: true
}
```

#### 控制流平坦化
```
const options = {
	compact: false,
	controlFlowFlattening: true
}
```

#### 无用代码注入
```
const a = function(){
	console.log("hello world");
};
const b = function(){
	console.log("nice to meet you");
};
a();
b();
```

```
const options = {
	compact: false,
	deadCodeInjection: true
}
```
#### 对象键名替换
```
const code = `
(function(){
	var object = {
		foo: 'test1',
		bar: {
			baz: 'test2'
		}
	};
})();
`
const options = {
	compact: false,
	transformObjectKeys: true
}
```

#### 禁用控制台输出
```
const code = `
console.log('hello world')
`
const options = {
	disableConsoleOutput: true
}
```
#### 调试保护
```
const options = {
	debugProtection: true,
	debugProtextionInterval: true
};
```
#### 域名锁定
```
const options = {
	domainLock:['iheng.com']
}
```

#### 特殊编码
### WebAssembly
### 总结
## 浏览器调试常用技巧
### 面板介绍
### 查看节点事件
### 代码美化
### 断点调试
### 观察调用栈
### 恢复JavaScript执行
### Ajax断点
### 改写JavaScript文件
### 总结
## JavaScript Hook 的使用
### Hook 技术
### Tampermonkey
### 安装Tampermonkey
### 获取脚本
### 脚本编写
### 实战分析
#### Ajax断点
#### Hook
```
// ==UserScript==
// @name         HookBase64
// @namespace    https://login1.scrape.center/
// @version      0.1
// @description  Hook Base64 encode function
// @author       Iheng
// @match        https://login1.scrape.center/
// @icon         data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==
// @grant        none
// ==/UserScript==

(function() {
    'use strict';
    function hook(object,attr){
        var func = object[attr]
        object[attr] = function(){
            console.log('hooked',object,attr)
            var ret = func.apply(object,arguments)
            debugger
            return ret
        }
    }
    hook(window,'btoa')
})();
```
### 总结
## 无限debug的原理与绕过
### 案例介绍
### 实现原理
### 禁用断点
### 替换文件

## 使用Python模拟执行 JavaScript
### 案例引入
### 分析
### 模拟调用

## 使用Node.js模拟执行JavaScript
### 准备工作
### 模拟执行
```
const CryptoJS = require('./crypto.js');

function getToken(player){
	      let key = CryptoJS.enc.Utf8.parse(this.key)
	      const {name, birthday, height, weight} = player
	      let base64Name = CryptoJS.enc.Base64.stringify(CryptoJS.enc.Utf8.parse(name))
	      let encrypted = CryptoJS.DES.encrypt(`${base64Name}${birthday}${height}${weight}`, 
		  key, 
		  {
	        mode: CryptoJS.mode.ECB,
	        padding: CryptoJS.pad.Pkcs7
	      })
	      return encrypted.toString()
}
const players = {
    name: '凯文-杜兰特',
    image: 'durant.png',
    birthday: '1988-09-29',
    height: '208cm',
    weight: '108.9KG'
}
console.log(getToken(players))
```
```
!function(t, e) {
    "object" == typeof exports ? module.exports = exports = e() : "function" == typeof define && define.amd ? define([], e) : t.CryptoJS = e()
}(this, function() {....
```
`node main.js`

	NfKD7htJRgvOyaUztSrr3NjDXqLHSjTZBziGQJY26cJQYQPtdIKMQLQPdfc3JNKd

### 搭建服务
`npm i express`

```
const CryptoJS = require('./crypto.js');
const express = require("express");
const app = express();
const port = 3000;
app.use(express.json())

function getToken(player){
	      let key = CryptoJS.enc.Utf8.parse(this.key)
	      const {name, birthday, height, weight} = player
	      let base64Name = CryptoJS.enc.Base64.stringify(CryptoJS.enc.Utf8.parse(name))
	      let encrypted = CryptoJS.DES.encrypt(`${base64Name}${birthday}${height}${weight}`, 
		  key, 
		  {
	        mode: CryptoJS.mode.ECB,
	        padding: CryptoJS.pad.Pkcs7
	      })
	      return encrypted.toString()
}

app.post("/",(req,res)=>{
	const data = req.body;
	res.send(getToken(data));
});
app.listen(port,()=>{
	console.log(`Example app listening on port ${port}!`);
});
```

`node main.js`

```
import execjs
import json

import requests

data = {
    'name': '凯文-杜兰特',
    'image': 'durant.png',
    'birthday': '1988-09-29',
    'height': '208cm',
    'weight': '108.9KG'
}

url = 'http://127.0.0.1:3000'
response = requests.post(url,json=data)
print(response.text)
```

	NfKD7htJRgvOyaUztSrr3NjDXqLHSjTZBziGQJY26cJQYQPtdIKMQLQPdfc3JNKd

## 浏览器环境下JavaScript的模拟执行
### 分析
#### 环境差异
#### 依赖库查找
### 准备工作
### 案例介绍
### 实战

	var a = (this.page - 1) * this.limit, e = Object(i["a"])(this.$store.state.url.index, a);

```
window.encrypt = Object(i["a"]);
```

```
from playwright.sync_api import sync_playwright
BASE_URL = 'https://spa2.scrape.center'
context = sync_playwright().start()
browser = context.chromium.launch()
page.route(
    "/js/chunk-10192a00.243cb8b7.js",
    lambda route: route.fulfill(path='./chunk.js')
)
```

```
def get_token(offset):
    result = page.evaluate('''()=>{
    return window.encrypt("%s","%s")
    }'''%('/api/movie',offset))
    return result
```

```
from playwright.sync_api import sync_playwright
import time
import requests


BASE_URL = 'https://spa2.scrape.center'
INDEX_URL = BASE_URL + '/api/movie?limit={limit}&offset={offset}&token={token}'
MAX_PAGE = 10
LIMIT = 10

context = sync_playwright().start()
browser = context.chromium.launch()
page = browser.new_page()
page.route(
    "/js/chunk-10192a00.243cb8b7.js",
    lambda route: route.fulfill(path='./chunk.js')
)
page.goto(BASE_URL)

def get_token(offset):
    result = page.evaluate('''()=>{
    return window.encrypt("%s","%s")
    }'''%('/api/movie',offset))
    return result

for i in range(MAX_PAGE):
    offset = i*LIMIT
    token = get_token(offset)
    index_url = INDEX_URL.format(limit = LIMIT,offset = offset,token = token)
    response = requests.get(index_url)
    print('response',response.json())
```
### 总结
## AST 技术简介
### AST介绍
### 实例引入
`const name = 'Germey'`
### 准备工作
```
{
	"presets":[
		"@babel/preset-env"
	]
}
```

### 节点类型

### @babel/parser 的使用
```
const a = 3;
let string = "hello";
for (let i = 0 ;i < a;i++){
	string += "world";
}
console.log("string",string)
```

```
import {parse} from "@babel/parser";
import fs from "fs";

const code = fs.readFileSync("codes/code1.js","utf-8");
let ast = parse(code);
console.log(ast)
```

```
console.log(ast.program.body)
```

### @babel/generate 的使用
```
import { parse } from "@babel/parser";
import generate from "@babel/generator";
import fs from "fs";

const code = fs.readFileSync("code1.js","utf-8");
let ast = parse(code);
const {code: output} = generate(ast);
console.log(output);
```

```
const {code: output} = generate(ast,{
	retainLines: true,
});
console.log(output);
```

### @babel/traverse 的使用
```
import { parse } from "@babel/parser";
import traverse from "@babel/traverse";
import fs from "fs";

const code = fs.readFileSync("code1.js","utf-8");
let ast = parse(code);
traverse(ast,{
	enter(path){
		console.log(path);
	},
});
```

`babel-node basic2.js`

```
import { parse } from "@babel/parser";
import traverse from "@babel/traverse";
import generate from "@babel/generator";
import fs from "fs";

const code = fs.readFileSync("code1.js","utf-8");
let ast = parse(code);
traverse(ast,{
	enter(path){
		let node = path.node;
		if (node.type === "NumericLiteral" && node.value === "hello"){
			node.value = 5;
		}
		if (node.type === "StringLiteral" && node.value === "hello"){
			node.value = "hi";
		}
	},
});

const {code: output} = generate(ast,{
	retainLines: true,
});
console.log(output);
```

	const a = 3;
	let string = "hi";
	for (let i = 0; i < a; i++) {
	  string += "world";
	}
	console.log("string", string);

```
import { parse } from "@babel/parser";
import traverse from "@babel/traverse";
import generate from "@babel/generator";
import fs from "fs";

const code = fs.readFileSync("code1.js","utf-8");
let ast = parse(code);
traverse(ast,{
	NumericLiteral(path){
		if (path.node.value === 3){
			path.node.value =5;
		}
	},
	StringLiteral(path){
		if(path.node.value === "hello"){
			path.node.value = "hi"
		}
	}
});
```
```
import { parse } from "@babel/parser";
import traverse from "@babel/traverse";
import generate from "@babel/generator";
import fs from "fs";

const code = fs.readFileSync("code1.js","utf-8");
let ast = parse(code);
traverse(ast,{
	CallExpression(path){
		let node = path.node;
		if(node.callee.object.name === "console" &&
		   node.callee.property.name === "log"){
			   path.remove();
		   }
	},
});

const {code: output} = generate(ast,{
	retainLines: true,
});
console.log(output);
```

### @babel.types 的使用
```
import traverse from "@babel/traverse";
import { parse } from "@babel/parser";
import generate from "@babel/generator";
import * as types from "@babel/types";

const code = "const a = 1;";
let ast = parse(code);
traverse(ast,{
	VariableDeclaration(path){
		let init = types.binaryExpression(
		"+",
		types.identifier("a"),
		types.numericLiteral(1));
		let declarator = types.variableDeclarator(types.identifier("b"),init);
		let declaration = types.variableDeclaration("const",[declarator]);
		path.insertAfter(declaration);
		path.stop();
	},
});
const output = generate(ast,{
	retainLines: true,
}).code;
console.log(output);
```

	const a = 1;const b = a + 1;
	

### 总结
## 使用AST技术还原混淆代码
### 表达式还原
```
const a = !![];
const b = "abc" == "bcd";
const c = (1<<3)|2;
const d = parseInt("5" + "0")
```
```
import traverse from "@babel/traverse";
import {parse} from "@babel/parser";
import generate from "@babel/generator";
import * as types from "@babel/types";
import fs from "fs";

const code = fs.readFileSync("code1.js","utf-8");
let ast = parse(code);

traverse(ast,{
	"UnaryExpression|BinaryExpression|ConditionalExpression|CallExpression":(
	path)=>{
		const {confident,value} = path.evaluate();
		if (value == Infinity || value == -Infinity)return;
		confident && path.replaceWith(types.valueToNode(value));
	},
});

const {code: output} = generate(ast);
console.log(output);
```

	const a = true;
	const b = false;
	const c = 10;
	const d = parseInt("50");

### 字符串还原
```
const strings = ["\x68\x65\x6c\x6c\x6f","\x77\x6f\x72\x6c\x64"]
```

```
import traverse from "@babel/traverse";
import {parse} from "@babel/parser";
import generate from "@babel/generator";
import * as types from "@babel/types";
import fs from "fs";

const code = fs.readFileSync("code1.js","utf-8");
let ast = parse(code);

traverse(ast,{
	StringLiteral({node}){
		if (node.extra && /\\[ux]/gi.test(node.extra.raw)){
			node.extra.raw = node.extra.rawValue;
		}
	},
});

const {code: output} = generate(ast);
console.log(output);
```