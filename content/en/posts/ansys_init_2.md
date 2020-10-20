---
title: python開啟ansys軟體問題 (2147221164, '類別未登錄')
date: 2020-10-20
keyword:
- auto_EM_analysis
- python_ansys_script
description: 由 python script 自動開啟Ansys maxwell軟體時，遇到 com_error(-2147221164, '類別未登錄', none, none) 的解決方法
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- motor_design_programming
---

## 問題描述

重灌了幾次ansys，又灌了其他軟體之後，突然某一天發現用 `oAnsoftApp = client.Dispatch("Ansoft.ElectronicsDesktop")` 無法正常開啟anays軟體，錯誤為 `com_error: (-2147221164, '類別未登錄', none, none)`。

使用ipython來執行，詳細問題如下 :

```python
<ipython-input-2-9633b38a1caf> in <module>
----> 1 client.dynamic.Dispatch("Ansoft.ElectronicsDesktop")

c:\python37\lib\site-packages\win32com\client\dynamic.py in Dispatch(IDispatch, userName, createClass, typeinfo, UnicodeToString, clsctx)
    125 def Dispatch(IDispatch, userName = None, createClass = None, typeinfo = None, UnicodeToString=None, clsctx = pythoncom.CLSCTX_SERVER):
    126         assert UnicodeToString is None, "this is deprecated and will go away"
--> 127         IDispatch, userName = _GetGoodDispatchAndUserName(IDispatch,userName,clsctx)
    128         if createClass is None:
    129                 createClass = CDispatch

c:\python37\lib\site-packages\win32com\client\dynamic.py in _GetGoodDispatchAndUserName(IDispatch, userName, clsctx)
    112         else:
    113                 userName = str(userName)
--> 114         return (_GetGoodDispatch(IDispatch, clsctx), userName)
    115
    116 def _GetDescInvokeType(entry, invoke_type):

c:\python37\lib\site-packages\win32com\client\dynamic.py in _GetGoodDispatch(IDispatch, clsctx)
     89                         IDispatch = pythoncom.connect(IDispatch)
     90                 except pythoncom.ole_error:
---> 91                         IDispatch = pythoncom.CoCreateInstance(IDispatch, None, clsctx, pythoncom.IID_IDispatch)
     92         else:
     93                 # may already be a wrapped class.

com_error: (-2147221164, '類別未登錄', none, none)
```

## Debug路程

### 確認Prog_id是否存在

使用 [分析自動化一]({{< ref "/posts/ansys_init" >}}) 的方式，確認是否存在 prog_id:  `Ansoft.ElectronicsDesktop` 。 有!

由於之前的認知只到這裡，第一次遇到有了prog_id還不能呼叫，google了一下，找到了[這篇](https://stackoverflow.com/questions/11297381/registered-com-object-not-recognized-by-pythons-win32com-client-dispatch)，還有一堆其他的，都是說python要用32位元才行，一開始想說應該不是這個問題，因為以前都可以正常呼叫，但找不到其他資料了，就試試看吧。

### 重灌python 32位元與pip

還好用 [Chocolatey](https://chocolatey.org/docs/commands-reference) 卸載跟安裝都超級快的，但是安裝了32位元的python後，還是無法執行呀!!!

### 註冊com object by regsvr32

試了上面的方法都不行，看來只能去研究windows的com元件了，首先確認一下 `類別未登錄` 這個錯誤訊息，亂打一個 `client.Dispatch("xxx")`，錯誤訊息是不一樣的，代表其實真的抓的到prog_id，只是在windows的某個地方 python抓不到 這個com object，那就來看怎註冊這個元件瞜。

經過了一段時間的找資料後，跟著 [windows官網指示](https://support.microsoft.com/zh-tw/help/249873/how-to-use-the-regsvr32-tool-and-troubleshoot-regsvr32-error-messages) 做了之後還是不行，其實說實在我根本不知道要註冊哪一個dll，所以就到ansys的資料夾裡面，把所有跟ansoft有關的dll都註冊了一次，但還是不行呀!!!。

## 解決方法 - regserver

又嘗試了一段時間，最後想到，其實只是單純想用python去開啟 `ansysedt.exe`，那有沒有方法是直接註冊 64位元的 `exe`檔呢，於是找到了 [regserver](https://stackoverflow.com/questions/3051823/what-is-difference-between-regsvr-and-regserver) 的方式，在powershell中進到 `AnsysEM\AnsysEM19.5\Win64` 的資料夾中，執行 `.\ansysedt.exe /regserver` ，就可以成功開啟了，太開心拉!!!



