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

### 函数模板(function template)
template<template parameter list>
	template<typename T1, typename T2>


### 模板实例化 instantiation.
```
When the compiler instantiates a template, it creates a new “instance” of
the template using the actual template argument(s) in place of the
corresponding template parameter(s).
```


#### 非类型模板参数(nontype parameter)
template<unsigned N, unsigned M>

非类型模板参数的模板实参必须是常量表达式


### 类模板(class template)
