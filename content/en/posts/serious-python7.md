---
title: Serious-Python note - 7. Methods and Decorators
date: 2021-03-14
keyword:
- python
description: Serious-Python 讀書筆記(7)
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- python
---

python 2.2的時候發表了 decorator的使用方式 `classmethod()`與`classmethod()`，後續也繼續support了許多decotators的開發，但仍有很多人不太清楚背後的運作。 這個章節首先探討decorator，接著來討論使用它來產生static, class, method，最後來講一下 `super()`這個function

## Decorators and When to Use Them
- Decorator是一個拿別的function來當作引數的function，且替換或調整丟進來的function成為一個新的funciton。
- 最主要的的使用方式是精簡通用的code (需要在前或後或在多個function中間呼叫的)

### Creating Decorators
- 最簡單的decorator就是 `identity()` function，就是單純回傳原本的funciton。

```python
def identity():
    return f

@identity
def foo():
    return 'bar'
```
- 上述的code其實等同於

```python
def foo():
    return 'bar'
    
foo = identity(foo)
```

- 雖然看起來沒什麼用，但是它可行，讓我們來看看另一個好用的例子

```python
_functions = {}

def register(f):
    global _functions
    _functions[f.__name__] = f
    return f
    
@register
def foo():
    return 'bar'

# 當執行foo之後，_functions中就會加上 {'foo': <function foo at 0x0000024E84035280>}
```
### Writing Decorators
- decorator非常常用在refactor重複的code，例如以下的簡化:

```python
  class Store(object):
      def get_food(self, username, food):
         if username != 'admin':
            raise Exception("This user is not allowed to get food")
         return self.storage.get(food)

      def put_food(self, username, food):
         if username != 'admin':
            raise Exception("This user is not allowed to get food")
         self.storage.put(food)

        
  # 簡化如下 ------------
  def check_is_admin(username):
      if username != 'admin':
         raise Exception("This user is not allowed to get food")

  class Store(object):
      def get_food(self, username, food):
         check_is_admin(username)
         return self.storage.get(food)

      def put_food(self, username, food):
         check_is_admin(username)
         self.storage.put(food)

        
  # 使用 decorator 簡化如下 ------------
  def check_is_admin(f):
      def wrapper(*arg, **kwargs):
         if kwargs.get('username') != 'admin':
            raise Exception("This user is not allowed to get food")
         return f(*arg, **kwargs)
      return wrapper

  class Store(object):
     @check_is_admin 
     def get_food(self, username, food):
         return self.storage.get(food)

     @check_is_admin 
      def put_food(self, username, food):
         self.storage.put(food)
```
- 介紹一下上述 `check_is_admin` decorator的寫法，外面傳進來的引數可以從*kwargs*中拿到，做完處理後，同樣的執行函數

### Stacking Decorators
- 如果不只想check *'admin'*，想把 *'admin'* 變成從外面輸入的參數，也可以同時check多個名字

```python
  def check_user_is_not(username):
      def user_check_deco(f):
         def wrapper(*arg, **kwargs):
            if kwargs.get('username') != username:
               raise Exception("This user is not allowed to get food")
            return f(*arg, **kwargs)
         return wrapper
      return user_check_deco


  class Store(object):
      @check_user_is_not('admin')
      @check_user_is_not('user1234')
      def get_food(self, username, food):
         return self.storage.get(food)
```
- `check_user_is_not()` 是`user_check_deco`的factory function，會製造一個具有*username*變數的decorator，而`user_check_deco`是 `get_food()`的function decorator。

- 另一個問題是 `get_food()`上方用了兩次`check_user_is_not()` decorator，哪一個名字會先被確認呢? 答案是decorator會被由上而下的呼叫，因此'admin'會先於'user1234'。

### Writing Class Decorators
- 也可以實現class的decorator，不過這比較少用到。運作的方式是一樣的

