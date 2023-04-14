---
title: Surface Permanent Magnet Motor Design Fundamental
date: 2020-10-10
keyword:
- auto_motor_design
description: Simple explain Surface Permanent Magnet Motor Design concept
draft: true
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- motor_design_programming
image:
math: true
---



## What's electric machine

Recently, electric vehicles can be seen running everywhere on the road. Although they are still considered toys for rich people, their popularity in the future should not be a problem. The power source of electric vehicles is the **motor**. Of course, motors are not only used in electric vehicles. In fact, there are motors everywhere in our daily life, such as laptop fans, washing machines, robotic arms, etc. The source of any machine that needs to be moved is the motor.

What is a motor? [Wikipedia](https://zh.wikipedia.org/wiki/Motor) It is mentioned that the most important principle is **electrical energy -> mechanical energy**, the description is more accurate, in fact is **electrical energy->magnetic energy->mechanical energy**.



## What is motor design

Motor design is to design **electrical energy -> magnetic energy -> mechanical energy** in the middle of **magnetic energy**, so that electrical energy can be converted into mechanical energy **efficiently**.

## Lorentz force

 [Lorentz force](https://zh.wikipedia.org/wiki/洛伦兹力)  :  **F (force density) =  $\sigma$( charge density) E (electric field)+ J (current density) x B (magnetic density)**

Let's talk about the source of electromagnetic force first - **Lorentz force**, in order to let everyone not to turn it off now, regardless of the first electric field force, we can recall a very simple formula in high school, long The force of a straight conductor current in a magnetic field - **f (electromagnetic force) = i (current) * l (wire length in the magnetic field) * B (magnetic flux density) * sin (perpendicular component)** , which is actually Lorentz force is calculated.

#### Torque Transformation

If it's a rotating motor, how will **f = ilB** change?

- Electromagnetic torque for unipolar: first add the shaft distance (rotor radius) **r** -> **T = ilBr**
- N, S pole: The magnet in the motor has N, S pole, and the coil is also one in and one out, so the real torque is **T = 2ilBr**
- n conductors: if more than one conductor has n conductors, the torque is **T = 2ilBrn**

#### Back - EMF Transformation

 $\epsilon=vBl$

I believe that the above formula is familiar to everyone. The long straight wire moves at the speed of v in the fixed magnetic field. Also, because of the magnetic force of the Lorentz force, an electric field (voltage) will be generated on the long straight wire.

Consider to a rotating motor, what will happen to **$\epsilon=vBl$**?

- v = $\omega$ * r : **$\epsilon= \omega rBl$**
- N, S pole: The magnet in the motor has an N, S pole, and the coil is one in and one out, so **$\epsilon= 2 \omega rBl$**
- n conductors: if more than one conductor has n conductors, the torque is **$\epsilon= 2 n \omega rBl$**

## Motor Constant (Kt, Ke)

Put the results of the above two calculation formulas together to compare the differences.

**T = 2ilBrn**

**$\epsilon= 2 n \omega rBl$**

Tidy up a bit

**T = 2nBlri = Kt i**

**$\epsilon= 2 nBlr \omega$ = Ke $\omega$**

Have you found that **2nBlr** is repeated, and can also be called motor parameters **Kt, Ke**, and **T, i, $\epsilon , \omega$** on both sides are actually electrical and mechanical specifications,

If the above two formulas are divided and cross-multiplied, **$\epsilon$ * i** is actually the electrical power, **T * $\omega$** is actually the so-called **mechanical power**, and then After removing the time item, it actually echoes the **electric energy->mechanical energy** at the beginning of the article.

## Parameters of motor design

 **2NBlr** is the magnetic part, and also points out the four most important parameters in the motor,

**n (the number of coil turns)**,

**B (the stator and rotor air gap magnetic flux density)**,

**l (the effective length of the motor)**,

**r (radius of stator and rotor air gap)**,

the permanent magnet motor design is designed around these four parameters.

The next article will actually be designed and pulled. If you have any questions, you can leave a message or mail to discuss

Thanks for watching
