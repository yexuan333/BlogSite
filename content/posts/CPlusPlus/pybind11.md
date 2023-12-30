+++
title = 'Pybind11'
date = 2023-12-27T01:52:39+08:00
draft = false
series = ["CPlusPlus"]
+++

## 起源
pybind11 是基于 Wenzel Jakob 在 2016 年创建的 pybind 项目的。pybind 是作为一个简单的动态绑定库开始的，目的是为了解决 CPython C API 的一些限制。在 2016 年，pybind11 作为 pybind 的一个分支被发布，它是完全重写的，专注于提供更干净、更简洁的代码接口。[GitHub仓库地址](https://github.com/pybind/pybind11)、[pybind11文档](https://pybind11.readthedocs.io/en/stable/)

## 安装
- PyPI `pip install pybind11`
- conda `conda install -c conda-forge pybind11`
- vcpkg `vcpkg install pybind11`


## 简例
`PYBIND11_MODULE`创建一个函数，`example`是模块名称（无引号）。`m`定义一个类型的变量，该变量是用于创建绑定的主接口。
```cpp
#include <pybind11/pybind11.h>
namespace py = pybind11;
PYBIND11_MODULE(example, m) {
    m.doc() = "pybind11 example plugin"; // optional module docstring
    m.def("add", &[](int a, int b){return a + b}, "A function that adds two numbers",py::arg("A") = 1, py::arg("B") = 1);
}
// 绑定了一个add函数，并添加了函数类型信息，函数名，默认参数，说明等
```
```python
import example
example.add(1, 1) #  >>> 2
example.add() #  >>> 2 
```
## 绑定对象
##### 函数
```cpp
py::class_<Pet>(m, "Pet")
    .def(py::init<>()) //构造函数
    .def(py::init<const std::string &>()) //重载构造函数
    .def("Method", &Pet::method, "Method") //成员函数
    .def_static("staticMethod", &Pet::staticMethod "Static Method") //静态函数
    .def("set", py::overload_cast<int>(&Pet::set), "Set the pet's age") //重载函数
    .def("set", py::overload_cast<const std::string &>(&Pet::set), "Set the pet's name"); ////重载函数
```
##### 字段
```cpp
py::class_<Pet>(m, "Pet")
    // 直接访问字段
    .def_readwrite("name", &Pet::m_name)
    // 只读字段
    .def_readonly("name", &Pet::m_name)
    // 可读写静态字段
    .def_readwrite_static("my_static_var", &&Pet::my_static_var)
    // 只读静态字段
    .def_readonly_static("my_static_var", &&Pet::my_static_var)
```
##### 属性
```cpp
py::class_<Pet>(m, "Pet")
    // 可读写属性
    .def_property("name", &Pet::getName, &Pet::setName)
    // 只读属性
    .def_property_readonly("name", &Pet::getName)
```
##### 动态属性
原生Python类可以动态获取新属性，从C++导出的类不支持此功能，若要为C++类启用动态属性，必须添加标记`py::dynamic_attr()`到构造函数中：
```cpp
py::class_<Pet>(m, "Pet", py::dynamic_attr())
```
##### 枚举
```cpp
py::enum_<Kind>(pet, "Kind")
    .value("Dog", Kind::Dog)
    .value("Cat", Kind::Cat)
    .export_values();
```
##### 内部类型
```cpp
py::class_<Pet::Attributes>(pet, "Attributes")
    .def(py::init<>())
    .def_readwrite("age", &Pet::Attributes::age);
```
##### 继承和向下转换
```cpp
py::class_<Dog>(m, "Dog", pet) //Methon 1
py::class_<Dog, Pet>(m, "Dog") //Methon 2
```
在使用 pybind11 将 C++ 代码绑定到 Python 时，如果一个 C++ 类是多态的（即基类包含虚函数），pybind11 能够自动识别这一点并相应地处理，而如果 C++ 类是非多态的（即基类不包含虚函数），派生类实例的基类指针将无法使用派生类的方法
```cpp
// Return a base pointer to a derived instance
m.def("pet_store", []() { return std::unique_ptr<Pet>(new Dog("Molly")); });
```
```python
>>> p = example.pet_store()
>>> type(p)  # `Dog` instance behind `Pet` pointer
Pet          # no pointer downcasting for regular non-polymorphic types
>>> p.bark()
# AttributeError: 'Pet' object has no attribute 'bark'
```
##### 智能指针
Python 和 C++ 使用根本不同的方式来管理内存和对象的生存期。pybind11精心设计的呼叫策略和生存期管理，参考[返回值策略](https://pybind11.readthedocs.io/en/stable/advanced/functions.html#return-value-policies)，作为替代方法，使用智能指针可以消除可能导致崩溃或未定义的行为。对于返回指针智能的函数，则无需指定返回值策略。
- std::shared_ptr
  ```cpp
  py::class_<Example, std::shared_ptr<Example>> obj(m, "Example");
  ```
- Custom smart pointers
    对于任何其他自定义智能指针，在绑定代码之前调用一下宏
    ```cpp
    PYBIND11_DECLARE_HOLDER_TYPE(T, SmartPtr<T>);
    ```

## STL 容器
##### 自动转换
当包含附加头文件 `pybind11/stl.h` 时，将自动启用 

`std::vector<> / std::deque<> / std::list<> / std::array<> / std::valarray<>` => Python list 

`std::set<> / std::unordered_set<>` => Python set

`std::map<> / std::unordered_map<>` => Python dict

std::pair<> 和 std::tuple<> 已经支持开箱即用，只需核心 pybind11/pybind11.h 头。这些隐式转换的主要缺点是容器必须在每次Python->C++和C++->Python转换时转换（即复制），这可能会影响程序语义和性能。请阅读`Opaque Types`避免这种情况的替代方法。
##### Opaque Types
当 STL 容器被绑定为 `opaque types `时，它们在 Python 和 C++ 之间不进行自动类型转换。这意味着，当 STL 容器作为函数参数或返回值时，它们在 Python 中的行为更接近它们在 C++ 中的原生行为，保持了引用语义，而不是转换为 Python 的内置类型。这对于性能优化和内存管理非常重要，特别是在处理大型数据或需要维持 C++ 端的数据结构不变时。
```cpp
PYBIND11_MAKE_OPAQUE(std::vector<int>);
```
调用该宏创建`Opaque Types`，并需要放在任何绑定代码之前（例如，调用 class_::def() 等）。这个宏必须在顶层指定（并且在任何命名空间之外），因为它添加了一个模板实例化。如果你的绑定代码包含多个编译单元，它必须出现在每个文件中（通常通过一个公共头），在使用 std::vector<int> 之前。不透明类型也必须有一个相应的 class_ 声明，以将它们与Python中的名称相关联，并定义一组可用的操作，例如：
```cpp
py::class_<std::vector<int>>(m, "IntVector")
    .def(py::init<>())
    .def("clear", &std::vector<int>::clear)
    .def("pop_back", &std::vector<int>::pop_back)
    .def("__len__", [](const std::vector<int> &v) { return v.size(); })
    .def("__iter__", [](std::vector<int> &v) {
       return py::make_iterator(v.begin(), v.end());
    }, py::keep_alive<0, 1>()) /* Keep vector alive while iterator is used */
    // ....
```
##### 绑定STL容器
pybind11提供 `pybind11/stl_bind.h `的可选头文件来完成这一任务。
```cpp
#include <pybind11/stl_bind.h>

PYBIND11_MAKE_OPAQUE(std::vector<int>);
PYBIND11_MAKE_OPAQUE(std::map<std::string, double>);

// later in binding code:
py::bind_vector<std::vector<int>>(m, "VectorInt");
py::bind_map<std::map<std::string, double>>(m, "MapStringDouble");
```

##### 编码
见[Strings, bytes and Unicode conversions](https://pybind11.readthedocs.io/en/stable/advanced/cast/strings.html)

## 符号文件
使用 pybind11 生成的 .pyd 文件通常不会自动生成对应的 `.pyi` 文件。`.pyi` 文件是 Python 接口文件，用于提供类型提示和代码补全等功能，增强 IDE 支持和静态类型检查。缺少 `.pyi` 文件可能会导致 IDE 无法提供完整的代码补全和类型提示功能。用户可能需要手动编写或使用其他工具生成 `.pyi` 文件来弥补这一点。

安装`pybind11-stubgen`生成`.pyi` [github仓库地址](https://github.com/sizmailov/pybind11-stubgen)

`pip install pybind11-stubgen==0.16.2`

```python 
from pybind11_stubgen import ModuleStubsGenerator

import module_name
module = ModuleStubsGenerator(module_name)
module.parse()
module.write_setup_py = False
# 生成module_name.pyi文件
with open("./module_name.pyi", "w") as stub:
    stub.write("#\n# Automatically generated file, do not edit!\n#\n\n")
    stub.write("\n".join(module.to_lines()))
```
