---
title: 程式化永磁馬達設計 by python 真實範例
date: 2020-10-07
keyword:
- python
- spm_motor_design
description: 由定子外徑限制、最大功率、DC bus電壓、最大轉矩、最高轉速 規格，推導出整個10p12s spm馬達的設計與細部尺寸，並不是一個優化的模型，但都是經由合理的計算與假設，實作出一個合乎規格的初始設計。
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- motor_design_programming
---

## 目標

由定子外徑限制、最大功率、DC bus電壓、最大轉矩、最高轉速 規格，推導出整個10p12s spm馬達的設計與細部尺寸，並不是一個優化的模型，但都是經由合理的計算與假設，實作出一個合乎規格的初始設計。

## 設計概覽

規格如下 

```python
{
    "stator_OD_limit": 120,
    "max_power":       5000,
    "voltage_dc":      48,
    "max_torque_nm":   27,
    "max_speed_rpm":   5000,
}
```

整個 [設計計算](https://github.com/MarkWengSTR/ansys-maxwell-EM-design-online/blob/master/params/ktke_calculation.py) 可以參考連結 

## 設計流程簡介

先看到整個script最底下的 `mech_structure_cal` 的計算

```python
def mech_stucture_cal(total_cal_params):
    assign_spec_value(total_cal_params) and \
        ktke_calculation(total_cal_params) and \
        expend_NBLR(total_cal_params) and \
        expend_stator_teeth_york(total_cal_params) and \
        expend_stator_slot(total_cal_params) and \
        expend_magnet(total_cal_params)
```

清楚的看到，流程就是

1. 將規格參數先塞到整個計算流程會用到的參數上
   - 為什麼要這樣呢?  因為將用到的參數(state)統整在同一個地方，這樣方便管理、修改與debug
2. 計算ke, kt，馬達最重要的兩個參數
3. 由ke展開 線圈匝數、氣隙磁密、馬達有效長度、氣隙半徑
4. 展開定子槽尺寸參數
5. 展開轉子磁石參數

## 設計流程說明

以下談到的參數與係數都在 [設計所需參數](https://github.com/MarkWengSTR/ansys-maxwell-EM-design-online/blob/master/params/motor_params.py) 中。

### ktke_calculation

ktke的計算說明可以參考  [淺談馬達設計]({{< ref "/posts/motor_principle" >}}) 這篇

1. 相ke計算式為  **相電壓 peak / 轉速 ( V / rad/s )**，而DC bus 等同於 **線對線電壓 peak值**，轉成相電壓需由線電壓除 $\sqrt{3}$ 。
2. 而通常為了保證實務上電子元件的一些壓降，會乘一個係數 (voltage_buffer)
3. 由ke換算kt，其實理想上應該是ke = kt，但由於最大轉矩時可能會有一些非線型的kt產生，因此也會乘上一個 kt_ke_ratio的係數來輔助，讓最後模擬出的結果是能達到規格的
4. 由最大轉矩與kt計算出所需要的 **最大電流**。

### expend_NBLR

1. 由合理的 **轉矩密度(Torque Density)** 與 規格給的外徑限制 得到 **馬達有效長度**，計算時取無條件進位到整數。
2. 由合理的 **定轉子外徑比** 與假設之 **氣隙磁密值** 來得到 **匝數(coil turn)**，取無條件進位整數。  (其實這個部分有些經驗與猜測的成分，如果有更好的方法也可以來信與我討論。)
3. 由上述算出的 匝數 來計算 **轉子半徑**

### expend_stator_teeth_york

1. 由磁鐵與定子齒部的比例，計算出 **定子齒部寬**
2. 由合理的 **齒軛比**，計算出 **定子軛部寬**

### expand_stator_slot

1. 由 **最大電流** 除以 (**合理的最大電流密度** * 導體面積 * 並聯迴路數) 取無條件進位整數 得到 **並聯導體數**
2. 由 並聯導體數 * (導體面積 + 膜厚) * 匝數 * 2 得到 **槽內繞組面積**
3. 由於定子齒軛寬與定子外徑限制都已知道，而直接用限制的定子外徑的話，定子槽可能會太大 (因為轉子的外徑是由一個比例先行決定的)，且因定子齒部決定，定子槽寬是不能動的，因此由 while loop 疊代算出 符合 **槽滿率** 的 **定子槽高**。 (如果超過定子外徑限制，但還沒達到預期槽滿率的話，就用定子外徑限制，原則上如果電流密度沒有低到不合理的話不會)。

### expand_magnet

由 合理的 **pc 操作點**，由 **磁石寬度比例** 與 **氣隙長度** 計算出 **磁石厚度**。

## 結論 (conclusion)

上述的計算，已經把整顆馬達所需要的參數與細部尺寸都設計完了，馬達設計其實要考量的要素很多，每個設計者也會有自己的一套方法，以上是我自己的方法，確實是可以由計算就展開成一個合理且符合規格的設計，目前僅限10p12s，未來希望可以成為一個General的設計方式，可以設計各種槽極配，且能延伸到IPM或感應馬達。

