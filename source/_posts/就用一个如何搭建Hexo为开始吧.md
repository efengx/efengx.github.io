---
title: 就用一个如何搭建Hexo为开始吧
date: 2019-03-06 21:40:31
tags: [blog,hexo]
---
经历了很多，之前开过很多的blog，基本上都是自己购买服务器搭建的，大部分是基于php的产品。
随着技术的发展很多的blog都被淘汰了，也有很多随着服务器运营商的关闭而随之灰灰了>.<
基本在第三方平台撰写博客（虽然这是一种很好的方式，平台会维护整个blog并且也可以帮助推荐），也许是源于一颗爱折腾的心。
今天偶然在网上看到一个live2dw的看板萌娘，戳中了要害>.<   之前一直也想基于github搭建一个blog毕竟被微软收购了，应该不会被关闭吧（为之前的博客默哀3秒...）刚好顺这股干劲走一个。
<!-- more -->

## 安装 hexo-cli

[可以点击我去hexo的github](https://github.com/hexojs/hexo)

开命令行界面window cmd/mac os terminal/linux (这个不用呢只要不是图形界面进入就是了，我很少在linux下用图形界面>.<)。
命令很简单一行就搞定，这里提供两种方式 npm 和 yarn 的方式

```bash
% npm install hexo-cli -g
# OR
% yarn global add hexo-cli
```

安装需要一点时间，具体视网络情况而定，我大概用了5分钟吧（坑爹的网络...）
-g 和 global 代表全局安装，也就是其他所有项目中的都可以使用这个插件不需要在次安装了。
（补充下，用README.md写博客真心很舒服，总算脱离了富文本编辑框>.<）

## 创建博客

```bash
% hexo init blog
% cd blog
```

blog为博客的名称，可以根据自己的命名喜好自定义一个即可。
接下来可以直接在命令行界面下如上用 cd 命令进入blog目录，也可以下载一个[vscode点我进入下载页面](https://code.visualstudio.com/)目前非常好用的Code编辑器，不得不说Microsoft还是很有几把刷子的。

## 启动

```bash
% hexo server
```

启动之后在输出中找到url，copy到浏览器中即可。（居然不能自动打开浏览器，我等了一会以为在网络上飞，谁知道没这功能...）

## 创建文章

```bash
# 创建一个文章
hexo new "就用一个如何搭建Hexo为开始吧"
# 创建一个带目录的文章
hexo new post [title]
hexo generate
```

自行查看更多[命令](https://hexo.io/zh-cn/docs/commands)

## 发布到github page

### 将项目与github进行链接

```bash
% git init
# 进行远程链接，请用自己的github地址。
% git remote add origin https://github.com/efengx/efengx.github.io.git
# 查看关联情况
% git remote -v
```

### 安装 hexo-deployer-git

```bash
% npm install hexo-deployer-git --save
# OR
% yarn add hexo-deployer-git
```

### 修改_config.yml 配置

```bash
deploy:
  type: git
  repo: https://github.com/efengx/efengx.github.io.git
  branch: gh-pages
```

repo 改成自己的github项目地址

### 部署

```bash
# 可选操作（清除缓存）
% hexo clean
# 生成静态页面
% hexo generate
% hexo g
# 发布到github（根据_config.yml中的deploy）
% hexo deploy
% hexo d

# 调试模式
% hexo s --debug
```

## 主题（可选）

### 安装 next 主题

```bash
# 在blog目录下运行
% git clone https://github.com/iissnan/hexo-theme-next themes/next
```

### 修改 _config.yml 配置文件

```bash
# landscape 为当前主题
> theme: landscape
```

## 创建新的页面

```bash
hexo new [layout] <title>
```

layout: post(默认), page, draft

## 访问

[blog.ofengx.com](https://blog.ofengx.com/)
