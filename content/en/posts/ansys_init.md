---
title: permanent-magnet synchronous motor 設計 & 分析自動化 (透過 ansys maxwell) - 開啟ansys軟體、新增project
date: 2020-08-01
keyword:
- auto_motor_design
- auto_EM_analysis
- python_ansys_script
description: 由 python script 自動開啟Ansys maxwell軟體、新增project
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- auto_motor_design
- python_ansys_script
image:
---

## 環境

- windows10
- 正版Ansys (我的是教學版) 
- python 3

## 目標

使用python script，執行後自動開啟maxwell、新增project

## win32com

首先要完成的是讓python開啟maxwell，參考了網路上的一些資訊，使用win32com這個module是最方便的，因此先跑`pip install pywin32`裝好module。

## client.Dispatch 開啟軟體

使用以下的方式就可以直接開啟 ansys 的電磁分析軟體。

```python
from win32com import client

oAnsoftApp = client.Dispatch("Ansoft.ElectronicDesktop")
```

看到上面的code，可能會有幾個問題

- `client.Dispatch`做了什麼事?

  - `win32com.client.Dispatch()`是利用 微軟 提供的一個 [IDispatch](https://zh.wikipedia.org/wiki/IDispatch) 的自動化介面，接收軟體的 ProgID為參數，來操作windows軟體  ([來源](https://stackoverflow.com/questions/23500274/what-exactly-does-win32com-client-dispatchwscript-shell)、[詳細資料](http://www.cesarkallas.net/arquivos/apostilas/python/doc/Python%20Programming%20on%20Win32_%20Chapter%2012%20Advanced%20Python%20and%20COM.pdf))

- 怎麼知道，我的系統有沒有 "Ansoft.ElectronicDesktop" 這個 ProgID呢 ?

  - 可以在powershell打上 `Get-ChildItem HKLM:\Software\Classes -ErrorAction SilentlyContinue | Where-Object {$_.PSChildName -match '^\w+\.\w+$' -and (Test-Path -Path "$($_.PSPath)\CLSID")} | Select-Object -ExpandProperty PSChildName`  找看看有沒有，如果太多的話可以在最後面加上 `| grep An`  來縮減結果。

- 為什麼使用 `oAnsoftApp` 這個Object name?

  因為這是官方的手冊裡控制軟體的API名稱，依照手冊裡的變數，之後繼續實作其他功能時對照會比較方便。

## 新增project

後續所有的操控源頭都是 `oAnsoftApp`  這個object，首先，就來開啟一個project，用下列的code就能成功開啟。

```python
oDesktop = oAnsoftApp.GetAppDesktop()
oProject = oDesktop.NewObject()
```

有任何問題歡迎留言討論~
