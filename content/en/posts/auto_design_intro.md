---
title: Python script for ansys maxwell, Surface Permanent Magnet Motor design automation demo
date: 2020-07-28
keyword:
- auto_motor_design
description: 利用python script 自動化設計 SPM 馬達 並控制ansys maxwell 模擬。
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- motor_design_programming
image:
---

## 影片介紹 (Vedio Intro)

目前的成果，拍成影片上傳到youtube上 

 {{< youtube uStT2k3V6x0>}}



輸入所需的參數 `spec_params` [input the required parameters]

```python
spec_params = {
    "stator_OD_limit": 120,
    "max_power":       5000,
    "voltage_dc":      48,
    "max_torque_nm":   27,
    "max_speed_rpm":   5000,
    "export_path":     None,
    "pj_key":          str(int(time.mktime(datetime.datetime.now().timetuple()))),
    "res_url":         None,
}

```

稍微解釋一下參數

- stator_OD_limit: 定子外徑限制 [Stator outer diameter]
- max_power: 最大功率 [maximum power]
- voltage_dc: DC bus 電壓 [DC bus voltage]
- max_torque_nm: 最大轉矩 [maximum torque]
- max_speed_rpm: 最高轉速 [maximum speed]
- export_path: 輸出路徑 (not required)
- pj_key: project的名稱(使用不重複的時間戳，以便同時多個模擬一起執行) (not required)
- res_url: 打response回去的url (這是之後串接要用的可以不用管) (not required)

輸入以上參數後，執行 `python run.py` 就會開始 執行設計 -> 跑模擬 -> 數據計算後處理。 [execute `python run.py`, begin to execute design, simulation, post data processing. ]

Github repo: https://github.com/MarkWengSTR/ansys-maxwell-EM-design-online

## 未來討論項目

接下來針對整個project的內容來講解，會分成幾個部分 [For the project content description, will break to parts]

- motor design concept
- python script code design
- python ansys co-operation
- build web api

敬請期待~~~



