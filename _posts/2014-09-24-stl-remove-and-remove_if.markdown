---
layout: post
title:  "stl-algorithm笔记之remove&remove_if"
date: 2014-09-24 19:28:18
excerpt: "stl algorithm remove&remove_if notes"
categories: [stl]
tags: [stl]
---

###function template

```
template <class ForwardIterator, class T>
ForwardIterator remove(ForwardIterator first, ForwardIterator last, const T& val)

template <class ForwardIterator, class UnaryPredicate>
ForwardIterator remove_if(ForwardIterator first, ForwardIterator last, UnaryPredicate p)
```


<!--more-->


###函数说明

用Effective STL的原话来讲：

> 它不接收一个容器，所以remove不知道它作用于哪个容器。此外，remove也不可能发现容器，因为没有办法从一个迭代器获取对应于它的容器。想想怎么从容器中除去一个元素。唯一的方法是调用那个容器的一个成员函数，几乎都是erase的某个形式，（list有几个除去元素的成员函数不叫erase，但它们仍然是成员函数。）因为唯一从容器中除去一个元素的方法是在那个容器上调用一个成员函数，而且因为remove无法知道它正在操作的容器，所以remove不可能从一个容器中除去元素。这解释了另一个令人沮丧的观点——从一个容器中remove元素不会改变容器中元素的个数。

> remove并不“真的”删除东西，因为它做不到。

> 非常简要地说一下，remove移动指定区间中的元素直到所有“不删除的”元素在区间的开头（相对位置和原来它们的一样）。它返回一个指向最后一个的下一个“不删除的”元素的迭代器。返回值是区间的“新逻辑终点”。

remove\_if与remove类似，只不过第三个参数变为一元操作符。

###函数行为  

```
template <class ForwardIterator, class T>
ForwardIterator remove(ForwardIterator first, ForwardIterator last, const T& val)
{
    ForwardIterator result = first;
    while (first != last) {
        if (!(*first == var)) {
            *result = *first;
            ++result;
        }
        ++first;
    }
}

template <class ForwardIterator, class UnaryPredicate>
ForwardIterator remove_if(ForwardIterator first, ForwardIterator last, UnaryPredicate p)
{
    ForwardIterator result = first;
    while (first != last) {
        if (!(p(*first))) {
            *result = *first;
            ++result;
        }
        ++first;
    }
}
```

###注意
1. remove不删除元素，只是保留了不删除的元素，如果真的删除，需要调用容器的成员函数。
2. remove要删除的区间内并不一定全是要删除的元素，只是保证了保留的区间内是所有不被删除的元素。删除区间内容没有规定，不过大部分stl与上述伪代码一致。

###code

```
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;

bool predicate(char c)
{
    return std::isspace(c);
}

int main()
{
    int ints[] = {1, 3, 3, 2, 4, 4, 3, 5, 6};
    vector<int> vec(ints, ints + 9);
    vector<int> vec2(ints, ints + 9);

    remove(vec.begin(), vec.end(), 3);//并没有真的删除，只是移动了元素。
    for (vector<int>::iterator iter = vec.begin(); iter != vec.end(); ++iter) {
        cout << " " << *iter;
    }
    cout << endl;

    vec2.erase(remove(vec2.begin(), vec2.end(), 3), vec2.end());
    for (vector<int>::iterator iter = vec2.begin(); iter != vec2.end(); ++iter) {
        cout << " " << *iter;
    }
    cout << endl;

    std::string str1 = "Text with some   spaces";
    str1.erase(std::remove(str1.begin(), str1.end(), ' '),
               str1.end());
    std::cout << str1 << '\n';
 
    std::string str2 = "Text\n with\tsome \t  whitespaces\n\n";
    str2.erase(std::remove_if(str2.begin(),
                              str2.end(),
                              predicate),
               str2.end());
    std::cout << str2 << '\n';
    return 0;
}
//1 2 4 4 5 6 3 5 6
//1 2 4 4 5 6
//Textwithsomespaces
//Textwithsomewhitespaces
```

###Reference:
1. [CPlusPlus Remove](http://www.cplusplus.com/reference/algorithm/remove)
2. [CPPReference Remove](http://en.cppreference.com/w/cpp/algorithm/remove)
3. [Effective stl](http://www.amazon.cn/%E5%9B%BE%E4%B9%A6/dp/B00CSWIJUQ/ref=pd_sim_b_5?ie=UTF8&refRID=18Z6X8EBY6YTNK0Q3EN9)

