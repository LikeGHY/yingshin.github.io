---
layout: post
title: chrome源码里的StringPiece
date: 2016-7-19 21:12:11
excerpt: ""
categories: [c/cpp]
tags: [chrome StringPiece]
---

记得最开始使用StringPiece的时候直接上手就用了，场景大概是这样子的：一个线程读取数据写到StringPiece，另外一个线程读取这些对象，结果可想而知。后来发现很多项目源码里都会有这个类的类似实现，于是仔细看了下这个类的设计目的、实现。

本文是对阅读过程中的一些笔记的整理。

<!--more-->

### 1. 为什么需要StringPiece

先看一个例子（摘自boost关于string_ref的介绍）

```
std::string extract_part ( const std::string &bar ) {
    return bar.substr ( 2, 3 );
    }

if ( extract_part ( "ABCDEFG" ).front() == 'C' ) { /* do something */ }
```

在上面代码执行过程中，首先"ABCDEFG"被转化成了一个临时string变量A，作为形参。  
按照传引用的方式进入函数`extract_part`，调用`std::string::substr`时产生一个临时string变量B作为返回值赋值到结果string变量C（C可能被RVO优化掉，结果值直接使用B所在的内存）,`front`产生一个char类型的临时变量。  
这也说明了在传递`string`时，我们经常遇到的问题：**不必要的拷贝**

