# ConvexHull3D Incremental


## 简介
三维凸包增量法是一种用于构建三维凸包的有效算法，它基于不断添加新的点来逐步构建凸包。
  <div style="display: flex; justify-content: center;">
  <image src="/Geometry/Incremental.gif"  style="width: 40%; height: auto; margin-right: 10px;">
  </div>

## 算法重点
- 新增点
  - 假设$p_n$在当前凸包外，从$p_n$向凸包望去，可见的小平面组成了一条地平线$horizon$,只需将可见的小平面全部删掉，然后用$p_n$依次与$horizon$连接成新的小平面
  <div style="display: flex; justify-content: center;">
  <image src="/Geometry/horizon.png"  style="width: 40%; height: auto; margin-right: 10px;">
  <image src="/Geometry/addNewPoint.png"  style="width: 40%; height: auto; margin-right: 10px;">
  </div>

- 通过冲突信息加速遍历
  - 对于凸包中的每一个小平面$f$维护一个集合$f_{conflict}$,这个集合包含小平面$f$的可见点信息
  - 对于每一个点$p_n$也要维护一个集合$p_{conflict}$，这个集合包含点$p_n$的可见面信息
  - 下图$e$为$horizon$，$f_2$为要删除的小平面，$f$为新增面，$p_r$为新增点，而$f$的可见点的信息一定为$f_2$的可见点的信息和$f_1$的可见点的信息的并集的子集
    <image src="/Geometry/conflict.png"  style="width: 40%; height: auto;">

## 算法步骤
输入：三维空间中`n`个点构成的集合 `P`
##### 1. 构建四面体
- 从点集中选取四个点构成四面体（双向链接边表结构）

##### 2. 构建冲突信息
- 遍历剩余的点
  - 为四面体的每个面添加可见点的index
  - 为每个点添加可见面的index

##### 3. 循环添加剩余点$P_n$
- 如果点$P_n$有可见面
  - 将所有可见面删除
  - 遍历所有可见面的边
    - 如果该边的TwinEdge的Face未被删除，则该TwinEdge为$horizons$之一
  - 对$horizons$进行排序，使其首位对应
  - 遍历$horizons$
    - 以Horizon的TwinEdge创建新面`NewFace`，记Horizon的TwinEdge原先的`RemovedFace`,Horizon的`NeighbourFace`
    - Horizon的face与$P_n$共面
      - 删除`NewFace`，将Horizon的face合并`NewFace`
    - Horizon的face与$P_n$不共面
      - 初始`NewFace`的冲突信息为`NeighbourFace`和`RemovedFace`的并集
      - 遍历`NewFace`的冲突信息去除看不到的点，并更新剩余点的冲突信息

##### 4. 完成凸包
- 返回双向链接边表

## 优点
**动态更新：** 该算法是在线算法，可以处理数据集动态变化的情况。在数据集不断更新时，可以仅考虑新加入的点，而不需要重新计算整个凸包。

## 参考文献
[1]计算几何算法与应用:第3版.第11章.邓俊辉译
## 项目链接
https://github.com/yexuan333/GeoAlgoVisual
