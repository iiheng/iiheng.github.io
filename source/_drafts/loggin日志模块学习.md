---
title: logging日志模块学习
date: 2022-02-22 15:12:05
tags:
---
## 日志级别
```
import logging

logging.debug(u"苍井空")
logging.info(u"麻生希")
logging.warning(u'小泽玛利亚')
logging.error(u'桃谷绘里香')
logging.critical(u'泷泽萝拉')
```

	WARNING:root:小泽玛利亚
	ERROR:root:桃谷绘里香
	CRITICAL:root:泷泽萝拉
	
> ***级别排序：CRITICAL>ERROR>WAENING>INFO>DEBUG***

debug : 打印全部的日志,详细的信息,通常只出现在诊断问题上

info : 打印info,warning,error,critical级别的日志,确认一切按预期运行

warning : 打印warning,error,critical级别的日志,一个迹象表明,一些意想不到的事情发生了,或表明一些问题在不久的将来(例如。磁盘空间低”),这个软件还能按预期工作

error : 打印error,critical级别的日志,更严重的问题,软件没能执行一些功能

critical : 打印critical级别,一个严重的错误,这表明程序本身可能无法继续运行

```
import logging

logging.basicConfig(level=logging.NOTSET)
logging.debug(u"如果设置了日志级别为NOTSET，那么这里采取debug、info的级别的内容也可以显示在控制台上了")
```

	DEBUG:root:如果设置了日志级别为NOTSET，那么这里采取debug、info的级别的内容也可以显示在控制台上了
	
`logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(filename)s[line:%(lineno)d] - %(levelname)s: %(message)s')`

> format常用格式说明
> %(levelno)s: 打印日志级别的数值
> %(levelname)s: 打印日志级别名称
> %(pathname)s: 打印当前执行程序的路径，其实就是sys.argv[0]
> %(filename)s: 打印当前执行程序名
> %(funcName)s: 打印日志的当前函数
> %(lineno)d: 打印日志的当前行号
> %(asctime)s: 打印日志的时间
> %(thread)d: 打印线程ID
> %(threadName)s: 打印线程名称
> %(process)d: 打印进程ID
> %(message)s: 打印日志信息