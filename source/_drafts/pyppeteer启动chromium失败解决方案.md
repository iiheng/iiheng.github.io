---
title: pyppeteer启动chromium失败解决方案
date: 2022-02-25 16:38:40
tags:
---

## 问题
运行pyppeteer时报应用程序无法启动
```
Traceback (most recent call last):
  File "E:\pythonproject\scrapel\main.py", line 15, in <module>
    asyncio.get_event_loop().run_until_complete(main())
  File "C:\Program Files\WindowsApps\PythonSoftwareFoundation.Python.3.9_3.9.2800.0_x64__qbz5n2kfra8p0\lib\asyncio\base_events.py", line 642, in run_until_complete
    return future.result()
  File "E:\pythonproject\scrapel\main.py", line 6, in main
    browser = await launch()
  File "E:\pythonproject\scrapel\venv\lib\site-packages\pyppeteer\launcher.py", line 307, in launch
    return await Launcher(options, **kwargs).launch()
  File "E:\pythonproject\scrapel\venv\lib\site-packages\pyppeteer\launcher.py", line 148, in launch
    self.proc = subprocess.Popen(  # type: ignore
  File "C:\Program Files\WindowsApps\PythonSoftwareFoundation.Python.3.9_3.9.2800.0_x64__qbz5n2kfra8p0\lib\subprocess.py", line 951, in __init__
    self._execute_child(args, executable, preexec_fn, close_fds,
  File "C:\Program Files\WindowsApps\PythonSoftwareFoundation.Python.3.9_3.9.2800.0_x64__qbz5n2kfra8p0\lib\subprocess.py", line 1420, in _execute_child
    hp, ht, pid, tid = _winapi.CreateProcess(executable, args,
OSError: [WinError 14001] 应用程序无法启动，因为应用程序的并行配置不正确。有关详细信息，请参阅应用程序事件日志，或使用命令行 sxstrace.exe 工具。
```

## 运行chromium检测是否可用
### 在`local-chromium\588429\chrome-win32`文件夹中找到安装的chromium
![Chromium路径图](https://pic.imgdb.cn/item/621898ec2ab3f51d91a79819.png)

### 发现运行错误

## 问题解决
