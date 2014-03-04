---
layout: post
title:  "几种排序算法的实际比较"
date:   2014-03-01 12:21
excerpt: "Sort algorithm compare: Bubble Sort | SelectSort | InsertSort | MergeSort | QuickSort"
categories: [algorithm]
tags: [sort]
---

最近复习了一下排序算法，实际测试了一下算法的执行时间。分别是冒泡，选择，插入，归并，快速排序。数据项用python的几行语句生成。
生成数据可以指定个数，数据会存在data.txt文件里。
每种算法执行100次，求出平均执行时间比较。

100000个数字的运行结果:


- BubbleSort: 51.8052
- SelectSort: 15.7363
- InsertSort: 8.21629
- MergeSort:  0.0243346
- QuickSort:  0.0146692

由于算法都非常基础，直接贴下程序了。


<!--more-->
```
/*
 * =====================================================================================
 *       Filename:  sort.cpp
 *    Description:  analysis of sort
 *
 *        Version:  1.0
 *        Created:  02/26/2014 03:44:40 PM
 *
 *         Author:  zhy (), zhy198606@gmail.com
 * =====================================================================================
 */
#include <stdio.h>
#include <string.h>
#include <sys/time.h>
#include <algorithm>
#include <iostream>
#include <vector>
using namespace std;

int len;
int arrayInitilized[10000000];
int array[10000000];
int auxiliaryArray[10000000];
double timeUsed[5];
const char* fileName = "data.txt";
const int sortTimes = 10;

typedef void (*SortFunction)();

class SortUtil
{
public:
    static void BubbleSort();
    static void SelectSort();
    static void InsertSort();
    static void MergeSort();
    static void QuickSort();
    
    static bool InitArrayFromFile();
    static void InitArrayFromMemory();
    static void Dump();

private:

    static void MergeSort(int left, int right);
    static void Merge(int left, int mid, int right);

    static void QuickSort(int left, int right);
    static int Partition(int left, int right);
};//SortUtil

bool SortUtil::InitArrayFromFile()
{
    FILE* fp = fopen(fileName, "r");
    if (!fp)
    {
        return false;
    }

    fscanf(fp, "%d", &len);
    for (int i = 0; i < len; ++i)
        fscanf(fp, "%d", arrayInitilized + i);
    fclose(fp);

    InitArrayFromMemory();
}

void SortUtil::InitArrayFromMemory()
{
    memcpy(array, arrayInitilized, len * sizeof(int));
}

void SortUtil::BubbleSort()
{
    for (int i = 0; i < len; ++i)
    {
        for (int j = len - 1; j > i; --j)
        {
            if (array[j] < array[j - 1])
                swap(array[j], array[j - 1]);
        }
    }
}

void SortUtil::SelectSort()
{
    for (int i = 0; i < len - 1; ++i)
    {
        int min = i;
        for (int j = i + 1; j < len; ++j)
            if (array[j] < array[min])
                min = j;
        swap(array[i], array[min]);
    }
}

void SortUtil::InsertSort()
{
    int i = 1;
    while (i < len)
    {
        int key = array[i];
        int j = i;
        while (j > 0 && array[j - 1] > key)
        {
            //最开始写成了array[j] = array[--j],导致一直出错
            array[j] = array[j - 1];
            --j;
        }
        array[j] = key;
        ++i;
    }
}

void SortUtil::MergeSort()
{
    MergeSort(0, len - 1);
}

void SortUtil::MergeSort(int left, int right)
{
    if (left >= right)
        return;

    int mid = left + ((right - left) >> 1);
    MergeSort(left, mid);
    MergeSort(mid + 1, right);
    Merge(left , mid, right);
}

void SortUtil::Merge(int left, int mid, int right)
{
    int leftStart = left;
    int leftEnd = mid;
    int rightStart = mid + 1;
    int rightEnd = right;
    int i = left;
    while (1)
    {
        while (leftStart <= leftEnd && array[leftStart] <= array[rightStart])
            auxiliaryArray[i++] = array[leftStart++];
        if (leftStart > leftEnd)
            break;

        while (rightStart <= rightEnd && array[leftStart] > array[rightStart])
            auxiliaryArray[i++] = array[rightStart++];

        if (rightStart > rightEnd)
            break;
    }

    while (leftStart <= leftEnd)
        auxiliaryArray[i++] = array[leftStart++];

    while (rightStart <= rightEnd)
        auxiliaryArray[i++] = array[rightStart++];

    for (int i = left; i <= right; ++i)
        array[i] = auxiliaryArray[i];
}

void SortUtil::QuickSort()
{
    QuickSort(0, len - 1);
}

void SortUtil::QuickSort(int left, int right)
{
    if (left >= right)
        return;
    int index = Partition(left, right);
    QuickSort(left, index - 1);
    QuickSort(index + 1, right);
}

int SortUtil::Partition(int left, int right)
{
    int pivot = array[right];
    while (left < right)
    {
        while (left < right && array[left] <= pivot)
            left++;
        if (left < right)
            array[right--] = array[left];

        while (left < right && array[right] >= pivot)
            right--;
        if (left < right)
            array[left++] = array[right];
    }

    array[left] = pivot;
    return left;
}

void SortUtil::Dump()
{
    for (unsigned int i = 0; i < len; ++i)
    {
        cout << array[i] << " ";
    }
    cout << endl;
}

int main(int argc, char* argv[])
{
    SortUtil::InitArrayFromFile();

    SortFunction funcs[5] = {&SortUtil::BubbleSort,
                             &SortUtil::SelectSort,
                             &SortUtil::InsertSort,
                             &SortUtil::MergeSort,
                             &SortUtil::QuickSort,
    };

    const char* sortName[5] = {"BubbleSort", "SelectSort", "InsertSort", "MergeSort", "QuickSort"};
    struct timeval startTime,endTime;
    for (int i = 0; i < 5; ++i)
    {
        for (int j = 0; j < sortTimes; ++j)
        {
            SortUtil::InitArrayFromMemory();
            gettimeofday(&startTime, NULL);
            funcs[i]();
            gettimeofday(&endTime, NULL);
            double tu = (endTime.tv_sec - startTime.tv_sec) + (endTime.tv_usec - startTime.tv_usec) / 1000000.0;
            timeUsed[i] += tu;
        }
    }

    for (int i = 0; i < 5; ++i)
        cout << sortName[i] << ":\t" << timeUsed[i] / sortTimes << endl;

    return 0;
}
```
```
数据的生成采用python语句
#!/usr/bin/python
import os, random, sys

def run(num):
    fp = open('data.txt', 'w')
    fp.write('%d%s' % (num, os.linesep))
    for i in range(num):
        fp.write('%d%s' % (random.randint(-10000000, 10000000), os.linesep));
    fp.close()

if __name__ == '__main__':
    if (len(sys.argv) == 2):
        run(int(sys.argv[1]))
```
