---
layout: post
category : c++
tagline:
tags : [c++, OO, inline]
---

## 构造函数（constructor）

控制类的对象初始化过程的函数，任务时初始化类对象的数据成员。

- 构造函数和类名一样
- 构造函数没有返回值
- 构造函数不能声明为const，因为构造过程需要写值

### 默认构造函数（default constructor）

#### 显式定义

#### 隐式定义
编译器创建的默认构造函数，又称为合成的默认构造函数（synthesized default constructor）
只有当类没有声明**任何**构造函数时，编译器才会自动生成默认构造函数。
一旦定义了其他的构造函数，除非显式定义默认构造函数，否则类将没有默认构造函数

初始化data memberf方式的优先顺序
1. 类内初始值（in-class initializer） C++11支持为data member提供初始值
2. 默认初始化（default initialized） 由变量类型决定。

### 委托构造函数（delegating constructor）


## 三大函数(Big Three)

拷贝控制操作(copy control) 包括：  
- 拷贝复制构造函数(copy constructor)
- 拷贝赋值运算符(copy assignment operator)
- 析构函数(destructor)

### 拷贝构造函数(copy constructor)

- 如果类没有显式定义, 则编译器生成一个默认的合成拷贝构造函数(synthesized copy constructor)，浅拷贝的。  
	- 和合成的默认构造函数(synthesized default constructor)不同，合成拷贝构造函数(synthesized copy constructor)即使在定义了其他构造函数，编译器也会生成。

- 拷贝构造函数 ==> 拷贝初始化： “=”右侧对象拷贝到正在创建的对象中
- 其他构造函数 ==> 直接初始化： 函数匹配实现

### 拷贝赋值运算符(copy assignment operator)

- 没有显示定义，编译器会生成一个合成拷贝赋值运算符(synthesized copy-assignment operator)

- 拷贝赋值运算符应该要返回一个指向其左操作数的引用。
	- 标准库通常要求容器中的类型要具有赋值运算符，且其返回值是左操作数的引用

- 注意检测自我赋值(self assignment)。否则会出错！
````
if (this == &str) {
	return *this;
}
````

### 析构函数(destructor)

- 没有显示定义，编译器会生成一个合成析构函数(synthesized destructor)

- 析构函数不接受任何参数，不能被重载。一个类只有一个析构函数

- 先执行函数体(函数体不直接销毁成员,一般是用来释放动态分配的内存)；在随后的析构阶段，销毁data member

- 销毁data member时发生什么依赖于data member的类型。内置类型没有析构函数，什么也不做；类类型成员则执行自己的析构函数。

### 总结

- class with pointer 一定要定义三大函数。否则就是默认合成函数实现的浅拷贝（只拷贝指针的值），这样会出现内存泄露等问题。

- 需要析构函数，一定需要拷贝和赋值操作

- 需要拷贝操作，也一定需要复制操作，反之亦然。但是可能不需要析构函数。


## 堆(heap)、栈(stack)和内存管理

### 堆(heap) vs. 栈(stack)


### new 和 delete

new, 编译器转化为三个动作
````
1. void * mem = operator new (sizeof(String))  //内部调用malloc
2. ps = static_cast<String *>(mem);  //转换类型
3. ps->String::String("hello");  //构造函数
````

delete, 编译器转化为两个动作
````
1. String::~String(ps);
2. operator delete(ps); //内部调用free(ps)
````

array new 一定要搭配 array delete，否则可能会造成内存泄露。
````
string * p = new string[3];
...
delete[] p; // 唤起三次 destructor
````

### 动态分配的内存模型

- 动态分配一个对象: Complex * p = new Complex();

|*00000011*      |
|:----------:    |
|complex(8bytes) |
|*00000011*      |

分配的内存大小为：4*2 + 8 = 16

- 动态分配一个数组: Complex * p = new Complex[3];

|*00000031*      |
|:--------------:|
|3							 |				
|complex(8bytes) |
|complex(8bytes) |
|complex(8bytes) |
|*00000011*      |

分配的内存大小为：4*2 + 8*3 + 4 = 36 ==> 48(必须为16的进位)

- 上下两个00000011是cookie。表示整个内存块的长度是16(00000010)字节，且目前是占用状态(00000001)
- 如果内存最小分块为16bytes，则刚好不需要pad，否则要pad直到为16的倍数。
