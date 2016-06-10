
## generic algorithm

### overview

- in general, the algorithm do not work directly on a container. instead, they operate by traversing a range of elements bounded by two iterators.
  - algorithms never execute container operation themselves. they operate in terms of iterators and iterators operation. therefore, `algorithms can not directly add/remove elements or any operation to change the size of container`(cause the algorithms operate on iterators, not containers. you know only container can invoke erase/insert/... container operation). however, algorithms can read/write the container elements by iterators operation.
  - e.g. you want to delete a specific element. using algorithms find the iterators denoted to the element, then using container operation `erase`.  vec.erase(find(beg, end, val));
  - e.g. you can use insert_iterator to add elements into a container.
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

### structure of generic algorithms
#### the 5 iterator category
- iterators separate the container and algorithm
- the iterator operations required by the algorithms are group into `5 iterator category`
  - input iterator
    - `==, !=, ++, *, ->`
  - output iterator
    - `++, *`
  - forward iterator
    - `==, !=, ++, *, ->`
    - e.g. forward_list
  - bidirectional iterator
    - `==, !=, ++, --, *, ->`
    - support operator ++ and --, algorithm reverse required bidirectional iterator.
    - e.g. list
  - random access iterator
    - `==, !=, <, <=, >, >=, ++, --, +, +=, -, -=, -(two iterators), *, ->, iter[n] == * (iter + n)`
    - e.g. contiguous-memory container: array, deque, string, vector

#### algorithm parameter patterns
- most of algorithms have one of the following four forms
  - alg(beg, end, args);
  - alg(beg, end, dest, args);
    - dest iterator can be: output_iterator, insert_iterator, ostream_iterator
  - alg(beg, end, beg2, args);
  - alg(beg, end, beg2, end2, args);

#### algorithm naming conventions

- `_if version`

- `_copy version`


### container-specific algorithm

- unlike the other containers, list and forward_list define several algorithms as members
- list and forward_list should use `member version algorithm` in preference to `generic algorithm`
  - list can use generic version algorithm, but at a cost in performance
- member version algorithm of list data structure
  ```
  lst.merge(lst2)
  lsg.merge(lst2, comp)

  lst.remove(val)
  lst.remove_if(pred)
  lst.reverse()

  lst.sort()
  lst.sort(comp)
  lst.unique()
  lst.unique(pred)
  ```

- splice member: this algorithm is particular to list data structure
  ```
  lst.splice(p, lst2)
  lst.splice(p, lst2, p2)
  lst.splice(p, lst2, b, e)
  ```
