---
title: pyvista初试
tags: [python, 数据可视化]
categories: 开发
toc: true
date: 2024-11-27 15:27:30
cover: /img/cov5.JPG
top_img: /img/dev.jpg
---


跳槽后工作没那么忙了, 遂开始写博客打法时间.

因为现在的项目需要加载三维模型并显示应力, 所以需要vtk, 为了开发简单, 选择了`python`

### 开发环境

需要 `python > 3.8`

使用 **`pip`** 进行安装

```bash
pip install pyvista
```

使用 **`conda`** 进行安装

需要有[*`Anaconda`*](https://aniakea.github.io/2024/11/29/anaconda%E5%AE%89%E8%A3%85/ "安装教程")环境

```bash
conda install -c conda-forge pyvista
```

详情参考 *[官方文档](https://docs.pyvista.org/getting-started/installation.html)*.

### 尝试

尝试官方示例

```python
import pyvista
mesh = pyvista.Sphere()
mesh.plot()
```

可以看到![image1](https://cdn.jsdelivr.net/gh/Aniakea/blog_pic@main/blog/Images/image1.png)

```python
from pyvista import examples
mesh = examples.download_dragon()
mesh['scalars'] = mesh.points[:, 1]
mesh.plot(cpos='xy', cmap='plasma')
```

可以看到![image2](https://cdn.jsdelivr.net/gh/Aniakea/blog_pic@main/blog/Images/image2.png)

更多请查看 *[官方文档](https://docs.pyvista.org/user-guide/)*

### 尝试通过excel或csv提供数据

为了方便,这里我们需要使用`pandas`.`pyvistaqt`.`pyqt`库

```shell
pip install pandas pyvistaqt PyQt5
```

分为两个数据文件, 一个为提供点的xyz轴数据, 一个提供单元所用的点

*point.xlsx*文件结构为

| 编号 | x | y | z |
| --- | --- | --- | --- |

*node.xlsx*文件结构为

| 编号 | unit | p1 | p2 | ... |
| --- | --- | --- | --- | --- |

考虑到单元类型不唯一, 这里只能选择 `UnStructedGrid`

代码如下

```python
from PyQt5.QtWidgets import (
    QFrame,
    QVBoxLayout,
    QApplication,
    QHBoxLayout,
    QButtonGroup,
    QRadioButton,
)
import pyvista as pv
import numpy as np
import pandas as pd
from pyvistaqt import QtInteractor
import os

class Widget(QFrame):

    def __init__(self):
        super().__init__()
        processor.change(0)
        self.vmin = 0
        self.vmax = 0.1319
        layout = QVBoxLayout()

        self.plotter = QtInteractor(self, auto_update=1)
        layout.addWidget(self.plotter.interactor)

        self.setLayout(layout)

        self.read_excel()

    def read_excel(self):
        point_data = pd.read_excel("./point.xlsx", header=None)
        point_pos = []
        point_keys = []
        for row in point_data.values:
            point_pos.append(row[1:4])
            point_keys.append(row[1])

        cell_data = pd.read_excel("./node.xlsx", header=None)
        cell = []
        cell_shapes = []

        def covert(code):
            return point_keys.index(code)

        for row in cell_data.values:
            cell_shapes.append(pv.CellType.QUAD)
            cell.append([4].append(map(covert, row[2:])))

        self.grid = pv.UnstructuredGrid(cell, cell_shapes, point_pos)

        self.actor = self.plotter.add_mesh(
            self.grid, cmap="viridis", smooth_shading=True, copy_mesh=True
        )


app = QApplication(sys.argv)


widget = Widget()


widget.show()
```

需要注意的一点为cell中的值对应的是point数组的下标, 这里这么写主要是应对point编号非从0开始递增的情况

目前的`unit_type`没有考虑非四边形的情况, 若需要考虑多类型组合的方式, 请参考[官方文档](https://docs.pyvista.org/examples/00-load/create-polyhedron#sphx-glr-examples-00-load-create-polyhedron-py)

从excel中加载数据通常很大, 建议完成读取后存储为模型文件

```python
self.grid.save("./mesh.vtk")
```

这种方式不能储存stl文件, 若是确定只需要表面的点需要存储为stl文件, 需要提取表面

```python
self.grid.extract_surface().save("./mesh.stl")
```

### 尝试添加数据到模型

注意数据的数量必须要和当前显示的模型的点的数量一致

```python
self.grid["range"] = data
self.actor = self.plotter.add_mesh(
            self.grid, 
            cmap="viridis", 
            scalars="range",
            smooth_shading=True, 
            copy_mesh=True
        )
```

### 尝试自定义色彩映射

```python
from matplotlib.colors import LinearSegmentedColormap

def make_cmap(vmin, vmax, n=256):
    """
    创建一个从蓝黑色到黄色再到红色的自定义颜色映射
    
    参数:
    vmin : float
        颜色映射的最小值
    vmax : float
        颜色映射的最大值
    n : int, 可选
        颜色映射中的颜色数量，默认为256
    
    返回:
    pyvista.LookupTable
        PyVista 查找表对象，可用于设置 mesh 的标量条
    """
    # 定义颜色映射的颜色
    colors = ['#000033', '#0000FF', '#00FFFF', '#FFFF00', '#FF0000']
    
    # 创建 matplotlib 颜色映射
    cmap = LinearSegmentedColormap.from_list("custom_blue_black_to_yellow_red", colors, N=n)
    
    # 创建 PyVista 查找表
    lut = pv.LookupTable()
    
    # 设置颜色映射的范围
    lut.SetRange(vmin, vmax)
    
    # 填充查找表
    for i in range(n):
        r, g, b, _ = cmap(i / (n - 1))
        lut.SetTableValue(i, r, g, b)
    
    return lut
```

函数 `make_cmap` 前两个参数决定了映射的上下限, 第三个参数决定映射的色阶数量

colors至少需要两个颜色, 此时色阶在rgb上线性变化, 这里定义了中间色号, 所以色阶呈贝塞尔曲线变化

在 add_mesh 函数中的 `cmap` 参数设置为函数 `make_cmap` 的返回值

```python
self.grid["range"] = data
actor = self.plotter.add_mesh(
    self.grid,
    scalars="range",
    cmap = make_cmap(self.vmin,self.vmax),
    # cmap="viridis",
    smooth_shading=True,
    copy_mesh=True,
)
```
