---
title: python fundamental concept 基礎概念
date: 2020-07-20
keyword:
- python_fundamental
- data_and_function
description: 介紹python基本語法，所有構建自動分析所需的基本概念與python語法，都在這篇講完。
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- motor_design_programming
- python
---

## 任何語言的基本概念

[SICP](https://www.youtube.com/watch?v=IcZSFewqr9k)有提到，或是可以參考網站內的文章 [sicp_1a]({{< ref "/posts/sicp_lec1a" >}})，當認識一個新語言，最重要的事情有四個

1. Primitive Object (語言的基本元素)
2. Means of combination (組合基本對象的方法)
3. Means of abstraction (如何抽象)
4. Capturing common patterns (如何利用領域的專業，找到通用函數)

第四點雖然也很重要，但比較屬於系統複雜度增加後採取的作法，目前先不討論

## Primitive Object (語言的基本元素) in python

### Expression and statement

- Expression(表示式): 寫出一段文字 or 程式碼，python的解釋器讀了之後，幫你做完裡面的事，並且回傳給你一個值 (ex: 1 + 1 回傳 2)

- statement(陳述式): 寫出一段文字 or 程式碼，python的解釋器讀了之後，幫你做完裡面的事，但不回傳任何東西 ( a = 1 )

(有個小問題可以思考一下，那回傳 `nil`算expresion還是statement呢?)

### Data

- 字串: `str`

- 數字: `int(整數), float(分數)`

- 有順序的集合: `list(由[]包起來的集合, 元素可以被覆蓋), tuple(由()包起來的集合, 元素不能被覆蓋)`

- Mapping的集合: `dict(由{}包起來的集合, 由key字串值 => value任意值)`
- Boolean布林值: `True, False`

## Means of combination (組合基本對象的方法) in python

- Operator 運算子: `加減乘除, 比較, 邏輯, assignment`，詳細可直接看 [w3cschool](https://www.w3schools.com/python/python_operators.asp)
- 判斷表示式: `if...else...`
- loop陳述式: `for, while`
- function: `def xxx: ...return yyy` (定義function時使用def...return陳述式, 此時沒有回傳值, 使用 `()` 執行function時才有回傳值) 詳細可直接看 [w3cschool](https://www.w3schools.com/python/python_functions.asp)

## Means of abstraction (如何抽象) in python

- 利用assignment指定data成為 `特定名稱的 "變數"`
- 將combination組合data且放入 `特定名稱的 "function"` 

## why no class / object

看到這裡應該有人會問，為什麼沒有 class / object ? 

其實在這個自動化系統並沒有用到OOP的方式，OOP是一個很好解決複雜系統的方式，OOP的話是把每個部件定子, 轉子, 線圈, 磁石...等 都做成class，要設計時將物件實體化來做設計。

但我覺得馬達設計是一個很數學的學問，直接用規格的data貫穿所有設計的function，其實就可以達成完整設計，而完成的code structure其實也清楚且好維護。

## 結論

上述所有關於python的語法非常的簡單，而且其實不單只適用於python，這些方法是所有語言都有的，也就是學會了接下來談到的設計方法後，無論轉去哪個語言，都是可以使用的，下一篇文章會利用此篇文章的基礎，先講解整個設計方法的概念。

