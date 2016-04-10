## container
- container
- adaptor
- algorithm

- sequential container
  - vector
  - deque (double-ended queue)
  - list
  - forward_list (singly linked list)
  - array
  - string
- sequential container adaptor 顺序容器适配器
  - stack
  - queue
  - priority_queue

- associative container

标准容器
非标准容器

### 顺序容器 sequential container
顺序容器：元素在容器中的顺序与其加入容器时的位置相对应，而不依赖于元素本身的值.

- performance trade-offs(性能折中) of container
  - the costs to add/remove elements in container
  - the costs to random access the elements

- contiguous memory storage container (vector, string, array)
  - support fast random access
  - In exchange, add/remove element in the middle of container is inefficient

- list storage container (list, forward_list)
  - fast to add/remove an element anywhere.
  - In exchange, not support random access
  - Moreover, memory overhead is often substantial. If your program has lots of small elements and space overhead matters, don’t use list or forward_list


- deque
  - supports fast random access
  - fast insert/delete at front or back
  - 存储方式待定




### 关联容器



### container common operation
#### typedef in container
```
iterator
const_iterator
size_type
difference_type
value_type
reference
const_reference
```

#### constructor and assignment
common
```
C c;
C c1(c2); // container type and element type must match
C c(b, e);  // when pass iterators. no requirement that container types are identical, Moreover element type can differ as long as compatible

C c{a,b,c...}; // list initialization.
C c={a,b,c...};

c1 = c2
c1 = {a,b,c...} // replace with the initializer list
a.swap(b) // swap is usually much faster than copy b to a
swap(a,b) //

```

only for seq container, not valid for associative containers or array
```
seq.assign(b, e)
seq.assign(il)
seq.assign(n,t)
```

#### size
```
c.size() // forward_list not support
c.max_size()
c.empty()
```

#### relational operator
the container type and element type of operands must be identical.

```
common
==, !=

all except unordered associative container
>, >=, <, <=
```

#### add/remove
not support array
```
c.insert(args)
c.emplace(inits)
c.erase(args)
c.clear
```

sequential container add
```
// add in front or back
c.push_back(t)
c.push_front(t) // only list and deque

// add in anywhere(before the element denoted by iterator p)
c.insert(p, t)  
c.insert(p, n, t)
c.insert(p, b, e) // return an iterator to the first element inserted
c.insert(p, il)

c.emplace_back(args) // args pass to element type's constructor
c.emplace_front(args)
c.emplace(p, args)
```

sequential container remove
```
c.pop_back() // return void
c.pop_back()
c.erase(p)
c.erase(b, e) // return an iterator to the element after the last deleted one
c.clear() // return void
```

#### access element
```
c.back() // not for forward_list
c.front()

// ranom access: at and subscript operation valid only for string, vector, deque, array
c[n] // if overrun, undefined error happen
c.at(n) // if overrun, throw out_of_range exception
```
notes:
- make sure the container is not empty, or undefined error happen
- all access operation return a reference.
- safe random access
  - subscript index must be "in range [0, size)", or undefined happen.
    - it's up to the programmer to ensure that the index is valid
    - compiler will not check whether the index is out_of_range
  - use "at" member to ensure the index is invalid
    - if the index is invalid, throw an out_of_range exception

#### resize a container
```
c.resize(n) // if n<c.size(), the excess elements are discarded
c.resize(n, t)
```

#### capacity
```
c.shrink_to_fit()
c.capacity()
c.reserve(n)
```

#### container operation may invalidate iterator
- container operations `add, remove, or resize`, may invalidate pointers, references, or iterators to container elements.
  - it's an serious run-time error to use an invalidated pointers, references, or iterators, as using uninitialized pointer
- ensure pointers, references, or iterators is refreshed when container is changed by add/remove/resize

#### iterator
```
c.begin(), c.end()
c.cbegin(), c.cend()
```

#### reverse_iterator
not support forward_list
```
c.rbegin(), c.rend() //return reverse_iterator
c.crbegin(), c.crend()
```




### iterator
- left-inclusive interval [begin, end)
- features
  - end point to the next position of the last element;
  - begin always before end, and keep begin++ always can reach end;
- meanings:
  - begin == end ==> empty;
  - begin != end ==> at last 1 elements, begin point to the first element; (convenient for using as loop conditions !!!)
- common operation
```
*iter
iter->mem
++iter
--iter // not support forward_list
iter1 == iter2
iter1 != iter2
```
