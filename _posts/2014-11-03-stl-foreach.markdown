---
layout: post
title:  "stl-algorithm笔记之for_each"
date: 2014-11-03 21:45:21
excerpt: "stl algorithm for_each notes"
categories: [stl]
tags: [stl]
---

for\_each对区间内的每个元素应用f()

###function template

```
template<class InputIt, class UnaryFunction>
UnaryFunction for_each(InputIt first, InputIt last, UnaryFunction f)
```


<!--more-->


###函数说明
对[first, last)区间内的每个值作用函数f。如果迭代器是可修改的，f有可能改变指向的值。如果f有返回值，则忽略。函数返回f。

###函数行为


```
template<class InputIt, class UnaryFunction>
UnaryFunction for_each(InputIt first, InputIt last, UnaryFunction f)
{
    for (; first != last; ++first) {
        f(*first);
    }
    return f;
}
```

###注意

第三个参数可以传入函数，也可以传入实现了`operator()`的类

###code

```
struct Sum {
    Sum() {sum = 0;}
    void operator()(int n)
    {
        sum += n;
    }

    int sum;
};

void f(int &n)
{
    ++n;
}
int main()
{
    vector<int> v;
    for (int i = 1; i <= 10; ++i) {
        v.push_back(i);
    }

    void (*pf1)(int &);
    void (*pf2)(int &) = &f;
    pf1 = for_each(v.begin(), v.end(), f);
    printf("%x, %x\n", pf1, pf2);
    for (int i = 0; i < 10; ++i) {
        cout << v[i] << " ";
    }
    cout << endl;

    Sum s;
    // for_each(v.begin(), v.end(), s);//no use, becase the s in f in pass by value.
    s = for_each(v.begin(), v.end(), Sum());
    for (int i = 0; i < 10; ++i) {
        cout << v[i] << " ";
    }
    cout << endl;
    cout << s.sum << endl;

    return 0;
}
```

###Reference:
1. [CPPReference for_each](http://en.cppreference.com/w/cpp/algorithm/for_each)

