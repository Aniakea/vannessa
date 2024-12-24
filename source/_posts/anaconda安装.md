---
title: anaconda安装
tags: [python, 开发环境]
categories: 开发
toc: true
date:
cover: https://cdn.jsdelivr.net/gh/Aniakea/blog_pic@main/blog/Images/cov5.JPG
top_img: /img/dev.jpg
---

### 为什么需要 anaconda

python 本身提供了一个项目级别的虚拟环境

但是如果我们需要在不同的项目中使用不同的python版本

这就是python自己提供的 venv 无法做到的事情

所以我们需要一个外部的环境管理器来做这种级别的隔离

这就是使用 anaconda 的原因

### 下载和安装

通过[官网](https://www.anaconda.com/download)或者[清华镜像站](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)下载

官网需要注册, 所以通常建议在镜像站下载

这里主要描述linux下的安装

先下载

```shell
wget --user-agent="Mozilla" https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/${select_version}.sh
```

这里加user-agent是因为清华源http请求需要这个

安装前需要对下载下来的 sh 文件授权

```shell
chmod 777 ./${select_version}.sh
```

直接执行脚本按照提示安装即可

### 基本使用

安装完成后会有一个 conda 指令, 通常情况下我们通过这个指令进行操作

```shell

# 安装包
conda install $pack_name

# 移除包
conda remove $pack_name

# 创建新环境
conda create -n $env_name $pack_name...

```

如果需要导出环境

需要安装 conda-pack 插件

```shell

conda install conda-pack

```

再导出

```shell

 conda pack -n $env_name -o $output

```

导入方式即拷贝导出包到需要导入的电脑, 解压到 anaconda 安装路径下的 env 路径下

为什么说是通常情况呢

如果安装的 anaconda 的版本比较早期, 那么 conda 没有使用 libmamba, Solving environment 会很慢

这时候要么切换solve引擎要么安装 mamba 指令
