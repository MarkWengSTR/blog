---
title: Serious-Python note - 6. Unit Testing
date: 2021-03-07
keyword:
- python
description: Serious-Python 讀書筆記(6)
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- python
---

寫code如果沒有test的話，基本上是沒有用，因為沒有辦法證明它是有效的。

## The Basics of Testing
在python中寫test與跑test是非常簡單的，並沒有侵入與破壞性，而test會大大的幫助你和其他開發者維護你的專案，接下來就會介紹基本的testing觀念。

### Some Simple Tests
- 把test放在submodule中
- 讓test中的階層模仿主要module的階層，可以更好的維護tests。 (如 *mylib/foobar.py* 的test就會放在 *mylib/tests/test_foobar.py*. 中)
- 最簡單的test就像下面的function，單純執行 `test_true()`就好。

```python
def test_true():
     assert True
    
test_true()
```
- 當然如果這樣一個一個執行test會非常的痛苦，可以使用 *pytest*這個module (需要pip install) ，它會幫你執行所有以 `test_` 開頭的function。 `pytest -v test_true.py` ，全部通過的話就會顯示 PASSED，沒有通過的話，就會把沒通過的部分顯示出來。
- pytest好用的關鍵是，當test越來越複雜時，它顯示出來的資訊可以幫助我們很好理解test fail的原因。

### Skipping Tests
- 有時候有些test想要跳過，pytest 也有提供幾個方式
  - 在function中 ` pytest.skip("skip this test")` ，跑test時就會顯示 *SKIPPED (skip this test)*
  - 在function上方用 `@pytest.mark.skip("skip this test")` 裝飾器也可以達成上述結果
  - 也可以用 **skip_if**，判斷某種情況下才需要跑此test function，`@pytest.mark.skipif(<condition>, reason="if condition then skip this test")`

### Running Particular Tests
- pytest 在CLI中，可以接收檔案或是資料夾當作參數，若接收資料夾的話會對跑資料夾內所有符合 test_*.py 的檔案跑Test。
- 若是在檔案後面再放 `-k function` ，就可以限制只跑哪一個test function。 
- 也可以幫 test 限定某些name scope，在function上用 `@pytest.mark.<scope name>`，在CLI上給定參數 `-m <scope name>` ，就會只跑給定name的test。
- `-m` 後面是可以接各種operator的(and, or, not)。 ex: 加上 `"not <scope name>"` 也可以跑不是scope name的test。

### Running Tests in Parallel
- 用 `pytest-xdist` (需 pip install) ，可以用 pytest 代 `-n <number>` 來用 <number> 個CPU運算tests。

### Creating Objects Used in Tests with Fixtures
- 有時候test需要做一些前置作業(object initial, temp file creation...)，*pytest*提供了*fixture*  裝飾器 `@pytest.fixture`，這個裝飾器下的function1需要return某個object，要使用function1的return值的話，就把function1當作自己的參數之一，範例如下。

```python
import pytest

class Car(object):
    def __init__(self, name):
       self.name = name


c1 = Car("Tesla")


@pytest.fixture
def car():
    return Car("Tesla")


def test_car_name(car):
    assert car.name == "Tesla"
```

- 如果fixture裡面是DB，需要執行完test馬上關閉呢? 可以用 `yield` 範例如下

```python
import pytest

@pytest.fixture
def database():
    db=<database connection>
    yield db
    db.close()


def test_insert(database):
    database.insert(123)
```

- 如果有很多個test都需要用到db，但只想開關一次呢? 可以加入 `scope="module"` 參數 ->`@pytest.fixture(scope="module")` 。

- 那如果不需要return db參數的話呢，可能單純是改變某個背景參數，但可以用 `autouse=True` -> `@pytest.fixture(autouse=True)` 。

### Running Test Scenarios
- 如果有很多種情境，但是call的方式是一樣的，可以使用 *parameterized fixtures* 如下，實際上"mysql", "postgresql"都有被call到。

```python
@pytest.fixture(params=["mysql", "postgresql"])
def database(request):
    d = myapp.driver(request.param)
    d.start()
    yield d
    d.stop()

def test_insert(database):
    database.insert("somedata")
```

### Controlled Tests Using Mocking
- Mock objects是用來在模仿真實app的行為，但是在可控且特定的方式，通常用mock的時機就是，雖然真實清況所要測的物件A的與其他物件B,C有互動，但利用mock B, C物件的行為，來達成獨立測試物件A的功能。 (ex: HTTP client，不太可能在測試的時候真的請別人打request過來) 
- 使用非常簡單， python3.3後已經進入標準的Library - unittest，因此需要*from unittest import mock*
- 基礎的使用方法

```python
from unittest import mock

m = mock.Mock()
m.some_attribute = "hello"

m.some_attribute #'hello'
```

- 也可以動態的創造method，例如創造一個假的method，永遠都return 42，無論給定什麼參數。

```
from unittest import mock
m = mock.Mock()
m.method.return_value = 42

m.method() # 42
m.method("with", "arguments") # 42
```
- 也可以在method被呼叫的同時放入side_effect

