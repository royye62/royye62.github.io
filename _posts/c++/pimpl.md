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
