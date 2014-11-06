---
layout: post
title:  "stl-algorithm笔记之accumulate"
date: 2014-10-27 13:45:18
excerpt: "stl algorithm accumulate notes"
categories: [stl]
tags: [stl]
---

accumulate主要用于整个序列的迭代统一操作，比如求序列和等，返回的也是整体之后的操作值。

###function template

```
template <class InputIt, class T>
T accumulate(InputIt first, InputIt last, T init)
template <class InputIt, class T, class BinaryOperation>
T accumulate(InputIt first, InputIt last, T init,
             BinaryOperation op)
```


<!--more-->


###函数说明
1. 三参数返回序列和，初始值是第三个参数init
2. 四参数版本返回op操作后的序列和，可以理解为三参数版本为`op = operation+`的特化版。

###函数行为
```
template <class InputIt, class T>
T accumulate(InputIt first, InputIt last, T init)
{
    for(; first != last; ++first) {
        init = init + *first;
    }
    return init;
}

template <class InputIt, class T, class BinaryOperation>
T accumulate(InputIt first, InputIt last, T init,
             BinaryOperation op)
{
    for (; first != last; ++first) {
        init += op(init, *first)
    }
}
```

###注意  
该函数位于\<numberic\>中   
第二个版本里op的定义如下：  
Ret op(const Type1& a, const Type2& b);  
1. const &不是必须的  
2. Type1需要能由T隐式转化成    
3. Type2需要能有InputIt的解引用隐式转化成   
4. Ret需要能由T赋值  

###code

```
#include <iostream>
#include <numeric>
#include <string>
#include <vector>

//examples for:
//1.sum 2.multiply 3.magic\_operator 4.string len

int multiply(int x, int y)
{
    return x*y;
}

std::string magic_function(std::string res, int x)
{
    return res += (x & 1) ? "b" : "s";
}

std::string::size_type string_len(std::string::size_type len, std::string str)
{
    return len + str.size();
}

int main()
{
    std::vector<int> v;
    for (int i = 1; i <= 10; ++i)
        v.push_back(i);

    int sum = std::accumulate(v.begin(), v.end(), 0);
    int product = std::accumulate(v.begin(), v.end(), 1, multiply);
    std::string magic = std::accumulate(v.begin(), v.end(), std::string(), magic_function);

    std::cout << sum << '\n'
              << product << '\n'
              << magic << '\n';

    std::vector<std::string> strv;
    strv.push_back("hello");
    strv.push_back("world");
    strv.push_back("stl");
    strv.push_back("numeric");
    strv.push_back("accumulate");

    //5 + 5 + 3 + 7 + 10 = 30
    std::cout << std::accumulate(strv.begin(), strv.end(), 0, string_len) << '\n';

    return 0;
}
```

###Reference:
1. [CPPReference accumulate](http://en.cppreference.com/w/cpp/algorithm/accumulate)

