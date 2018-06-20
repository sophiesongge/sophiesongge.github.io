---
layout: post
title:  "Bloom Filter详解"
date:   2016-09-06 11:42:55 +0200
categories: big data
use_math: true
---

在我们设计一个算法的时候，经常要考虑时间效率和空间效率之间的平衡。以时间换空间，或者以空间换时间在大多数情况下都是不可避免的操作。如果我告诉你，有那么个数据结构，它在时间效率和空间效率上都很优秀，意外不意外，惊喜不惊喜？！

今天我们来讨论一个常用在大数据处理中的数据结构: 布隆过滤器 ([Bloom Filter])。布隆过滤器的主要用途是用来检索一个元素是不是在一个集合中。
这是一个让人又爱又恨的数据结构。因为它占用很少的空间, 并提供常数时间的查询; 但同时, 它也有一些不可避免的缺点, 比如它有一定的误判率$\($false positive rate$\)$;
同时, 原始版本的Bloom Filter无法对元素进行删除操作。

 这篇文章主要讨论以下内容:

*   Bloom Filter的基本概念
*   Bloom Filter主要参数的计算
*   Bloom Filter的Java实现

Bloom Filter 简介
=================

在数据处理的过程中, 我们最常遇到的操作之一就是**membership查询**。 所谓的membership查询就是想判断一个元素是否存在于一个给定的集合里。 
最简单的做法是将集合数据存储在一个链表结构里, 然后每次查询的过程即为对链表的遍历过程。 当数据量变大时, 这种链表结构肯定是不够优化的, 因为每
次查询的时间复杂度为$O\(n\)$。 面试的时候经常有follow up问题, 让提高查询效率, 这个时候往往树状结构就要登场了, 因为它们可以使查询的效率变
为$O\(logn\)$。 当然树状结构还是可以被优化的, 就是用我们伟大的哈希方法, 比如Chain Hashing 和Bit Strings等, 或者就是简单的哈希表, 从而
得到 $O\(\frac{n}{k}\)$的时间复杂度。 这里提句题外话, 面试的时候是可以根据题目的复杂度要求大概猜出需要用到的方法的, 虽然可能不是100%正确
, 但几乎可以猜个八九不离十。

这个时候, 如果面试官继续追问, 有没有时间和空间复杂度都更优的方法呢? 那么, 你就可以放心大胆的跟他说: yes, 有的, 它就是传说中的Bloom Filter。
Bloom Filter的思想非常简单。 一个Bloom Filter的物理结构其实是一个bit vector。 这个bit vector的每一位初始的时候都被设为0。 同时, 每个Bloom Filter
都伴随着k个hash functions。 往Bloom Filter插入元素的过程就是用每个hash function计算这个元素, 从而将结果所对应的比特位改为1。 如果当前比特位已经为1,
则在插入的过程保持这一位不变。

