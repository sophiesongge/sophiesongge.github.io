---
layout: post
title:  "LeetCode刷题记之Best Time to Buy and Sell Stock"
date:   2017-02-15 17:56:00 +0200
categories: leetcode
use_math: false
---

今天要讨论的题目是Best Time to Buy and Sell Stock, 不过不要妄想会做这道题之后就能变成像巴菲特那样的股坛神话, 因为这道题其实是个马后炮 :D

我为什么这么说呢, 因为它的目的是, 如果知道了每天的股票价钱, 让你求这些天可能的最大收益是多少。 而现实生活中如果想成为股坛神话则需要在不知道这一切的情况下预测走势。

Anyway, 这并不影响这一个系列的题成为很好的一个系列。 因为它和我们上回书讨论的House Robber系列一样, 属于动态规划问题, 而且是思路非常巧妙的动态规划。

这个系列一共有五道题, 如下:

* [Best Time to Buy and Sell Stock]
* [Best Time to Buy and Sell Stock II]
* [Best Time to Buy and Sell Stock with Cooldown]
* [Best Time to Buy and Sell Stock III]
* [Best Time to Buy and Sell Stock IV]

前言: 动态规划
========

上一讲中有同学反映希望具体讲一讲动态规划。 那今天宝宝就来和大家讲讲动态规划, 同时宝宝再给大家一个福利, 每一道题对比一下动态规划和贪心两种算法。 

维基百科上说动态规划适用于有**重叠子问题**和**最优子结构**性质的问题。

**重叠子问题**表示子问题之间是重叠的(<-_<- 废话), 即子问题的解可能在下一阶段的求解中被用到。举个简单的栗子, 大家都知道著名的Fibonacci数列, 从第三位开始, 每一位都是前面两位的和。 比如, 
f(10) = f(9) + f(8), 也就是说, 当我们计算第10位的时候, 需要用到第9位和第8位的结果, 即我们需要重新计算f(9)和f(8), 这就是"重叠子问题"。 当然, 如果我们将f(9)和f(8)的结果*缓存*起来 -- 比如用一个数组, 当我们
再次用到的时候只需要从缓存中将值读出即可, 而不需要进行重新计算。 --- 简单的说, 就是一个以空间换时间的过程。

**最优子结构**, 如果我跟你说: 如果问题的最优解所包含的子问题的解也是最优的，我们就称该问题具有最优子结构性质。 --- 你看了是不是很想去死啊…… 通俗一点说, 如果每个阶段的最优状态可以从前面的某个阶段的某个或某些状态直接得到, 则
可以说这个问题有最优子结构, --- 再免费附赠一个概念叫**"无后效应"**, 不管之前那个状态是如何得到的, 则称其具有无后效应。 注意我这里用词是非常准确的, 某个阶段的某个状态或某些状态, 后面的题目中我们将会用到这一点。

宝宝现在要再说一句让你崩溃的话, 你坐稳了: 其实动态规划的本质不是这些, 也不用去纠结是不是空间换了时间。 你是不是人生观瞬间崩坏? 

故事还得从头说起, 话说计算机是怎么工作的?? 宝宝不是在开玩笑!!! 计算机的本质其实是一个"状态机"。 什么意思? 内存里存储的所有数据构成了计算机当前的状态, 而CPU利用当前的状态(所有数据)算出下一状态。 发现问题没有? "下一状态"只能由"当前状态"
来计算出来。 我们每次用电脑完成一个指令的时候, 其实真正的思考过程是如何将这个指令表达成计算机的状态 --- 比如有哪些变量, 哪些数据需要被存储/调用, 然后需要考虑的是状态是如何转移的 --- 比如如何根据这一状态下的变量和数据来计算出下一状态的变量和数据。
面试官问你的所谓"空间复杂度"其实是为了完成你的指令, 你保存了多少状态, 而"时间复杂度"是从当前状态到下一状态需要走几步(把大象放冰箱需要用几步 =^-^=)?

太抽象了是吧? 来, 咱还用前面那个Fibonacci数列的例子 --- 说句题外话, 曾经去芬兰的旧首都图尔库开会, 居然看到一个大烟囱上面赫然的镶嵌着斐波那契数列。 在斐波那契数列中, 每一个数字可以被理解成这个数列的一个状态。 
比如你要计算f(10), 那么你需要的状态是f(9)和f(8), 所以其实每一步指令最多保存两个状态即可。而从旧状态到新状态的转变只需要走一步, 就是一个加法。

