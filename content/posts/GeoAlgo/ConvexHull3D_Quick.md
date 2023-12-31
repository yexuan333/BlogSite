+++
title = 'ConvexHull3D Quick'
date = 2023-12-17T22:06:22+08:00
draft = false
series = ["计算几何"]
series_weight = 3
seriesNavigation = true
+++

一组点的凸包是最小的凸集的点。凸包是数学和计算几何的基本结构。
例如，Boardman [1993] 在光谱数据分析中使用了凸包，而Weeks [1991] 则利用凸包来确定有尖端的双曲3-流形的规范三角剖分。其他问题可以归结为凸包问题——例如，半空间交集、Delaunay三角剖分、Voronoi图和幂图。Aurenhammer [1991] 在其综述文章中描述了这些结构在网格生成、文件搜索、聚类分析、碰撞检测、晶体学、冶金学、城市规划、制图学、图像处理、数值积分、统计学、球形包装和点定位中的应用。

提出并分析了一种处理点的更有效策略，以便于处理有内部点的分布。结果是一个对于具有内部点的分布来说更快的算法。