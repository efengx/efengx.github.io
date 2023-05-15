---
title: "My First Post Start Hugo"
date: 2023-04-26T09:56:23+08:00
draft: true
---
## Introduction

记录常用的Hugo指令，用于快速start

<!--more-->

## 在Github上托管

No1. 创建Github账号

No2. 将本地的存储库推送到Github

```base
# 将本项目添加到远程存储库
git init
git remote add origin https://github.com/efengx/efengx.github.io.git
git add .
git commit -m "update blog post"
# 将本地当前分支推送到远程main分支
git push origin main --force

# (可选)删除远程分支
git push origin --delete master
```

No3. 访问您的Github存储库。在主菜单中选择**设置** **>** **页面**

No4. 将**Source**更改为**Github Actions**

No5. 在本地存储库中创建一个空的文件

```base
.github/workflows/hugo.yaml
```

No6. 更新hugo.yaml文件内容，[内容详情](https://gohugo.io/hosting-and-deployment/hosting-on-github/)

## huggingface space 使用

No1. streamlit本地启动命令

```base
streamlit run app.py
```

No2. 本地配置secrets

```
vim ~/.streamlit/secrets.toml
# 添加以下内容
"read_key"=""
```

## hugo 文章操作

创建新的文章

```base
hugo new post/rjxai-model-card.md
```

启动本地环境

-D 表示可以查看 draft=true 的文章（否则只能查看 draft=false 的文章）

```base
hugo server -D 
```

## 快速参考

查看手册 [Hugo docs](https://gohugo.io/getting-started/quick-start "start hugo")

查看主题 [Ananke](https://github.com/theNewDynamic/gohugo-theme-ananke "github")

多语言支持 [创建文章](https://gohugo.io/content-management/multilingual/)