这个问题太简单了? 来, 咱么换个难的。 在斐波那契数列中, 其实我们只往一个方向走, 就是只往前走。 现在假设我给你一个矩阵, 每次你可以往前后左右四个方向走, 状态怎么算? 晕了吗??? 假设每走一步, 被我们称为一个阶段, 那每个阶段我们都有前后左右四个选择,
走N步之后我们会怎样?? 我的天哪, 我将有4^N个可能存在的位置, 而这些可能性的集合就是我当前所有可能的状态。这时如果我要问你, 在第n+1步的时候怎么走离第一步最远呢??? 你需要把这些所有的状态都给我计算出来嘛??? 本宝宝怎么会教你们这么笨的方法, 答案当然是不需要啊!
因为第n+1步最远的位置其实只跟第n步最远的位置有直接关系, 换句话说, 当我们求出了第n步的最优解, 在第n+1步中, 我们只要再往正确的方向走一步, 就会得到第n+1步的最优解。 这句话是不是听着很熟悉呢, "下一步的最优解是从当前最优解推算到的"。
所以聪明的宝宝们只需要存储每一步的最优解就好了啊 (递推)。 这个过程乍一看是不是和斐波那契的那个很像? 但是不好意思, 其实这个思想叫作"贪心"。为什么? 因为这个过程中每一次我们只保留上一步的最优解即可, 我们不关心之前的情况, 也不关心这个最优解是怎么来的, 因为我们
确定的知道上一步的最优解可以推出这一步的最优解。 这就是很好的"贪心"的思想, 在贪心算法中, 我们每一步都希望走在当前看来最好的方向。 

但是在动态规划中, 我们并不能确定这一步的最优解是不是从上一步的最优解得来, 唯一能确定的是这一步的最优解肯定是从之前某一步的最优解得来的, 所以我们需要存储之前所有步的最优解。 如果还是觉得抽象可以去看看[这道题]。


换句话说, 动态规划会有很多子问题, 而且这些子问题是不独立的, 需要把全部子问题都解决掉才能找到最终解; 而贪心只有一个子问题, 只要解决掉这个子问题即可。 动态规划是自底向上的, 每一步都要根据策略得到一个更小规模的问题。最后解决所有小规模的问题, 得到整个问题最优解。
而贪心是自顶向下的, 每一步根据策略得到一个当前最优解。然后传递到下一步，从而保证每一步都是选择当前最优的, 最后得到结果。

咱们来画个草图。

动态规划:
初始状态→│决策１│→│决策２│→…→│决策ｎ│→结束状态

贪心:
初始状态→│第一步最优解│→│第二步最优解│→…→│第n步最优解│→结束状态

其实很多问题都可以用动态规划和贪心两种方法来解决, 比如著名的背包问题(这是宝宝大学时期的一个大作业, 至今印象深刻)。 相对来讲, 一般情况下, 贪心算法占的空间会小一点, 但是, 贪心算法并不能保证最终得到的是全局最优解。


Best Time to Buy and Sell Stock
===============================
这道题的输入是一个数组prices\[\], 这个数组存储的是每天的股票价格。 题目的要求是, 如果只能买卖一次股票(一个transaction), 则最大收益是多少。

首先让我们来看一下动态规划的思路。

动态规划的一般方式是利用一个数组dp\[\]用来存储如果以今天结尾, 能得到的结果。 对于我们这个问题来说, 就是用来存储到第i天的为止, 如果只能买卖一次股票, 所能获得的最大收益。
这个收益和截止到前一天为止的最大收益有关 --- 或者说是昨天的收益有关, 我们将这个收益称为A。 它还和今天的股价有关, 确切的说, 是和今天的股价和截止到今天为止的最小股价的差有关, 这个差即为如果今天卖出, 可以得到
的最大收益, 我们将这个收益称为B。 今天的最终最大收益则为A和B中大的那个。 

如果用贪心的思想, 我们不需要保存每天的状态, 而只需要保存截至到目前为止最大的收益即可, 则最后一天的最大的收益即为最后的收益。

完整代码请参考[代码I]。

Best Time to Buy and Sell Stock II
==================================
这道题与前一道题的区别在于, 我们可以买卖股票很多次(as many as you want), 求最大收益。 由于可以买卖多次, 比如因为今天涨了所以我今天卖出去了, 但是我还可以再把它买回来, 如果明天还涨的话 -_-b

所以, 最大收益为累加的和, 其他思路和第一题一样, 没什么好说的, 看代码[代码II]。

Best Time to Buy and Sell Stock with Cooldown
=============================================
这道题在上一道题的基础上又做出了一些变化, 这次我们仍然是可以爱买卖几次就买卖几次, 但是多了一个限制条件, 即卖出之后的第二天不能再买入。 这道题变得更难一点了, 因为每天有两种选择, 要么今天卖掉, 要么今天不卖。
这其实就是解题思路, 如果用动态规划的话, 就利用两个数组profitSell和profitDoNothing来存储相应的最大值。 最后的结果就是这两个数组最后一位中大的那个。

