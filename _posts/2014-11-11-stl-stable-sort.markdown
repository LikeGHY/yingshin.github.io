---
layout: post
title:  "stl-algorithm笔记之sort"
date: 2014-11-09 21:27:16 
excerpt: "stl algorithm sort notes"
categories: [stl]
tags: [stl]
---

stable_sort用于对给定的区间排序，默认排序方式是递减，比较函数是operator<。与[sort](http://yingshin.github.io/stl/2014/11/09/stl-sort/)不同的是，该函数保证稳定性。 

###function template

```
template<class RandomIt>
void stable_sort(RandomIt first, RandomIt last);
template<class RandomIt, class Compare>
void stable_sort(RandomIt first, RandomIt last, Compare comp);

```


<!--more-->


###函数说明
对[first, last)区间稳定排序。


###code

```
struct Employee {
    Employee(std::string name, int age)
        : name(name)
        , age(age)
    {}
    std::string name;
    int age;
};

bool operator<(const Employee& lhs, const Employee& rhs) {
    return lhs.age < rhs.age;
}

int main()
{
    std::vector<Employee> vec1, vec2;

    for (unsigned int i = 0; i < 17; ++i) {
        vec1.push_back(Employee(std::string(1, 'A' + i), i % 2));
        vec2.push_back(Employee(std::string(1, 'A' + i), i % 2));
    }

    //sort, stable_sort输出都是有序，但是不同
    std::stable_sort(vec1.begin(), vec1.end());
    for (unsigned int i = 0; i < vec1.size(); ++i) {
        std::cout << vec1[i].name << " ";
    }
    std::cout << std::endl;

    std::sort(vec2.begin(), vec2.end());
    for (unsigned int i = 0; i < vec2.size(); ++i) {
        std::cout << vec2[i].name << " ";
    }
    std::cout << std::endl;

    return 0;
}
```

###Reference:
1. [CPPReference stable_sort](http://en.cppreference.com/w/cpp/algorithm/stable_sort)

