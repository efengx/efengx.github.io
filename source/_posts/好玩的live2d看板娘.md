---
title: 好玩的live2d看板娘
date: 2019-03-06 23:20:27
tags: [hexo,live2d]
---

创建blog的主要目的 >.<
确实好玩 >.<
<!-- more -->

## 安装 live2d

```bash
% npm install --save hexo-helper-live2d
# OR
% yarn add hexo-helper-live2d
```

[点我查看live2d项目](https://github.com/EYHN/hexo-helper-live2d)

## 修改 _config.xml 配置

将以下内容添加到 _config.xml 配置文件中

```json
live2d:
  enable: true
  scriptFrom: local
  pluginRootPath: live2dw/
  pluginJsPath: lib/
  pluginModelPath: assets/
  tagMode: false
  debug: false
  model:
    use: live2d-widget-model-shizuku
  display:
    position: right
    width: 150
    height: 300
  mobile:
    show: true
```

此时还没有看板娘，只是加了一个显示模块。还需要下载看板娘

## 下载看板娘 live2d-widget-models

[点我下载看板娘资源](https://github.com/xiazeyu/live2d-widget-models)

```bash
% npm install {packagename}
# OR
% yarn add {packagename}
```

packagename 中的可选资源本站采用的是以下资源

- live2d-widget-model-shizuku
[点我查看其他跟多看板娘资源](https://github.com/xiazeyu/live2d-widget-models/tree/master/packages)
[点我查看模块与看板娘实际对照表, 名字在上图片在下](https://huaji8.top/post/live2d-plugin-2.0/)
(注意安装之后需要重启运行 hexo server 命令之后看板娘才会加载出来)
