
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
- object-oriented Programming
- functional Programming
- generic Programming
- meta-programming

### sub-language of C++
- C
  - 兼容C语法（为此C++付出了很大的代价）
  - 兼容C的编译模型和运行模型
- object-oriented C++
  - classes
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
