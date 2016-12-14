---
layout: post
title:  "LeetCode刷题记之Word Ladder"
date:   2016-12-14 11:04:00 +0200
categories: leetcode
use_math: false
---

嗨(｡･∀･)ﾉﾞ欢迎回来, 今天想跟大家讨论一道在FLAG面试中出现的概率非常大的一道题Word Ladder (尤其A家非常喜欢考这道题, G家也曾经考过这道题)。

让我们先来看一下题目(题目出处请[点击这里]):

![Image](https://github.com/sophiesongge/sophiesongge.github.io/blob/master/images/Word_Ladder_I.png?raw=true)

**样例**

给出数据如下： start = "hit" end = "cog"

dict = $\[$"hot","dot","dog","lot","log"$\]$

一个最短的变换序列是 "hit" -> "hot" -> "dot" -> "dog" -> "cog"

返回它的长度 5

这是一道典型的图问题, 也是LeetCode上关于图的广度优先搜索少见的一道难题。但是其实图的问题都是纸老虎, 乍一看很难, 一旦理解了这一类问题都会迎刃而解。下面就让我们一起来一步一步将这个问题分解。

图论的问题一般都涉及两个步骤:

* 图的构建
* 图的搜索

**图的搜索**无非就是**深度优先**和**广度优先**。 在这里我墙裂建议大家打好基本功, 把深搜和广搜以及各种变种包括图的拓扑排序等等写熟练, 然后找一个你最喜欢最记得住的方式写成模板, 
因为它们都可以无限复用同一个模板。 不要在这里浪费时间。<font color="red">关于图的搜索部分我们不再赘述, 如果你不会写深搜和广搜那么请你不要继续往下看了, 返回去练好基本功先。<font> 

往往**图的构建**才是题目的难点。

我在遇到一个比较难的问题的时候往往喜欢把问题分解成小问题, 然后再各个击破。 拿图的构建举例, 如果用一个HashMap来表示图上的节点, 我们无非需要确定两件事情, (1)key是什么, (2)value是什么(<-_<-废话)。
 key一般就是当前节点的值, 既它在图中的显示部分, 在这道题中, 很明显它将是每个单词, 因为我们是将每个单词连接在一起构成的图。 而value的部分则是这个节点的adjacent nodes, 说白了就是它和谁连在一起。神奇吧,
 如此一来, 我们就用一个抽象的数据结构, 把一个具体的图表示出来了。
 
 那么问题来了, 如何确定key和value的值??? key是图上的节点, 但是在最开始, 我们还真无法确定这个图上的节点都有哪些。 我们唯一确定的是start和end这两个词肯定在图上, 因为我们的目的是在这个图上从start走到end。
 那好吧, 先把start和end画在图上。
 
            hit--
                 \
                 /
                cog

 接下来我们还能做什么呢? 好像可以帮hit找到它的邻居们。 嗯, 没错! hit的邻居将是和hit相差一个字母, 并且在dictionary中的词。太容易了, 是不是~~~! 先让我们写两个辅助的methods来实现我们要做的事情。
 
 首先, 第一个method让我们来写找到跟hit差一个字母的词。我们将这个方法称作replace, 它的作用是, 将一个单词的指定一位替换成一个字母。
 
 {% highlight java %}
public String replace(String word, index i, char c){
    char[] wordArray = word.toCharArray();
    wordArray[i] = c;
    return new String(wordArray);
}
 {% endhighlight %}

接下来, 让我们来写一个method, 为hit找到它的adjacent。 我们将这个方法称作getAdjacent, 它的作用是, 为每一个单词找到它的相邻单词。这一步有多种实现方法, 
我用的方法是将单词的每一位从a换到z, 如果换过的单词在dictionary中, 我就认为这个单词是原单词的邻居。
{% highlight java %}
public ArrayList<String> getAdjacent(String word, Set<String> dictionary){
    ArrayList<String> adjacent = new ArrayList();
    for(char letter = 'a'; letter <= 'z'; letter++){
        for(int i=0; i<word.length(); i++){
            if(!word.charAt(i) == letter){
                if(dictionary.contains(replace(word, i, letter))){
                    adjacent.add(replace(word, i, letter));
                }
            }
        }
    }
    return adjacent;
}
{% endhighlight %}

通过这个方法, 我们给hit找到了邻居{hot}。

        hit --
              \
               \
               hot-- ... -- cog
               
那为了构建这个图, 下一步我们当然是为hit的每一个邻居找到它们的邻居们, 以此类推, 知道end出现在为止。 图构建如下:

        hit -- hot -- lot---
               /       /    \
              /       /      \
            dot -----/       log
               \             / \  
                \           /   \
                 ----- dog -    cog
                 


               









[点击这里]: http://www.lintcode.com/zh-cn/problem/word-ladder/