+++
title = 'CPP对象(TODO)'
date = 2024-01-07T19:10:35+08:00
draft = false
series = ["CPlusPlus"]
+++

面向对象编程（OOP）是一种特殊的、设计程序的概念性方法，面向对象的三大特征是：**封装**，**继承**和**多态**

## 构造函数
- 默认构造函数：如果未定义任何构造函数，编译器会提供一个默认的构函数
- 参数化构造函数
  - `explicit`显示声明，禁止隐式调用
  - 隐式构造函数
    ```cpp
    class MyClass {
    public:
        MyClass(int x) {}
        MyClass(int x, int y) {}
    };
    MyClass function(MyClass obj){
        return 10 // 隐式调用 MyClass(int)
    }

    void main(){
        MyClass obj = 1;// 隐式调用 MyClass(int)
        MyClass obj = {1,1};// 多参数隐式调用 MyClass(int, int)
        function(10);  // 隐式调用 MyClass(int)
    }
    ```
- 拷贝构造函数
  - 在对象被复制时被调用
  - 默认拷贝构造函数，会逐成员复制对象（浅拷贝）
  - `explicit`显示声明，禁止隐式调用
  - 隐式调用
    - 对象作为参数传递给函数（通过值）
    - 对象从函数返回（通过值）
    - 初始化另一个对象时
    ```cpp
    MyClass obj2 = obj1;  // 使用拷贝构造函数
    ```
- 移动构造函数（C++11）
  - 从即将被销毁的对象（右值）中“移动”资源，无需复制，从而提高效率
  - 隐式调用
    ```cpp
    class MyClass {
    public:
        MyClass(std::MyClass&& other){ 
        }
    };

    void main(){
        MyClass obj1;
        MyClass obj2 = std::move(obj1);  // 隐式调用移动构造函数
    }
    ```
- 委托构造函数（C++11）
  - 当类有多个构造函数，它们之间具有公共的初始化步骤时，使用委托构造函数来避免重复的代码
  ```cpp
  class MyClass {
    public:
        MyClass() : MyClass(0, "") {}  // 委托给另一个构造函数
        MyClass(int x, std::string y) {}
    };
  ```
- `default`声明
  - 显式要求编译器实现默认的构造函数等。
    ```cpp
    class MyClass {
    public:
        MyClass() = default;           // 实现默认构造函数
        MyClass(const MyClass&) = default;  // 实现默认拷贝构造函数
        MyClass(MyClass&&) = default;       // 实现默认移动构造函数
        ~MyClass() = default;          // 实现默认析构函数
    };
    ```
- `delete`声明
    ```cpp
    class MyClass {
    public:
        MyClass() = delete; //禁止构造函数
        MyClass(const MyClass&) = delete;  // 禁止拷贝构造函数
        MyClass(MyClass&&) = delete;       // 禁止移动构造函数
    };
    ```

inline 成员函数
在类声明中定义方法等同于用原型替换方法定义，然后在类声明的后面将定义改写为内联函数。

列表初始化
只要提供与某个构造函数的参数列表匹配的内容，并用大括号将它们括起，就可以构造一个类吗

const 成员函数，保证函数不会修改调用对象
```cpp
// file a.h
class A{
    void f() const;
}

// file a.cpp
void A::f() const{}
```