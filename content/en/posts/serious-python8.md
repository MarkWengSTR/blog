---
title: Serious-Python note - 8. functional programming
date: 2021-03-15
keyword:
- python
description: Serious-Python 讀書筆記(8) - 來討論一下在python中如何利用functional programming來寫更簡潔有效的code。
draft: true
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- python
---



## Creating Pure Functions
- 當想把code寫成functional style時，function就要盡量設計成沒有side effect。也就是並不會改變送進去的參數的state，比較一下以下兩個case

```python
# state change
def remove_last_item(mylist):
    mylist.pop(-1)

# pure function
def remove_last_item(mylist):
    return mylist[:-1] # This returns a copy of mylist
```

- 實際上functional style 的好處
    - Modularity: 其實會迫使我們在寫的時候，將問題拆解成好幾個小的問題的function，會非常自然的讓code可以重用
    - Brevity: 通常會比較簡潔
    - Concurrency: 因為不會共用state，併發的時候就不會互相影響
    - Testability : test在functional program非常的簡單， 因為不用考慮到function中的會不會有其他影響，單純準備輸入，測試輸出，而且每次的結果都會一樣。

## Generators
- *generator* 是個行為像是 iteractor的object，會一直回傳每一次call `next()` 的結果，直到 `StopIteration` 錯誤產生。 最早引進在PEP255，簡單的在object中實現 *iterator protocol*，雖然在functional programming中不是必要，但這是實務上非常好寫也方便除錯的方式。
- 要創造 Generator，簡單的在function中加上 *yield* statement，當執行到*yield* ，function會回傳東西像是執行 *return* 一樣，但是有一個顯著的差別，解釋器會把這個stack reference存起來，直到執行到*next()*的時候再去call它。
- 當function被執行時，整串function執行的鏈稱為stack (function的呼叫是一個一的堆積起來的)，當function被執行後，它就被這個stack刪除，且回傳執行後的值，但在generator中，function 執行後回傳的不是值而是yield，python會把這個function的狀態存起來稱為 *stack reference* ，而在iteration中的下一個generator要被呼叫的時候才會執行。

### Creating a Generator
- 來實際做一個看看吧

```python
  def mygenerator():
      yield 1
      yield 2
      yield 'a'


  print(mygenerator()) # <generator object mygenerator at 0x000001ECF7FF0820>
  g = mygenerator()
  print(next(g)) # 1
  print(next(g)) # 2
  print(next(g)) # 'a'
  print(next(g)) # StopIteration
```

- 那為什麼要開發這個方法呢，讓我們思考一個例子 "在 1 - 10,000,000 中 找到符合 50,000的數字" ，聽起來很簡單，但想想看光要產生 1 - 10,000,000的list，可能就會耗盡記憶體

```python
  for value in range(100000000):
      if value == 5000:
         print("Found it")
         break
```

