---
layout: post
category : c++
tagline:
tags : [c++, OO, inline]
---


## 面向对象 Object Oritented  

#### 基于对象(Object Based) vs. 面向对象(Object Oritented)
- **Object Based** 面对的是单一 class 的设计（只有封装，即带类的C）
- **Object Oritented** 面对的是多重classes的设计，classes 和 classes 之间的关系（继承＋多态）

#### 面向对象编程的特性 features of OOP (A PIE)
- 抽象 Abstration
- 多态 Polymorphism
- 继承 Inheritance
- 封装 Encapsulation

#### 对象的构成 an object consist of
- identity
- state
	data fields(properties) with its current value
- behaviors
	functions

#### C++ Class 的两种经典分类
- class without pointer members
- class with pointer members

#### 参数传递和返回值

**传参**
- pass by value
- pass by pointer
- pass by reference (to const)

**返回值**
- return by value
- return by pointer
- return by reference (to const)

**Tips**
- reference相对于pointer的一个优点是传递者无需知道接受者是以reference形式接收（接受者既可以by value，也可以by reference），pointer则必须双方都是pointer。
- 尽量用pass by reference(C++尽量用引用替代指针)，并且必要的时候const
- 尽量用return by reference。但是如果返回的是local object，则必须return by value，因为函数退出reference引用无效了


````c++
// return by reference
// 操作符重载实质就是调用函数
// 编译器认为操作符重载总作用于左操作数，即操作符左侧对象调用该函数
inline complex&
complex::operator += (const complex& r) //也可以定义为全局函数
{
	return __doapl(this, r); // class function, 有隐含的this，因此只需要传一个右操作数参数
}

// return by value
//注意不能return by reference.因为返回的一定是个local object，函数退出reference引用无效了
inline complex
operator + (const complex& x, const complex& y)
{
    return complex(real(x) + real(y),
                   imag(x) + imag(y)); //  typename(): temp object 临时对象。生命周期只有一行。分配完整的对象空间，只是没有对象名而已
}
````

### operator overloading 操作符重载

- 操作符重载实质就是调用函数
- 编译器认为操作符重载总作用于左操作数，即操作符左侧对象调用该函数
- 分为成员函数操作符重载 和 非成员函数操作符重载。
	- 主要区别就是成员函数操作符重载包含隐藏的this指针，左操作数的参数可以省略。如+=的操作符。
	- 非成员函数重载，没有隐藏的this，因此需要左操作数和右操作数两个参数。如<<的重载。

### friend 友元


其他类或者函数

- friend 不是类成员，不受public/private访问控制的限制，所以出现的位置没有限制
- friend 只能声明在类定义的内部，最好在类定义的开始或结束的位置集中声明
- 同一个class的各个objects互为friends

----
### const 关键字(未完待续)  

C++中的const关键字的用法非常灵活，而使用const将大大改善程序的健壮性。

#### 初始化和const
- const必须初始化。
- 默认情况下，const对象仅在文件（translation unit）内有效。这一点和下文的inline类似。
	- 默认情况下，编译器在编译时将用到该const对象的地方都替换成对应的值。也就是说每个文件都要有该const对象的定义。
	- 如果想在多个文件之间共享const对象（在一个文件中定义const，在其他文件中只是声明）。解决的方法是声明和定义都加上extern关键字。

#### 普通变量和const
const int n = 10；  
n是一个只读变量，程序不可以直接修改其值。这里还有一个问题需要注意，即如下使用：int a[n]；在ANSI C中，这种写法是错误的，因为数组的大小应该是个常量，而n只是一个变量。
> 注：看到const关键字，很多人想到的可能是const常量，其实关键字const并不能把变量变成常量！在一个符号前加上const限定符只是表示这个符号不能被赋值。也就是它的值对于这个符号来说是只读的，但它并不能防止通过程序的内部(甚至是外部)的方法来修改这个值(C专家编程.p21)。也就是说 const变量是只读变量，既然是变量那么就可以取得其地址，然后修改其值。看来const也是防君子不防小人啊！:)

