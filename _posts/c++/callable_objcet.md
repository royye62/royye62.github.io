### callable objects

A callable object is something that can be called like a function, with the syntax object() or object(args)

- function
- function pointer/reference
- functor/function objcet: class which overloaded operator()
- std::function object  
- objects created by std::bind
- lambda expression
- closure generate by std::bind or lambda

### function, bind,  and closure

- closure = a callable object + an environment
- closure 是一个函数和它所引用的非本地变量的上下文环境的集合。非本地变量：正常情况下无法访问的变量，如：从A函数访问另B函数的局部变量b，一般情况下无法实现，但可以通过闭包，将B函数+局部变量b=>闭包BClosure，通过BClosure可以访问b
- A closure (also lexical closure, function closure or function value) is a `function` together with  
a `referencing environment` for the `non-local variables` of that function.  

- std::function 保存可调用对象
  - type-safety function wrapper, can store, copy, and invoke any Callable target

- std::bind 生成可调用对象（加上绑定参数）
  - bind callable object with some arguments and generates a new callable function wrapper.
  - notes: ????

- lambda expressions 生成`匿名的`可调用对象（加上在作用域内捕获的变量）
  - an unnamed function object capable of capturing variables in scope.

- closure 可以保持在 std::function 对象中
- 通过std::bind绑定的参数 和 lambda捕获的变量就实现了闭包。

#### Lambda expressions

- Lambda 表达式简单说就是个匿名函数（译注：原文为in-place function，意思是“用来镶嵌的函数”，但是匿名函数能更精确表达这个含义）

- lambda form:
  - capture list: an list of local variables defined in the enclosing function, often empty.
  - parameter list: same as ordinary function
  - return type: unlike ordinary function, a lambda must use a `trailing return type(尾置返回类型)` to specify its return type
  - function body: same as ordinary function
  ```
  [capture list](parameter list) -> return type { function body }

  // must always include [capture list] and {function body}
  // when omit parameter list is equivalent to a empty parameter list
  // when omit return type, than the return type is inferred from the type of expression
  auto f = [] { return 42; }; // return type is int
  cout << f() << endl; // output is 42
  ```

- capture list list can be passed as follows
  - [a,&b] where a is captured by value and b is captured by reference
  - [this] captures the this pointer by value
  - [&] captures all automatic variables odr-used in the body of the lambda by reference
  - [=] captures all automatic variables odr-used in the body of the lambda by value
  - [] captures nothing
