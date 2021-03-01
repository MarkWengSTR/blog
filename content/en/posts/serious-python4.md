---
title: Serious-Python note - 4. Handling Timestamps and Time Zones
date: 2021-03-01
keyword:
- python
description: Serious-Python 讀書筆記(4)
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- python

---

Time zone其實是一個非常複雜的問題，不單單只是UTC +- 12 hours而已呀，這章就來好好探討這個問題。

## The Problem of Missing Time Zones
- 當timestamp沒有time zone資訊，那就等於沒有用，因為無法得知確切可以比較的時間。
- 在把timestamp儲存起來的時候也要特別小心，因為當times zone不同時，轉換出來的時間也不同，接下來會詳細說明。
- python 提供了 `datetime.datetime` 可以存放 時間與日期，精度至微秒(ms)，但可惜的是，這個object都沒有提供timezone的資訊。

## Building Default datetime Objects
- 如果想找目前 UTC region 的日期與時間，可以用 ` datetime.datetime.utcnow()` ，想找在目前機器上的日期與時間可以用 `datetime.datetime.now()`
- 但沒有time zone aware真的是不太好用，接下來討論如何加上 time zone。

## Time Zone–Aware Timestamps with dateutil
- 雖然datetime.datetime object沒有timezone資訊，難道就要自己開發自己的datetime object了嗎，其實不用，這邊介紹一個module是 `dateutil` 的 `tz`，可以進到os系統層面找到系統的timezone。
- 需要 ` pip install python-dateutil` ，然後 `from dateutil import tz`
- `tz.gettz(<地點可由/etc/timezone得知，沒給的話會回傳 tzlocal()>)` 就會回傳一個time zone object，我們可以利用這個object來填入 datetime的tzinfo

```python
import datetime
from dateutil import tz
now = datetime.datetime.now()
tz = tz.gettz('Asia/Taipei')
now.replace(tzinfo=tz)
```
- 如果dateutil進去不你的os抓這個資料的話，可以用 `datetutil.zoneinfo` 來抓
- 有的時候，系統不知道目前的time zone，就可以直接用 `datetutil.tz.gettz()` 來找出local的time zone，也可以用這個來替換 `tzinfo`

## Serializing Time Zone–Aware datetime Objects
- 目前看到 datetime 物件回傳的都是 `datetime.datetime(2021, 2, 25, 22, 27, 59, 871981)` 這種資訊，但如果是不同語言透過HTTP REST API來call的話，應該要回傳一個格式化的資訊回去，可以使用 `isoformat` 這個method來做。
- 以下開發了一個 utcnow 的function來做這件事，得確保包含 timezone資訊(+00:00 part)
```python
  import datetime
  fom dateutil import tz

  def utcnow():
      return datetime.datetime.now(tz=tz.tzutc())

  print(utcnow())
  #  datetime.datetime(2021, 2, 26, 3, 16, 26, 323306, tzinfo=tzutc())
  print(utcnow().isoformat())
  # '2021-02-26T03:17:05.038592+00:00'
```
- 那如果有iso format要怎麼轉回來呢? 需要先  `pip install iso8601` ，可以用  ` iso8601.parse_date(<iso format>)`

