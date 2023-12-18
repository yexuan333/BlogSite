# RTree


## RTree
### 背景
RTree 是一种用于多维数据查询和存储的树形数据结构，最早由 Antonin Guttman 在 1984 年提出*R-TREES: A DYNAMIC INDEX STRUCTURE FOR SPATIAL SEARCHING*。在RTree中，"R"代表"rectangle"，即矩形。它将空间中的数据对象表示为矩形，使用这些矩形构建一棵树形结构以支持高效的空间查询操作。每个矩形代表一个数据对象，它的边界是该对象在空间中的边界。因此，RTree是一种基于矩形的数据结构，它的设计旨在使得空间查询操作的效率尽可能高。

### 基本属性
![](/Rtree/Tree.jpg)
- **节点（Non Leaf Node ）**：RTree 的基本单元，保存的数据形式为：(I, child-pointer)
  - I是覆盖所有子结点对应矩形的矩形
  - child-pointer是指向孩子结点的指针
- **边界矩形（MBR，Minimum Bounding Rectangle）**：通常，我们不选择去索引几何物体本身，而是采用最小限定箱MBB作为不规则几何图形的key来构建空间索引。
  - 在二维的情况下，我们称之为MBR(minimum bounding retangle)
    ![](/Rtree/MBR.jpg)
  - 三维的情况下，我们称MBB(minimum bounding box)
    ![](/Rtree/MBB.jpg)
- **根节点（Root Node）**：RTree 中最顶层的节点，不包含任何父节点。
- **叶节点（Leaf Node）**：包含数据的节点，位于树的最底层。所保存的数据形式为：(I, tuple-identifier)
  - I：一个n维空间的矩形，恰好可以框住这个叶子结点中所代表的数据
  - tuple-identifier：包含各种条目
- **条目（Entry）**：包含MBR的最基础的数据
- **深度（Depth）**：从根节点到叶节点的最长路径。
### 基本性质
- 对于所有在叶子中存储的记录（条目），I是最小的可以在空间中完全覆盖这些记录所代表的点的矩形（注意：此处所说的“矩形”是可以扩展到高维空间的）。
- 每一个**非叶子结点拥有m至M个孩子结点**，除非它是根结点。
- 对于在非叶子结点上的每一个条目，i是最小的可以在空间上完全覆盖这些条目所代表的店的矩形（同性质2）。
- R树为平衡树，所有叶子结点都位于同一层
### 算法介绍
#### Search
给定一棵根节点为 T 的 R 树，找到其矩形与搜索矩形 S 重叠的所有索引记录
1. **搜索子树** 如果 T 不是叶子节点，则检查每个子节点的MBR 是否与 S 重叠。对于所有重叠的节点，在该节点上调用 Search
2. **搜索叶子节点** 如果 T 是叶子结点，并且T的MBR与 S 重叠。则 T 是合格记录。
#### Insertion
描述：将新的条目E插入给定的R树中。
  <div style="display: flex; justify-content: center;">
  <image src="/Rtree/2d.gif" style="width: 50%; height: auto; margin-right: 10px;">
  <image src="/Rtree/3d.gif" style="width: 50%; height: auto; margin-right: 10px;">
  </div>

1. **为新记录找到合适插入的叶子结点** 开始ChooseLeaf方法选择叶子结点L以放置记录E。
2. **添加新记录至叶子结点** 如果L有足够的空间来放置新的记录条目，则向L中添加E。如果没有足够的空间，则进行SplitNode方法以获得两个结点L与LL，这两个结点包含了所有原来叶子结点L中的条目与新条目E。
3. **将变换向上传递** 开始对结点L进行AdjustTree操作，如果进行了分裂操作，那么同时需要对LL进行AdjustTree操作。
4. **对树进行增高操作** 如果结点分裂，且该分裂向上传播导致了根结点的分裂，那么需要创建一个新的根结点，并且让它的两个孩子结点分别为原来那个根结点分裂后的两个结点。

#### ChooseLeaf
描述：选择叶子结点以放置新条目E,设置N为根结点
1. **叶子结点的检查** 如果N为叶子结点，则直接返回N。
2. **选择子树** 如果N不是叶子结点，则遍历N中的结点，找出添加E.I时扩张最小的结点，并把该结点定义为F。如果有多个这样的结点，那么选择面积最小的结点。
3. **下降至叶子结点** 将N设为F，从CL2开始重复操作。

