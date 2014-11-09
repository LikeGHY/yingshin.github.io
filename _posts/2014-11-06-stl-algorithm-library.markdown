---
layout: post
title:  "stl-algorithm笔记"
date: 2014-11-06 15:52:38
excerpt: "stl algorithm notes"
categories: [stl]
tags: [stl]
---

STL Algorithm的整体总结

<!--more-->

<table class="table table-hover table-striped">
<thead><h4>Non-modifying operations</h4></thead>
<tr><td>all_of<br>none_of<br>any_of</td><td></td></tr>
<tr>
<td><a href=http://yingshin.github.io/stl/2014/11/03/stl-foreach/>for_each</a></td>
<td>在一个区间上应用指定的函数</td>
</tr>
<tr><td>count<br>count_if</td><td>返回满足给定条件的元素个数</td></tr>
<tr><td>mismatch</td><td></td></tr>
<tr><td>equal</td><td></td></tr>
<tr>
<td><a href=http://yingshin.github.io/stl/2014/09/25/stl-find/>find<br>find_if<br>find_if_not</a></td>
<td>返回第一个匹配的迭代器位置</td>
</tr>
<tr><td>find_end</td><td></td></tr>
<tr><td>find_first_of</td><td></td></tr>
<tr><td>adjacent_find</td><td></td></tr>
<tr><td>search</td><td></td></tr>
<tr><td>search_n</td><td></td></tr>
</table>
<table class="table table-hover table-striped">
<thead><h4>Modifying sequence operations</h4></thead>
<tr><td>copy<br>copy_if</td><td></td></tr>
<tr><td>copy_n</td><td></td></tr>
<tr><td>copy_backward</td><td></td></tr>
<tr><td>move</td><td></td></tr>
<tr><td>move_backward</td><td></td></tr>
<tr><td>fill</td><td></td></tr>
<tr><td>fill_n</td><td></td></tr>
<tr>
<td><a href=http://yingshin.github.io/stl/2014/09/23/stl-transform/>transform</a></td>
<td>用一个/两个原始区间更新目标区间</td>
</tr>
<tr><td>generate</td><td></td></tr>
<tr><td>generate_n</td><td></td></tr>
<tr>
<td><a href=http://yingshin.github.io/stl/2014/09/24/stl-remove-and-remove_if/>remove<br>remove_if</a></td>
<td>移动所有不满足删除条件的元素至区间开头，返回最后一个不删除元素的下一个位置</td>
</tr>
<tr><td>remove_copy<br>remove_copy_if</td><td></td></tr>
<tr><td>replace<br>replace_if</td><td></td></tr>
<tr><td>replace_copy<br>replace_copy_if</td><td></td></tr>
<tr><td>swap</td><td></td></tr>
<tr><td>swap_ranges</td><td></td></tr>
<tr><td>iter_swap</td><td></td></tr>
<tr><td>reverse</td><td></td></tr>
<tr><td>reverse_copy</td><td></td></tr>
<tr><td>rotate</td><td></td></tr>
<tr><td>rotate_copy</td><td></td></tr>
<tr><td>random_shuffle<br>shuffle</td><td></td></tr>
<tr><td>unique</td><td></td></tr>
<tr><td>unique_copy</td><td></td></tr>
</table>
<table class="table table-hover table-striped">
<thead><h4>Partitioning operations</h4></thead>
<tr><td>is_partitioned</td><td></td></tr>
<tr><td>partition</td><td></td></tr>
<tr><td>partition_copy</td><td></td></tr>
<tr><td>stable_partition</td><td></td></tr>
<tr><td>partition_point</td><td></td></tr>
</table>
<table class="table table-hover table-striped">
<thead><h4>Sorting operations</h4></thead>
<tr><td>is_sorted</td><td></td></tr>
<tr><td>is_sorted_until</td><td></td></tr>
<tr><td>sort</td><td></td></tr>
<tr><td>partial_sort</td><td></td></tr>
<tr><td>partial_sort_copy</td><td></td></tr>
<tr><td>stable_sort</td><td></td></tr>
<tr><td>nth_element</td><td></td></tr>
</table>
<table class="table table-hover table-striped">
<thead><h4>Binary sort operations(on sorted ranges)</h4></thead>
<tr>
<td><a href=http://yingshin.github.io/stl/2014/10/28/stl-lowerbound/>lower_bound</a></td>
<td>返回刚好大于等于给定值的迭代器位置</td>
</tr>
<tr>
<td><a href=http://yingshin.github.io/stl/2014/10/29/stl-upperbound/>upper_bound</a></td>
<td>返回刚好大于给定值的迭代器位置</td>
</tr>
<tr><td>binary_search</td><td></td></tr>
<tr><td>equal_range</td><td></td></tr>
</table>
<table class="table table-hover table-striped">
<thead><h4>Set operations(on sorted ranges)</h4></thead>
<tr><td>merge</td><td></td></tr>
<tr><td>inplace_merge</td><td></td></tr>
<tr><td>includes</td><td></td></tr>
<tr><td>set_difference</td><td></td></tr>
<tr><td>set_intersection</td><td></td></tr>
<tr><td>set_symmetric_difference</td><td></td></tr>
<tr><td>set_union</td><td></td></tr>
</table>
<table class="table table-hover table-striped">
<thead><h4>Heap operations</h4></thead>
<tr><td>is_heap</td><td></td></tr>
<tr><td>is_heap_until</td><td></td></tr>
<tr><td>make_heap</td><td></td></tr>
<tr><td>push_heap</td><td></td></tr>
<tr><td>pop_heap</td><td></td></tr>
<tr><td>sort_heap</td><td></td></tr>
</table>
<table class="table table-hover table-striped">
<thead><h4>Minimum/Maximum operations</h4></thead>
<tr><td>max</td><td></td></tr>
<tr><td>max_element</td><td></td></tr>
<tr><td>min</td><td></td></tr>
<tr><td>min_element</td><td></td></tr>
<tr><td>minmax</td><td></td></tr>
<tr><td>minmax_element</td><td></td></tr>
<tr><td>lexicographical_compare</td><td></td></tr>
<tr><td>is_permutation</td><td></td></tr>
<tr>
<td><a href=http://yingshin.github.io/stl/2014/10/22/stl-permutation/>next_permutation</a></td>
<td>求全序列排列下的下个排列</td>
</tr>
<tr>
<td><a href=http://yingshin.github.io/stl/2014/10/22/stl-permutation/>prev_permutation</a></td>
<td>求全序列排列下的上个排列</td>
</tr>
</table>
<table class="table table-hover table-striped">
<thead><h4>Numeric operations</h4></thead>
<tr><td>iota</td><td></td></tr>
<tr>
<td><a hread=http://yingshin.github.io/stl/2014/10/27/stl-accumulate/>accumulate</a></td>
<td>对整个区间进行求和的操作，求和函数可以指定</td>
</tr>
<tr><td>inner_product</td><td></td></tr>
<tr><td>adjacent_difference</td><td></td></tr>
<tr><td>partial_sum</td><td></td></tr>
</table>
<table class="table table-hover table-striped">
<thead><h4>C library</h4></thead>
<tr><td>qsort</td><td></td></tr>
<tr><td>bsearch</td><td></td></tr>
</table>
