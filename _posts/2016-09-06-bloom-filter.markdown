---
layout: post
title:  "Bloom Filter详解"
date:   2016-09-06 11:42:55 +0200
categories: big data
use_math: true
---

今天我们来讨论一个常用在大数据处理中的数据结构: 布隆过滤器 ([Bloom Filter])。布隆过滤器的主要用途是用来检索一个元素是不是在一个集合中。
这是一个让人又爱又恨的数据结构。因为它占用很少的空间, 并提供常数时间的查询; 但同时, 它也有一些不可避免的缺点, 比如它有一定的误判率(false positive rate),
同时, 原始版本的Bloom Filter无法对元素进行删除操作。

 这篇文章主要讨论以下内容:

*   Bloom Filter思想简介
*   Bloom Filter主要参数的计算
*   Bloom Filter的Java实现

Bloom Filter 简介
=================

在数据处理的过程中, 我们最常遇到的操作之一就是membership查询。 所谓的membership查询就是想判断一个元素, 是否存在于一个给定的集合里。 最简单的做法是将集合
数据存储在一个链表结构里, 然后每次查询的过程即为对链表的遍历过程。 当数据量变大时, 这种链表结构肯定是不够优化的, 因为每次查询的时间复杂度为 $O\(n\)$



[Bloom Filter]: https://en.wikipedia.org/wiki/Bloom_filter