```python
import uuid

def set_class_name_and_id(klass):
    klass.name = str(klass)
    klass.random_id = uuid.uuid4()
    return klass

@set_class_name_and_id
class SomeClass(object):
    pass
```

### Retrieving Original Attributes with the update_wrapper Decorator
- 上面有說到，decorator是一層一層function包住的，這會造成一個問題，就是呼叫包了decorator之後，呼叫 `__doc__` 與 `__name__`就會變成decorator的資訊，但是原本的才是正確的。
- *functools.wraps*解決了這個問題，將它放在decorator回傳的function上方

```python
import functools

def check_is_admin(f):
    @functools.wraps(f)
    def wrapper(*args, **kwargs):
        if kwargs.get('username') != 'admin':
            raise Exception("This user is not allowed to get food")
        return f(*args, **kwargs)
    return wrapper

class Store(object):
    @check_is_admin
    def get_food(self, username, food):
         """Get food from storage."""
         return self.storage.get(food)
```

- 目前為止，對於call function的時候丟進來的值，例如 ` username` 並沒有做任何的保護，這有點危險，這裡介紹一個module *inspect*，它的function `getcallargs`，會輸出所有的引數成為一個dict，可以幫助我們完成這件事。

```python
import functools
import inspect

def check_is_admin(f):
    @functools.wraps(f)
    def wrapper(*args, **kwargs):
        func_args = inspect.getcallargs(f, *args, **kwargs)
        if func_args.get('username') != 'admin':
            raise Exception("This user is not allowed to get food")
        return f(*args, **kwargs)
    return wrapper
    
class Store(object):
    @check_is_admin
    def get_food(self, username, food):
         """Get food from storage."""
         return self.storage.get(food)
```

## How Methods Work in Python
- method是一個很容易理解且使用的東西，通常不用很深入的去了解背後的運作，但為了能真正了解decorator的運作，我們需要深入的了解一下method。
- method是一個存在class屬性中的function，看下面的例子

```python
class Pizza(Object):
    def __init__(self, size):
        self.size = size
    def get_size(self):
        return self.size

Pizza.get_size # <function Pizza.get_size at 0x7fdbfd1a8b90>
Pizza.get_size() # TypeError: get_size() missing 1 required positional argument: 'self'
Pizza.get_size(Pizza(42)) # 42
Pizza(42).get_size # <bound method Pizza.get_size of <__main__.Pizza object at 0x7f3138827910>>
Pizza(42).get_size() # 42
```

- 因為現在`get_size` 這個function已經與特定的obejct黏在一起了，不能把它當成一個普通的function，只能透過*Pizza*的instance來call `get_size`

## Static Methods
- static method不用依賴任何class或object，可以用來產生utility function
- static method只要單純的加上`@staticmethod` ，來比較一下下面的例子

```python
class Pizza(object):
    @staticmethod
    def mix_ingredients(x, y):
        return x + y
    def cook(self):
        return self.mix_ingredients(self.cheese, self.vegetables)

Pizza().cook is Pizza().cook # False
Pizza().mix_ingredients is Pizza.mix_ingredients # True
Pizza().mix_ingredients is Pizza().mix_ingredients # True
```
- 這次就不用先產生Object再call了，不過其實實際上還是有產生object的，雖然會多消耗資源，但使用`@staticmethod` 可以容易的通用化，且可讀性好，在繼承時也可覆蓋原本的function，好處多多的。

## Class Methods
- *class method* 會綁在class上而非object，以下是寫的方式

```python
  class Pizza(object):
      radius = 42

      @classmethod
      def get_radius(cls):
         return cls.radius


  print(Pizza.get_radius) # <bound method type.get_radius of <class '__main__.Pizza'>>
  print(Pizza().get_radius) # <bound method type.get_radius of <class '__main__.Pizza'>>
  print(Pizza.get_radius == Pizza().get_radius) # True
  print(Pizza.get_radius()) # 42
```

- *class method* 原則上適合用來做 *factory method*，像以下這樣

