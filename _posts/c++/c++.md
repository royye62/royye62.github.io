### why c++

- c++的强大之处在于抽象不以性能为代价
  - c++的class的memory layout和c的基本一样， 这样的设计带来性能的好处`memory locality`

[面向对象真正核心的思想是消息传递](http://blog.csdn.net/myan/article/details/5928531)
[杂谈现代高级编程语言](https://cxwangyi.wordpress.com/2011/06/19/%E6%9D%82%E8%B0%88%E7%8E%B0%E4%BB%A3%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80/)
面向对象真正核心的思想是消息传递，封装、继承、多态只是表象

- 静态消息机制：Simula ==> c++
  - 导致了形而上学的“面向类设计”
- 动态消息机制: Smalltalk ==> object-c



### c++ 重点
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
    - the reference `must` be initialized (binded with a variable) and can not rebind with another, this hint that reference may be const
  - why use? it's fast as pointer and easy-to-read

### value sematics, object sematics
- value semantics 值语义:
  - 复制后与以前的对象无关的对象叫做值语义，就像拷贝 int 一样
  - C++ 的内置类型(bool/int/double/char)都是值语义
  - 标准库里的 complex<> 、pair<>、vector<>、map<>、string 等等类型也都是值语意，拷贝之后就与原对象脱离关系
  - C++ 要求凡是可以放入容器的元素都必须具备value语义，即必须具备拷贝的能力，放入标准容器的元素和之前的元素没有任何的关联。
  - abstract data type(ADT) 是值语义

- object semantics/reference semantics 对象语义/引用语义
  - 对象语义指的是面向对象意义下的对象，对象拷贝是禁止的
  - 无法复制或者复制后与原来的对象存在关联的对象称为引用语义。
  - object-based, object-oriented 是对象语义

- value sematics vs. object semantics
```
值语义的一个巨大好处是生命期管理很简单，就跟 int 一样——你不需要操心 int 的生命期。值语义的对象要么是 stack object，或者直接作为其他 object 的成员，因此我们不用担心它的生命期（一个函数使用自己stack上的对象，一个成员函数使用自己的数据成员对象）
相反，对象语义的 object 由于不能拷贝，我们只能通过指针或引用来使用它。一旦使用指针和引用来操作对象，那么就要担心所指的对象是否已被释放。
有了 smart pointer，我们可以借助 smart pointer 把对象语义（裸指针）转换为值语义，从而轻松解决对象生命期
```

### initialization
- default initialized 默认初始化
  - what the default value is ? depend on variable data type and the position of variable
  - build-in Type
    - if outside the functions, initialized as 0
    - if inside the functions, uninitialized. it's a danger.
    ```
    int i; // initialized as 0
    void bar() {
      int j; // uninitialized
    }
    ```
  - class Type
    - default constructor
    - if no default constructor, compiler error

- value-initialized
  - the initialized value depends on element type
  ```
  vector<int> ivec(10); //10 elements, all initialized to 0
  vector<string> svec(10); //10 elements, all initialized to empty string
  int *p = new int(); // *p initialized t 0
  ```

## C++ best-practice
- 前置声明



## C++11 语言新特性
[C++11 的 5 个实用特性](http://blog.jobbole.com/95719/)


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


右值引用??