如果利用贪心算法, 则用两个Integer -- profitSell和profitDoNothing来存储每一步相应的最优解, 然后再求他俩中大的那个。 没啥好说的, 看代码[代码Cooldown]。

Best Time to Buy and Sell Stock III
===================================

这道题又进一步提高了难度, 这一次我们可以有两次买卖的机会。 当然在最后一题中(IV), 我们对这一题进行了推广, 如果我们有k次买卖的机会, 那么最大获益是怎样。 其实这两道题是同一道题, 所以我们放在一起讲。

这一道题虽然代码不多, 但是要想明白还是挺难的。 我一开始就想错了, 用了优先级队列来做, 最后想想其实不对, 因为不能把所有情况都算到。

正确的思路是使用"局部最优解和全局最优解"的思想, 和Cooldown那道题的思路很像, 每一天将有两个状态 -- "卖出"和"不卖出"。 利用两个动态规划数组local和global分别来存储局部最优解和全局最优解, 难点在于
这道题的状态更复杂, 因为要在两个维度计算, 第一个维度是天数的维度, 第二个维度是交易次数的维度, 所以local和global都是二维数组。 其中, global\[i\]\[j\]表示到第i天最多进行j次交易可以达到的最大利润，而
local\[i\]\[j\]表示到第i天最多进行j次交易，并且在第i天卖出可以达到的最大利润。

则： global\[i\]\[j\] = Math.max(local\[i\]\[j\], global\[i-1\]\[j\]) 也就是到今天为止的全局最好，是到今天为止的局部最好与到昨天的全局最好中大的那个。因为今天的最大利润有两种情况，第一是几天卖出了（既local\[i\]\[j\]），另一种是今天什么都没做，那今天的最好的情况就是昨天的全局最好情况
  
而： local\[i\]\[j\] = Math.max(global\[i-1\]\[j-1\] + prices\[i\] - prices\[i-1\], local\[i-1\]\[j\] + prices\[i\] - prices\[i-1\]) --- 这里是local\[i-1\]\[j\]因为就算第i-1天卖了， 可以再买回来在第i天再卖，所以并不增加transaction。 这里就不太好理解了， 因为今天需要卖出，所以需
要加prices\[i\] - prices\[i-1\], 两种加的可能性，（1） 和全局比，（2）和局部比。
  
如果用贪心的做法, 则不需要维护天数的维度, 因为我们只需要记录每天的local和global即可, 后一天的根据前一天的结果进行计算。 如果只是两次操作，相当于j = 2。但是贪心的时候有一个小技巧需要注意一下, 就是我们需要倒序的算, 因为我们其实需要和前一天第2次操作的比, 所以我们不希望这个值被
覆盖掉。

具体请参考[代码III]。

Best Time to Buy and Sell Stock IV
==================================

第四题是第三题的推广, 由第三题中只能卖两次, 推广到现在可以卖k次。 思路和第三题是一样的, 我们在这里不再赘述。

但是需要注意的一点技巧是, 需要注意一个细节, 当k比prices.length的一半还要大的时候, 说明我们无法买卖够k次, 那么这道题就转变为了第二题。 这一点需要提前考虑到, 否则会得到一个TLE。

好了, 看代码吧: [代码IV]。

如果您觉得本文有用, 请扫描一下二维码, 随意打赏 =^-^=

![Image](https://github.com/sophiesongge/sophiesongge.github.io/blob/master/images/dashang.JPG?raw=true=10x10)



[Best Time to Buy and Sell Stock]: https://leetcode.com/problems/best-time-to-buy-and-sell-stock/
[Best Time to Buy and Sell Stock II]: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/
[Best Time to Buy and Sell Stock with Cooldown]: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/
[Best Time to Buy and Sell Stock III]: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/
[Best Time to Buy and Sell Stock IV]: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/

[代码I]: https://github.com/sophiesongge/LeetCode/blob/master/src/solution/BuySellStock.java
[代码II]: https://github.com/sophiesongge/LeetCode/blob/master/src/solution/BuySellStockII.java
[代码Cooldown]: https://github.com/sophiesongge/LeetCode/blob/master/src/solution/BuySellStockCooldown.java
[代码III]: https://github.com/sophiesongge/LeetCode/blob/master/src/solution/BuySellStockIII.java
[代码IV]: https://github.com/sophiesongge/LeetCode/blob/master/src/solution/BuySellStockIV.java

[这道题]: https://leetcode.com/problems/word-break/
