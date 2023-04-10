---
title: SICP - Identity? of objects (by clojure)
date: 2020-07-01
keyword:
- sicp
- clojure
description: simple describe problem of identity of object and church encoding use clojure
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- sicp
- clojure
image:
---
## 本文重點

**非預期的共享**，是大型系統的bug來源，透過給object一個身份(賦值) ，給它一個別名互相共享，是有蠻多方便，但也相對付出了代價。 但不賦值又怎麼能取用資料結構中的值呢?  能不能直接用function來解決以上問題?

為[计算机程序的构造和解释（Lec5b：计算对象)](https://www.youtube.com/watch?v=2Iz7agtk614&t=302s) 後面部分的心得，整個影片都很值得看，但這篇要說的是從 46:35左右開始講的。且最近開始學Clojure，所以內容中操作的部分會用clojure來呈現。

## 定義最簡單的資料結構

首先討論，什麼是 **最簡單的資料結構(data structure)**，任何 object 的最小單位就是一個pair。

### 如何組成pair

就要談到組成pair的方法，叫做  **cons**  。

### cons 的定義

```clojure
;;對於任何的 x, y
(car (cons x y)) = x
(cdr (cons x y)) = y
```

簡單來說` (cons x y) `就是一個pair，也是最基本的資料結構。 就這個的定義來說，其實是一種抽象，cons包含兩個參數，如果兩個cons包含的參數是一樣的，那這兩個cons就是一樣的。

不過，上述定義並沒有闡述，到底cons是否具有身份(identity)？

## 給資料身分，引入賦值

如何解釋以下這段code ?

```clojure
(def a (cons 1 2))
```

指的是在一個環境裡面，創造了一個名為 a 的 pair，這個pair裡面包含兩個指針一個指向1，一個指向2。

再試著回答下列問題:

- 假設又定義了一個 b `(def b (cons a a))`，那 b 這個pair指向的兩個a，是同一個a嗎？ 
- 呈上，且現在呼叫 a 的方式有三種 `a`, `(car b)`, `(cdr b)` ，哪個才是真正的呢？
- 假設又用了賦值去改 `car b (set-car! (car b) 3)` ，原本存在 a 的 1被改成3了，現在如果再呼叫 (car a) 回傳的會是 3，儘管一開始我們定義的 (car a) 是1。

是不是有點難回答呢?

## 賦值的危險

**非預期的共享**，是大型系統的bug來源，透過給object一個身份 ，給它一個別名互相共享，是有蠻多方便，但也相對付出了代價。

當開始任意使用賦值，以下問題便開始產生

- Change: 變數不再代表一個值，可能會被改變
- Time: 函式的回傳值，可能會因為時序的不同而不一樣
- Identity: pair不再只是用car cdr去呼叫，它可以有別稱
- Share: 兩個別稱之間有可能會共享同一個pair



## Data Structure is Just a function

為了盡量避免object的問題，能不能就把它當成數學的函式，而不要認為它是有身分的object呢 ?

### 如何使用lambda function憑空製造一個 cons ! - by Alonzo Church

```clojure
(defn cus-cons [x y] (fn [fc] (fc x y)))
(defn cus-car [fc2] (fc2 (fn [a d] a)))
(cus-car (cus-cons 1 2)) ;=> 1
```

是不是看不太懂? 沒關係，我來演繹一下這個神奇的程序

- 1, 2被代換掉 cus-cons中的x, y，回傳了一個function `(fn [fc] fc 1 2)`
- 傳入 cus-car之後代換掉 fc2 : `((fn [fc] fc 1 2) (fn [a d] a))` ， `(fn [a d] a)` 被當成參數代換掉 fc
- 最後變成 `((fn [a b] a) 1 2)` ，回傳值就是1了，超級神奇吧！



Lambda這個方式，完全是用function來完成 cons car cdr，並沒有存在任何一的地方。真的很有趣啊，推薦一定要去看一下傳說中的 [Alonzo church lambda calculus](https://zh.wikipedia.org/wiki/Λ演算)！！！
