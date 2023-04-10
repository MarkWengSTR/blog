---
title: Functional programming serious - The Elements of Programming in SICP
date: 2020-07-10
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



## 重點摘要

本篇取自[SICP-1a](https://www.youtube.com/watch?v=IcZSFewqr9k&list=PLkEwH_Z2WOlppy8oUfrGwFVlOuKyo3RO_)，解釋最基礎的computer science

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
本篇不討論第二項與第三項，純粹討論最基礎的第一項。

## 用一個例子解釋何謂black-box abstraction

### Square root algorithm - 將數字開根號(x^0.5)

計算過程:

- Make a guess G (猜一個值 G)
- Improve the guess by average G and x/G  (對於 G + x/G 的平均值做優化)
- Keep improving the guess until it is good enough (持續優化到足夠正確)

由上述的process，可以看出一個 "通則" (猜值 -> 優化 -> 持續優化到正確)，

"找尋函數不動點" 的 定理為 F(函數)有一不動點值滿足 F(Y) = Y，

而開根號的計算流程 ，可以視為 “找尋函數不動點” 這個通則 的其中一種方法: 找尋 Y與 x/Y的平均值函數的不動點。 (ex: F= (Y + x/Y)/2, 可以用 x = 36, Y = 6 來試試)

computer science 就是透過設計各式各樣的元件來解決問題，SICP提供了撰寫元件時可依循的想法

1. 解決問題的時候，將流程寫出來。

2. 想想是否解決這個單一問題，是一個通用問題的其中一個應用 (產生自 通用函數 )

看上述的問題，本來以為開根號函數非常的複雜，但抽出通用函數之後，我們把複雜度切成 Y, x/Y兩個參數的平均值 與“找尋函數不動點”的通用函數 這兩件事情，複雜度就被限縮在怎麼實作 "找尋函數不動點"這件事情上，這樣持續的做下去，複雜度是不是就層層的減低了呢?

## 總結

black-box abstraction可由開根號函數看出，使用此函數其實不必知道開根號函數裡面的實作細節，反正調用它，就能達到需求，這是上述提到的第一個關鍵。 而第二個關鍵找到通用性是為了降低複雜度。

當然這只是第一堂課，但也是最核心的觀念，後面會持續使用這個觀念來解決更多問題。