#### AdjustTree
描述：叶子结点的改变向上传递至根结点以改变各个矩阵。在传递变换的过程中可能会产生结点的分裂。
1. **初始化** 将N设为L。
2. **检验是否完成** 如果N为根结点，则停止操作。
3. **调整父结点条目的最小边界矩形** 设P为N的父节点，EN为指向在父节点P中指向N的条目。调整EN.I以保证所有在N中的矩形都被恰好包围。
4. **向上传递结点分裂** 如果N有一个刚刚被分裂产生的结点NN，则创建一个指向NN的条目ENN。如果P有空间来存放ENN，则将ENN添加到P中。如果没有，则对P进行SplitNode操作以得到P和PP。
5. **升高至下一级** 如果N等于L且发生了分裂，则把NN置为PP。从AT2开始重复操作。

#### SplitNode
为了向包含 M 个条目的节点添加一个新条目，有必要把 M+1 个条目的集合分裂成两个节点，使得两个新节点都需要在后续搜索中被检查的可能性尽可能小。由于决定是否访问一个节点取决于它的MBR是否与搜索区域重叠，所以分割后的两个MBR的总面积应该最小。下图说明了这一点。 “坏分割”情况下MBR的面积比“好分割”情况下的大得多。
![](/Rtree/goodsplit.jpg)
在进行节点分裂之前先了解一些变量
- **$S_{Extend}$**：$S_{覆盖条目后的MBR} - S_{覆盖条目前的MBR}$，例如下图，假设叶子节点有一个条目A,现在加入条目B后的扩张面积为下图的绿色部分
![](/Rtree/extend.jpg)
- **$S_{ExtendDiff}$**：$|A_{S_{Extend}} - B_{S_{Extend}}|$, 假设有两个叶子节点A、B分别包含一个条目，现在分别计算加入条目c的扩张面积$A_{S_{Extend}}$和$B_{S_{Extend}}$,然后便计算可得
  
下列是将 M+1 个条目的集合分成两组的算法：以下图为例：红色矩形是一个叶子节点，红色小正方形是叶子节点内的9个条目，目前叶子节点的条目数量已经满了，现在要插入这个绿色的条目到这个叶子节点中
![](/Rtree/split1.jpg)

- **穷举算法**
把所有的分裂的可能都尝试一遍，复杂度太高就不介绍了
- **二次分裂** A Quadratic-Cost Algorithm
该算法选择 M+1 个条目中的两个作为两个新组的第一个元素，方法是选择放在同一组中将浪费最多区域的两个条目组成的对，即覆盖两个条目的矩形区域，减去条目本身的区域将是最大的。然后将剩余的条目一次一个地分配给组。在每一步中，计算将每个剩余条目添加到每个组所需的面积扩展，分配的条目是显示两组之间最大差异的条目。这个分裂方式**会造成很多重叠区域**，以下是计算步骤
1. **PickSeeds** 先选择两个节点到分裂后的节点中去，计算每两个条目合并后的扩张面积，找到扩张面积最大的两个条目
   - 图一中每个线段代表着两两配对计算一次扩张面积
   - 图二的绿色部分为计算后，最大的扩张面积的两个条目
  <div style="display: flex; justify-content: center;">
  <image src="/Rtree/match.jpg" style="width: 50%; height: auto; margin-right: 10px;">
  <image src="/Rtree/pickseed.jpg" style="width: 50%; height: auto; margin-right: 10px;">
  </div>
1. **ChoosePartition** 根据所得到的两个条目，新建两个叶子节点，然后开始分割，对剩余的每个条目计算AB两个节点的$S_{ExtendDiff}$，选择$S_{ExtendDiff}$最大的条目，加入到对应的节点中，然后重复上述过程，在下图的最后一步中，为保证分裂后的子节点数量合法，所以会把剩余的节点全给另一个


<div style="display: flex; justify-content: center;">
<image src="/Rtree/step1.jpg"  style="width: 12%; height: auto; margin-right: 10px;">
<image src="/Rtree/step2.jpg"  style="width: 12%; height: auto; margin-right: 10px;">
<image src="/Rtree/step3.jpg"  style="width: 12%; height: auto; margin-right: 10px;">
<image src="/Rtree/step4.jpg"  style="width: 12%; height: auto; margin-right: 10px;">
<image src="/Rtree/step5.jpg"  style="width: 12%; height: auto; margin-right: 10px;">
<image src="/Rtree/step6.jpg"  style="width: 12%; height: auto; margin-right: 10px;">
<image src="/Rtree/step7.jpg"  style="width: 12%; height: auto; margin-right: 10px;">
</div>

- **线性分裂** A Linear-Cost Algorithm
与 Quadratic Split 仅区别在不同版本的PickSeeds。并且**会造成更多的重叠区域，但是构建速度快一点**
1. **沿所有维度查找极端矩形** 沿每个维度，找到其矩形具有最高低边和最低高边的条目。记录分离。
2. **调整矩形簇的形状** 通过沿相应维度除以整个集合的宽度来标准化分离。
3. **选择最极端的对** 选择沿任何维度具有最大归一化分离的对。
