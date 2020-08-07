---
title: git代码分支管理规范
date: 2020-08-07 10:20:38
tags: [git, git分支管理]
---

## GIT代码提交规范

初始化 Git 仓库

1. 克隆远程仓库

```bash
git clone <仓库地址>
```

2. 提交并推送初始版本到master

![git-master](git-master.jpg)

```bash
# 提交本地修改:
git add .
git commit –m “提交日志”
# 推送 master 分支:
git push origin master
```

## 创建开发分支

. 克隆master分支到 develop分支，用于开发

![git-develop](git-develop.jpg)

```bash
# 从 master 分支上创建 develop 分支:
git checkout –b develop master
# 推送 develop 分支:
git push origin develop
```

## 开发新功能

. 在 develop 分支上实现新功能，包括：新特性与Bug修复

![git-bug](git-bug.jpg)

```bash
# 切换到 develop 分支:
git checkout develop
# 提交本地修改:
git add .
git commit –m “提交日志”
# 推送 develop 分支:
git push origin develop
```

. 若存在多个新特性可以并行开发，可创建一个或多个 feature 分支(特性分支)，命名规范:feature-分支创建日期-新特性关键字，例如:feature-20190919-i18n

![git-bug-1](git-bug-1.jpg)

. 新特性开发完毕后，需将 feature 分支合并到 develop 分支，最后需删除 feature 分支

## 准备发布新版本

1. 确定 develop 分支是否开发完毕

2. 若开发完毕，则创建 release 分支，命名规则:release-分支创建日期-待发布版本号

3. 首先在 release 分支中升级 Maven 版本号，例如:1.0.0-SNAPSHOT，然后修改 version.ini 文件(便于在部署时查看当前版本号)，最后在 release 分支上做一次提交

4. 通知测试可对 release 分支进行测试

![git-release](git-release.jpg)

```bash
# 从 develop 分支上创建 release 分支:
git checkout –b release-20190919-v1.0.0 develop
```

## 修复待发布版本的Bug

开发人员在 release 分支上修复测试人员提交给自己的 Bug

![git-fix](git-fix.jpg)

```bash
# 切换到 release 分支:
git checkout release-20190919-v1.0.0
# 提交本地修改:
git add .
git commit –m “提交日志”
# 推送 release 分支:
git push origin release-20190919-v1.0.0
```

## 发布新版本

1. 集成测试

2. 冒烟测试

3. 发布新版本

![git-release-1](git-release-1.jpg)

```bash
# 合并 release 分支到 master 分支:
git checkout master
git merge --no-ff release-20190919-v1.0.0
# 合并 release 分支到 develop 分支:
git checkout develop
git merge --no-ff release-20190919-v1.0.0
# 在 master 分支上创建标签:
git tag tag-20190919-v1.0.0
# 删除本地 release 分支:
git branch –d release-20190919-v1.0.0
# 删除远程 release 分支:
git push origin :release-20190919-v1.0.0
```

4. 修复线上Bug

- 第一步: 创建hotfix分支:

```bash
# 从某个标签上创建 hotfix 分支:
git branch hotfix-20190919-v1.0.1 tag-20190919-v1.0.0
```

- 第二步: 验证Bug是否修复

- 第三步: 创建标签并发布新版本

![git-bug-2](git-bug-2.jpg)

```bash
# 合并 hotfix 分支到 master 分支:
git checkout master
git merge --no-ff hotfix-20190919-v1.0.1
# 合并 hotfix 分支到 develop 分支:
git checkout develop
git merge --no-ff hotfix-20190919-v1.0.1
# 在 master 分支上创建标签:
git checkout master
git tag tag-20190919-v1.0.1
# 删除本地 hotfix 分支:
git branch –d hotfix-20190919-v1.0.1
# 删除远程 hotfix 分支:
git push origin :hotfix-20190919-v1.0.1
```

### (若无法将hotfix 分支合并到 master 与 develop 分支时，应该如何发布？)

比如:现在 master 分支已经发布了 2.0.0 版本(代码结构发生了很大的变化)，但线上发现了一个 1.0.0 版 本的 Bug，当修改了 Bug后，是无法再合并到 master 与 develop 分支的，开发主管需完成以下任务:

1) 直接在 hotfix 分支上创建标签
2) 删除 hotfix 分支(分支删除了，只要标签还在，版本就可以找得回来)
3) 手工修改 develop 分支中的代码(在后续发布时再合并到 master 分支中)

## 定制化项目

当需要对某项目进行定制化时，可从源项目的 Git 仓库 fork 出一个新的 Git 仓库:

![git-made](git-made.jpg)

当 fork 后，对 repo1 做出的任何修改，都不会影响到 repo2
在 repo2 中修复了 Bug，可通过 Merge Request 的方式提交给 repo1
在 repo2 中可随时拉取 repo1 中的提交，但 repo1 不能拉取 repo2 中的提交

## Maven 版本号命名规范

Maven 版本号命名规范
格式:Major.Minor.Micro

| 版本 | 说明 |
| --- | --- |
|Major 版本| 架构调整 |
|Minor 版本| 新特性 |
|Micro 版本| Bug 修复、优化 |

Git 分支类型

| 分支 | 用途 |
| --- | --- |
|master 分支(主分支)| 稳定版本 |
|develop 分支(开发分支)| 最新版本 |
|release 分支(发布分支)| 发布新版本 |
|hotfix 分支(热修复分支)| 修复线上 Bug |
|feature 分支(特性分支)| 实现新特性 |

![git-fix-1](git-fix-1.jpg)

## 参考资料
