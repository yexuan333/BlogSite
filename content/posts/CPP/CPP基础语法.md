+++
title = 'CPP基础语法（Doing）'
date = 2023-12-31T19:26:49+08:00
draft = false
series = ["CPlusPlus"]
+++

## 基本语法
##### 基本数据类型
基本数据类型包括整型、浮点型、字符型和布尔型。
- **简单类型**
  - 整型
    - `short`：2字节，$(-2^{15},2^{15}-1)$。
    - `int`：4字节，$(-2^{31},2^{31}-1)$。
    - `long`：4字节（Windows，Linux32），8字节(Linux64)
    - `long long`：8字节，$(-2^{63},2^{63}-1)$。
  - 浮点型
    - `float`：4字节，7位有效数字
    - `double`：8字节，15～16位有效数字
  - 字符型
    - `char`：1字节。
    - `wchar_t`：2字节（Windows），4字节（Linux）
  - 布尔型
      - `bool`：1字节。
- **复合类型**
  - **数组**：用于存储相同类型的多个元素。之所以数组是复合类型是因为数组需要其他类型来创建，如`int []`
    ```cpp
    int myArray[10]; // 声明一个包含10个整数的数组
    int myArray2[5] = {1, 2, 3, 4, 5}; // 初始化一个整数数组
    char myCharArray[4] = {'a', 'b', 'c', 'd'}; // 初始化一个字符数组
    int size = sizeof(myArray) / sizeof(myArray[0]); // 数组长度
    ```
  - **字符串**：字符串是字符序列。主要有两种类型的字符串表示方法：C 风格字符串和 C++ 风格字符串。
    - C风格，`char []`，使用 null 终止字符 (`'\0'`) 的字符数组。
    - C++风格， `std::string`表示
  - **指针**：用于存储变量的内存地址
  - **结构体**（Struct）：用于将不同类型的数据项组合成一个单元
    - 默认的成员访问权限是 public
    - 默认的继承类型是 public
    - 与C兼容，class不能
  - **联合体**（Union）：允许在相同的内存位置存储不同的数据类型，但一次只能使用其中一种类型
    - 所有成员共享同一块内存空间。用于节省内存，内存大小为最大成员的大小
    - 联合体的一个成员被赋值后，其他成员将变得不可访问
    ```cpp
    union MyUnion {
        int intValue;
        float floatValue;
        char charValue;
    };
    ```
  - **枚举**（Enum）：用于定义一组命名的整型常量
    - 使用 `enum` 关键字定义枚举类型。默认情况下，枚举的第一个元素的值是 0，后续元素的值依次增加。
    - 枚举类（C++11），使用 enum class 定义更严格的枚举类型。具有作用域限制，避免了传统枚举的命名冲突问题。比传统枚举提供更好的类型安全。
##### 循环与分支语句
- **for**：
    ```cpp
    for (int i = 0; i < 10; i++) {
        // 当 i 小于 10 时执行循环体
        // 在每次迭代后 i 自增
    }

    // C++ 11
    std::vector<int> v = {1, 2, 3, 4, 5};
    for (int x : v) {
        // 遍历 v 中的每个元素
        //auto item : container,调用拷贝构造函数，会复制对象
        //auto& item : container,使用引用避免复制，可进行修改
        //const auto& item : container,使用const 引用避免复制，不可进行修改
    }
    ```
- **while**：
    ```cpp
    while (condition) {
        // 当条件为真时执行的代码
    }
    ```
- **do-while**：
    ```cpp
    do {
        // 至少执行一次的代码
    } while (condition);
    ```
- **if**：
    ```cpp
    if (condition1) {
        // 条件1为真时执行
    } else if (condition2) {
        // 条件2为真时执行
    } else {
        // 所有条件均为假时执行
    }
    ```
- **switch**：
    ```cpp
    switch (expression) {
        case constant1:
            // 当表达式等于 constant1 时执行
            break;
        case constant2:
            // 当表达式等于 constant2 时执行
            break;
        // 可以有更多的 case 分支
        default:
            // 当没有匹配的 case 时执行
    }
    ```
- **break**：
    ```cpp
    for (int i = 0; i < 10; i++) {
        if (i == 5) {
            break; // 当 i 等于 5 时退出循环
        }
    }
    ```
- **continue**：
    ```cpp
    for (int i = 0; i < 10; i++) {
    if (i == 5) {
        continue; // 跳过循环中 i 等于 5 的迭代
    }
    // 其他代码
    }
    ```