## initialization

### declaration, definition, initialization
- declaration 用于想使用别的 translation unit 定义的变量时，需要包含该变量的声明
- definition 创建变量，给变量分配内存。
**因此声明可以多次，定义只能一次**
```
extern int i; // declaration i
int j;        // declaration and definition j
int m = 1;    // declaration and definition m, and initialization m
extern double pi = 3.14; // declaration and definition pi. extern 的作用被抵消了
```

### initialization modes
- 对于内置类型，四种初始化方式基本上是等价的
```
int units_sold = 0; // copy initialization
int units_sold(0); // direct initialization
int units_sold = {0}; // list initialization
int units_sold{0}; // list initialization
```

- 对于非内置类型
```
string s1 = "hello"; // copy initialization。只能提供一个初始值
string s2("hello");  // direct initialization
string s2(10, 'c');  // direct initialization。可以多个初始值

vector<string> v1{"a", "b", "c"} //初始值是列表形式，必须用 list initialization
```

- in-class initializer (C++11)
```
class Test
{
  int i = 10;  // in-class initializer 只能使用copy initialization和list initialization两种形式
  vector<string> v1 = {"a", "b", "c"};
}
```

**initialization is not assignment**
- initialization: create a variable and give it a initialized value
- assignment: erase the variable value, and replace with a new value


### default initialization
定义变量没有指定初值，编译器将赋予默认值。
默认值由变量类型和变量的位置决定
- 内置类型
  - 函数体外。默认初始化
  - 函数体内。undefined
- 复合类型初始化
  - 指针？
  - 引用？
- 类类型
  - 类默认初始化

### 类的初始化
- 类的初始化完成是在 member initialization list
  - 对于非内置类型，在member initialization list进行的是copy construct

- 在构造函数函数体中进行的不是初始化，而是赋值assignment,赋值之前会进行一次 default constructor.
 - default construct + copy assignment operator 的效率一般情况下肯定是不如一次性 copy construct


直接初始化
拷贝初始化

初始化的顺序
- 先基类，再继承类
- 按声明的次序进行初始化

初始化次序不确定性
-




## constructor, destructor, assignment operator


### declare destructor virtual in Polymorphic base classes
- Polymorphic base classes must declare a virtual destructor.
  - **delete derived object through a base class pointer and the base class has a non-virtual destructor, the result is undefined. (What often happens at runtime is that the derived class's destructor is never called)**  
- class with any virtual function, must have a virtual destructor.

- non-polymorphic base classes, should not declare a virtual destructor
  - string, STL


### assignment operator should return a reference to `*this`
- implement the chain-assignment(连锁赋值)
- operator =, +=, -=, ...


### copy function(copy constructor and copy assignment operator) make sure copy all members include base class part.
```
inline Circle::Circle(const Circle& other)
        : Shape(other), // copy base class part
          radius_(other.radius_),
          center_(other.center_)
{}


inline Circle&
Circle::operator = (const Circle& other)
{
    if (this == &other) {
        return *this;
    }

    Shape::operator=(other); // copy base class part
    this->radius_ = other.radius_;
    this->center_ = other.center_;
    return *this;
}
```
