+++
title = 'ConvexHull2D GrahamScan'
date = 2023-11-27T02:50:04+08:00
draft = false
series = ["计算几何"]
series_weight = 1
seriesNavigation = true
+++
## 简介
`Graham Scan`算法是一种有效计算凸包的方法。
  <div style="display: flex; justify-content: center;">
  <image src="/Geometry/GrahamScan.gif"  style="width: 40%; height: auto; margin-right: 10px;">
  </div>

## 算法步骤

##### 1. 找到基准点
- 从点集中选取最低（或最高）Y坐标的点。
- 如果有多个点具有相同的Y坐标，则选择X坐标最小（或最大）的点作为基准点。

##### 2. 排序
- 按照每个点相对于基准点的极角进行排序。

##### 3. 创建凸包
- 初始化栈，将基准点和排序后的前两个点放入栈中。
- 遍历排序后的其他点，对于每个点执行以下操作：
  - 当栈顶的两个点与当前点不构成“右转”时，从栈中弹出顶部点。
  - 将当前点压入栈。

##### 4. 完成凸包
- 遍历结束后，栈中剩余的点即构成凸包的顶点。

## 优点
- **效率高**：时间复杂度为`O(n log n)`，主要耗时在于排序步骤。

## 缺点
- **不适用于动态点集**：对于频繁变动的点集，需要重新计算整个凸包。

## 参考文献
[1]计算几何算法与应用:第3版.第1章.邓俊辉译
## 项目链接
https://github.com/yexuan333/GeoAlgoVisual