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