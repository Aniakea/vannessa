---
title: 在c++中嵌入python解释器
tags: [c++, python]
categories: 开发
toc: true
date:
cover: https://cdn.jsdelivr.net/gh/Aniakea/blog_pic@main/blog/Images/IMG_0239.PNG
top_img: /img/dev.jpg
---

还是哪个展示应力模型的项目, 由于是基于之前的项目的, 而之前的项目的gui是qt做的, 之前又没有告诉我需要嵌入这个程序, 而我python的已经写完了

所以开始研究嵌入方案

首先需要确定python是否独立运行于老的gui程序

如果独立运行的情况, 我需要在python端额外写一个网络模块来和老gui程序进行交互,两边都得改, 怪麻烦的, 不考虑

非独立运行的话, 就需要嵌入python解释器来执行我写的python代码

### 准备

首先我们先安装 pybind11 库, 这是个 head-only 的库, 所以只需要下载源码, 在项目中添加头文件搜索的路径即可

先下载, ~~最简单的方案就是去github下载zip(he~~

为了不出现违法收入, 我们使用`gitee`的镜像, [链接](https://gitee.com/mirrors/pybind11)

直接一键下载或者使用git

```bash
# if has ssh
git clone git@gitee.com:mirrors/pybind11.git

# or use https
git clone https://gitee.com/mirrors/pybind11.git
```

如果是项目是cmake的话可以直接用 `FetchContent`

```cmake
include(FetchContent)

FetchContent_Declare(
        pybind11
        GIT_REPOSITORY https://gitee.com/mirrors/pybind11.git
        GIT_TAG v2.13
)

FetchContent_MakeAvailable(pybind11)
```

如果环境变量中包含python的路径会自动寻找到python和libpython

可惜的是, 这~~傻逼~~项目用的qmake, 所以我们只能在pro文件里手动加include路径和link路径了

### 使用

这里建议去逛[官方文档](https://pybind11.readthedocs.io/en/stable/index.html), 比我这啰啰嗦嗦好多了, 在这我只讲一些我遇见的坑

1. 解释器需要自己设置`pythonhome`, 不设置的话啥都import不了
但是pybind11库没有提供接口, 只能使用libpython提供的c接口, libpython提供的接口命名又怪的要死

1. 暂留, 之后想起来了再更新
