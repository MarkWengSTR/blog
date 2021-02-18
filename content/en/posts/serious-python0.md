---
title: Serious-Python note - 0. introduction
date: 2021-02-16
keyword:
- python
description: Serious-Python 讀書筆記(0)
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- python
---

## 緣起

最近因為工作關係，需要大量的使用python，想找一本書來好好精進自己的python功力，於是找了這本[Serious-Python](https://www.amazon.com/Serious-Python-Black-Belt-Deployment-Scalability-ebook/dp/B074S4G1L5)來讀，也把看書的心得做些紀錄。

## 為什麼選這本書?

本書的作者 [Julien Danjou](https://julien.danjou.info/) 是一個非常多產出的developer，其中一個專案叫做[hy](https://github.com/hylang/hy) ，用lisp的syntax來操作python (在書中也會提到怎麼做)。 

之前因為看了sicp的關係，一直想找用lisp與python的結合，在github找到了hy真的是非常的興奮，如此愛lisp的python developer的書一定非常精彩，就來好好研讀一下吧

## Introduction

簡單介紹一下本書的每個章節，每一章的結束都會跟一個python大神做訪問。

Chapter 1:
提供一些開始動手寫project **前**的建議，例如: project 架構, 版本號, automated error checking。

Chapter 2:
介紹 python的 modules, libraries, and frameworks, 以及它們如何作用的

Chapter 3:
主要介紹 python 文件產生器Sphinx，且如何管理API

Chapter 4:
永遠甩不掉的issue: 時間，介紹 datetime, tzinfo

Chapter 5:

如何 packaging, distributions standards，介紹 distutils, setuptools

Chapter 6:
介紹最好的 pytest 實現 unitest的方式，且嘗試使用虛擬環境來做test。

Chapter 7:
深入的研究 methods 及 decorators，深入研究static, class, and abstract methods 以及如何混和它們來製作更紮實的專案。

Chapter 8:
主要討論 Generator，也討論一些functional programming 以及好用的  functools library。

Chapter 9:
深入語言的本身，討論 abstract syntax tree (AST) ，也會擴展flake8來利用AST製作較複雜的自動化check。

Chapter 10:
性能優化，包含使用適合的data structure、有效率的define function

Chapter 11:
多執行緒處理，包含多工處理、event-oriented 以及 service-oriented architecture

Chapter 12:

關聯式資料庫，主要介紹PostgreSQL

Chapter 13:
如何寫一個兼容python2, 3的lisp-like code，且學習用context managers 以及attr libery來減少重複

迫不及待要來看內容了吧!!  

趕快進入第一章~~