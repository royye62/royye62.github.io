
- iterator
  - insert iterator
  - stream iterator
  - reverse iterator
  - move iterator

### iterator
  - left-inclusive interval [begin, end)
  - features
    - end point to the next position of the last element;
    - begin always before end, and keep begin++ always can reach end;
  - meanings:
    - begin == end ==> empty;
    - begin != end ==> at last 1 elements, begin point to the first element; (convenient for using as loop conditions !!!)

### iterator common operation
```
*iter
iter->member  <==> (*iter).member
++iter
--iter // not support forward_list
iter1 == iter2
iter1 != iter2
```


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
