---
layout: post
title:  "stl-algorithm笔记之transform"
date: 2014-09-23 20:03:18
excerpt: "stl algorithm transform notes"
categories: [stl]
tags: [stl]
---

###function template

```
#unary operation
template<class InputIterator, class OutputIterator, class UnaryOperation>
OutputIterator transform(InputIterator first1, InputIterator last1,
                         OutputIterator result, UnaryOperation op);
#binary operation
template<class InputIterator1, class InputIterator2, class OutputIterator, class BinaryOperation>
OutputIterator transform(InputIterator1 first1, InputIterator1 last1,
                         InputIterator2 first2, OutputIterator result,
                         BinaryOperation binary_op);
```


<!--more-->


###函数说明

用一个/两个区间去更新目标区间 

该函数两个原型   
1. 处理[first1, last1)的每个元素，逐个写进result，处理方式为op.   
2. 处理[first1, last1)， [first2, last2)的每个元素（注意last2没有显示给出，因为这两段区间长度一样），结果写进result，处理方式为binary\_op;  

###函数行为  

```
#unary operation
template<class InputIterator, class OutputIterator, class UnaryOperation>
OutputIterator transform(InputIterator first1, InputIterator last1,
                         OutputIterator result, UnaryOperation op)
{
    while (first1 != last1) {
        *result = op(*first1);
        ++result;++first1;
    }
}
#binary operation
template<class InputIterator1, class InputIterator2, class OutputIterator, class BinaryOperation>
OutputIterator transform(InputIterator1 first1, InputIterator1 last1,
                         InputIterator2 first2, OutputIterator result,
                         BinaryOperation binary_op)
{
    while (first1 != last1) {
        *result = op(*first1, *first2);
        ++result;++first1;++first2;
    }
}
```

###注意
1. 结果存放的result迭代器需要事先resize范围或者使用插入迭代器。  
    例如下面L30会core掉，因为迭代器无效。

###code

```
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

int op_inc(int i) { return ++i; }
int op_sum(int i, int j) { return i + j; }

void print(const vector<int>& v)
{
    for (vector<int>::const_iterator it = v.begin(); it != v.end(); ++it) {
        cout << " " << *it;
    }
    cout << endl;
}

int main()
{
    vector<int> first;
    vector<int> second;
    vector<int> third;
    vector<int>::iterator it;

    for (int i = 1; i < 5; ++i)
        first.push_back(i * 10);

    second.resize(first.size());

    transform(first.begin(), first.end(), second.begin(), op_inc);
    //transform(first.begin(), first.end(), third.begin(), op_inc);会引起core
    transform(first.begin(), first.end(), back_inserter(third), op_inc);
    cout << "firt container" << endl;
    print(first);
    cout << "second container" << endl;
    print(second);
    cout << "third container" << endl;
    print(third);
    transform(first.begin(), first.end(), second.begin(), first.begin(), op_sum);

    cout << "firt container" << endl;
    print(first);
    cout << "second container" << endl;
    print(second);

    return 0;
}
```

###Reference:
1. [transform](http://www.cplusplus.com/reference/algorithm/transform)


