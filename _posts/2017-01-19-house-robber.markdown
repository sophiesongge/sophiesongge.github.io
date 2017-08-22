---
layout: post
title:  "LeetCode刷题记之House Robber"
date:   2017-01-19 17:36:00 +0200
categories: leetcode
use_math: false
---

今天要讨论的是LeetCode的一个系列的题, 这个系列叫做House Robber, 虽然在这个法治社会, 去抢劫别人家的房子是不对的, 但是这不影响这一系列成为非常好的一个系列。 它涉及到的题是:

* [House Robber]
* [House Robber II]
* [House Robber III]

House Robber
============

第一道题是一道非常典型的动态规划。 之前看过很多写动态规划的攻略, 有写的非常好的, 但大部分其实都不知道想说什么鬼。。。 其实动态规划很简单, 用一句话来说它的思想就是: 用一个数组把中间状态存起来,
从而instead of每次都重新计算, 我们希望每次用到之前的结果的时候都可以在数组中取得。难点在于*中间状态*怎么定义, 也就是用来动态规划的数组dp\[\]里面存的是什么。 这道题很简单, 所以这个中间状态也
很好找, 让它存储以当前位为结尾小偷能偷到的最大值即可, 由于不能连续偷两家, 那么, 第dp\[i\]则有两种可能, (1)偷当前这家, (2)不偷当前这家, 由于小偷希望偷到最大值, 则最优解为(1)和(2)中大的那个, 
既, dp\[i\]的值即为nums\[i\]+dp\[i-2\]和dp\[i-1\]中大的那个。

多的就不用说了吧, 请直接参考完整[代码]。


House Robber II
===============

第二道题是第一道题的变种, 变化在于, 现在所有房子首尾相接, 排到一个环上了。 其他不变, 还是不能连续偷两家。 

这个变种非常好, 因为它涉及到了动态规划的另外一个难点: 初始状态怎么定义, 上一道题中由于初始状态很简单dp\[0\] = nums\[0\], 所以我们并没有啰嗦关于初始状态的问题。

但是这道题涉及到了首尾相接的时候, 首和尾也变得不能同时偷了。 别慌。 大问题化小问题。 无非就是你小学时候学到的应用题, 分情况讨论嘛, 情况(1), 偷第一家, 情况(2), 不偷第一家。 所以这道题需要create两个
动态规划的数组, 结果当然是取两种情况中大的那个。 其他思路和第一题一样。

Tips: 这道题有一个小技巧, 如果你想以最精炼的代码来完成这道题, 那么你需要比较偷第一家的倒数第二位, 和不偷第一家的最后一位, 因为偷了第一家就不能偷最后一家了, 所以用最后一家比是不对的。

废话少说, [上代码]。

House Robber III
================

第三道题, 房子被排列在了一个二叉树上, 厉害了word城市规划哥。 其他要求还是一样, 相连的房子不能被同时偷。
 
第一反应肯定是加了个树的遍历过程, 由于相连的房子都在树的同一个支上, 所以肯定是个DFS (注: 如果是同一个层上则是BFS)。

那树的DFS有几种写法呢? 最常用的当然是用Stack进行吞吐, 第二常用的是Backtracing, 还有一个最不常用的方法是divide and conquer。(不知道这个的同学请回去练基本功) 前两个都可以用, 但是没有那么直观。 最后那个则非常直观, 假设一棵树如下图所示:

          Root
        //    \\
       //      \\
     Left      Right
     
那么最简单的情况就是如果不偷Root这一家, 则能偷到的最大值即为Left偷到的最大值与Right偷到的最大值的和。 如果偷Root这一家, 问题就变得稍微有点复杂, 因为如果偷Root, 那就意味着不能偷Left和Right, 只能偷他们的孩子们。
这样一来又回到了II中分类讨论的情况。 那不妨我们就仿照II好了, 让这个DFS的返回是一个二维数组, 第一位装的是偷Root的情况, 第二位装的是不偷Root的情况, 由于Divide and Conquer的方法, 其实是递归的思想。 所以每次递归的时候, 
其实对于Left和Right来说也有偷和不偷两种情况。 Root能偷到的最大值就是偷Root和不偷Root两种情况之中大的那个。 不偷Root的最大值是左边能偷到的最大值与右边能偷到的最大值的和, 即为 Max(Left\[0\], Left\[1\]) + Max(Right\[0\], Right\[1\])。
而偷Root的时候, 则不能偷Left和Right了, 所以偷Root的结果是Root的值加不偷左边的最大值和不偷右边的最大值, 即为: Root.val + Left\[1\] + Right\[1\]。

好了, 问题解决了, [代码请点我]。

如果您觉得本文有用, 请扫描一下二维码, 随意打赏 =^-^=

![Image](https://github.com/sophiesongge/sophiesongge.github.io/blob/master/images/dashang.JPG?raw=true=10x10)


[House Robber]: https://leetcode.com/problems/house-robber/
[House Robber II]: https://leetcode.com/problems/house-robber-ii
[House Robber III]: https://leetcode.com/problems/house-robber-iii/
[代码]: https://github.com/sophiesongge/LeetCode/blob/master/src/HouseRobber.java
[上代码]: https://github.com/sophiesongge/LeetCode/blob/master/src/HouseRobberII.java
[代码请点我]: https://github.com/sophiesongge/LeetCode/blob/master/src/HouseRobberIII.java
