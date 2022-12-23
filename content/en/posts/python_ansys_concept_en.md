---
title: Surface Permanent Magnet Motor design programming concept in python
date: 2020-10-11
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

- Save labor cost - program has the advantage of automation, repeating steps only need to be written once, and then only need to execute the program

- Code represent design concept and step - When the design becomes code, and a well-understood and universal code structure is used, the work problems caused by personnel switching will be reduced, because as long as everyone can understand the code, they can understand the design steps and then understand the design.

## How to represent motor design concept and step

Just need to focus on two simple but very important stuff **Data & Function**

### Input & Output Data

First think about what the data of input and ouput are

- input data - specifications
- output data - stator outer diameter, motor length, number of turns, BEMP(@1000rpm), operating point data (torque data, average torque, speed, torque ripple, current, current density, line voltage, coreloss, copperloss)

### Design function and data

1. Convert Specifications to **Motor Model Data** and **Electrical Data** by hand.
2. Record all the **steps** in the middle, and the **data name** used in them.
3. Classify **Data Name** according to the concept of **Step** (stator, magnet, electrical...etc). Use the `dict data structure` to collect it, and give it to `None` if you don't know the value. The motor design part of the project is presented as follows.

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

4. Divide the steps into small functions, the motor design part of the project is as follows

```python
def ktke_calculation(total_cal_params):
    # 計算 total_cal_params 的某個值 [calculating some value of total_cal_params dict]
    # 將 計算值 替算掉原本在total_cal_params中的未定值 [Replace the dict value by calculated results]
    return total_cal_params
def assign_spec_value(total_cal_params):
    # 計算 total_cal_params 的某個值 [calculating some value of total_cal_params dict]
    # 將 計算值 替算掉原本在total_cal_params中的未定值 [Replace the dict value by calculated results]
    return total_cal_params
def expend_NBLR(total_cal_params):
    # 計算 total_cal_params 的某個值 [calculating some value of total_cal_params dict]
    # 將 計算值 替算掉原本在total_cal_params中的未定值 [Replace the dict value by calculated results]
    return total_cal_params
def expend_stator_teeth_york(total_cal_params):
    # 計算 total_cal_params 的某個值 [calculating some value of total_cal_params dict]
    # 將 計算值 替算掉原本在total_cal_params中的未定值 [Replace the dict value by calculated results]
    return total_cal_params
def expend_stator_slot(total_cal_params):
    # 計算 total_cal_params 的某個值 [calculating some value of total_cal_params dict]
    # 將 計算值 替算掉原本在total_cal_params中的未定值 [Replace the dict value by calculated results]
    return total_cal_params
def expend_magnet(total_cal_params):
    # 計算 total_cal_params 的某個值 [calculating some value of total_cal_params dict]
    # 將 計算值 替算掉原本在total_cal_params中的未定值 [Replace the dict value by calculated results]
    return total_cal_params
```

5. Put data dict from step 3 in step 4 functions pipeline

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

6. The communication part of Ansys maxwell is also in the same way, please refer to [here](https://github.com/MarkWengSTR/ansys-maxwell-EM-design-online/tree/master/software)

## Conclusion

Using the above method, is it very simple and intuitive to understand the design process, and debugging is also very easy, just print `total_cal_params` when executing the program.

When the entire program design is completed, the next step is to optimize each function & data to make them easier to understand and more intuitive to correspond to the motor design.

For more details, please refer to [My Design](https://github.com/MarkWengSTR/ansys-maxwell-EM-design-online/blob/master/run.py), and the design will continue to be optimized in the future.





