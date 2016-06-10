[RAII](http://en.cppreference.com/w/cpp/language/raii)
[Use RAII to prevent leaks](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#e6-use-raii-to-prevent-leaks)
[RAII和unique_ptr](http://www.cnblogs.com/shengjianjun/p/3699920.html)

### RAII(Resource Acquisition Is Initialization) 资源获取即初始化

- `资源的生命周期和对象的生命周期严格绑定(binds resource life cycle to the object lifetime)`
Resource Acquisition Is Initialization or RAII, is a C++ programming technique which binds the life cycle of a resource (allocated memory, thread of execution, open socket, open file, locked mutex, database connection—anything that exists in limited supply) to the lifetime of an object.

- 将资源封装在类中。当对象构造的时候，在构造函数中申请资源(resource allocation)，而在对象的析构函数中释放资源(resource deallocation)，只要`对象能正确地析构`，就不会出现资源泄露问题
  - C++保证了所有栈对象在生命周期结束时会被销毁(即调用析构函数)
  - C++处理异常安全资源管理（exception-safe resource management）无论在函数正常返回时，还是在途中抛出异常时，都会引发函数的堆栈回退(stack unwinding)。从而会自动调用对象的析构函数
  - 当一个函数需要通过多个`局部变量`来管理资源时，RAII就显得非常好用。因为`只有被构造成功(构造函数没有抛出异常)的对象才会在返回时调用析构函数`，同时`析构函数的调用顺序恰好是它们构造顺序的反序(order of initialization and stack unwinding)`，这样既可以保证多个资源(对象)的正确释放，又能满足多个资源之间的依赖关系。

- gcc 的 RAII
  - gnu针对C提供了一种非标准的扩展来支持RAII： "cleanup" variable attribute
  - Linux内核驱动，在编写驱动的过程中，一般也是在init函数中申请资源，然后在exit函数中释放资源，能够避免资源泄露，因此这种思想普遍运用在编程中

### Benefits
- The advantages of RAII as a resource management technique are that it provides encapsulation, exception safety (`for stack resources`), and locality (it allows acquisition and release logic to be written next to each other).
- don't require explicit cleanup. relieves the burden of calling "resource release" operation in a clever way.
- RAII可以极大地简化资源管理，并有效地保证程序的正确和代码的简洁，所以通常会强烈建议在C++中使用它
  - 虽然RAII和finally都能保证资源管理时的异常安全，相对于使用用finally来说，使用RAII能减少代码量

### transfer resource ownership
Move semantics make it possible to safely transfer resource ownership between objects, across scopes, and in and out of threads, while maintaining resource safety.

### Typical uses
- The C++ library classes that manage their own resources follow RAII:
  - std::string, std::vector, std::thread

- The RAII design is often used for controlling mutex locks in multi-threaded applications.
  - std::lock_guard, std::unique_lock, std::shared_lock ????
```
std::mutex m;

void bad()
{
    m.lock();                    // acquire the mutex
    f();                         // if f() throws an exception, the mutex is never released
    if(!everything_ok()) return; // early return, the mutex is never released
    m.unlock();                  // if bad() reaches this statement, the mutex is released
}

void good()
{
    std::lock_guard<std::mutex> lk(m); // RAII class: mutex acquisition is initialization
    f();                               // if f() throws an exception, the mutex is released
    if(!everything_ok()) return;       // early return, the mutex is released
}  
```

- Another typical example is interacting with files

- smart_ptr to manage dynamically-allocated memory
  - shared_ptr: shared-ownership
  - unique-ptr: sole-ownership
