# 

# R*Tree:一种高效稳定的点和矩形访问方法
##### 摘要
R树是最流行的矩形访问方法之一，但是R*Tree性能明显优于RTree，它可以同时有效地支持点和空间数据，并且它的实现成本仅略高于其他R树
 边界矩形的边距或重叠进行最小化，
#### ChooseSubtree
设 N 为root node
1. - if N是叶子节点
      - return N
   - else 
     - if N的子节点是叶子节点
       - 选择子节点中，包含新数据的矩形后，最少扩张的节点
     - else
       -  选择子节点中，包含新数据的矩形后，最少扩张的节点,将 N 设置为这个节点的子节点，再重复
### split of the R*-tree
#### Split
1. 调用 ChooseSplltAxis 以确定执行拆分的垂直轴
2. 调用 ChooseSplltIndex 以确定沿该轴分成两组的最佳分布
3. 将条目分成两组
