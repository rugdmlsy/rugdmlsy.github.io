---
layout: post
title: "如何使用 GIthub Pages + Jekyll 搭建博客"
date:   2025-03-28 16:00:00 +0800
categories: tech
---
## 1. 配置 Github 仓库
创建名为 yourusername.github.io（将 yourusername 替换成你的用户名）的 repository；  

将 repository 克隆至本地：
```sh
git clone git@github.com:yourusername/yourusername.github.io.git  # 替换成你的用户名
cd yourusername.github.io
```
## 2. 配置 ruby 和 Jekyll
Jekyll 使用 ruby 管理，因此需要安装 ruby。  
使用 rbenv 管理 ruby 版本：
```sh
# 安装 rbenv
brew install rbenv

# 初始化并配置 Shell
rbenv init
echo 'eval "$(rbenv init - zsh)"' >> ~/.zshrc  # 或 ~/.bashrc
source ~/.zshrc

# 列出 ruby 版本列表
rbenv install -l

# 安装 ruby 并设为默认版本
rbenv install 3.4.2
rbenv global 3.4.2
source ~/.zshrc

# 验证
ruby -v
```
安装 Jekyll 和 Bundler：
```sh
gem install bundler jekyll

# 验证安装
jekyll -v  
```
## 3. 本地搭建 Jekyll 博客
初始化 Jekyll 项目：
```sh
jekyll new . --force  # 强制覆盖现有文件（如克隆的空仓库）
```
本地预览:
```sh
bundle exec jekyll serve
```
- 访问 http://localhost:4000 查看效果。
- 按 Ctrl+C 停止服务。

## 4. 部署到 GitHub
```sh
git add .
git commit -m "初始化博客"
git push origin main
```
现在，你可以通过 yourusername.github.io 来访问你的专属博客了！