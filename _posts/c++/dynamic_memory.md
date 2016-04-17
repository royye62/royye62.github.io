
### memory problems
- buffer overrun 缓冲区溢出
- double free

## resource
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

- two smart pointer types + 1伴随类指针
  - unique_ptr: `owns` the object to which it points.
  - shared_ptr: allow multiple pointers to refer to same object
  - weak_ptr: a weak reference to an object managed by shared_ptr.
      weak_ptr 是shared_ptr的好搭档，可以作弱回调、对象池等

- other
  - boost::scoped_ptr
    - scoped_ptr is similar to unique_ptr but does not allow transfer of ownership. It works great as long as the smart pointer is meant to retain exclusive ownership throughout its lifetime.
    - scoped_ptr is neither copyable nor movable. It is the preferred choice when you want to make sure pointers are deleted when going out of scope.
  - auto_ptr(obsolete)
    - neve use it, using unique_ptr instead


### shared_ptr
  std::tr1::shared_ptr 或 boost::shared_ptr
  实现了 reference-counting
  控制对象的生命周期

### weak_ptr


boost::scoped_ptr
  memory leak, double delete


采用 shared_ptr/scoped_ptr 来管理对象的生命周期