请看Bloom Filter示例图:
![Image](https://github.com/sophiesongge/sophiesongge.github.io/blob/master/images/Bloom_Filter.png?raw=true)

在这个示例中, 我们的Bloom Filter由一个30 bits的Bit Vector以及3个Hash Functions来构成。 我们将三个元素$S_1$, $S_2$和$S_3$分别插入这个Bloom Filter中。
然后对另外三个新元素$S_1$, $S_x$和$S_y$进行查询。 由图所示, $S_1$和$S_x$将被认为属于这个Bloom Filter, 因为所以相应的bit位均为1, 而$S_y$则被视为
不属于这个Bloom Filter。 但是, 其中$S_x$为一个false positive的答案, 因为在插入的时候我们并没有插入这个元素。False Positive的答案是由Hash Collision造成的。我们可以根据要插入的元素的个数来变化BF的长度，从而减少误判率。

Bloom Filter 主要参数的计算
=========================

影响Bloom Filter的性能的参数主要有四个:

* n : 需要插入Bloom Filter的最多的元素的个数
* m : Bloom Filter中bit位的个数
* k : Hash Functions的个数
* p : Bloom Filter的False Positive rate

其中需要插入Bloom Filter的最多元素的个数n我们是知道的, 或者至少是大致可以估算的。 所以构建一个Bloom Filter的时候, 主要需要设定bit位的个数, 从而来限制false positive rate
的大小。 或者控制false positive rate的大小在一个固定值, 从而推算Bloom Filter的bit位的长度。

在介绍Bloom Filter的参数计算之前, 我想先介绍一个概率理论中的经典呢模型, [Balls into Bins]模型。 这个模型在Computer Science领域有非常广泛的应用。 它涉及n个球和m个盒子。
每次, 随机将一个球投入其中一个盒子中。 将所有球都放在盒子中后, 我们观察每个盒子中球的数目。 我们将这个数目称为每个盒子的负载, 并且, 我们希望知道: 每个盒子的最大负载是多少。 Bloom
 Filter的思想很大程度借鉴了Balls and Bins问题。 不同的是, 我们用多个hash functions将每个"球"扔了多次。 而false positive则是与每个"盒子"的负载相关。 下面我们将一步步介绍
Bloom Filter中每个参数的计算方式。

我们首先来看一个引理:

**Lemma 1:**  用k个Hash Functions, 将n个元素插入到一个m bits的Bloom Filter中, 则这个Bloom Filter的任意一个bit位为0的概率将不会大于 $e^ {\frac{-k*n}{m}}$

这个Lemma计算Bloom Filter中任意一个bit位为0的概率, 计算的思路与计算balls into bins模型中任意一个盒子为空的概率类似。

**证明：**
用一个Hash Function插入一个元素后，某个特定bit位为0的概率为：$1 - \frac{1}{m}$

所以，用k个Hash Functions插入一个元素后，某个特定bit位为0的概率为：$(1 - \frac{1}{m})^{kn}$

并且：$\lim\limits_{m\to\infty}(1-\frac{1}{m})^{kn}=e^{\frac{-kn}{m}}$

**Lemma 2:** 假设我们运用[Simple Uniform Hashing Functions]对Bloom Filter进行插入操作，则这个Bloom Filter的False Positive率p是m，n和k的函数，并且p = $(1-e^{-\frac{nk}{m}})^k$

**证明：**
Simple Uniform Hashing函数会将每一个元素以相等的概率hash去m个bit位中的一个。当用一个确定的hash函数处理一个确定的元素，某个特定的bit位$b_x$没有被设置为1的概率为：$1-\frac{1}{m}$

所以，当用k个Hash Function来处理这个元素的时候，某个特定的bit位$b_x$没有被设置为1的概率为：$(1-\frac{1}{m})^k$

然后，当用k个Hash Function来处理n个元素的时候，某个特定的bit位$b_x$没有被设置为1的概率为：$(1-\frac{1}{m})^{kn}$

相反，这个bit位被设置为1的概率为：$1-(1-\frac{1}{m})^{kn}$

在查询阶段，如果这个元素在Bloom Filter中对应的的所有hash bits都被设置为了1，则这个元素被认为存在于查询集中。所以False Positive的概率为：

p = $(1-(1-\frac{1}{m})^{kn})^k$

鉴于：

$\lim\limits_{x\to0}(1+x)^{\frac{1}{x}}=e$

$\lim\limits_{m\to\infty}(-\frac{1}{m})=0$

$\Rightarrow$ $\lim\limits_{m\to\infty}(1-(1-\frac{1}{m})^{kn})^k$ = $\lim\limits_{m\to\infty}(1-(1-\frac{1}{m})^{-m\times\frac{-kn}{m}})^k$ = $(1-e^{-\frac{nk}{m}})^k$

<font color="red">**笔者注：** 很多人认为 p = $(1-(1-\frac{1}{m})^{kn})^k$ 为一个Bloom Filter的False Positive的概率，这里我希望澄清一下，事实上它是一个元素被认为属于一个Bloom Filter的概率，所以这个概率其实也包含True Positive的概率。但是它是false positive的upper bound，所以在本文中仍以这个概率为false positive的概率作为基础来进行计算。</font>






[Bloom Filter]: https://en.wikipedia.org/wiki/Bloom_filter
[Balls into Bins]: https://en.wikipedia.org/wiki/Balls_into_bins
[Simple Uniform Hashing Functions]: https://en.wikipedia.org/wiki/SUHA_(computer_science)