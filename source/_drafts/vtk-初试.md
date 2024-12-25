---
title: vtk初试
tags: [
  c++, 数据可视化
]
categories: 开发
toc: true
cover: https://cdn.jsdelivr.net/gh/Aniakea/blog_pic@main/blog/Images/cov5.JPG
top_img: /img/dev.jpg
date: 2024-12-23 15:40:19
---

之前写的哪个软件需要进行优化, 所以这里我们需要将之前python开发的部分native化

所以我们需要使用vtk和libtensorflow进行开发

首先完成vtk部分的开发

## 下载与安装

### 下载

既然要用那就用最新的版本

这里我们使用 `9.x` 版本, 点击[查看](https://vtk.org/download/)

可以看到如下页面![20241223173813](https://cdn.jsdelivr.net/gh/Aniakea/blog_pic@main/blog/Images/20241223173813.png)

我们这里下载源码自行编译

点击 source 栏的 VTK-9.4.0.tar.gz 下载

或者直接在终端执行

```shell
wget https://www.vtk.org/files/release/9.4/VTK-9.4.0.tar.gz
```

或者使用 git

```shell
git clone -b v9.4.0 --depth=1 git@gitlab.kitware.com:vtk/vtk.git
```

### 安装

由于当前软件是在 windows 平台下运行, 所以我们这里主要描述 windows 下的安装流程

## 集成使用
