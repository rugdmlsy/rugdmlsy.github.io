---
layout: post
title: "如何在服务器上设置代理"
date:   2025-03-31 14:35:00 +0800
categories: tech
tags: [linux, server, proxy, clash]
---
## 1. 代理软件
可以选用 [Clash-Premium](https://github.com/DustinWin/proxy-tools/releases/tag/Clash-Premium) 或 [clash-for-linux](https://github.com/wnlen/clash-for-linux?tab=readme-ov-file) 等. 以 Clash-Premium 为例：
```sh
# 下载 Clash 内核
wget https://github.com/DustinWin/proxy-tools/releases/download/Clash-Premium/clashpremium-nightly-linux-amd64-v3.tar.gz

# 解压
tar -xzvf clashpremium-nightly-linux-amd64-v3.tar.gz -C ~/clash

# 重命名
mv ~/clash/clashpremium-nightly-linux-amd64-v3.tar.gz ～/clash/clash

# 添加可执行权限
chmod +x ~/clash/clash
```
第一次运行 Clash 时需要下载 `Country.mmdb` 文件。没有代理时自动下载较慢，可以[手动下载](https://github.com/Dreamacro/maxmind-geoip/releases)至 Clash 目录：
```sh
wget https://github.com/Dreamacro/maxmind-geoip/releases/download/20250312/Country.mmdb
mv Country.mmdb ~/clash/Country.mmdb
```
然后需要下载代理配置文件 `config.yaml`，下载地址由代理商提供：
```sh
wget link/to/config.yaml
mv config.yaml ~/clash/config.yaml
```
运行 Clash：
```sh
cd ~/clash
./clash -d .
```
可以在运行命令后添加 & 使得 Clash 在后台静默运行：
```sh
./clash -d . &
```
将别名添加至终端配置文件（~/.zshrc 或 ~/.bashrc 等）便于使用：
```sh
alias clash='~/clash/clash -d ~/clash &'
```
## 2. 环境配置
需要在命令行中使用代理时，需设置环境参数：
```sh
export HTTP_PROXY='127.0.0.1:7890'
export HTTPS_PROXY='127.0.0.1:7890'
```
可以编写一个脚本来自动管理代理配置：
```sh
mkdir ~/Scripts
vim ~/Scripts/toggle_proxy.sh
```
```sh
#!/bin/bash

# 检查代理配置
if [[ -z "$http_proxy" && -z "$https_proxy" ]]; then
    # 设置代理
    export http_proxy="http://127.0.0.1:7890"
    export https_proxy="http://127.0.0.1:7890"
    export HTTP_PROXY="http://127.0.0.1:7890"
    export HTTPS_PROXY="http://127.0.0.1:7890"
    echo "Proxy enabled: http://127.0.0.1:7890"
else
    # 取消代理
    unset http_proxy
    unset https_proxy
    unset HTTP_PROXY
    unset HTTPS_PROXY
    echo "Proxy disabled"
fi

```
将别名添加进终端配置文件：
```
alias toggleproxy="source ~/Scripts/toggle_proxy.sh"
```
之后，只需要使用 `toggleproxy` 命令即可自动设置代理了：
```sh
root@ubuntu:~$ toggleproxy 
Proxy enabled: http://127.0.0.1:7890
root@ubuntu:~$ toggleproxy 
Proxy disabled
```
> 注意，设置 alias 时使用 `source ~/Scripts/toggle_proxy.sh` 而非 `~/Scripts/toggle_proxy.sh`，因为 sh 脚本运行在一个**子终端**，其进行的环境配置不影响父终端，因此会出现代理配置无效的情况

有一些软件如 git 不使用 HTTP_PROXY 参数作为代理，此时需要手动配置：
```sh
git config --global http.proxy "http://127.0.0.1:7890"
git config --global https.proxy "https://127.0.0.1:7890"
```
同样可以编写脚本一键切换代理：
```sh
vim ~/Scripts/git_proxy.sh
```
```sh
#!/bin/bash

# 检查 git 代理配置
git_proxy=$(git config --global --get http.proxy)

if [[ -z "$git_proxy" ]]; then
    # 设置代理
    git config --global http.proxy "http://127.0.0.1:7890"
    git config --global https.proxy "https://127.0.0.1:7890"
    echo "Git proxy enabled: http://127.0.0.1:7890"
else
    # 取消代理
    git config --global --unset http.proxy
    git config --global --unset https.proxy
    echo "Git proxy disabled"
fi
```
```sh
chmod +x ~/Scripts/git_proxy.sh
alias gitproxy='~/Scripts/git_proxy.sh'
```