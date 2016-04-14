
## generic algorithm

### overview

- in general, the algorithm do not work directly on a container. instead, they operate by traversing a range of elements bounded by two iterators.
  - algorithms never execute container operation themselves. they operate in terms of iterators and iterators operation. therefore, `algorithms can not directly add/remove elements or any operation to change the size of container`(cause the algorithms operate on iterators, not containers. you know only container can invoke erase/insert/... container operation). however, algorithms can read/write the container elements by iterators operation.
  - e.g. you want to delete a specific element. using algorithms find the iterators denoted to the element, then using container operation `erase`
  - Why do you think the algorithms don’t change the size of containers? because the design aim of iterator is to separate the algorithms and containers. if algorithms can directly operate containers, thus, the algorithms have to be compatible with all the containers.

- so, iterators make the algorithms and container independent.
  - iterators not depend on the type of container

- but, algorithms do depend on element-type operation
  - for example, `find` algorithm requires element-type's `operator==`, some algorithm require `operator<`

### a first look at the algorithm
#### read-only algorithms

#### algorithm that write container elements

#### algorithm that reorder container elements


### customizing operations

- by default, algorithm use the element-type's `operator==` or `operator<`

- if the default operator not exist, or we want customizing algorithm, we need to overload the algorithm. for example:
  - default version:  sort(begin, end)
  - overload version: sort(begin, end, predicate)

#### predicate(谓词)
- a predicate is an expression that can be called and that returns a value that can be used as a condition.

- category
  - `unary predicate`
  - `binary predicate`

- the parameter type of the predicate must be compatible with element type!

#### Lambda expressions

Lambda 表达式简单说就是个匿名函数（译注：原文为in-place function，意思是“用来镶嵌的函数”，但是匿名函数能更精确表达这个含义）
