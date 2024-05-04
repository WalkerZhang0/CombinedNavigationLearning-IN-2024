### C++学习笔记

#### 实现寄存器

| Title |    Explanation     |
| :---: | :----------------: |
| About | 指针与动态分配内存 |
| Site  |  E:\C++_file\test  |

1. **整形指针可以直接指向整形数组**：因为数组名本身就是一个指向数组首元素的指针，因此，指针可以直接指向一个数组；

```c++
int arr[5] = {1, 2, 3, 4, 5};
int* ptr = arr;  // ptr 现在指向数组 arr 的第一个元素
```

*扩展：*

- 使用指针访问数组元素：

```C++
int arr[5] = {1, 2, 3, 4, 5};
int* ptr = arr; // 定义整型指针指向数组

for (int i = 0; i < 5; ++i) {
    std::cout << *(ptr + i) << " ";  // 使用指针访问数组元素--因为指针指向地址，通过地址的变更访问数组元素
}

```

2. ------

   关于动态分配内存：C和C++在动态分配内存方面有不同的方法，但是C++兼容C，所以适用于C的方法往往适用于C++，常见的有

```C
int size = 5;
int* arr = (int *)malloc(sizeof(int)); // 分配内存

free(arr); // 释放内存
```

```C++
int* jac[] = new int[5];

free(jac);
```

3. ------

   如何获取一个数组的大小：

```c++
int arr[5];
int count = sizeof(arr)/sizeof(arr[0]);  // sizeof获取该数据所占用的字节，然后获取该数据所占的数组的第一个元素的字节数； 
```

#### 黑马视频

1. 指针：本质上是一个地址；

- 定义：指针指向一个地址，可以通过访问该地址从而获得在该地址内存储的变量。具体语法如下：

```c++
int a = 1;
int * p = &a; // 将指针p指向变量a的地址
// 如果此时直接输出指针p，输出的应该是一串地址
cout << p << endl;
// 具体想要访问变量，应该对该指针进行解引用
cout << *p <<endl;

```

- 空指针：指向系统内存所占的部分，不能直接访问，可以用来初始化指针；

```c++
int * p = NULL;
```

- 野指针：指向非法空间，可能的情况是这段空间你并没有申请，但是却将指针指向了这部分空间；

2. 指针与const；

```c++
// 常量指针：指针指向的地址可以修改，但是指针所指向的地址存储的值是不能改变的；
int a = 1;
const int * p = &a;

// 指针常量：指针的指向的值可以修改，但是指针的指向不能修改；
int * const p = &a;

// const如何即修饰指针，又修饰常量,此时指针的指向和指针指向的值都不可以修改
const int * const p = &a;
```

3. 

------

#### 直角<>大地

| Title |        Explantion        |
| :---: | :----------------------: |
| About | 类的定义：头文件和源文件 |
| Site  | E:\C++_file\Coors_trans  |

在写本程序的过程中，尝试使用了类。类的使用能够很好的实现代码的组织和管理。

类的构成包括头文件和源文件，即.h文件和.cpp文件。

- .h文件：包括了类的声明、函数原型和变量声明，该文件所提供的接口可以供其他cpp文件使用

  > 一个头文件是可以包括多个类的，然后一个类也需要分为头文件和源文件两部分。

  ```c++
  // example.h
  
  #ifndef EXAMPLE_H // 判断是否定义了以下宏，如果已经定义了，跳过代码，避免重复执行；如果没有，执行.if not defined
  #define EXAMPLE_H // 声明宏：
  // 注意宏的使用，宏通过定义一个标识符来实现一个或者多个代码片段。
  
  // 函数声明
  void sayHello();
  
  // 类声明
  class MyClass {
  public:
      void myMethod();
  };
  
  #endif  // EXAMPLE_H
  
  ```

- .cpp文件：包含了实际的函数定义、类成员函数的实现、全局变量的定义等；

  ```c++
  // example.cpp
  
  #include "example.h"  // 包含对应的头文件
  
  #include <iostream>
  
  // 函数定义
  void sayHello() {
      std::cout << "Hello, World!" << std::endl;
  }
  
  // 类成员函数实现
  void MyClass::myMethod() {
      std::cout << "MyMethod is called!" << std::endl;
  }
  ```

关于类的使用，其实C++中的类和C#的类的定义和使用都有所不同。

- 类的创建：

  ```c++
  class ClassName {
  public:
      // 成员变量
      DataType memberVariable1;
      DataType memberVariable2;
      
      // 成员函数
      ReturnType memberFunction1(ParameterType1 param1, ParameterType2 param2);
      ReturnType memberFunction2(ParameterType param);
  };
  ```

- 类的成员属性：类的成员一共有三种属性：public、private和protected；

  - public：类的内外部都可以访问；
  - private：只有在类的内部可以访问；
  - protected：类似于private，但是允许派生类访问；

- 类的引用：引用类，需要创建类的对象，即实例化类。类内部的成员函数和变量可以看作是类的对象的属性，从而通过类的对象来进行访问，*这里是否要区分静态static函数呢？*

  ```c++
  MyClass obj;  // 创建MyClass类的对象
  
  obj.publicVar = 10;  // 访问和修改公有成员变量
  obj.publicMethod();  // 调用公有成员函数
  ```

- 构造函数和析构函数：

  - 构造函数：在创建类的对象时调用，用于初始化类内部的成员；
  - 析构函数：在对象销毁时自动调用，清理资源；

- 类的继承：一个类能够从另一个类中派生，从而继承其内部的部分变量和函数；

  ```c#
  class BaseClass {
  public:
      void baseMethod() {
          cout << "Base method" << endl;
      }
  };
  
  class DerivedClass : public BaseClass {
  public:
      void derivedMethod() {
          cout << "Derived method" << endl;
      }
  };
  ```
