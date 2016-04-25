## stl
- stl 6 components
  - allocator
  - container
  - algorithm
  - iterator
  - functor
  - adaptor

### sequential container 顺序容器
- elements are stored and accessed sequentially by their position in the container
  - the sequence of elements is corresponding with the position while they adding to the container, not their value.

- category of sequential container
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

- performance trade-offs(性能折中) of container
  - the costs to add/remove elements in container
  - the costs to random access the elements

- contiguous-memory container (vector, string, array)
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

### associative container 关联容器
- Elements in an associative container are stored and retrieved by a `key`
  - In contrast, elements in a sequential container are stored and accessed `sequentially by their position in the container`.

- two primary types
  - map
    - The elements in a map are key–value pairs
    - use as dictionary
    - often refer to as an associative array. an associative array is like a normal array except that its subscript don't have to be int.

  - set
    - A set element contains only a key
    - a set is most useful when we simply want to know whether a value is present. for example: white-list
    - use when to keep elements sorted and unique

- 8 associative containers
```
// elements ordered by key
map // key-value pairs, unique key
set // key is the value, unique key
multimap // allow multiple key
multiset

// unordered key
unordered_map
unordered_set
unordered_multimap
unordered_multiset
```

- pair: key-value
  - the data members of pair are public. these members are named `first` and `second`
  - In a map, the elements are key-value pairs. That is each element is a pair object.
    - map<T1,T2>::value_type => pair<`const` T1, T2> //the value type of map is a pair and `we can change the value but not the key of pair`!
    - map<T1,T2>::key_type => T1
    - map<T1,T2>::mapped_type => T2
```
pair<T1, T2> p(v1, v2)
pair<T1, T2> p = {v1, v2}
make_pair(v1, v2)

p.first
p.second
```

- set<T>::iterator are const
  - `the keys in a set are const!`, we can use a set iterator to read, but not write.

- associative container and algorithm
  - In general, we do not use the generic algorithm with associative container. the fact that the keys are `const` means that we can not pass the associative container iterators to `algorithm that write or reorder container elements`.
  - associative container can be used with read-only algorithm. however, it is much faster to use the find member(`map.find()`) than to call generic version.
  - In practice, if we can use an associative container with the algorithm either as source sequence or as destination.
  ```
  set<int> c;
  vector<int> v;
  copy(c.begin(), c.end(), back_inserter(v, v.end())) // as a source sequence
  copy(v.begin(), v.end(), inserter(c, c.end())) // as a destination
  ```

### container common operation
#### typedef in container
common
```
iterator
const_iterator
size_type
difference_type
value_type
reference
const_reference
```
associative container
```
key_type    // type of the key
mapped_type // type associated with each key, map only
value_type // for set, same as key_type; for map, pair<const key_type, mapped_type>
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
common, but not support array
```
c.insert(args)
c.emplace(inits) //???
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

associative container add
```
c.insert(b, e) // iterator range
c.insert(il) // map.insert({word, 1})
.... TODO
```

#### remove
common
```
c.erase(args)
c.clear
```

sequential container remove
```
c.pop_back() // return void
c.pop_back()
c.erase(p)
c.erase(b, e) // return an iterator to the element after the last deleted one
c.clear() // return void
```

associative container remove
```
c.erase(k) // remove all element with key k, return size_type indicating the number of elements removed.
c.erase(p)  // remove element denoted by iterator p
c.erase(b, e)
```

#### access element
sequential container access
```
c.back() // not for forward_list
c.front()

// ranom access: at() and subscript(operator[]) valid only for contiguous-memory containers
c[n] // no bounds checked, if overrun, undefined error happen. more efficient than at
c.at(n) // bounds checked, if overrun, throw out_of_range exception
```
notes:
- make sure the container is not empty, or undefined error happen
- all access operation return a reference.
- safe random access
  - operator[] must be "in range [0, size)", or undefined happen.
    - it's up to the programmer to ensure that the index is valid
    - compiler will not check whether the index is out_of_range
  - use at() to ensure the index is invalid
    - if the index is invalid, throw an out_of_range exception


associative container acces
subscript a map
- map and unordered_map container provide the subscript operator and at function. other associate container not support subscript.
```
c[k]
c.at(k) // if k not exist, throw a out_of_range exception
```
- unlike other subscript operators, if the key is not exist, a new element is created and inserted into the map for the key.
- when we dereference a map iterator, we get a `value_type` object
  - `*map.begin()` ==> return value_type
- when we use `key_type` to subscript a map, we get a `mapped_type` object. and it's an `lvalue(left-value)`, we can read and write it.
  - map[key_type] ==> return mapped_type

using find instead of subscript for map
```
// count is better to deal with multimap or multi multiset
// find is better to deal with unique key associative container
c.find(k)  // return an iterator denoted the first element with key k.
c.count(k) // return the number of elements with key k

c.lower_bound(k) // return an iterator to the first element with key not less than k, that is the fist element with key k.
c.upper_bound(k) // return an iterator to the first element with key greater than k, that is the next position of last element with key k.
c.equal_range(k) // return a pair of iterators, denoted the range of elements with key k
```
using subscript has a important side-effect. insert an element if the key not exist.
e.g. check if an element is present, we should use find instead.
```
if (word_count.find("foobar") == word_count.end())
  cout << "foobar is not in the map" << endl;
```

finding elements in a multi-map or multi-set
method1
```
for (auto beg = authors.lower_bound(search_item)),
          end = authors.upper_bound(search_item);
          beg != end; ++beg)
    cout << beg->second << endl;
```
method2
```
for (auto pos = authors.equal_range(search_item))；
        pos.first != post.second; ++pos.first)
    cout << pos.first->second << end;
```

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

## 选择合适的容器

- list : anytime when a doubly-linked list is required.
- vector : anytime when a dynamic array is required.
- deque :
  - [Why do we need Deque data structures in the real world?](http://stackoverflow.com/questions/3880254/why-do-we-need-deque-data-structures-in-the-real-world)
- map : dictionary.
- set : when to keep elements sorted and unique.


关心查找速度，首先应该考虑散列容器（非标准STL容器,如：unordered_map,unordered_set)；其次是排序的vector，然后是标准的关联容器；
