
### memory problems
```
buffer overrun 缓冲区溢出
buffer overflow
dangling pointer 空悬指针(pointer after free/delete)
uninitialized pointer 野指针
double free/delete
memory leak
memory fragmentation
```

## resource
[microsoft Welcome Back to C++ - Smart Pointers](https://msdn.microsoft.com/en-us/library/hh279674.aspx)
[What is a smart pointer and when should I use one?](http://stackoverflow.com/questions/106508/what-is-a-smart-pointer-and-when-should-i-use-one/106614#106614)
[boost::shared_ptr文档](http://www.boost.org/doc/libs/1_60_0/libs/smart_ptr/shared_ptr.htm)


## smart pointer
- what is smart pointer?
  - A smart pointer is a class that wraps a 'raw' (or 'bare') C++ pointer, to manage the lifetime of the object being pointed to.
- why smart pointer?
  - dynamic memory is problematic because it's hard to ensure that we free memory at the right time
    - we always forget to free memory, in which case we have a `memory leak`
    - or we free the memory when there are still pointers referring to the memory, in which case we have a pointer that refer to invalid memory. (`dangling pointer` 空悬指针:指向已销毁的对象和已回收的地址)
    - we may `double free` the same memory when multiple pointers refer to the same memeory.
    - when exception occurs before delete, thus the memory can't free, also `memory leak`
    - `wild pointer` 野指针：未初始化的指针
  - use smart pointer can avoid those problems

- smart pointer types
  - std::shared_ptr: allow multiple pointers to refer to same object
    - `shared, copyable, movable ownership`
    - some overhead (reference counting), can be copied.
    - beware of circular references
  - std::weak_ptr: hold a non-owning ("weak") reference  reference to an object managed by shared_ptr.
    - It must be converted to std::shared_ptr in order to access the referenced object
    - std::weak_ptr is used to track the object, and it is converted to std::shared_ptr to assume temporary ownership
    - weak_ptr 是shared_ptr的好搭档，可以作弱回调、对象池等

  - std::unique_ptr: `owns` the object to which it points.
    - `non-shared, non-copyable, but movable ownership`
    - no overhead, cannot be copied, can be moved.

  - boost::scoped_ptr
    - `non-shared, non-copyable, non-movable ownership`
    - no overhead, cannot be copied or moved.
    - scoped_ptr is similar to unique_ptr but does not allow transfer of ownership. It works great as long as the smart pointer is meant to retain exclusive ownership throughout its lifetime.
    - scoped_ptr is neither copyable nor movable. It is the preferred choice when you want to make sure pointers are deleted when going out of scope.

  - auto_ptr(obsolete)
    - never use it, using unique_ptr instead

### shared_ptr
  std::tr1::shared_ptr 或 boost::shared_ptr
  实现了 reference-counting
  控制对象的生命周期

### weak_ptr
boost::scoped_ptr
  memory leak, double delete


采用 shared_ptr/scoped_ptr 来管理对象的生命周期

### best-practice

A simple guideline that nearly eliminates the possibility of memory leaks is: always use a named smart pointer variable to hold the result of new. Every occurence of the new keyword in the code should have the form:

shared_ptr<T> p(new Y);
It is, of course, acceptable to use another smart pointer in place of shared_ptr above; having T and Y be the same type, or passing arguments to Y's constructor is also OK.

If you observe this guideline, it naturally follows that you will have no explicit delete statements;

- 当我们使用智能指针管理资源时，必须统一使用智能指针，而不能再某些地方使用智能指针，某些地方使用原始指针，否则不能保持智能指针的语义，从而产生各种错误
