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






[House Robber]: https://leetcode.com/problems/house-robber/
[House Robber II]: https://leetcode.com/problems/house-robber-ii
[House Robber III]: https://leetcode.com/problems/house-robber-iii/
[代码]: https://github.com/sophiesongge/LeetCode/blob/master/src/HouseRobber.java