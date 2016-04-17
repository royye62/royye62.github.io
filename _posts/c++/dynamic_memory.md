
### memory problems
- buffer overrun 缓冲区溢出
- double free


## smart pointer
- why smart pointer?
  - dynamic memory is problematic because it's hard to ensure that we free memory at the right time
    - we always forget to free memory, in which case we have a `memory leak`
    - or we free the memory when there are still pointers referring to the memory, in which case we have a pointer that refer to invalid memory. (`dangling pointer` 空悬指针)
    - we may `double free` the same memory when multiple pointers refer to the same memeory.
    - when exception occurs before delete, thus the memory can't free, also `memory leak`
  - use smart pointer can avoid those problems

- two smart pointer types
  - shared_ptr: allow multiple pointers to refer to same object
    - weak_ptr: a weak reference to an object managed by shared_ptr
  - unique_ptr: `owns` the object to which it points.


### shared_ptr
