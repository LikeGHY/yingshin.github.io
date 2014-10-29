---
layout: post
title:  "stl-algorithm笔记之upper_bound"
date: 2014-10-29 15:39:21
excerpt: "stl algorithm upper_bound notes"
categories: [stl]
tags: [stl]
---

upper\_bound函数返回第一个&gt;给定值的迭代器位置，可以与[lower_bound](http://yingshin.github.io/stl/2014/10/28/stl-lowerbound/)的实现伪代码、应用对照着来学习，在之前的[笔记](http://yingshin.github.io/algorithm/2014/04/18/binary-search-analysis/)里对实现曾做过详细的分析。

###function template

```
template<class ForwardIt, class T>
ForwardIt upper_bound(ForwardIt first, ForwardIt last, const T& value);
template<class ForwardIt, class T, class Compare>
ForwardIt upper_bound(ForwardIt first, ForwardIt last, const T& value, Compare comp);
```


<!--more-->


###函数说明
返回第一个&gt;给定值的迭代器位置，比较函数为comp，第一个版本则为operator\<;

###函数行为
可以看到跟上面链接里的笔记思路一致。不过这里用了迭代器的一些性质。


```
template<class ForwardIt, class T>
ForwardIt lower_bound(ForwardIt first, ForwardIt last, const T& value)
{
    ForwardIt it;
    typename std::iterator_traits<ForwardIt>::difference_type count, step;
    count = std::distance(first, last);

    while (count > 0) {
        it = first;
        step = count / 2;
        std::advance(it, step);
        if (!(value < *it)) {//分两种情况,> <=来更新区间。
            first = ++it;
            count -= step + 1;
        }
        else {
            count = step;
        }
    }
}

template<class ForwardIt, class T, class Compare>
ForwardIt lower_bound(ForwardIt first, ForwardIt last, const T& value, Compare comp)
{
    ForwardIt it;
    typename std::iterator_traits<ForwardIt>::difference_type count, step;
    count = std::distance(first, last);

    while (count > 0) {
        it = first;
        step = count / 2;
        std::advance(it, step);
        if (!comp(value, *it)) {
            first = ++it;
            count -= step + 1;
        }
        else {
            count = step;
        }
    }
}
```

###注意  
需要区间非递减，以及跟lower_bound的不同。   
根据前面链接里的分析，应当使用`a[mid] > key; a[mid] <= key;`来判断区间，不过这里只有operator<，因此反过来用`key < a[mid]`


###code
跟之前lower\_bound的相同

```
int main()
{
    int array[] = {1, 1, 2, 3, 3, 3, 3, 4, 4, 4, 5, 5, 6};
    std::vector<int> data(array, array + 13);
    std::vector<int>::iterator lower = std::lower_bound(data.begin(), data.end(), 4);
    std::vector<int>::iterator upper = std::upper_bound(data.begin(), data.end(), 4);

    std::copy(lower, upper, std::ostream_iterator<int>(std::cout, " "));
    std::cout << std::endl << std::distance(data.begin(), lower) << std::endl;//7,即第一个4.
    std::cout << std::distance(data.begin(), upper) << std::endl;//10，即第一个5.

    return 0;
}
```

###Reference:
1. [CPPReference upper_bound](http://en.cppreference.com/w/cpp/algorithm/upper_bound)

