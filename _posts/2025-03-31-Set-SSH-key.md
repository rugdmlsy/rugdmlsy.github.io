---
layout: post
title: "如何配置 SSH 密钥"
date:   2025-03-31 14:53:00 +0800
categories: tech
tags: [linux, server, ssh]
---
生成 SSH Key：
```sh
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
添加 SSH Key 到 SSH 代理：
```sh
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```
复制 SSH 公钥：
```sh
cat ~/.ssh/id_rsa.pub
```
添加至 Github；  
添加至服务器：
```sh
ssh-copy-id -i ~/.ssh/id_rsa.pub username@serverIP
```