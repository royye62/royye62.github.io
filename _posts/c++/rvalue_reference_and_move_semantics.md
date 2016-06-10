### expression and value category(lvalue, rvalue, xvalue)

[A Taxonomy of Expression Value Categories
](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2010/n3055.pdf)

- 每一个表达式有两种属性：类型和值分类
```
Each C++ expression (an operator with its operands, a literal, a variable name, etc.) is characterized by two independent properties: a type and a value category. Each expression has some non-reference type, and each expression belongs to exactly one of the three primary value categories.
```

In C++03, an expression is either an rvalue or an lvalue.
In C++11, because of move semantics, an expression can be an lvalue, rvalue, glvalue, prvalue, xvalue.


- value categories (lvalue, rvalue, glvalue, prvalue, xvalue) classify expressions by their values
```
     expression
      /      \
   glvalue  rvalue
    /  \    /    \
lvalue  xvalue   prvalue
```

- glvalue(generalized lvalue): either a lvalue or a xvalue
- rvalue: either a prvalue or an xvalue. It can be moved from. It may or may not have identity.

- lvalue: have identity and cannot be moved from
- prvalue(pure rvalue): do not have identity and can be moved from
- xvalue(expiring value): has identity and can be moved from

### rvalue reference

-  in-depth explanation of rvalue and lvalue
```
lvalue have `persistent` state, address can be taken. provides a (semi)permanent piece of memory. You can make assignments to lvalues.
rvalue are `ephemeral(短暂的)`, either literals or temporary objects created in course of evaluating expressions. (temporary objects which are destroyed at the next semicolon)

在C/C++中，不是所有的数据都有地址，譬如某些存放在寄存器中的数据，此时就没有地址了，因此，对于有地址的数据我们叫做lvalues，而没地址的数据叫rvalues。那实际中有哪些rvalues，譬如函数的返回值，数值计算式等

总之，可以这样理解
lvalue, 有内存地址的(地址可定位的，如通过名称找到地址)，并且是持久的对象，可赋值的
rvalue, 没有内存地址的（literals字面量），或者是临时对象（当行有效，地址无法定位），不可赋值的
```

- why rvalue reference?
```
primary aim of rvalue reference is realize move semantics.
via overloading move semantics constructor => move constructor and move assignment operator
avoid to invoke unnecessary copy-constructor which maybe expensive cost.

class string
{
    string (string&&); //move constructor
    string&& operator=(string&&); //move assignment operator
};
```

### move semantics

[What are move semantics?](http://stackoverflow.com/questions/3106110/what-are-move-semantics)
[Move semantics and rvalue references in C++11](http://www.cprogramming.com/c++11/rvalue-references-and-move-semantics-in-c++11.html)

- move operation means "stolen" the resource from moved-from object
  - calling move implicitly promises that we will not use the moved-from object except to destroy it or assign a new value to it.

- why move rather than copy
```
1. avoid unnecessary copies (when working with temporary objects)
copy can be expensive(always need new memory allocation and sometimes even need to iterator, whereas move do not allocate memory, just "stolen" the data that originally belonged to the source object)

sometimes copy is unnecessary. e.g.:

vector<int> foo ()
{
    vector<int> new_values {1, 2, 3, 4};
    return new_values; // there is also a copy. but compiler do return value optimization (RVO)
}

int main()
{
    vector<int> bar;
    // the return object of foo() is a temporary object, will destroy after copy anyway.
    // if copy, which requires a new memory allocation and another iteration over the entire vector.
    // if move, no memory allocation, more efficient!
    bar = foo();
}

2. classes such as the IO or unique_ptr classes. These classes have a resource (such as a pointer or an IO buffer) that may not be shared. Hence, objects of these types can’t be copied but can be moved
```

- Rvalues Are Moved, Lvalues Are Copied
```
the compiler chooses between the copy constructor and the move constructor based on whether the argument to the assignment operator is an lvalue or an rvalue
```

- copyable and movable
```
The library containers, string, and shared_ptr classes support move as well as copy.
The IO and unique_ptr classes can be moved but not copied.
```

### Perfect Forwarding
????


### copy-and-swap idiom
????