```python
class Pizza(object):
    def __init__(self, ingredients):
        self.ingredients = ingredients
    
    @classmethod
    def from_fridge(cls, fridge):
        return cls(fridge.get_cheese() + fridge.get_vegetables())
```

- 只要你想要寫一個method，但不知道要用class還是object，且你也不知道object的狀態，那就用class method吧

## Abstract Methods
- 有一種情況，預設subclass需要override baseclass，如果沒有的話需要提醒，python有個built-in的library就是做這件事情 *abc (abstract base classes)*

```python
import abc

class BasePizza(object, metaclass=abc.ABCMeta):
    @abc.abstractmethod
    def get_radius(self):
        """Method that should do something."""
```

## Mixing Static, Class, and Abstract Methods
- 讓我們來循序漸進的介紹，如何將前面介紹的method混和使用

```python
import abc

# base class
class BasePizza(object, metaclass=abc.ABCMeta):
    @abc.abstractmethod
    def get_ingredients(self):
        """Returns the ingredient list."""

# object method
class Calzone(BasePizza):
    def get_ingredients(self, with_egg=False):
        egg = Egg() if with_egg else None
        return self.ingredients + [egg]

# static method
class DietPizza(BasePizza):
    @staticmethod
    def get_ingredients():
        return None
```

- 如果 base class就想做成 class method

```python
import abc

class BasePizza(object, metaclass=abc.ABCMeta):
    ingredients = ['cheese']

    @classmethod
    @abc.abstractmethod
    def get_ingredients(cls):
        """Returns the ingredient list."""
        return cls.ingredients
```

- 想要繼承某些base class的method的東西在來更改呢，用 `super()`

```python
# base class 如上一個範例
class DietPizza(BasePizza):
    def get_ingredients(self):
        return [Egg()] + super(DietPizza, self).get_ingredients()
```

## The Truth About super
- python可以允許單個或多重或同時多個繼承(mixin)，來討論一下super()在這之中的關係
- 開啟class的方式是 `class classname(expression of inheritance)`，而繼承的順序可以使用 `mro (method resolution order)` 這個function來看
- *super* 這個 funciton實際上是一個建構函數，每一次call它的時候都會實例化一個super的object

```python
  class A(object):
      bar = 42

      def foo(self):
         pass


  class B(object):
      bar = 0


  class C(A, B):
      xyz = 'abc'


  print(C.mro())
  print(C.mro()) # [<class '__main__.C'>, <class '__main__.A'>, <class '__main__.B'>, <class 'object'>]
  print(super(C, C()).bar)  # 42
  print(super(B).__self__)  # None
  print(super(B, B()).__self__)  # <__main__.B object at 0x000002780C2FF5B0>
```

- 當我們對 C 實例的 *super* object 索取 attribute的時候，*super*的 `__getattribute__` 方法會根據 *MRO*列表回傳第一個找到的class的attribute。

- 我們也可以不給super第二個參數，稱為 *unbound* super object (可想而知有給的就是 *bound* super object)，而 *unbound* 的話，super object並沒有綁到任何instance上，就不能access到任何class attribute

```python
super(C) # <super: <class 'C'>, NULL>
super(C).foo # AttributeError: 'super' object has no attribute 'foo'
```

- 這樣看來，難道 unbound 的super object就沒有用了呀，那幹嘛發明它呢，不過其實 super 最好用的是它的class加入了一個好用的descriptor protocol *__get__* ，讓unbound的時候也非常的有用。  (descriptor protocol - python實作的機制，讓object將自己以外的東西儲存成為一個attribute)

```python
class D(C):
    sup = super(C)

D().sup # 可以寫成 `super(C).__get__(D())` -> <super: <class 'C'>, <D object>> 
D().sup.bar # 42
```

- python3之後，super()不用給任何參數就能直接去parent class找attribute，這樣其實最直覺，作者鼓勵盡量用這樣的方式來寫super。