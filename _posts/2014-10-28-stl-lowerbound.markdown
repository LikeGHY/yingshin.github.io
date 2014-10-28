---
layout: post
title:  "stl-algorithm笔记之lower_bound"
date: 2014-10-28 10:20:08
excerpt: "stl algorithm lower_bound notes"
categories: [stl]
tags: [stl]
---

lower\_bound函数返回第一个&ge;给定值的迭代器位置，需要给定的区间非递减，逻辑非常的简洁高效，属于典型的二分法的应用，在之前的[笔记](http://yingshin.github.io/algorithm/2014/04/18/binary-search-analysis/)里曾做过详细的分析。

###function template

```
template<class ForwardIt, class T>
ForwardIt lower_bound(ForwardIt first, ForwardIt last, const T& value);
template<class ForwardIt, class T, class Compare>
ForwardIt lower_bound(ForwardIt first, ForwardIt last, const T& value, Compare comp);
```


<!--more-->


###函数说明
返回第一个&ge;给定值的迭代器位置，比较函数为comp，第一个版本则为operator\<;

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
        if (*it < value) {//分两种情况,< >=来更新区间。
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
        if (comp(*it, value)) {
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
需要区间非递减，以及跟upper_bound的不同。

###code

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
1. [CPPReference lower_bound](http://en.cppreference.com/w/cpp/algorithm/lower_bound)