#### 指针和const
- `const char * p` 指向常量的指针（pointer to const）
- `char * const p` 常量指针（const pointer）
- `const char * const p` 指向常量的常量指针
> 诀窍：沿着*号划一条线，如果const位于*的左侧，则const就是用来修饰指针所指向的变量，即指针指向为常量；如果const位于*的右侧，const就是修饰指针本身，即指针本身是常量。

#### 顶层const和底层const
- **顶层const** 对象本身是常量。对任何类型都适合
````
	- const int i = 10;
	- char * const p = NULL;
````
- **底层const** 所指的对象是常量（针对指针和引用等符合类型）  
````
	- const int *p2 = NULL;
	- const int &r = i;
````

#### cosnt对象拷贝
- 顶层const，不受影响  
````
	void func(const int i) // 既可以传const int，也可以传int
````

- 顶层const，必须有相同的底层const类型，或者非const转换成const（反之不行）
````
	int i = 42;
	const ci = 32;
	const int *cp = &ci; // OK. 底层const类型匹配
	const int *cp = &i; // OK. int * 能转换成 const int *
	int * p = cp; // Error, 底层const类型不匹配，const int * 不能转换成 int *
	int &r = ci;  // Error, 底层const类型不匹配，int引用不能绑定到int常量上
	void func(int *p);
	func(&i); // OK.
	func(cp); // Error. const int * 不能转换成 int *
````

#### 类和const
- const成员变量

      class Foo
      {
        Foo(int x)
          :n(x)           //只能在初始化列表中赋值   
        { }

        const int n;      //成员常量不能被修改   
      }

- const成员函数

      class Foo
      {
        //不能改变对象的成员变量
        //不能调用类中任何非const成员函数
        void func() const;        
      }

- const类对象/对象指针/对象引用

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

#### constexpr 和 const expression(常量表达式)
- **const expression** 值不会改变并且编译过程中就能得到结果的表达式。
````
	- const int max = 20; // 字面值一定是常量表达式
	- int max = 20; //不是。非const int
	- const max = get_max(); // 不是。get_max运行时知道结果
````
- **constexpr 变量** 声明为constexpr的变量必须一个常量表达式。
	- 如果你认定变量是一个常量表达式，声明称constexpr类型

#### const定义常量 代替 常量宏
  - const定义的常量在程序运行过程中只有一份拷贝
  - 宏定义的常量在内存中有若干个拷贝

----
## inline vs. marco

#### 相同点
消除函数调用的开销（参数压栈、跳转、弹栈、返回等指令操作）

#### 不同点
- macro 宏
  - 预编译阶段。进行
  - 预编译器进行文本替换 textual substitution
  - 无类型，无类型安全检查
  - 不可调试

- inline
  - 编译阶段
  - 编译器在调用点内联展开函数的代码
    - 编译器必须在translation unit中找到inline函数的定义才能进行展开，因此inline函数应该在头文件中定义（只声明没用）
  - 有类型，类型安全检查  

#### 注意
- inline是对编译器的请求。现代编译器能够根据函数的定义决定是否接受inline，甚至自动inline一些没有请求inline的函数
- 当函数只有 10 行甚至更少时才将其定义为内联函数。滥用内联会导致代码膨胀`binary size bloat`。现代处理器由于更好的利用了指令缓存, 小巧的代码往往执行更快，因此代码膨胀反而会增加开销
- 简单的inline函数可以放在`.h`文件中，复杂的内联函数的定义, 应放在后缀名为`-inl.h`的头文件中
- 定义在类声明之中的成员函数将自动地成为内联函数
- constexpr自带inline属性
- 尽量使用inline取代macro
  - assert是例外，assert是宏  


### prefer const, inline, enum to macro(#define)

macro
- preprocessor
- symbol table
```
#define ASPECT_RATIO 1.653 //bad
const double ASPECT_RATIO = 1.653; //good
const char * const authorName = "Scott Meyers";

```
