+++
title = 'Pybind11(Doing)'
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

## 基础知识
#### 标头和命名空间约定
```cpp
#include <pybind11/pybind11.h>
namespace py = pybind11;
PYBIND11_MODULE(example, m) {
    m.doc() = "pybind11 example plugin"; // optional module docstring
    m.def("add", &[](int a, int b){return a + b}, "A function that adds two numbers",py::arg("A") = 1, py::arg("B") = 1);
}
// 绑定了一个add函数，并添加了函数类型信息，函数名，默认参数，说明等
```
```py
import example
example.add(1, 1) #  >>> 2
example.add() #  >>> 2 
```
PYBIND11_MODULE创建一个函数，当从 Python 中发出语句时将调用该函数。模块名称`example`作为第一个宏参数给出（它不应该用引号引起来）。第二个`m`定义一个类型的变量，该变量 是用于创建绑定的主接口。该方法生成向 Python 公开函数的绑定代码。

#### 基础知识
