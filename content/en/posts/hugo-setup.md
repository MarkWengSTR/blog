---
title: 用Hugo建立blog
date: 2020-05-21
keyword:
- hugo
- blog
- GitHub
- static site
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
去hugo的github上找[最新的release版本](https://github.com/gohugoio/hugo/releases)來下載，hugo 與 extend 都裝一下比較好，因為有些theme會用到sass/scss，像我用的zzo就會用。


#### 安裝指令
不推薦用apt裝是因為，apt的hugo版本不是最新的。

```bash
sudo dpkg -i hugo_0.70.0_Linux-64bit.deb #寫文章時的最新版本是0.70.0
sudo dpkg -i hugo_extended_0.70.0_Linux-64bit.deb
```
裝完可以先用 `hugo version` 來看一下有沒有安裝成功。

#### 新增hugo專案指令
```bash
hugo new site xxxx # xxxx 請改成自己的專案名稱
cd xxxx
hugo server
```

在瀏覽器開啟 localhost:1313 沒有錯誤就是OK摟，空白是正常的。


## 選擇theme並開啟hugo
去 https://themes.gohugo.io/ 選個喜歡的theme吧!  我個人是選擇 `zzo`，因為很漂亮而且功能很齊全。

要怎麼把theme裝到剛剛產生的hugo專案上呢，非常簡單的，照著以下步驟就行了。

目前在xxxx目錄裡(以zzo為例)：
```bash
<CTRL> + c (先關掉server)
git init
git clone https://github.com/zzossig/hugo-theme-zzo.git themes/zzo
hugo server
```
就看theme的頁面裡面怎麼做，照做就是了。

## deploy on github
(以下是我自己的步驟，網路上其他人好像都用git submodule去管理，由於自己還不太熟悉，之後慢慢再更新)
* 申請github帳號

* 開兩個repositories
  一個放整個hugo專案，一個用來deploy上線(它只需要存hugo build出來的public資料夾就好)。
  放專案的名稱應該就是xxxx，而delpoy的要叫做 `xxxx.github.io` 。

* 第一次要做的步驟： (以下為一系列commemd)

``` shell
  # 目前在專案的目錄裡。
  # 參考github push的步驟
  git init

  git remote add origin https://github.com/<你的GitHub>/xxx.git

  vi .gitignore # 產生.gitignore

  # 按小寫英文a可以編輯，填入 public/，:wq 存取&退出
 # 目的是不想讓產生的public使這個專案一直肥大

  git push origin master
  # 首次推的話會需要名稱、email以及帳號密碼(每次都需要，除非額外設定，這邊不講)

  # Build the project.
  hugo # if using a theme, replace with `hugo -t <YOURTHEME>`

  # 將新產生的public folder複製到原本專案外
  cp -r public ../

  # Go To Public folder
  cd ../public

  # 再產生一個delpoy的git版本
  git init

  git remote add origin https://github.com/<你的GitHub>/xxxx.github.io.git

  git push origin master
```
用瀏覽器開啟 `www.xxxx.github.io` 就可以看到啦!!!

* 之後寫完文章要deploy的話，都可以利用以下指令來完成delpoy, 最好是寫成一個shell script以後run它就好。
``` shell
  #!/bin/sh

  # 在專案的目錄裡。
  # If a command fails then the deploy stops
  set -e

  printf "\033[0;32mDeploying updates to GitHub...\033[0m\n"

  # remove old public file.
  rm public

  # Build the project.
  hugo # if using a theme, replace with `hugo -t <YOURTHEME>`

  # 將新產生的public folder複製到原本專案外
  cp -r public ../

  # Go To Public folder
  cd ../public

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

