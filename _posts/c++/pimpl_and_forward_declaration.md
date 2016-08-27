### pimpl idiom
The pimpl idiom is a modern C++ technique to hide implementation, to minimize coupling, and to separate interfaces. Pimpl is short for "pointer to implementation."

[Pimpl For Compile-Time Encapsulation](https://msdn.microsoft.com/en-us/library/hh438477.aspx )
[PIMPL（一）](http://www.cnblogs.com/lovers/p/pimpl.html)
[PIMPL（二）](http://www.cnblogs.com/lovers/p/pimpl2.html)
《effective C++》 条款31：将文件间的编译关系降至最低

### Why use pimpl?
- Minimization of compilation dependencies. 将文件间的编译关系降至最低，编译会快很多
- Separation of interface and implementation. 更有利于单元测试？？？
- Portability

### how to use pimpl?

- pimpl + forwarding declaration
```
// my_class.h
class my_class {
   //  ... all public and protected stuff goes here ...
private:
   class impl;
   unique_ptr<impl> pimpl; // opaque type here
};
```

### 前置声明(forward declaration)
[关于前置声明与C++中头文件相互包含的几点问题](http://blog.csdn.net/crayondeng/article/details/10830351)
[C++中前置声明的应用与陷阱](http://blog.csdn.net/yunyun1886358/article/details/5672574)

- 意义
  - 减少使用include，和pimpl技法配合，可以减少文件的编译依赖。
  - 前置声明可以解决两个类相互依赖的情况

- 原理
  - C++编译器自上而下编译源文件的时候，对每一个数据的定义，总是需要知道定义的数据的类型的大小。
  - 先声明类CBed，告诉编译器CBed是一个类（不用包含CBed的头文件，因此编译器不知道CBed的完整定义type incomplelte）
  - 如果在CHouse类中，定义任何需要CBed完整类型的内容，都不可能编译通过。比如说调用CBed类的成员变量或成员函数，定义CBed类对象作为类成员等，都需要CBed的完整定义，编译时会报错type incomplelte。
  - 但是在CHouse类中，定义CBed*类成员，因为CBed*的大小是确定，因此House.h可以编译通过。

```
// House.h  
class CBed; // 盖房子时：现在先不买，肯定要买床的  
class CHouse  
{  
    CBed* bed; // 我先给床留个位置。指针的大小已知 type complete  
    //CBed bed; // 直接把床放到房子里，需要床的大小，否则type incomplelte，就必须include <CBed.h>
public:  
    CHouse(void);  
    virtual ~CHouse(void);  
    void GoToBed();  
};

// House.cpp  
#include "Bed.h"  // 在类的实现部分，需要CBed的具体类对象，因此需要 #include "Bed.h"，并链接bed的定义
#include "House.h" // 等房子开始装修了，要买床了  

CHouse::CHouse(void)
{  
    bed = new CBed(); // 把床放进房子  
}  

CHouse::~CHouse(void)  
{  
}  

void CHouse::GoToBed()  
{  
    bed->Sleep();  
}
```

- 原则
  - 能不包含头文件，就不包含
  - 尽量在cpp文件里包含头文件，而不要在头文件里包含头文件
