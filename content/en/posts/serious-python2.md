---
title: Serious-Python note - 2. modules, libaries and frameworks
date: 2021-02-18
keyword:
- python
description: Serious-Python 讀書筆記(2) - 來好好理解import module 這檔事
draft: true
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- python
---

## The Import System
- `import itertools` 實際上是  `itertools=__import__("itertools")` 的語法糖
- `import itertools as it` 其實就是  `it=__import__("itertools")` 

### sys module
- `sys.module` 是一個dictionary，包含目前所有load進環境中的modules，key是module名稱，value是module位置 (ex: `"os": "<module 'os' from 'c:\\python39\\lib\\os.py'>"` )
- 找尋build in的module list可以用 `sys.builtin_module_names`

### import path
- python是靠存在 `sys.path` 中的path list去找尋 module。
- `sys.path` 的內容，是可以被改變的，`sys.path.append('/foo/bar')` 等同於 commen line執行 `PYTHONPATH=/foo/bar python` 。
- `sys.path` 的內容的順序是很重要的，因為import的機制是疊代的去尋找需要的module，讓module越早找到，就等於加速程式運作。
- `sys.path` 的內容是可以重複的，最早找到的就會被import
- project 目錄在sys.path` 的順序先於Standard Library，因此如果自己做了一個 `random.py` import的時候就會先呼叫自己的 `random.py`，這個要小心。

### Custom Importers
- 自己客製import 機制也是很不錯的，可以參考 PEP302所定義的 *import hook mechanism* ，像是直接import database的資料，或是在import前做些確認機制 (作者有建議可以看 `hy` 這個project的機制)
- python 提供了兩種方式來擴增import system `sys.meta_path` (meta path finder)、`sys.path_hooks` (path entry
finders)。

### Meta Path Finder
- Meta Path Finder 是一個物件，可以讓你load 自己客製的script像是普通的.py檔。
- Meta Path Finder 必須要有一個 `find
_module(fullname, path=None)` method 且回傳一個loader物件，loader物件也必須有一個 `load_module(fullname)` 來從文件 load module。
- 下面來看一下怎麼用python load .hy file 而不是.py

```python
class MetaImporter(object):
    def find_on_path(self, fullname):
  	"""
  	確認path是否有東西
  	"""
    fls = ["%s/__init__.hy", "%s.hy"]
    dirpath = "/".join(fullname.split("."))
  	
    for pth in sys.path:
        pth = os.path.abspath(pth)
    
    for fp in fls:
        composed_path = fp % ("%s/%s" % (pth, dirpath))
        if os.path.exists(composed_path):
           	return composed_path
    
    def find_module(self, fullname, path=None):
 	"""
  	Meta Path Finder 必要的method，配合sys.meta_path 可讓python認為這個cutoms的file的路徑是有效的，也是一個有效的module
  	"""
    	path = self.find_on_path(fullname)
    	if path:
      	return MetaLoader(path)

	sys.meta_path.append(MetaImporter())
```
- 當python認為路徑是有效且可成為module之後，就會return 一個MetaLoader object。

```python
class MetaLoader(object):
      def __init__(self, path):
         self.path = path

      def is_package(fullname):
         dirpath = "/".join(fullname.split("."))
         for pth in sys.path:
            pth = os.path.abspath(pth)
            compose_path = "%s/%s/__init_.hy" % (pth, dirpath)
         if os.path.exists(compose_path):
            return True
         return False

      def load_module(self, fullname):
         if fullname in sys.modules:
            return sys.modules[fullname]
         if not self.path:
            return
         sys.modules[fullname] = None
         # import_file_to_module reads a .hy source file, compiles it to Python code, and returns a Python module object.
         mod = import_file_to_module(fullname, self.path)
         ispkg = is_package(fullname)

         mod.__file__ = self.path
         mod.__loader__ = self
         mod.__name__ = fullname

         if ispkg:
            mod.__path__ = []
            mod.__package__ = fullname
         else:
            mod.__package__ = fullname.rpartition('.')[0]

         sys.modules[fullname] = mod
         return mod
```

### Useful Standard Libraries
- python其實內建了很多很好用的standard libraries，當有實作某個function的需求的時候，可以先找找 libraries有沒有已經寫好的。
- funtools 與 itertools在最後兩章會講，以下先介紹其他好用的
- `atexit` 可以註冊function在程式結束時被呼叫
- `argparse` 可以產生命令列指令
- `bisect` 提供一個list 排序的二分列演算法(第十章會講)
- `calendar` 提供日期相關的function
- `codecs` 提供 編碼器與解碼器的function
- `collections` 提供了多種好用的data structure
- `copy` 提供了許多複製data的方式
- `csv` 提供了讀寫csv的方式
- `datetime` 提供了許多處理時間跟日期的方式
- `fnmatch` 提供unit-like 文件名稱 pattern的匹配funciton
- `concurrent` 提供了  asynchronous 的計算 (python3 native)
- `glob` 提供unit-like 文件路徑 pattern的匹配funciton
- `io` 提供操作 I/O streams 的方式
- `json` 提供 讀寫json的function
- `logging` 提供function 去access python的build-in logging
- `multiprocessing` 可以讓user做multiple subprocesses
- `operator` 提供function讓user去implement基本的操作符(第十章也會說明)
- `os` 提供function去操作底層os
- `random` 提供 functin產生 pseudorandom numbers
- `re` 提供 regular expression 的function
- `select` 提供通訊的function
- `shutil` 可以操作文件
- `signal` 可操作 unix 類系統的signal
- `tempfile` 提供新增temp file的function
- `threading` 提供 多執行緒 function
- `urllib` 協助操作URL
- `uuid` 協助產生 UUID
另外提一下，以上的module幾乎都是用python寫的，也可以去參考裡面的source code 學習一下。

### External Libraries
- 作者其實不太喜歡 external libraries，因為個人或是opensource的module比較可能會有bug，越用越多的話，bug就越疊越多，如果停止更新的話，自己的project也會受影響。
#### 如何確認 external libraries
- python3的支援度
- Active development
- Active maintenance: 有bug的時候開Issue，很快就有人可以解
- Packaged with OS distributions: 盡量選各種os都能支援的
- API compatibility commitment: 如果API隨著更版就變化的話，可能會造成整個project crush
- License
- 可以的話盡量做一個wrapper的API去隔離(第五章會講到)

### Package Installation: Getting More from pip
- pip 提供了從 *Python Packaging Index (PyPI)* install, uninstall package 的方式。
- `pip install --user <library>` 非常簡單，`user` option可裝在 home目錄中，不要汙染操作目錄
- `pip freeze` or `pip list` 列出安裝的套件
- `pip uninstall --user <library>` 解除安裝套件
- `pip install -e .` 可以安裝套件到目前的目錄，方便測試，下這個指令之後，pip沒有放檔案在site-packages中，不過它放了一個 <package-name>.egg-link的link，這個link會幫忙把目前檔案的link放到 `sys.path` 中，讓module能import的到。 也可以直接從URL下載 ex: `pip install -e git+https://github.com/jd/daiquiri.git\#egg=daiquiri`