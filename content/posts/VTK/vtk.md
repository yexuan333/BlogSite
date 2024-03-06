系统架构 (System Architecture)：介绍了VTK的整体架构和关键组件。第3章, 页 19
低级别对象模型 (Low-Level Object Model)：讨论了VTK中的基础对象和它们的作用。第3章, 页 20
渲染引擎 (The Rendering Engine)：详细说明了VTK的渲染引擎及其功能。第3章, 页 21
可视化管线 (The Visualization Pipeline)：介绍了VTK的可视化处理流程。第3章, 页 25
创建应用程序 (Create An Application)：指导如何利用VTK构建应用程序。第3章, 页 29
用户方法、观察者和命令 (User Methods, Observers, and Commands)：讨论了如何在VTK中使用各种用户定义的方法和命令。第3章, 页 29
Tcl、C++、Java在VTK中的应用：分别介绍了这三种编程语言在VTK中的使用和特点。第3章, 页 30-36
### 基本对象
**vtkProp** 
vtkProp子类表示场景中存在的数据的可见描述。在 3D 中显示对象时，最常用的 vtkProp 子类是 vtkActor（用于表示场景中的几何数据）和 vtkVolume（用于表示场景中的体积数据）。用于表示场景中的几何数据）和 vtkVolume（用于表示场景中的体积数据）。vtkActor2D可表示 2D 数据。vtkProp 子类通常 负责了解其在场景中的位置、大小和方向。通常还有一个映射器 对象，用于保存数据并知道如何渲染数据，以及一个属性对象，用于控制参数 如颜色和不透明度
- vtkImageActor用于显示图像
- vtkPieChartActor用于创建数据值数组的饼图可视化表示
- vtkFollower是一个特殊子类，它会自动更新方向，以便持续面对指定的摄像机
- vtkLODActor能自动更改其几何表示，以保持交互式帧速率
- vtkLODProp3D 则是 vtkProp3D 的一个子类，它能在多个不同的模型之间进行选择。甚至可能是体积贴图和几何贴图的混合体
- ...
**tkProperty 和 vtkVolumeProperty**
- vtkActor 对象使用 vtkProperty 来存储参数，如颜色、不透明度、材质的环境系数、漫反射系数和镜面系数。
- vtkVolume 对象则使用 vtkVolumeProperty 来捕获适用于体积对象的参数，如例如将标量值映射到颜色和不透明度的传递函数。许多映射器还提供了设置剪切平面的功能，可用于显示内部的结构
**vtkCamera**
包含控制如何查看场景的参数。vtkCamera 有一个位置、一个焦点和一个定义场景中 "向上 "方向的向量。其他参数控制特定的视角变换（平行或透视）、图像的比例或视角，以及近景和远景。图像的比例或视角，以及视锥的远近剪切平面
**vtkLight**
vtkLight 对象存储灯光的位置和方向，以及颜色和强度。灯光也有一种类型，用于描述灯光相对于摄像机的移动方式。例如头灯总是位于摄像机的位置并照射在摄像机的焦点上，而场景灯则位于场景中的固定位置
**vtkRenderer**. 构成场景的对象（包括道具、摄像机和灯光）都集中在 vtkRenderer 中。vtkRenderer 负责管理场景的渲染过程。在一个 vtkRenderWindow 中可以同时使用多个 vtkRenderer 对象。这些渲染器可以渲染到渲染窗口的不同矩形区域（称为视口）、或重叠
**vtkRenderWindow**
提供了操作系统与 VTK 渲染引擎之间的连接。负责在计算机的本地窗口系统中打开窗口并管理显示过程。使用 VTK 开发时，您只需使用与平台无关的 vtkRenderWindow，在运行时会自动替换为正确的特定平台子类。vtkRenderWindow 包含一个 vtkRenderers 集合，以及控制渲染功能的参数，如参数，这些参数可控制立体声、抗锯齿、运动模糊和焦距深度等渲染功能。
**vtkRenderWindowInteractor**
负责处理鼠标、按键和计时器事件，并将这些事件通过 VTK 实现的命令观察者设计模式的实现。vtkInteractorStyle 监听并处理这些事件，以便提供运动控制，如旋转、平移和缩放。vtkRenderWindowInteractor会自动创建一个适用于 3D 场景的默认交互样式，但您也可以选择一个适用于 2D 图像查看的样式，例如或创建您自己的自定义交互样式。
**vtkTransform**. 场景中许多需要放置的对象（如道具、灯光和摄像机）都有一个 vtkTransform 参数，可用于轻松操纵对象的位置和方向。摄像机等需要放置的场景中的许多对象都有一个 vtkTransform 参数，可用于轻松操控对象的位置和方向。vtkTransform 可用于描述三维空间中所有的线性（也称为仿射）坐标变换，其内部表示为一个 4x4均质变换矩阵。vtkTransform 对象将以默认的同调矩阵开始或以流水线方式将变换串联起来，以创建复杂的行为。流水线机制可确保在修改流水线中的任何变换时，所有后续的变换都会相应更新
**vtkLookupTable、vtkColorTransferFunction 和 vtkPiecewiseFunction**
标量数据可视化，通常需要定义从标量值到颜色和不透明度的映射。这在几何曲面渲染和体积渲染中都是如此，在几何曲面渲染中，不透明度将定义曲面的半透明度，而在体积渲染中，不透明度将代表体积的半透明度。渲染中，不透明度将代表穿过体积的某段光线累积的不透明度。在几何渲染中，此映射通常使用 vtkLookupTable 来创建，而在体绘制中，vtkColorTransferFunction 和vtkPiecewiseFunction 都会被使用。
### 渲染管线
VTK 中的可视化流水线可用于读取或创建数据、分析和创建这些数据的衍生版本，以及将数据写入磁盘或传递给渲染引擎进行显示。vtk使用数据流方法将信息转化为图形数据。
数据。这种方法涉及两类基本对象
- vtkDataObject
VTK中所有数据类型的基类，可以被视为数据的通用“blob”。
它代表完全任意的数据，不一定具有固有的空间结构。
    - vtkDataSet是vtkDataObject的子类，表示具有固有空间结构的数据。
    它描述了数据点和单元(cells)之间的几何和拓扑关系。
- vtkAlgorithm
  