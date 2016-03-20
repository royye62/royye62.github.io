---
layout: post
category : c++
tagline:
tags : [c++, OO, inline]
---

## OOD(Object Oritented Design)
- Inheritance 继承
	- 表示 is-a
	- public, private, protected Inheritance
	- 子类继承父类的所有数据成员
	- 子类对函数继承是继承调用权
- Composition 复合/组合
	- 表示 has-a
- Delegation 委托
	- Composition by reference 也称为引用复合/组合(不是真正的has-a，而是has-a-pointer which point to a object)
		- 实际上component是指针，但约定俗成称为引用复合。component指针指向另一个类，需要的时候可以通过指针调用（委托）另一个类
	- Handle/Body。Handle是接口，Body是实现。这是一种松耦合的思想
	- pimpl(pointer to implication)
	- reference counting 就是一种Delegation的实现。另外，当一个对象想通过reference改变指向的对象时，就需要实现copy-on-write(写时复制)

### UML关系图
```
+ public
- private
# private
_ static

```

### Inheritance with virtual functions
- non-virtual function：不希望derived class overwrite

- virtual function
	- 希望derived class overwrite。
	- 已有默认定义。

- pure virtual function
	- 一定要 derived class overwrite。
	- 没有默认定义。(其实可以有定义)

```
class Shape {
public:
		virtual void draw() const = 0;
		virtual void error(const std::string& msg);
		int objectID() const;
};
```

### Inheritance, Composition的构造和析构顺序
- Inheritance
	- 构造由内到外：super class constructor ==> derived class constructor
	- 析构由外到内：derived class destructor ==> super class destructor
		- **super class destructor must be virtual, otherwise occurs undefined behavior**

- Composition
	- 构造由内到外：component default constructor ==> derived class constructor
	- 析构由外而内：derived class destructor ==> component destructor

- Inheritance + Composition
	- 构造由内到外，先父类再组合：super class constructor ==> component default constructor ==> derived class constructor
	- 析构由外到内，先组合在父类：derived class destructor ==> component destructor ==> super class destructor


## Inheritance,Composition,Delegation 和设计模式
### Template Method 模板方法/函数
- Inheritance with virtual functions 的实现，晚绑定
- MFC, 通过Template Method思想实现的Application Framework

### Adapter 适配器模式
Composition的实现

### Observer 观察者模式
Delegation + Inheritance

### Composite 组合模式
Delegation + Inheritance

### Prototype
Delegation + Inheritance
现在要创建未来的class对象，使得Prototype能看到新创建的对象


## 类型转换与继承

### 派生类向基类的类型转换

- 从派生类到基类的类型转换只对指针或者引用类型有效
```
Quote item;       // 基类对象
Bulk_quote bulk;  // 派生类对象
Quote *p = &item; // p指向Quote对象
p = &bulk;       // p指向bulk的Quote部分
Quote &r = bulk; // r绑定到bulk的Quote部分
```

- （非指针或引用）派生类对象也可以拷贝、移动或者赋值给一个基类对象。但由于是调用基类的拷贝控制成员，所以只能处理派生类的基类部分，派生部分会被切掉（sliced down）
```
Bulk_quote bulk;  // 派生类对象
Quote item(bulk); // 使用Quote::Quote(const Quote&)构造函数
item = bulk; // 使用Quote::operator=(const Quote&)赋值操作符
```

- 不存在基类向派生类的隐式转换
```
Bulk_quote bulk;  // 派生类对象
Quote *itemP = &bulk; // itemP的动态类型是Bulk_quote
// 即使itemP指针绑定的动态类型是Bulk_quote（派生类），但itemP的静态类型是Quote（基类）
// 编译器只能通过检查指针或引用的静态类型来判断该转换是否合法
Bulk_quote *bulkP = itemP; // 错误，不能将基类转换成派生类
```

### 静态类型 vs. 动态类型
- 存在条件
	- 当使用存在继承关系的类型时
	- 且当变量或者表达式是引用或指针时
	- 存在静态类型(static type)和动态类型(dynamic type)

- 静态类型是在编译时已知的，是变量声明的类型
- 动态类型是在运行时才可知的，是表示内存中的对象的类型

### 动态绑定（dynamic binding/ run-time binding）， 多态性， 虚函数，虚函数表

- 动态绑定：函数运行时选择函数版本
	- 和overload区分。overload是指同一个类里，同名函数通过参数不同来确定调用哪个函数。注意和返回值无关，和继承无关，和virtual无关

- `当且仅当` `基类`通过`指针或者引用`指向`派生类类型`，且调用的是`虚函数`时，才能实现动态绑定（会在运行时解析该调用）。但这时还不算实现了多态性，还需要派生类`重写override`了虚函数，这是才会调用动态类型的虚函数，实现多态性
	- 不满足以上的任何一个条件，都无法实现动态绑定（多态性），调用的都是本身的函数（编译时绑定的函数）
	- 调用非虚函数，普通对象调用函数都是在编译时绑定了调用函数的版本。

- 多态性：父类的指针有“多种形态”。简而言之就是用父类的指针指向其子类的实例，然后通过父类的指针调用实际子类的成员函数

- 虚函数的作用主要是实现了多态的机制
	- 虚函数（Virtual Function）是通过一张虚函数表（Virtual Table）来实现的


### 显式类型转换
C++风格的类型转换提供了4种类型转换操作符来应对不同场合的应用
cast-name<type>(expression)

- const_cast
	- 去const属性
- dynamic_cast
	- 转换的安全检查将在运行时执行
	- 多态类之间的类型转换用daynamic_cast
- static_cast
	- 类似于C风格的强制转换。无条件转换
	- 强制覆盖编译器的检查工作
	- 基本类型转换用static_cast
- reinterpret_cast
	- 不同类型的指针类型转换用reinterpret_cast
