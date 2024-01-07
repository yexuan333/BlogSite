+++
title = 'CPP高级特性(DOING)'
date = 2024-01-07T19:11:33+08:00
draft = false
+++

### 模板
##### 函数模板
- **函数模板实例化**
  - 隐式实例化,当模板函数被调用时，编译器根据提供的参数类型自动生成一个特定的函数实例
  - 显式实例化,可以显式地指定模板实例的类型
- **函数模板具体化**：创建一个特定版本的模板函数，以适应特定类型或条件的需求。
```cpp
template <class T> // 模板函数
T Operator(T a, T b) {
    return a + b;
} 

template <> //模板具体化类型为int
int Operator<int>(int a, int b) {
    return a * b;
}

int main(int argc, char* argv[]) {
    std::cout << Operator(1.0, 1.0) << std::endl; //模板隐式实例化T为double类型
    // >>2
    std::cout << Operator(1, 1) << std::endl; //模板隐式实例化T为int类型
    // >>1
    std::cout << Operator<int>(1.0, 1.0) << std::endl; ////模板显示实例化T为int类型,会将double转换为int
    // >>1
    std::system("pause");
}
```
##### 类模板
 
### 名称空间
使用 `namespace` 关键字定义，提供一个声明名称的区域。避免命名冲突，在默认情况下，在名称空间中声明的名称的链接性为外部的（引用了常量 `const`的链接性为内部）。

**全局名称空间（global namespace）**，对应于文件级声明区域
```cpp
namespace MyNamespace {
    int myVar;
    void myFunction() {}
}
```
#### using
- **using声明**，引入名称空间中的特定成员
  ```cpp
  using MyNamespace::myVar;
  myVar = 10;  // 直接使用，不需要 MyNamespace:: 前缀
  ```
- **using编译指令**，引入整个名称空间中的所有成员
  ```cpp
  using namespace MyNamespace;
  myFunction();  // 直接调用，不需要 MyNamespace:: 前缀
  ```

未命名的名称空间（匿名名称空间），避免全局命名空间的污染，并提供一种替代静态全局变量的机制，不能在未命名名称空间所属文件之外的其他文件中，使用该名称空间中的名称。
```cpp
namespace{
  int a = 10; //内部链接性
}
void main(){
  std::cout << a;
}

//等同于在全局名称空间中使用`const`变量，但是更符合现代化的C++
static int a = 10;
void main(){
  std::cout << a;
}
```

### 智能指针（shared_ptr, unique_ptr, weak_ptr）
### Lambda 表达式