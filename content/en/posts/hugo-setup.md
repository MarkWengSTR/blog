---
title: 用Hugo建立blog
date: 2020-05-21
description: 從無到有利用hugo建立部落格 + delpoy github static page
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- hugo
- blog
image: 
---

<!--more-->

## WSL(Windows Subsystem for Linux) ubuntu 18.04 如何安裝 hugo

#### 先將apt升級
```bash
sudo apt-get update -y
sudo apt-get upgrade -y
```

#### 下載指令
```bash
wget https://github.com/gohugoio/hugo/releases/download/v0.70.0/hugo_0.70.0_Linux-64bit.deb
wget https://github.com/gohugoio/hugo/releases/download/v0.70.0/hugo_extended_0.70.0_Linux-64bit.deb
```
去hugo的github上找最新的release版本來下載，hugo 與 extend 都裝一下比較好，因為有些theme會用到sass/scss，像我用的zzo就會用
https://github.com/gohugoio/hugo/releases 像我這個時期最新的版本是0.70.0,
不推薦用apt裝是因為，apt的hugo版本不是最新的。

#### 安裝指令
```bash
sudo dpkg -i hugo_0.70.0_Linux-64bit.deb
sudo dpkg -i hugo_extended_0.70.0_Linux-64bit.deb
```

可以先用 `hugo version` 來看一下有沒有安裝成功

#### 新增hugo專案指令
```bash
hugo new site xxxx
cd xxxx
hugo server
```
(xxxx 請改成自己的專案名稱)

在瀏覽器開啟 localhost:1313 沒有錯誤就是OK摟，空白是正常的


## 選擇theme並開啟hugo
去 https://themes.gohugo.io/ 選個喜歡的theme吧!
我個人是選擇 `zzo`，因為很漂亮而且功能很齊全
要怎麼把theme裝到剛剛產生的hugo專案上呢，非常簡單

目前在xxxx目錄裡(以zzo為例)
```bash
<CTRL> + c (先關掉server)
git init
git clone https://github.com/zzossig/hugo-theme-zzo.git themes/zzo
hugo server
```
就看theme的頁面裡面怎麼做，照做就是了，我的網頁最下方有zzo的網站連結可參考

## deploy on github
* 申請帳號
首先當然要申請github帳號拉

* 開兩個repositories
  一個放整個hugo專案，一個用來deploy上線
  放專案的名稱應該就是xxxx拉，而delpoy的要叫做 `xxxx.github.io`

* 利用以下指令來完成delpoy, 最好是寫成一個shell script以後run它就好
``` shell
  #!/bin/sh

  # If a command fails then the deploy stops
  set -e

  printf "\033[0;32mDeploying updates to GitHub...\033[0m\n"

  # Build the project.
  hugo # if using a theme, replace with `hugo -t <YOURTHEME>`

  # Go To Public folder
  cd public

  # Add changes to git.
  git add .

  # Commit changes.
  msg="rebuilding site $(date)"
  if [ -n "$*" ]; then
    msg="$*"
  fi
  git commit -m "$msg"

  # Push source and build repos.
  git push origin master
```
用 `www.xxxx.github.io` 就可以看到啦!!!