- python3中，*range()*會回傳generator，所以可以執行上述程式，不會出錯摟，因為它會動態的產生需要的list中的整數值，generator可以讓我們處理大量的資料集，且節省記憶體。 ( range是generator，但不是iterator，不可以直接call *next*喔，這篇寫得蠻詳細的 https://home.gamer.com.tw/creationDetail.php?sn=4337438 )

### Returning and Passing Values with yield
- 直接用yield要怎麼做成像range那樣的generator呢，可以用 *send()*，看一下以下例子

```python
  def shorten(string_list):
      length = len(string_list[0])

      for s in string_list:
         length = yield s[:length]


  mystringlist = ['loremipsum', 'dolosit', 'ametfoobar']

  shortstringlist = shorten(mystringlist)

  result = []

  try:
      s = next(shortstringlist)
      result.append(s)
      while True:
         number_of_vowels = len(
            list(filter(lambda letter: letter in 'aeiou', s)))
         s = shortstringlist.send(number_of_vowels)
         result.append(s)
  except StopIteration:
      pass

  print(result) # ['loremipsum', 'dolo', 'am']
```

- 看一下上述的code，真的是很難懂(笑)，我看了好久才大概理解，主要想做的事是 "把後一個字串的字數根據前一個母音的字數做縮減"。  *send()*的用途是可以當成 `yield xxx` 可以被丟到 `=` 左邊的回傳值，因此可以看到每一次 `shortstringlist.send(number_of_vowels)` 丟進去的length都會進去下一次的 `yield s[:length]` ，所以就會配合到每一次進去的新字串。

- PEP289 提出了新的 generator 表示，`(<iterable>)` ex: `(x.upper() for x in ['hello', 'world'])`

### Inspecting Generators
- *inspect.isgeneratorfunction* 可以幫忙我們確認是否為generator。
-  *inspect.getgeneratorstate* 可以幫忙我們確認generator目前的狀態。

## List Comprehensions
- *listcomp* 簡單來說，就是可以把iterator放到list裡面，像是 `[i for i in (1, 2, 3)]`
- 而且還可以用多行的方式

```python
  x = [word.capitalize()
         for line in ("hello world?", "world!", "or not")
         for word in line.split()
         if not word.startswith("or")]
 print(x) # ['Hello', 'World?', 'World!', 'Not']
```

- 當然也可以做成 dictionary

```python
 {x:x.upper() for x in ['hello', 'world']} # {'world': 'WORLD', 'hello': 'HELLO'}
```

- 這樣有非常多的好處，除了簡潔之外，可以不用定一個空的變數再append它，也容易理解

## Functional Functions Functioning (介紹 python 所提供好用的function)
- Applying Functions to Items with map(): 

```python
list(map(lambda x: x + "bzz!", ["I think", "I'm good"])) 
# ['I thinkbzz!', "I'm goodbzz!"]
```

- Filtering Lists with filter():

```python
list(filter(lambda x: x.startswith("I "), ["I think", "I'm good"])) 
[x for x in ["I think", "I'm good"] if x.startswith("I ")]
# ['I think']
```

- Getting Indexes with enumerate()

```python
for i, item in enumerate(mylist):
    print("Item %d: %s" % (i, item))
```

- Sorting a List with sorted(): 除了普通排序外，還提供了key的參數，可以輸入function來重定排序的指標。

```python
 sorted([("a", 2), ("c", 1), ("d", 4)])
# [('a', 2), ('c', 1), ('d', 4)]
sorted([("a", 2), ("c", 1), ("d", 4)], key=lambda x: x[1])
# [('c', 1), ('a', 2), ('d', 4)]
```

- Finding Items That Satisfy Conditions with any() and all(): *any(iterable)* 與 *all(iterable)* 回傳布林值，通常可以配合map

```python
mylist = [0, 1, 3, -1]
if all(map(lambda x: x > 0, mylist)):
    print("All items are greater than 0")
if any(map(lambda x: x > 0, mylist)):
    print("At least one item is greater than 0")
```

- Combining Lists with zip()

```python
keys = ["foobar", "barzz", "ba!"]

list(zip(keys, map(len, keys))) # [('foobar', 6), ('barzz', 5), ('ba!', 3)]
dict(zip(keys, map(len, keys))) # {'foobar': 6, 'barzz': 5, 'ba!': 3}
```

### A Common Problem Solved
- 先來解決一個很常遇到的 "找第一個大於0的數字" 問題，來看看如何去優化且讓它更有彈性

```python
def first_positive_number(numbers):
    for n in numbers:
        if n > 0:
            return n

# 提出 condition
def first(predicate, items):
    for item in items:
        if predicate(item):
            return item
first(lambda x: x > 0, [-1, 0, 1, 2])

# 直接用filter更簡單
list(filter(lambda x: x > 0, [-1, 0, 1, 2]))[0] 

# 比filter更有效率的方式
next(filter(lambda x: x > 0, [-1, 0, 1, 2]))

# 最簡單，直接import first library
from first import first
first([0, False, None, [], (), 42],  key=lambda x: x > 0)
```

- 接下來遇到一個問題，如果想連 "大於的數字" 都想自己定義，怎麼辦呢? 可以使用 `functools.partial()`，可以讓我們更有彈性的去創造function。

```python
from functools import partial
from first import first

def greater_than(number, min=0):
    return number > min

first([-1, 0, 1, 2], key=partial(greater_than, min=42)) 
# partial(greater_than, min=42) 就變成一個 greater_than_42的function了
```

- 有用的 *itertools* Functions:
  - `accumulate(iterable[, func])`
  - `chain(*iterables) ` : 把很多 list(iterable)串起來 
  - `combinations(iterable, r)`
  -  `compress(data, selectors)`
  - `count(start, step)`
  - `cycle(iterable)`
  - `repeat(elem[, n])`
  - `dropwhile(predicate, iterable)`
  - `groupby(iterable, keyfunc)`
  - `permutations(iterable[, r])`
  - `product(*iterables) `
  - `takewhile(predicate, iterable)`

- 總之，多看看 functools,  itertools 與 operator 裡面的function，多拿來用，可以讓你的code更好重用、測試與除錯。