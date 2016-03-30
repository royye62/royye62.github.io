---
layout: post
category : c++
tagline:
tags : [c++, OO, inline]
---

## Templates and Generic Programming

泛型技术
比如：模板技术，RTTI技术，虚函数技术，要么是试图做到在编译时决议，要么试图做到运行时决议。

- 模板是泛型编程的基础

## 模板 template
- 模板不是类或函数，可以将模板看作编译器生成类或者函数的一份说明书;
- 编译器根据模板创建类或者函数的过程称为模板的实例化(instantiation);
- 使用模板时，必须(隐式或显式)指定模板实参
	- 显式模板实参(explicit template argument)
	```
	max<int> (1, 3.0); //function template
	Blob<int> a; //class template 必须显式指定
	```
	- 隐式模板实参(implicit template argument)
	```
	max(1, 2); //function template
	```

### 函数模板(function template)
template<template parameter list>
	template<typename T1, typename T2>


#### 函数模板实例化 instantiate
```
When the compiler instantiates a template, it creates a new “instance” of
the template using the actual template argument(s) in place of the
corresponding template parameter(s).
```

#### 非类型模板参数(nontype parameter)
```
template <unsigned N, unsigned M>
inline int compare(const char (&p1)[N], const char (&p2)[M]) {
    return strcmp(p1, p2);
};

// 非类型模板参数的模板实参必须是常量表达式
cout << compare("a", "ba") << endl;
```

### 类模板(class template)

#### 类模板的实例化
- 类模板实例化必须提供显式模板实参
```
Blob<int> ia;
Blob<int> ia2 = {0, 1, 2, 3, 4};
```
- 一个实例化了的类模板，其成员函数只有在使用时才被实例化
```
Blob<int> ia2 = {0, 1, 2, 3, 4}; // 类模板实例化
ia2.size(); // 实例化 Blob<int>::size() const
```
