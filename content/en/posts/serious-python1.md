---
title: Serious-Python note - 1. start your project
date: 2021-02-16
keyword:
- python
description: Serious-Python 讀書筆記(1)
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- python
---

## python版本
- 每一個小的進版都會有18個月的bug fix suport以及5年的安全性維護
- 作者建議新的專案，就不要再用python2了 (python2.7是最後一版，且2020已停止維護)

## 展開 project
### 好的觀念
- 讓hierarchy盡量平坦，不要太深
- 把unit test放到subpackage中。 因為如果使用 `setuptools` 或其他安裝工具，很可能不小心讓使用者把unit test也裝了進去。
- project folder第一層主要是 安裝(setup.py and setup.cfg) 、文件(README and doc folder) 與 project name folder (主要程式)

- 第一層也可能包含 etc (sample configuration file), tools (shell scripts or related tools), bin (binary scripts) ...等 setup.py會call到的工具。

- project name folder (主要程式) 除了主要程式之外，裡面需包含 `__init__.py` ，tests 與 額外資訊的資料夾(ex: 圖檔、data) 也放在這一層。

### 盡量避免
- 將資料夾或檔案根據 **type** 命名 (ex: function.py, exception.py)。 -> 要根據**feature** 命名。
- 開一個只有 `__init__.py` 的folder (ex: hello/__init__.py) -> 直接用 hello.py就好。
- 放有不好的side effect的code在 `__init__.py` 中 -> `__init__.py` 在第一次load directory時是會被執行的，通常裡面會是空的
- submodule中不放 `__init__.py` 這樣在import module時可能會有問題 -> python中 放置`__init__.py`的folder才會被python認為是submodule

## 版本號
- 版本號規則為 `N[.N]+[{a|b|c|rc}N][.postN][.devN]`
- `N[.N]+` 就是 final版
- `N[.N]+aN` 為 alpha release，可能不太穩定，且有些尚未做好的功能
- `N[.N]+bN` 為 beta release，功能已經完成，但可能還有些bug
- `N[.N]+cN or N[.N]+rcN` 為candidate release 最終可能上線版本，若沒有顯著的bug就會上線。
- `.postN` 就是上線後的修正版，因此如果有看到這個版本號，應該要下載這個版本
- `.devN` 先於 alpha release的開發版本

## Coding Style and Automated Checks
- 作者使用 `flake8` tool (我自己在neovim也是用這個來做自動化修正style)