在chrome的[StringPiece源码](https://cs.chromium.org/chromium/src/base/strings/string_piece.h?dr=CSs&q=string_piece.h&sq=package:chromium&l=1)里，说明了该类设计的主要目的：

> // You can use StringPiece as a function or method parameter.  A StringPiece
// parameter can receive a double-quoted string literal argument, a "const
// char*" argument, a string argument, or a StringPiece argument with no data
// copying.  Systematic use of StringPiece for arguments reduces data
// copies and strlen() calls.

1. 统一了参数格式，不需要为`const char*` `const std::string&`等分别实现功能相同的函数了，参数统一指定为`StringPiece`即可  
2. 节约了数据拷贝以及`strlen`的调用  

根据boost里[string_ref](http://www.boost.org/doc/libs/1_61_0/libs/utility/doc/html/string_ref.html)的介绍，同时适用于以下两种情况：  

1. 函数参数传入了`string`，而该函数内调用的另一个函数需要接收该string的一个substring  
2. 函数参数传入了`string`，而该函数需要return一个该string的substring  
boost里string_ref的实现参考了[这篇文章](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2012/n3442.html)，跟`StringPiece`的想法是完全一致的。

StringPiece的使用非常广泛，muduo也引用了[pcre的StringPiece的实现](https://github.com/vmg/pcre/blob/master/pcre_stringpiece.h.in)

在llvm的源码里也有类似的[实现](http://llvm.org/docs/doxygen/html/StringRef_8h_source.html)

### 2. 实现及使用

以chrome里的StringPiece为例

`StringPiece`实际上是`BasicStringPiece`的特化，相关定义如下：

```
template <typename STRING_TYPE> class BaseStringPiece;
typedef BasicStringPiece<std::string> StringPiece;
typedef BasicStringPiece<string16> StringPiece16;
```
其中`string16`封装的是`std::wstring wchar_t`

根据上面提到的需求，BasicStringPiece的构造函数有多个，可以接收`const char*`，`const std::string&`：

```
  BasicStringPiece() : ptr_(NULL), length_(0) {}
  BasicStringPiece(const value_type* str)
      : ptr_(str),
        length_((str == NULL) ? 0 : STRING_TYPE::traits_type::length(str)) {}
  BasicStringPiece(const STRING_TYPE& str)
      : ptr_(str.data()), length_(str.size()) {}
  BasicStringPiece(const value_type* offset, size_type len)
      : ptr_(offset), length_(len) {}
  BasicStringPiece(const typename STRING_TYPE::const_iterator& begin,
                    const typename STRING_TYPE::const_iterator& end)
      : ptr_((end > begin) ? &(*begin) : NULL),
        length_((end > begin) ? (size_type)(end - begin) : 0) {}
```

没有析构函数

同时成员变量只有两个:

```
  const value_type* ptr_;
  size_type     length_;
```

因此**BasicStringPiece没有字符串的控制权，不负责构造以及销毁。调用者需要保证在BasicStringPiece的生命周期里源buffer始终有效**

BasicStringPiece支持string的常见操作

```
const value_type* data() const { return ptr_; }
void remove_prefix(size_type n);
void remove_suffix(size_type n);
void trim_spaces();
int compare(const BasicStringPiece<STRING_TYPE>& x) const;
STRING_TYPE as_string() const;

bool starts_with(const BasicStringPiece& x);
bool ends_with(const BasicStringPiece& x);
```
注意想`remove_prefix`这种操作都是常数级别的，因为只是在操作`ptr_`和`length_`

```
  void remove_prefix(size_type n) {
    ptr_ += n;
    length_ -= n;
  }
```

行为上跟容器也很像，有自己的迭代器

```
  value_type operator[](size_type i) const { return ptr_[i]; }
  const_iterator begin() const { return ptr_; }
  const_iterator end() const { return ptr_ + length_; }
  const_reverse_iterator rbegin() const {
    return const_reverse_iterator(ptr_ + length_);
  }
  const_reverse_iterator rend() const {
    return const_reverse_iterator(ptr_);
  } 
```

同时支持find系列的需求

```
size_type find(const BasicStringPiece<STRING_TYPE>& s,
    size_type pos = 0) const;
size_type rfind(const BasicStringPiece& s,
    size_type pos = BasicStringPiece::npos);
size_type find_first_of(const BasicStringPiece& s,
    size_type pos = 0) const;

template <typename STRING_TYPE>
const typename BasicStringPiece<STRING_TYPE>::size_type
BasicStringPiece<STRING_TYPE>::npos =
    typename BasicStringPiece<STRING_TYPE>::size_type(-1);
```

实现大部分也是通过`std::min search find find_end find_first_of`来完成，其中像`find`实现里使用的是`std::search`，`find_first_of`的实现里稍微变动了下，在`const BasicStringPiece& s`大小为1的情况下，退化为`find`查找，>1的情况下，则建表查询，也就是以空间换时间的做法。

```
  bool lookup[UCHAR_MAX + 1] = { false };
  BuildLookupTable(s, lookup);
  for (size_t i = pos; i < self.size(); ++i) {
    if (lookup[static_cast<unsigned char>(self.data()[i])]) {
      return i;
    }
  }
```

BuildLookupTable则是遍历`s`建表

```
// For each character in characters_wanted, sets the index corresponding
// to the ASCII code of that character to 1 in table.  This is used by
// the find_.*_of methods below to tell whether or not a character is in
// the lookup table in constant time.
// The argument `table' must be an array that is large enough to hold all
// the possible values of an unsigned char.  Thus it should be be declared
// as follows:
//   bool table[UCHAR_MAX + 1]
inline void BuildLookupTable(const StringPiece& characters_wanted,
                             bool* table) {
  const size_t length = characters_wanted.length();
  const char* const data = characters_wanted.data();
  for (size_t i = 0; i < length; ++i) {
    table[static_cast<unsigned char>(data[i])] = true;
  }
}
```

stl的`find_first_of`的实现经常被简化为这样的伪代码：

```
template<class ForwardIterator1, class ForwardIterator2>
  ForwardIterator1 find_first_of ( ForwardIterator1 first1, ForwardIterator1 last1,
                                   ForwardIterator2 first2, ForwardIterator2 last2)
{
  for ( ; first1 != last1; ++first1 )
    for (ForwardIterator2 it=first2; it!=last2; ++it)
      if (*it==*first1)          // or: if (comp(*it,*first)) for the pred version
        return first1;
  return last1;
}
```

实际实现的情况我不大清楚，一直没有系统的看过stl的源码解析。

对比了下boost的string_ref实现，boost多了`std::distance find_if`的实现，但是没有chrome源码里的代码时间复杂度的优化。

以及计算hash值

```
std::size_t operator()(const base::StringPiece& sp) const;
```