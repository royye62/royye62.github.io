
- c++, c++11
- c++ library
  - STL
  - iostreams
  - locales
  - C99 stdlib
- algorithm and data structure
- multi-threads


### C++ is multi-paradigm Programming Language
- procedural Programming
  - better C
- data abstraction (value sematics)
  - abstract data type(ADT)
- object-based (object sematics)
  - Encapsulation
- object-oriented (object sematics)
  - Encapsulation
  - Inheritance
  - Polymorphism
- generic Programming
- functional Programming
- meta-programming

### sub-language of C++
- C
  - 兼容C语法（为此C++付出了很大的代价）
  - 兼容C的编译模型和运行模型
- object-oriented C++
  - Encapsulation
  - Inheritance
  - Polymorphism
- Template C++
- STL
  - container
  - iterator
  - algorithm
  - function object


## 编译期 vs. 运行期
	C++在编译期中做了很多工作，以提高运行期的性能

- vtable
在C++的标准规格说明书中说到，编译器必需要保证虚函数表的指针存在于对象实例中最前面的位置（这是为了保证正确取到虚函数的偏移量）。 这意味着我们通过对象实例的地址得到这张虚函数表，然后就可以遍历其中函数指针，并调用相应的函数。


前置声明(forward declaration)减少使用include


### C++语言的三大约束是
- 与C兼容
  - 不仅是语法兼容，更重要的是兼容C语言的编译模型与运行模型，也就是能直接使用C语言的头文件。
- 零开销
  - ?
- 值语义

### variable, reference
- variable => physical concept
  - data type. decide the memory layout
  - data value
- variable name => logic concept
  - the alias of a block of memory, compiler can associate the value name with value memory, so programmer can manipulate the memory by value name.

- reference => the alias of a variable
  - the essence is a const pointer(Type * const name)
  - why use? it's fast as pointer and easy-to-read

### C++对象语义与值语义
object-based 是对象语义
abstract data type(ADT) 是值语义
？？

## C++ best-practice
- 前置声明



## C++11
[C++11 的 5 个实用特性](http://blog.jobbole.com/95719/)

### 语言新特性
- Strongly Typed Enums 强类型枚举
传统C++中枚举常量被暴漏在外层作用域中，这样若是同一作用域下有两个不同的枚举类型，但含有相同的枚举常量也是不可的，比如：
另外一个缺陷是传统枚举值总是被隐式转换为整形，用户无法自定义类型
```
enum Side{Right,Left};  
enum Thing{Wrong,Right};  
```

强类型枚举使用enum class语法来声明，如下：
```
enum class Enumeration{  
 VAL1,  
 VAL2,  
 VAL3=100,  
 VAL4  
};  
```

- static_assert
static_assert 简单说就是一个在编译期执行的断言。
```
static_assert(sizeof(unsigned int) * CHAR_BIT == 32);
```