```python
def print_hello():
    print("hello world")
    return 43

m.method.side_effect = print_hello

m.method() # hello world 
           # 43
```
- 那也可以計算method到底被call幾次

```python
m.method.call_count # 2
```
- 如果從mock創造一個接收參數的method，且想確認mock method的參數，可用 ` assert_called_once_with()` ，參數不知道要代什麼的話可以用 `mock.ANY`。

```python
from unittest import mock

m = mock.Mock()
m.some_method('foo', 'bar') # <Mock name='mock.some_method()' id='26144272'>
m.some_method.assert_called_once_with('foo', 'bar')
m.some_method.assert_called_once_with('foo', mock.ANY)
m.some_method.assert_called_once_with('foo', 'baz')
# Traceback (most recent call last):
# File "<stdin>", line 1, in <module>
# File "/usr/lib/python2.7/dist-packages/mock.py", line 846, in assert_called_once_with
# return self.assert_called_with(*args, **kwargs)
# File "/usr/lib/python2.7/dist-packages/mock.py", line 835, in assert_called_with
# raise AssertionError(msg)
# AssertionError: Expected call: some_method('foo', 'baz')
# Actual call: some_method('foo', 'bar')

```

- mock這個東西到底怎麼用在test裡面呢，就是拿來代替某個function被call的時後的回傳值，以下就示範一個最基本的方式

```python
def fake_os_unlink(path):
    print("fake fake")


with mock.patch('os.unlink', fake_os_unlink):
    os.unlink("C:/")

# >> fake fake
```

- 也可以用裝飾器 `@mock.patch('<function>', <mock_function>)` 放在測試的function上來mock某個東西，非常的簡潔明瞭

### Revealing Untested Code with coverage
- 測試的覆蓋率也是專案很重要的指標
- 利用 *coverage* 這個Library (需要 pip install)，配合*pytest*在CLI打上 `pytest --cov=<code file> <test file>`

## Virtual Environments
- 前幾章有說到使用external library會有的問題
   - 系統有些library沒裝到
   - 系統有裝library但版本不對
   - 有時候系統需要兩個版本的library來給兩個application使用
- 這時候就需要一個資料夾(或稱為環境)，獨立外部環境，且其中已經安裝好自己所有需要的library，稱為虛擬環境 (Virtual Environments)

### Setting Up a Virtual Environment
- 作者推薦使用 *virtualenv* 這個套件 (不過其實現在比較常用的是conda)，需要先 pip install
- 安裝完後非常單純地用 ` python3 -m venv myvenv` 就會創造出一個myvenv的虛擬環境出來
- 簡單看一下 myvenv資料夾裡面，有 *bin*,*include*, *lib*, *pyvenv.cfg*四個資料夾，而在虛擬環境中用pip安裝的套件都會存在*myvene/lib*中。
- 啟動虛擬環境 ` (source) myvenv/bin/activate` 
- 離開虛擬環境 `deactivate`
- 非常單純的連續技

```basic
virtualenv myappvenv # 創造虛擬環境
source myappvenv/bin/activate # 進入虛擬環境
pip install -r requirements.txt # 安裝所有套件
deactivate # 離開虛擬環境
```

### Using virtualenv with tox
- 測試是跟環境與各套件的依賴性非常大的關聯，會不會有一種情況是，以為自己有測到，但其實忽略了某些關聯，這時候就需要直接用script來自動測試，確認這些環境與套件關聯，就是tox。
- 安裝完tox後，需要在*setup.py*(當然是要已經建好的，因為第一個就會去call它) 的旁邊新增一個 *tox.ini* 的檔案。
-可以直接run `tox` 這個指令， tox 會創造一個用預設的python環境在.tox/python創造虛擬環境，且用*setup.py*安裝好所需的套件。
- *tox.ini*裡面可以做許多的動作，例如執行commend

```
[testenv]
deps=pytest
commands=pytest
```

- 當然如果要執行pytest，一定要有pytest library，加上deps可以方便檢查一下library安裝。

- 如果想要同時測試各種python版本，可以加上

```
[tox]
envlist=py35,py36,pypy
```

- 也可以加上integration test 像是flake8

```
[tox]
envlist=py35,py36,pypy,pep8
[testenv]
deps=pytest
commands=pytest
[testenv:pep8]
deps=flake8
commands=flake8
```

### Testing Policy
- 既然都做到script自動測試了，不如就直接要push的時候就測吧，如果是使用Github的話，作者推薦*Travis CI*，只要定義像是以下的yaml檔，就能成功執行自動化測試瞜。

```yaml
language: python
python:
 - "2.7"
 - "3.6"
# command to install dependencies
install: "pip install -r requirements.txt --use-mirrors"
# command to run tests
script: pytest
```

- 要跟tox一起使用的話，還可以安裝 *tox-travis*，把yml檔改一下。

```yaml
sudo: false
language: python
python:
 - "2.7"
 - "3.4"
install: pip install tox-travis
script: tox
```