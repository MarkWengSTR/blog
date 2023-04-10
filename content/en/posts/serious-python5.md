---
title: Serious-Python note - 5. Distributing Your Software (順便distributing我的project "anmotordesign" 到pypi上)
date: 2021-03-05
keyword:
- python
description: Serious-Python 讀書筆記(5) - 當project build到某一個程度，你會希望能有一個方便安裝的方式提供給終端的使用者，在python當中提供了許多工具來完成這個部分，通常都會放在 setup.py 中，接下來就來好好探討這個部分，順便distributing我的project "anmotordesign" 到pypi上。
draft: true
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- python
---

## A Bit of setup.py History
- *distutils* library 從1988年就存在python standard library，開發自Greg Ward，提供了 setup.py 檔案如下
```python
from distutils.core import setup
setup(name="rebuildd",
    description="Debian packages rebuild tool",
    author="Julien Danjou",
    author_email="acid@debian.org",
    url="http://julien.danjou.info/software/rebuildd.html",
    packages=['rebuildd'])
```
- 而 *distutils* 在2000的時候停止了開發，而其他的開發者把還是有接著繼續開發，*setuptools* 就是其中一個，且提供更進階的功能來打包project

```python
import setuptools
setuptools.setup(
    name="rebuildd",
    version="0.2",
    author="Julien Danjou",
    author_email="acid@debian.org",
    description="Debian packages rebuild tool",
    license="GPL",
    url="http://julien.danjou.info/software/rebuildd/",
    packages=['rebuildd'],pyt
    classifiers=[
        "Development Status :: 2 - Pre-Alpha",
        "Intended Audience :: Developers",
        "Intended Audience :: Information Technology",
        "License :: OSI Approved :: GNU General Public License (GPL)",
        "Operating System :: OS Independent",
        "Programming Language :: Python"
   ],
)

```
- 另外還有 * distribute* module提供更多好用的功能，還有一些bug，而在2013年時，*distribute* 與 *setuptools* 合併在 *setuptools version 0.7* 底下。
- 而 *distutils2* 用不一樣的方式，將所有的metadata都放在 setup.cfg 中，後來改名 *packaging* ，在2012的時候也停止開發
- 目前 *setuptools* 還是最多開發者使用的，而接下來也可以密切注意 *distlib* 這個module，它會替代最原始的 *distutils*，且繼承 *packaging*的所有好處。

## Packaging with setup.cfg
- 有時候在 setup.py 裡面寫這麼多不是python code的資訊有點奇怪，作者建議改善的方法，把這些 metadata的資訊放在 setup.cfg中

setup.py中的資訊

```python
import setuptools
setuptools.setup()
```

setup.cfg的資訊

```python
[metadata]
name = foobar
author = Dave Null
author-email = foobar@example.org
license = MIT
long_description = file: README.rst
url = http://pypi.python.org/pypi/foobar
requires-python = >=2.6
classifiers =
    Development Status :: 4 - Beta
    Environment :: Console
    Intended Audience :: Developers
    Intended Audience :: Information Technology
    License :: OSI Approved :: Apache Software License
    Operating System :: OS Independent
    Programming Language :: Python
```
- 另一個好用的打包工具是 `pbr` (Python Build Reasonableness)，pkg是依附在 *setuptools* 一起使用的。

```python
import setuptools
setuptools.setup(setup_requires=['pbr'], pbr=True)
```
- pkg 可以
    -  自動產生 Sphinx documentation
    - 在git的基礎上自動產生 Authors 與 changelog 檔案
    - 自動幫git產生文件列表
    - 在git的基礎上進行打包的版本管理
    - 一定要有setup.cfg
    - 使用 `python setup.py --version` 可產生開發版本號 *0.1.0.dev43*，後面那個dev43是根據git tags

## The Wheel Format Distribution Standard
- 一直以來，python當中並沒有一個官方正式的安裝格式，就算是PEP376的出現，也還沒辦法處理已經存在的格式。
- 為了解決這個問題，PEP427定義了新的python標準安裝package - *Wheel*，需透過pip安裝，簡單執行 `pip install wheel` 。
- 如果你有安裝了 *setuptools* 與 *Wheel*，有一個透過 *setuptools* 自動呼叫*Wheel*的指令叫做 `bdist_wheel` ，可以直接把code壓縮至dist/xxx.whl 中，其實就是一個zip檔，可以透過 `python xxx.whl/<module_name> -h`  來執行你的code。



## Distribute My project "anemdesign"

為什麼叫做 **anemdesign** 呢?  因為想說用ansys electric machine design 的簡寫，沒什麼創意哈哈，記的起來就好

### 先註冊testpypi與pypi (兩個都要)

- https://test.pypi.org/
- https://pypi.org/

### 需安裝的library

- setuptools
- wheel
- twine (方便我們upload pypi的好用工具)

### whole project directory

- Test還沒寫，所以用<tests/>表示，接下來就要慢慢補上摟

```
project-directory/
├── LICENSE.txt
├── MANIFEST.in
├── README.md
├── setup.cfg
├── setup.py
├── anemdesign/
│    └── <run.py>
│    └── __init__.py
└── <tests/>
```

### setup.py file

```python
  import setuptools

  install_requires = [
      "setuptools",
      "pywin32==227",
      "ramda==0.5.5",
      "six==1.13.0",
      "functional-pipeline==0.3.1",
      "ipdb==0.12.3",
      "Flask==1.1.2",
      "Flask-Cors==3.0.8",
      "pandas==1.0.1",
      "numpy==1.18.1",
      "requests==2.24.0",
  ]

  with open("README.md", "r") as fh:
      long_description = fh.read()

  setuptools.setup(
      name="anmotordesign",
      version="0.1a",
      author="Mark Weng",
      author_email="bskin0330@gmail.com",
      description="Electrical Machines Design Automation by Ansys Maxwell Script",
      long_description=long_description,
      long_description_content_type="text/markdown",
      url="https://github.com/MarkWengSTR/ansys-maxwell-EM-design-online",
      packages=setuptools.find_packages(),
      classifiers=[
          "Programming Language :: Python :: 3",
          "License :: OSI Approved :: MIT License",
          "Operating System :: OS Independent",
      ],
      install_requires=install_requires,
      python_requires=">=3.6",
  )
```

- 沒有再包成 setup.cfg，目前覺得還沒有很雜，等project開始複雜之後再來優化

### License file

直接去 https://choosealicense.com/ 找，變成LICENSE.txt

### MANIFEST.in

這個可有可無，如果有設定 `include_package_data=True` 的話就會需要，就是額外需要包進去的data file，像是

```
include LICENSE.txt
include README.md
```

### 指令集

```bash
# 產生 tar wheel 的壓縮檔在 dist/ 中
python setup.py sdist bdist_wheel

# 上傳到測試的伺服器上
python -m twine upload --repository-url https://test.pypi.org/legacy/ dist/* 
# 上傳完後會給一個project的位置，像我的是 https://test.pypi.org/project/anmotordesign/

# 上傳到真正的pypi伺服器上
python -m twine upload dist/*
```

上傳完後，可以自己試試 pip install <project> 能不能載下來瞜

## Future work

現在所有的指令都需要follow 我的[project/run.py](https://github.com/MarkWengSTR/ansys-maxwell-EM-design-online/blob/master/anmotordesign/run.py) ，好像不太方便，應該要開發成各種簡易的API指令，讓別人可以用來開發自己的程式才是，就繼續修改瞜~~。