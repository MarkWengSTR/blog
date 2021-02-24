---
title: Serious-Python note - 3. Documentation and Good API Practice
date: 2021-02-24
keyword:
- python
description: Serious-Python 讀書筆記(3)
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- python
---

## Documenting with Sphinx
- 作者認為 Documentation 是非常重要的一件事，雖然寫doc是一件感覺非常煩雜的事，但其實不需要，python developer有很好的工具來幫助這件事。
- 大多的contributer都喜歡寫code甚於寫文件，甚至有doc跟project
是分開的，doc根本趕不上開發的速度。
- 最常用的python documentation是 *reStructuredText* 與 * reST* ，輕量級的markup 語言(類似 Markdown)，好撰寫也好閱讀，而 **Sphinx**是開發它們最常用的工具。
- Project的doc一定要包含:
    - 1~2句話簡短描述 此project想要解決的問題
    - Under哪個lisence
    - 給個最核心的作用的example
    - 介紹如何安裝
    - 有沒有哪個社群支持，給個連結
    - Bug的追蹤機制
    - 如果有developer想要contribute的話要去哪找source code?
- 可以利用reST加在README.rst，GitHub 或 PyPI 都可以讀取reST的format

## Getting Started with Sphinx and reST
- `pip install sphinx` 然後在project的最外層 run `sphinx-quickstart`
- 在 source 資料夾裡面可以看到 `conf.py` 與 `index.rst` 兩個檔案
  - `index.rst` 有點像是文˙件的封面
  - `conf.py` 就是你在  quick-start command 中輸入的資訊，當中有許多變數，這些是之後build之後會顯示在html中的資訊
- 做好之後用 `sphinx-build.exe source build` 就會build一個文件的靜態頁面摟
- index.rst只是一個起始頁面，但接著你可以不停的分類你的文件。
- Sphinx 的擴充性非常的高，不過每個module有一些自己的設定方式，例如 sphinx.ext.autodoc可以幫助你產生code對應的文件，只要在 `conf.py` 中加上 `extensions = ['sphinx.ext.autodoc']` ，然後在index.rst加上想執行的方法。
- 也可以使用 Sphinx來做doctest `sphinx-build -b doctest doc/source doc/build`。

## Managing Changes to Your APIs
- 有好文˙件的code是很方便讓別人import，且幫助別人建立自己的project，接下來會說到最好的public API如何實現。
- 對於public API 與 private API ，最顯而易見的差異就是 private API會在前面加上一個底線 `_` ，不過在python中，private api是不會被限制執行的，只是一個命名的慣例。

### Documenting Your API Changes
- 對於美一次的API更版，最首要且最重要的事情就是要寫下更版的文件，包含
  - 新的interface的元素
  - 不推薦使用的舊的interface的元素
  - migrate到新的interface的建議
- 千萬不要直接把舊的API移除，建議要一直留著一直到不能不刪的時候，給予不推薦使用的建議，使用者會知道不要去使用的。
- docstring 可以遵照 PEP257 (https://www.python.org/dev/peps/pep-0257/) 的規範
- 以下給個範例，這個開發者想在Car裡面將turn_left這個method改成turn這個比較general的method

```
  class Car(object):
      def turn_left(self):
         """Turn the car left.
         .. deprecated:: 1.1
         Use :func:`turn` instead with the direction argument set to left
         """""
         self.turn(direction='left')

      def turn(self, direction):
         """Turn the car in some direction.
         :param direction: The direction to turn to.
         :type direction: str
         """""
         # Write actual code for the turn function here instead
         pass
```

## Marking Deprecated Functions with the warnings Module
- python有提供 warnings模組，像上一個例子可以在 `turn_left` 的注釋中再加上 `warnings.warn("turn_left is deprecated; use turn instead",
 DeprecationWarning)` ，當使用者call ` Car().turn_left()` 就會跳出此warning。
- 不過在runtime的時候，warning是不會被print出來的，可以加上 `-W` 這個指令在python後方讓warning顯示出來。
- 在production上run -W顯示warning應該不是個好選擇，應該在test的時候再執行會比較好。
- ` debtcollector` 這個module提供了一些裝飾器function確認 正確的warning message有產生，以及確認文件的文字是被正確的更新。