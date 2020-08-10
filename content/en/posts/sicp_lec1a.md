---
title: SICP - The Elements of Programming
date: 2020-08-10
keyword:
- sicp
description: the fundamental of computer science, black-box abstraction and Conventional interface
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- sicp
image:
---



## SICP 1a - The Elements of Programming

## What is computer science

- Process: 如何編寫解決問題的 "過程"
- Method: 組成process的元素，而method的名稱應該要陳述出裡面的知識

## Computer science 的特性

- 處理" 理想化的元件": 因為我們對所有要組合在一起的 data 與 function都能完全的掌控， 因此在理想與現實之間是差不多的 (如果是電子或機械工程師 那可能會需要考慮很多真實世界的限制)。
- 一個工程中的 "抽象形式": 因為不需考慮現實的限制

## Computer science 的技術 - 控制複雜度

- black-box abstraction: 將功能封裝起來，使用的人不需要知道裡面的技術，只要呼叫它，丟入值就會輸出結果，而使用者又可以拿這個功能再去組合成更多功能。
  - 將細節隱藏 (用以構建更大型的盒子)
  - 具有通用性 (generality)
- Conventional interface: 如果系統裡的數據有許多不一樣的類型，計算的時候總不能混雜在一起，那怎麼辦呢?
  - Generic operations
  - large-scale structure and modularity
  - object-oriented programming
  - operations on aggregate (流)
- Metalinguistic Abstraction: 定義一個新語言 (用lisp做一個解析器來解析lisp)

## black-box abstraction

### Example: Square root algorithm  

#### process of algorithm (find an approximation to x^0.5)

- Make a guess G (猜一個值 G)
- Improve the guess by average G and x/G  (對於 G + x/G 的平均值做優化)
- Keep improving the guess until it is good enough (持續優化到足夠正確)

#### Generality function - Fixed Point

由上述的process，可以看出一個 "通則" (猜值 -> 優化 -> 持續優化到正確)

 "找尋函數不動點" 的 定理為 F(函數)有一不動點值滿足 F(Y) = Y

拉更高層次來看的話，process of Square root algorithm ，可以視為 "找尋函數不動點" 這個通則 的其中一種方法: 找尋 Y與 x/Y的平均值函數的不動點。 (F= (Y + x/Y)/2, 可以用 x = 36, Y = 6 來試試)

這帶出一些觀念

1. 當解決一個問題的時候，將流程寫出來。

2. 想想是否解決這個單一問題，是一個通用問題的其中一個應用 

3. **解決單一問題的函數，產生自 <u>通用函數</u> 接受一個特定目的的函數** (ex: 找平方根的函數 產生自 <u>找尋不動點函數</u> 接收 (Y + x/Y)/2 = Y 這個函數)

### black-box abstraction details

後續會更深入的解析 "黑盒抽象"，大概可以分成幾個部分

- Primitive Object (lisp 的基本對象)
  - Primitive Procedure (基本過程)
  - Primitive data (基本數據)
- Means of combination (組合基本對象的方法)
  -  Procedure composition
  - Construction of compound data
- Means of abstraction (如何抽象)
  - Procedure definition
  - Simple data abstraction
- Capturing common patterns (如何利用領域的專業，找到通用函數?)
  - High order procedure (高階過程 像是上述的 平方根函數)
  - data as function (最後越來越抽象時，數據與函數的界線會越來越模糊)

## Conventional interface

同樣一個方程式 `(*x (+ a1 a2))` 用於 **純數字、向量、信號波形，或是a1 a2不是數值，而是多項式、分數...等**