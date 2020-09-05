---
title: motor design programming & 分析自動化 (透過 ansys maxwell) - 淺談永磁馬達設計原理
date: 2020-08-01
keyword:
- auto_motor_design
description: 馬達是一種能讓 電能 轉換成 機械能 的元件，其中的各種原理其實非常簡單。
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- auto_motor_design
image:
math: true
---



## 馬達 (電動機) 是什麼?

最近可以看到電動車在路上到處跑了，雖然目前還算是有錢人的玩具，但未來普及應是不在話下，而電動車的動力來源，就是 **馬達** 。 當然不只電動車用到馬達，其實日常生活中到處都是馬達，筆電風扇、洗衣機、機器手臂...等，任何要讓機械動起來的源頭都是馬達。

而馬達是什麼? [維基百科](https://zh.wikipedia.org/wiki/电动机) 裡面提到，最重要的原理就是 **電能 -> 機械能** ，描述的更準確一點，其實是  **電能 -> 磁能 -> 機械能**  。

## 馬達設計 是什麼?

馬達設計，就是設計 **電能 -> 磁能 -> 機械能**  中間的 **磁能**， 讓電能 **有效率** 的轉換為機械能。

## 勞倫茲力 (Lorentz force) 

 [勞倫茲力 (Lorentz force)](https://zh.wikipedia.org/wiki/洛伦兹力)  :  **F (force density) =  $\sigma$( charge density) E (electric field)+ J (current density) x B (magnetic density)**

先來談談電磁力的源頭 - **勞倫茲力** ，為了讓大家不要看到這邊就關掉，不考慮第一項電場力，我們可以回想一下高中一個很簡單的公式，長直導體電流在磁場裡的作用力 - **f (電磁作用力) = i (電流) * l (磁場中的導線長度) * B (磁通密度) * sin (垂直分量)** ，其實就是勞倫茲力計算得到的。

#### 換算馬達轉矩 Torque

如果是旋轉電機的話，**f = ilB** 會怎麼樣變化呢 ? 

- 單極的電磁轉矩： 首先加上轉軸距離 (轉子半徑) **r** -> **T = ilBr** 
- NS極：馬達裡的磁鐵有NS極，線圈也是一進一出，因此真正的轉矩為  **T = 2ilBr**
- Ｎ條導線：若是不只一條導線有Ｎ條導線，則轉矩為   **T = 2ilBrN**

#### 反電動勢 ( Back - EMF)

 $\epsilon=vBl$

相信上述公式，大家應該也不陌生，長直導線在固定磁場以v速度前進，一樣因為勞倫茲力的磁場力的作用，在長直導線上會產生一個電場 (電壓) 。

一樣考慮是旋轉電機的話 **$\epsilon=vBl$** 會怎麼樣變化呢?

- v = $\omega$ * r : **$\epsilon= \omega rBl$**
- NS極：馬達裡的磁鐵有NS極，線圈也是一進一出，因此 **$\epsilon= 2 \omega rBl$**
- Ｎ條導線：若是不只一條導線有Ｎ條導線，則轉矩為**$\epsilon= 2 N \omega rBl$**

## 馬達參數 Kt, Ke

將上述兩個計算公式的結果擺在一起，來對照一下差異

**T = 2ilBrN**

**$\epsilon= 2 N \omega rBl$**

稍微整理一下

**T = 2NBlri = Kt i**

**$\epsilon= 2 NBlr \omega$ = Ke $\omega$**

有沒有發現，其中的 **2NBlr** 是重複的，又可稱為馬達參數 **Kt, Ke**，而兩側的 **T, i, $\epsilon , \omega$** 其實就是電氣規格與機械規格，

如果把上述兩個公式相除，交叉相乘後，  **$\epsilon$ * i** 其實就是電功率， **T * $\omega$** 其實就是所謂的 **機械功率** ，再將時間項除掉後，其實就呼應文章最一開始的  **電能 -> 機械能** 。

## 馬達設計重點參數

 **2NBlr**  自然就是磁的部分了，也點出了馬達裡面最重要的四個參數，**N(線圈匝數)、B(定轉子氣隙磁通密度)、l(馬達有效長度)、r(定轉子氣隙半徑)**，永磁馬達設計就是圍繞著這四個參數來執行設計。

接下來的文章就會實際的來設計拉，大家有問題的話都可以留言或是mail來討論

謝謝收看