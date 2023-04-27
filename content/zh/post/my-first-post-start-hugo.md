---
title: "My First Post Start Hugo"
date: 2023-04-26T09:56:23+08:00
draft: false
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

查看手册 [Hugo docs](https://gohugo.io/getting-started/quick-start "start hugo")

查看主题 [Ananke](https://github.com/theNewDynamic/gohugo-theme-ananke "github")

多语言支持 [创建文章](https://gohugo.io/content-management/multilingual/)
