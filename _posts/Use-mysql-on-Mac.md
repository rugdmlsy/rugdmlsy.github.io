---
layout: post
title: "如何在 MacOS 上使用 MySQL"
date:   2025-03-31 14:55:00 +0800
categories: tech
tags: [MacOS, MySQL]
---
## 1. 安装 MySQL
使用 Homebrew 安装 MySQL：
```sh
brew install mysql
```
管理 MySQL 服务：
```sh
brew services list
brew services start mysql
brew services stop mysql
brew services restart mysql
```

> `brew services start mysql` 与 `mysql -u root` 的区别：\
> `brew services start mysql` 用于启动 mysql 服务，`mysql -u root` 用于连接至**已启动的** mysql 以进行交互式操作。
