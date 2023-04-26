---
title: "My First Post Start Hugo"
date: 2023-04-26T09:56:23+08:00
draft: true
---
## Introduction

记录常用的Hugo指令，用于快速start

## 在Github上托管

No1. 创建Github账号

No2. 构建静态页面&&将本地的存储库推送到Github

```base
# 构建静态页面
hugo
# 将本地静态页面推送到github
cd public/
git init
git remote add origin https://github.com/efengx/efengx.github.io.git
git add .
git commit -m "update blog post"
# 将本地当前分支推送到远程gh-pages分支
git push origin gh-pages

# 删除远程分支
git push origin --delete master
```

No3. 访问您的Github存储库。在主菜单中选择**设置 > 页面**


查看手册 [Hugo docs](https://gohugo.io/getting-started/quick-start "start hugo")

查看主题 [Ananke](https://github.com/theNewDynamic/gohugo-theme-ananke "github")
