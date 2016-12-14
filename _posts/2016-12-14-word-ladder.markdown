---
layout: post
title:  "LeetCode刷题记之Word Ladder"
date:   2016-12-14 11:04:00 +0200
categories: leetcode
use_math: false
---

嗨(｡･∀･)ﾉﾞ欢迎回来, 今天想跟大家讨论一道在FLAG面试中出现的概率非常大的一道题Word Ladder (尤其A家非常喜欢考这道题, G家也曾经考过这道题)。

让我们先来看一下题目(题目出处请[点击这里]):


>给出两个单词（start和end）和一个字典，找到从start到end的最短转换序列

>比如：

>每次只能改变一个字母。
>变换过程中的中间单词必须在字典中出现。

> *注意事项:* 

>如果没有转换序列则返回0。
>所有单词具有相同的长度。
>所有单词都只包含小写字母。

>样例
>给出数据如下：

>start = "hit"

>end = "cog"

>dict = $\[$"hot","dot","dog","lot","log"$\]$

>一个最短的变换序列是 "hit" -> "hot" -> "dot" -> "dog" -> "cog"，

>返回它的长度 5















[点击这里]: http://www.lintcode.com/zh-cn/problem/word-ladder/