---
title: Wordpress plugin development by Vue.js (vue-cli setting, hot reload)
date: 2021-07-05
keyword:
- vue
- wordpress
description: 使用vue搭配vue-cli來開發wordpress plugin
draft: false
hideToc: false
enableToc: true
enableTocContent: true
author: Mark
tags:
- vue
- wordpress
---

## 前言

最近也剛好有朋友有個wordpress後台功能擴增的需求，一個小型的商品ERP，由於在wordpress裡面，因此直接使用wordpress當後端API，而前端就用Vue，練一下這兩個這幾年非常夯的framework。

## Use Vue-cli in Wordpress admin page

以為Vue跟wordpress都是很多人在使用的工具，應該很好找學習資源，個別找是真的蠻多資源沒錯，但是兩個加在一起的教學就非常的少，不然就是非常複雜，沒有比較簡單能上手的工具，找了很久之後終於找到了[這篇文章](https://devs-group.medium.com/wordpress-vue-js-with-webpack-and-hot-reload-7c4faea9d0d9)， 需要做一些設定，之後再使用vue-cli在wordpress裡面hot-reload開發Vue。 

[專案連結](https://github.com/MarkWengSTR/wordpress-vue2-starter)在此。

## Wordpress wp_rest_controller

後端部分因為已經習慣使用model與controller的方式開發，還好wordpress也提供了wp_rest_controller的工具，可以做好router與controller的部分，跟db溝通的部分拆出一個repository來使用。

為了讓以後開發能夠有基礎可以參考，我製作了一個簡單的[CURD的branch](https://github.com/MarkWengSTR/wordpress-vue2-starter/tree/study/full-CRUD)，把前端Vue與後端wordpress串起來。

這個專案也有將常用的vue-boostrap, font-awsone裝到切開的branch供以後的專案使用，歡迎有興趣使用Vue開發wordpress plugin的朋友們一起討論~~。

