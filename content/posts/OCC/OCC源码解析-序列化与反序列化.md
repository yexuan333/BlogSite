+++
title = 'OCC源码解析-序列化与反序列化'
date = 2024-03-31T14:51:07+08:00
draft = false
series = ["OCC"]
+++

序列化和反序列化基本差不多，怎么序列化的就怎么反序列化回来就行了，所以只介绍序列化了
#### 入口函数
使用`BRepTools::Write`函数序列化一个`Shape`，这是序列化B-Rep形状的入口函数。这个函数接受两个参数：要序列化的TopoDS_Shape对象和输出流（通常是指向一个文件的流）。
该方法会创建`BRepTools_ShapeSet`对象,这个对象负责收集和管理形状的所有相关信息，包括拓扑结构和几何数据
  ```cpp
  Standard_Boolean  BRepTools::Write (const TopoDS_Shape& theShape,
                                      const Standard_CString theFile,
                                      const Standard_Boolean theWithTriangles,
                                      const Standard_Boolean theWithNormals,
                                      const TopTools_FormatVersion theVersion,
                                      const Message_ProgressRange& theProgress){
    const Handle(OSD_FileSystem)& aFileSystem = OSD_FileSystem::DefaultFileSystem();
    std::shared_ptr<std::ostream> aStream = aFileSystem->OpenOStream (theFile, std::ios::out | std::ios::binary);
    BRepTools_ShapeSet SS (theWithTriangles, theWithNormals);
    SS.SetFormatNb (theVersion);
    SS.Add(theShape);-
    SS.Write (*aStream, theProgress);
    aStream->flush();
  }
  ```

#### 整合几何数据
实际调用的是基类`TopTools_ShapeSet`的Add方法，没什么区别，这个类目前只有这一个派生类，通过调用`Add`方法，先判断是否在集合中，如果不在就会先添加Shape的几何信息，然后递归遍历所有sub-Shape并加入到集合`myShapes`中
  ```cpp
  Standard_Integer  TopTools_ShapeSet::Add(const TopoDS_Shape& S)
  {
    myLocations.Add(S.Location());
    TopoDS_Shape S2 = S;
    S2.Location(TopLoc_Location());
    Standard_Integer index = myShapes.FindIndex(S2);
    if (index == 0) {
      AddGeometry(S2);
      for (TopoDS_Iterator its(S2,Standard_False,Standard_False);
          its.More(); its.Next())
        Add(its.Value());
      index = myShapes.Add(S2);
    }
    return index;
  }
  ```

  - `AddGeometry`过程中会枚举`Shape`的类型得到点线面等几何信息，包括拓扑相关的`PointOnCurve`等，然后将这些几何信息存储到对应类型的几何池中
    ```cpp
    void BRepTools_ShapeSet::AddGeometry(const TopoDS_Shape& S){
      if (S.ShapeType() == TopAbs_VERTEX) {
        //...
      }
      else if (S.ShapeType() == TopAbs_EDGE) {
        //...
      }
      else if (S.ShapeType() == TopAbs_FACE) {
        //...
      }
    }
    ```

#### 序列化
`SS.Write (*aStream, theProgress);`这一步是序列化的过程，这时已经将所有的`Shape``Geometry`都存在了`BRepTools_ShapeSet`对象中，然后开始正式的序列化,首先会把几何对象都序列化出去，然后序列化`Shape`的信息，在把`Shape`的几何信息的索引关联上，然后递归遍历`sub-Shape`重复这个过程
  ```cpp
  void  TopTools_ShapeSet::Write(Standard_OStream& OS, const Message_ProgressRange& theProgress)
  {
    myLocations.Write(OS, aPS.Next(10));
    WriteGeometry(OS, aPS.Next(75));
    Standard_Integer i, nbShapes = myShapes.Extent();
    for (i = 1; i <= nbShapes && aPS1.More(); i++, aPS1.Next()) {
      const TopoDS_Shape& S = myShapes(i);
      PrintShapeEnum(S.ShapeType(),OS,Standard_True);
      WriteGeometry(S,OS);

      Standard_Integer l = 0;
      TopoDS_Iterator its(S,Standard_False,Standard_False);
      while (its.More()) {
        Write(its.Value(),OS);
        its.Next();
      }
      Write(TopoDS_Shape(),OS);
    }
  }
  ```
  - 有两个`WriteGeometry`,第一个会把池子里的几何对象都序列化到`OStream`中，第二个是给`Shape`填入几何数据的类型和索引，之后反序列化就先把几何对象都反序列出来，然后根据索引去找几何对象，然后重建出`Shape`
    ```cpp
    void  BRepTools_ShapeSet::WriteGeometry(Standard_OStream& OS, const Message_ProgressRange& theProgress){}
    void  BRepTools_ShapeSet::WriteGeometry (const TopoDS_Shape& S, Standard_OStream& OS)const{}
    ```
#### 导出
  `aStream->flush();`