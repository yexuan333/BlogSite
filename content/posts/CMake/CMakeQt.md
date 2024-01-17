set(CMAKE_AUTOUIC ON)  # 自动处理ui文件,转换为相应的 C++ 头文件
set(CMAKE_AUTOMOC ON)  # 自动调用moc(元对象编译器 Meta-Object Compiler)
set(CMAKE_AUTORCC ON)  # 自动处理源文件（.qrc）编译成用于应用程序的二进制资源

set_property (GLOBAL PROPERTY AUTOMOC_SOURCE_GROUP "/Generated Files/Moc")
set_property (GLOBAL PROPERTY AUTORCC_SOURCE_GROUP "/Generated Files/Resources")
set_property (GLOBAL PROPERTY AUTOUIC_SOURCE_GROUP "/Generated Files/UIC")
set_property (GLOBAL PROPERTY AUTOGEN_SOURCE_GROUP "/Generated Files/GEN")