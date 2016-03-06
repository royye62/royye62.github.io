---
layout: post
category : c++
tagline:
tags : [c++, OO, inline]
---


## 面向对象 Object Oritented  

### 基于对象(Object Based) vs. 面向对象(Object Oritented)
Object Based 面对的是单一 class 的设计（只有封装，即带类的C）
Object Oritented 面对的是多重classes的设计，classes 和 classes 之间的关系（继承＋多态）

### features of OOP (A PIE)
1. Abstration
2. Polymorphism
3. Inheritance
4. Encapsulation

an object consist of
1. identity
2. state
	data fields(properties) with its current value
3. behaviors
	functions
an object is an instance of a class

## C++ intro
C++ <==> C++ language + C++ library

class 的两种经典分类
class without pointer members
class with pointer members

### C vs C++
1. C is procedure programming  
2. C++ is Object-Oriented Programming (OOP)

### features of OOP (A PIE)
1. Abstration
2. Polymorphism
3. Inheritance
4. Encapsulation

an object consist of
1. identity
2. state
	data fields(properties) with its current value
3. behaviors
	functions
an object is an instance of a class

## C++ intro
C++ <==> C++ language + C++ library

class 的两种经典分类
class without pointer members
class with pointer members

### C vs C++
1. C is procedure programming  
2. C++ is Object-Oriented Programming (OOP)


## const 关键字(未完待续)  

C++中的const关键字的用法非常灵活，而使用const将大大改善程序的健壮性

* const定义常量 代替 常量宏
  * const定义的常量在程序运行过程中只有一份拷贝
  * 宏定义的常量在内存中有若干个拷贝

* const 修饰指针
  * `const char * p` 指针本身是常量不可变
  * `char * const p` 指针所指向的内容是常量不可变
  * `const char * const p` 两者都不可变

* 类相关
  * const成员变量

        class Foo
        {
          Foo(int x)
            :n(x)           //只能在初始化列表中赋值   
          { }

          const int n;      //成员常量不能被修改   
        }
        
  * const成员函数

        class Foo
        {
          //不能改变对象的成员变量
          //不能调用类中任何非const成员函数
          void func() const;        
        }

  * const类对象/对象指针/对象引用

        struct Foo
        {
        	int func1() const {}
        	int func2() {}
        };

        int main()
        {
        	const Foo f;
        	f.func1();
        	f.func2(); //Error. const object can not invoke non-const member function!
        }


## inline vs. marco

#### 相同点
消除函数调用的开销（参数压栈、跳转、弹栈、返回等指令操作）

#### 不同点
* macro 宏
  * 预编译阶段。进行
  * 预编译器进行文本替换 textual substitution
  * 无类型，无类型安全检查
  * 不可调试

* inline
  * 编译阶段
  * 编译器在调用点内联展开函数的代码
    * 编译器必须在translation unit中找到inline函数的定义才能进行展开，因此inline函数应该在头文件中定义（只声明没用）
  * 有类型，类型安全检查  

#### 注意
* inline是对编译器的请求。现代编译器能够根据函数的定义决定是否接受inline，甚至自动inline一些没有请求inline的函数
* 当函数只有 10 行甚至更少时才将其定义为内联函数。滥用内联会导致代码膨胀`binary size bloat`。现代处理器由于更好的利用了指令缓存, 小巧的代码往往执行更快，因此代码膨胀反而会增加开销
* 简单的inline函数可以放在`.h`文件中，复杂的内联函数的定义, 应放在后缀名为`-inl.h`的头文件中
* 定义在类声明之中的成员函数将自动地成为内联函数
* constexpr自带inline属性
* 尽量使用inline取代macro
  * assert是例外，assert是宏  
