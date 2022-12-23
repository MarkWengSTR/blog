---
title: Real Example of Surface Permanent Magnet Motor Design by python script
date: 2020-10-12
keyword:
- python
- spm_motor_design
description: The design and detailed dimensions of the entire 10p12s spm motor are derived from the stator outer diameter limit, maximum power, DC bus voltage, maximum torque, and maximum speed specifications. It is not an optimized model, but it is based on reasonable calculations and assumptions. Implement an initial design that meets specifications.
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- motor_design_programming
---

## Goal

The design and detailed dimensions of the entire 10p12s spm motor are derived from the stator outer diameter limit, maximum power, DC bus voltage, maximum torque, and maximum speed specifications. It is not an optimized model, but it is based on reasonable calculations and assumptions. Implement an initial design that meets specifications.

## Contents of Design

Required Specification

```python
{
    "stator_OD_limit": 120,
    "max_power":       5000,
    "voltage_dc":      48,
    "max_torque_nm":   27,
    "max_speed_rpm":   5000,
}
```

The whole [design calculation](https://github.com/MarkWengSTR/ansys-maxwell-EM-design-online/blob/master/params/ktke_calculation.py) can refer to the link

## Introduction of Design Process

First see the calculation of `mech_structure_cal` at the bottom of the entire script.

```python
def mech_stucture_cal(total_cal_params):
    assign_spec_value(total_cal_params) and \
        ktke_calculation(total_cal_params) and \
        expend_NBLR(total_cal_params) and \
        expend_stator_teeth_york(total_cal_params) and \
        expend_stator_slot(total_cal_params) and \
        expend_magnet(total_cal_params)
```

Clearly, the process is

1. Put the specification parameters on the parameters that will be used in the entire calculation process
    - Why do you want to do this? Because the parameters (state) used are integrated in the same place, which is convenient for management, modification and debugging
2. Calculate ke, kt, the two most important parameters of the motor
3. Expanded by ke Number of coil turns, air gap flux density, motor effective length, air gap radius
4. Expand the stator slot size parameters
5. Expand the rotor magnet parameters

## Explanation of Design process

The parameters and coefficients mentioned below are in [Design Required Parameters](https://github.com/MarkWengSTR/ansys-maxwell-EM-design-online/blob/master/params/motor_params.py).

### ktke_calculation

For the calculation description of ktke, please refer to [On Motor Design]({{< ref "/posts/motor_principle" >}}) this

1. The calculation formula of phase ke is **phase voltage peak / speed (V / rad/s)**, and DC bus is equivalent to **line-to-line voltage peak value**, and the phase voltage needs to be divided by the line voltage $ \sqrt{3}$ .
2. Usually, in order to ensure some voltage drops of electronic components in practice, a coefficient (voltage_buffer) will be multiplied.
3. Convert kt from ke, in fact, it should be ke = kt ideally, but since some nonlinear kt may be generated at the maximum torque, it will also be multiplied by a coefficient of kt_ke_ratio to assist, so that the final simulation result is up to specification
4. Calculate the required **max current** from the max torque and kt.

### expend_NBLR

1. **The effective length of the motor** is obtained from the reasonable **Torque Density** and the outer diameter limit given by the specification, and the calculation is unconditionally rounded to an integer.
2. Obtain the **coil turn** from the reasonable **stator-rotor outer diameter ratio** and the assumed **air-gap magnetic density**, and take an unconditional rounded integer. (Actually, there are some elements of experience and guesswork in this part. If there is a better method, you can write to me to discuss it.)
3. Calculate the **rotor radius** from the number of turns calculated above

### expend_stator_teeth_york

1. Calculate **stator tooth width** from the ratio of magnet to stator teeth
2. Calculate the **stator yoke width** from a reasonable **gear yoke ratio**

### expand_stator_slot

1. Divide **maximum current** by (**reasonable maximum current density** * conductor area * number of parallel circuits) and take an unconditional rounded integer to get **number of parallel conductors**
2. From the number of parallel conductors * (conductor area + film thickness) * the number of turns * 2 to get **winding area in the slot**
3. Since the stator tooth yoke width and the stator outer diameter limit are known, and the limited stator outer diameter is directly used, the stator slot may be too large (because the rotor outer diameter is determined by a ratio in advance), and because the stator The tooth portion determines that the width of the stator slot cannot be moved, so the **stator slot height** that meets the **slot full rate** is calculated by the while loop iteration. (If the stator outer diameter limit is exceeded, but the expected slot full rate has not been reached, the stator outer diameter limit will be used. In principle, it will not be if the current density is not unreasonably low).

### expand_magnet

From a reasonable **pc operating point**, the **magnet thickness** is calculated from the **magnet width ratio** and **air gap length**.

## conclusion

In the above calculation, the parameters and detailed dimensions required for the entire motor have been designed. In fact, there are many factors to be considered in motor design. Each designer will also have his own set of methods. The above is my own method, indeed It can be developed into a reasonable and specification-compliant design by calculation. Currently, it is limited to 10p12s. In the future, it is hoped that it will become a general design method, which can design various slot configurations and can be extended to IPM or induction motors.

