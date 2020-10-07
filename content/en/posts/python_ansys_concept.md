---
title: motor design programming & 分析自動化 (透過 ansys maxwell) - motor design programming concept in python
date: 2020-09-01
keyword:
- python_fundamental
- data_and_function
description: Introduce how to use python in motor design and why use program to design motor。
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- motor_design_programming
---

## Why use program to design motor

- Save labor cost - program具有自動化的好處，重複的步驟只要編寫一次，之後只需要執行程式就好
- Code represent design concept and step - 當設計變成code，並且利用的是好理解且通用的 code structure ，人員轉換造成的工作問題就會下降，因為所有人只要能看懂code，就能看懂設計步驟，進而理解設計。

## How to represent motor design concept and step

專注在兩個非常重要卻又簡單的東西 **data & function**

### Input & Output Data

先思考 input 與 ouput的data是什麼

- input data - 規格
- output data - 定子外徑、馬達長度、匝數、BEMP(@1000rpm)、操作點數據(torque data, average torque, speed, torque ripple, current, current density, line voltage, coreloss, copperloss)

### Design function and data

1. 手算一次將 規格 轉換成 **馬達模型數據** 與 **電氣數據**。
2. 將中間所有的 **步驟**，以及當中用到的 **數據名稱** 記錄下來。
3. 將 **數據名稱** 根據 **步驟** 的概念分類 (定轉子、磁石、電氣...等)。使用 `dict 這個 data structure`將其收起來，還不知道的值就先給 `None`，用專案中馬達設計的部分呈現如下。

```python
motor_cal_params = {
    "stator": {
        "OD_limit": None,
        "slot": 12,
        "shoes_height_front": 1,
        "shoes_height_back": 1,
        "slot_open": 4.5,
        "slot_corner_arc": 0.5,
    },
    "rotor": {
        "pole": 10,
        "mag_emb": 0.8,  # easier magetization
    },
    "coil": {
        "conductor_OD": 1,
        "y_para": 1,
        "membrane_ratio": 1.075,
        "slot_fill_factor": 0.43,
    },
    "estimate": {
        "kt_ke_ratio": 0.9,
        "max_J": 18,
        "voltage_buffer": 0.9,
        "torque_density":   25,
        "teeth_mag_ang_ratio": 0.6,
        "york_teeth_ratio": 0.7,
        "rotor_OD_ratio": 0.6,
        "bg": 1.2,
        "mag_pc": 7.5,  # for not easy to broke
    },
    "calculation": {
        "est_rotor_OD": None,
        "est_stator_OD": None,
        "mag_thick": None,
        "teeth_width": None,
        "york_width": None,
        "slot_height": None,
        "slot_width_front": None,
        "slot_width_back": None,
        "para_conductor": None,
        "coil_turns": None,
        "real_slot_fill_factor": None,
    },
    "material": {
        "stator": "\"35CS250_steel\"",
        "rotor": "\"35CS250_steel\"",
        "magnet": "\"N44SH_20deg_mag\"",
        "coil": "\"copper\"",
    },
    "setting": {
        "cycle": 1,
        "split_step": 50
    },
    "voltage_dc": None,
    "length": None,
    "airgap": 0.5,
    "w_factor_10p12s": 0.933,
    "ke":               None,
    "kt":               None,
    "corner_speed_rpm": None,
    "max_speed_rpm":    None,
    "max_current_rms":  None,
    "core_loss_factor": 1,
}
```

4. 將步驟拆成小的function，專案中馬達設計的部分如下

```python
def ktke_calculation(total_cal_params):
    # 計算 total_cal_params 的某個值
    # 將 計算值 替算掉原本在total_cal_params中的未定值
    return total_cal_params
def assign_spec_value(total_cal_params):
    # 計算 total_cal_params 的某個值
    # 將 計算值 替算掉原本在total_cal_params中的未定值
    return total_cal_params
def expend_NBLR(total_cal_params):
    # 計算 total_cal_params 的某個值
    # 將 計算值 替算掉原本在total_cal_params中的未定值
    return total_cal_params
def expend_stator_teeth_york(total_cal_params):
    # 計算 total_cal_params 的某個值
    # 將 計算值 替算掉原本在total_cal_params中的未定值
    return total_cal_params
def expend_stator_slot(total_cal_params):
    # 計算 total_cal_params 的某個值
    # 將 計算值 替算掉原本在total_cal_params中的未定值
    return total_cal_params
def expend_magnet(total_cal_params):
    # 計算 total_cal_params 的某個值
    # 將 計算值 替算掉原本在total_cal_params中的未定值
    return total_cal_params
```

5. 將 3. 的data貫穿 4. 的所有function (3.的 `motor_cal_params` 放在 `ctx[params]`中)

```python
total_cal_params = ctx["params"]

ktke_calculation(total_cal_params) and \
    assign_spec_value(total_cal_params) and \
    expend_NBLR(total_cal_params) and \
    expend_stator_teeth_york(total_cal_params) and \
    expend_stator_slot(total_cal_params) and \
    expend_magnet(total_cal_params)

    print(total_cal_params)

```

6. Ansys maxwell 的溝通部分也是用同樣的方式，可參考 [這裡](https://github.com/MarkWengSTR/ansys-maxwell-EM-design-online/tree/master/software)

## 結論

使用上述的方法，是不是很單純，且很直觀就能看懂設計的過程，debug也非常容易，執行程式時將`total_cal_params` 印出來看就好。

當整個程式設計完成後，接下來就是去優化每一個 function & data 讓它們更容易被看懂，更能直觀的對應馬達設計。

更詳細可參考[我的設計](https://github.com/MarkWengSTR/ansys-maxwell-EM-design-online/blob/master/run.py)，未來還會持續的優化設計。







