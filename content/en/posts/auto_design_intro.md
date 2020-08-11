---
title: permanent-magnet synchronous motor 設計 & 分析自動化 (透過 ansys maxwell) - python script 自動化設計 demo
date: 2020-08-10
keyword:
- auto_motor_design
description: 利用python script 自動化設計 SPM 馬達 並控制ansys maxwell 模擬。
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

## 影片介紹

目前的成果，拍成影片上傳到youtube上

 {{< youtube uStT2k3V6x0>}}



輸入所需的參數 `spec_params`

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

- stator_OD_limit: 定子外徑限制
- max_power: 最大功率
- voltage_dc: DC bus 電壓
- max_torque_nm: 最大轉矩
- max_speed_rpm: 最高轉速
- export_path: 輸出參數 (這是之後串接要用的可以不用管)
- pj_key: project的名稱(使用不重複的時間戳，以便同時多個模擬一起執行)
- res_url: 打response回去的url (這是之後串接要用的可以不用管)

輸入以上參數後，執行 `python run.py` 就會開始 執行設計 -> 跑模擬 -> 數據計算後處理。

Github repo: https://github.com/MarkWengSTR/ansys-maxwell-EM-design-online

## 未來討論項目

接下來針對整個project的內容來講解，會分成幾個部分

- 馬達設計觀念
- python script code design
- python ansys co-operation
- build web api

敬請期待~~~



