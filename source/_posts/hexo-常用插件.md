---
title: hexo 必备的插件
date: 2019-03-07 11:55:33
categories: Other
tags: [hexo]
---

## 安装 rss

```bash
% npm install hexo-generator-feed --save
# OR
% yarn add hexo-generator-feed
```

## 修改 _config.yml 配置

```bash
feed:
  type: atom
  path: atom.xml
  limit: 20
  hub:
  content:
  content_limit: 140
  content_limit_delim: ' '
  order_by: -date
  icon: icon.png
```
<!-- more -->

## 参考资料

[点我查看插件源码及配置说明](https://github.com/hexojs/hexo-generator-feed)
