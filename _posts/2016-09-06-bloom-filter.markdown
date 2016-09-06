---
layout: post
title:  "Bloom Filter详解"
date:   2016-09-06 11:42:55 +0200
categories: big data
use_math: true
---

今天我们来讨论一个常用在大数据处理中的数据结构: 布隆过滤器 ([Bloom Filter])。布隆过滤器的主要用途是用来检索一个元素是不是在一个集合中。
这是一个让人又爱又恨的数据结构。因为它占用很少的空间, 并提供常数时间的查询; 但同时, 它也有一些不可避免的缺点, 比如它有一定的误判率\\(false positive rate\\),
同时, 原始版本的Bloom Filter无法对元素进行删除操作。

 这篇文章主要讨论以下内容:

*   Bloom Filter的基本概念
*   Bloom Filter主要参数的计算
*   Bloom Filter的Java实现

Bloom Filter 简介
=================

在数据处理的过程中, 我们最常遇到的操作之一就是**membership查询**。 所谓的membership查询就是想判断一个元素, 是否存在于一个给定的集合里。 
最简单的做法是将集合数据存储在一个链表结构里, 然后每次查询的过程即为对链表的遍历过程。 当数据量变大时, 这种链表结构肯定是不够优化的, 因为每
次查询的时间复杂度为$O\(n\)$。 面试的时候经常有follow up问题, 让提高查询效率, 这个时候往往树状结构就要登场了, 因为它们可以使查询的效率变
为$O\(logn\)$。 当然树状结构还是可以被优化的, 就是用我们伟大的哈希方法, 比如Chain Hashing 和Bit Strings等, 或者就是简单的哈希表, 从而
得到 $O\(\frac{n}{k}\)$的时间复杂度。 这里提句题外话, 面试的时候是可以根据题目的复杂度要求大概猜出需要用到的方法的, 虽然可能不是100%正确
, 但几乎可以猜个八九不离十。

这个时候, 如果面试官继续追问, 有没有时间和空间复杂度都更优的方法呢? 那么, 你就可以放心大胆的跟他说: yes, 有的, 它就是传说中的Bloom Filter。
Bloom Filter的思想非常简单。 一个Bloom Filter的物理结构其实是一个bit vector。 这个bit vector的每一位初始的时候都被设为0。 同时, 每个Bloom Filter
都伴随着k个hash functions。 往Bloom Filter插入元素的过程就是用每个hash function计算这个元素, 从而将结果所对应个比特位改为1。 如果当前比特位已经为1,
则在插入的过程保持这一位不变。 







[Bloom Filter]: https://en.wikipedia.org/wiki/Bloom_filter