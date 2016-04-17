### boost是野生的stl
因为STL和Boost基本上都是标准委员会那批人在策划、审核和维护，所以口味上是相对接近的。
但是因为Boost并不在标准中，或者说是下一代标准的试验场，所以库的种类要更多一些，手法也更加丰富一些。

cpp-netlib

Boost.Asio


## boost 中有用的库

### noncopyable
```
class noncopyable
  {
   protected:
      noncopyable() {}
      ~noncopyable() {}
   private:  // emphasize the following members are private
      noncopyable( const noncopyable& );
      const noncopyable& operator=( const noncopyable& );
  };
```

boost::noncopyable比较简单, 主要用于单例的情况。通常情况下, 要写一个单例类就要在类的声明把它们的构造函数, 赋值函数, 析构函数, 复制构造函数隐藏到private或者protected之中, 每个类都这么做麻烦。
有noncopyable类, 只要让单例类直接继承noncopyable。class noncopyable的基本思想是把构造函数和析构函数设置protected权限，这样子类可以调用，但是外面的类不能调用，那么当子类需要定义构造函数的时候不至于通不过编译。但是最关键的是noncopyable把复制构造函数和复制赋值函数做成了private，这就意味着除非子类定义自己的copy构造和赋值函数，否则在子类没有定义的情况下，外面的调用者是不能够通过赋值和copy构造等手段来产生一个新的子类对象的。

```
class Test ： public noncopyable
{
};

void main()
{
   Test a;

   Test b(a);  // error
   Test c = a;     // error
}
```
