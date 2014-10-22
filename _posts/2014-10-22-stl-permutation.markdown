---
layout: post
title:  "stl-algorithm笔记之permutation"
date: 2014-10-22 19:35:28
excerpt: "stl algorithm permutation notes"
categories: [stl]
tags: [stl]
---

全排列相关的算法有两个，一个是字典序递增next\_permutation，一个是字典序递减prev\_permutation。  
主要与std::sort函数合作生成去全排列。  


###function template

```
template<class BidirIt>
bool next_permutation(BidirIt first, BidirIt last);
template<class BidirIt>
bool next_permutation(BidirIt first, BidirIt last, Compare comp)

template<class BidirIt>
bool prev_permutation(BidirIt first, BidirIt last);
template<class BidirIt>
bool prev_permutation(BidirIt first, BidirIt last, Compare comp)
```


<!--more-->


###函数说明
1. next\_permutation返回字典序的下一个排列，比较方式为&lt;或者comp。如果排列存在返回True,否则False.
2. prev\_permutation返回字典序的上一个排列，比较方式为&lt;或者comp。如果排列存在返回True,否则False.

###函数行为

next\_permutation函数的实现是一个全排列的实现算法，概括的说就是从字典的最小排列开始，逐个输出次小序列，直到序列的字典最大值。  
1. 从后往前找到第一个递减对，即找到符合a[i] &lt; a[i + 1]的位置i    
2. 从后往前找到第一个大于a[i]的位置i2  
3. 交换a[i] a[i + 2]的值  
4. 反转[i+1, i+2, ..., last)  

这样保证了尽可能长的共同前缀，同时使后面的序列最小，因此处理后的序列严格刚好大于最初序列。

pre\_permutation函数的实现类似，只是1.改为递增对2.改为第一个小于，其余步骤相同。

```
template<class BidirIt>
bool next_permtation(BidirIt first, BidirIt last)
{
    if (first == last) return false;//没有元素
    BidirIt i = last;
    if (first = --i) return false;//只有一个元素

    while (1) {
        BidirIt i1, i2;

        i1 = i;
        if (*--i < *i1) {//从后往前找到第一个递减的对,记为i
            i2 = last;
            while (!(*i < *--i2))//从后往前找到第一个大于a[i]的值a[i2]
                ;
            std::iter_swap(i, i2);//交换a[i] a[i2]
            std::reverse(i1, last);//reverse [i + 1, last)
            return true;
        }

        if (i == first) {
            std::reverse(first, last);
            return false;
        }
    }
}

template<class BidirIt>
bool prev_permutation(BidirIt first, BidirIt last)
{
    if (first == last) return false;
    BidirIt i = last;
    if (first == --i) return false;

    while (1) {
        BidirIt i1, i2;

        i1 = i;
        if (*i1 < *--i) {
            i2 = last;
            while (!(*--i2 < *i))
                ;
            std::iter_swap(i, i2);
            std::reverse(i1, last);
            return true;
        }

        if (i == first) {
            std::reverse(first, last);
            return false;
        }
    }
}
```

###注意  
1. next\_permutation最好用do{} while;的形式，从pseudo可以看出参数是最大序列时返回false。  
2. prev\_permutation最好用do{} while;的形式，从pseudo可以看出参数是最小序列时返回false。  
3. 要求的条件都是严格的，比如next\_permutation L58要求是严格小于的递减对, L60如果是&ge;都会跳过，直到\*i&lt;\*i2

###code

```
int main()
{
    std::string s = "abac";
    std::sort(s.begin(), s.end());
    do {
        std::cout << s << '\n';
    } while (std::next_permutation(s.begin(), s.end()));

    s = "54321";
    std::cout << std::next_permutation(s.begin(), s.end()) << '\n';

    s = "abac";
    std::sort(s.begin(), s.end(), std::greater<char>());
    do {
        std::cout << s << '\n';
    } while (std::prev_permutation(s.begin(), s.end()));

    s = "12345";
    std::cout << std::prev_permutation(s.begin(), s.end()) << '\n';

    return 0;
}
```

###Reference:
1. [CPPReference next_permutation](http://en.cppreference.com/w/cpp/algorithm/next_permutation)
2. [CPPReference prev_permutation](http://en.cppreference.com/w/cpp/algorithm/prev_permutation)

