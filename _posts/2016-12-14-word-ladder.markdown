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

dict = \["hot","dot","dog","lot","log"\]

一个最短的变换序列是 "hit" -> "hot" -> "dot" -> "dog" -> "cog"

返回它的长度 5

这是一道典型的图问题, 也是LeetCode上关于图的广度优先搜索少见的一道难题。但是其实图的问题都是纸老虎, 乍一看很难, 一旦理解了这一类问题都会迎刃而解。下面就让我们一起来一步一步将这个问题分解。

图论的问题一般都涉及两个步骤:

* 图的构建
* 图的搜索

**图的搜索**无非就是**深度优先**和**广度优先**。 在这里我墙裂建议大家打好基本功, 把深搜和广搜以及各种变种包括图的拓扑排序等等写熟练, 然后找一个你最喜欢最记得住的方式写成模板, 
因为我们以后可以无限复用同一个模板。 不要在这里浪费时间。<font color="red">关于图的搜索部分我们不再赘述, 如果你不会写深搜和广搜那么请你不要继续往下看了, 返回去练好基本功先。<font> 

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
 
 首先, 第一个method让我们来写找到跟hit差一个字母的词。我们将这个方法称作replace, 它的作用是, 将一个单词的指定一位替换成另一个字母。
 
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
               
那为了构建这个图, 下一步我们当然是为hit的每一个邻居找到它们的邻居们, 以此类推, 直到end出现在为止。 图构建如下:

        hit -- hot -- lot---
               /       /    \
              /       /      \
            dot -----/       log
               \             / \  
                \           /   \
                 ----- dog ---- cog
                 

如此图就构建完了。 如果这时候我给你出另外一道题: 找到这个图上从hit到cog的最短路。 那么你一定会不假思索的跟我说: 太简单了, 这就是图的广搜嘛。 恭喜你答对了。
广度优先的方法有很多, 我个人比较喜欢用queue来每次吞吐的方式(同样的, 深搜我喜欢用stack来吞吐的方式)。 但是这些都是凭个人喜好的。 下面我们就来将这段代码完成:

{% highlight java %}
public int WordLadder(String start, String end, Set<String> dict){
    if(dict == null){
        return 0;
    }
    if(start.equals(end)){
        return 1;
    }
    //将start和end添加到dict中, 将dict补充完整
    dict.add(start);
    dict.add(end);
    //用来做吞吐
    Queue<String> q = new LinkedList();
    //用来记录已经找过邻居的节点, 不想对他们进行重复计算
    Queue<String> visited = new LinkedList();
    q.add(start);
    visited.add(start);    
    //用来记录最短路的长度
    int length = 1;    
    //吞吐吧, word哥!
    while(!q.isEmpty()){
        length++;
        //这里要记录q的size, 它的size可以理解为这一层的节点的数目
        int size = q.size();
        for(int i=0; i<size; i++){
            //将最上面的节点吐出来
            String top = q.poll();
            for(String adj : getAdjacent(top, dict)){
                //不做无用功
                if(visited.contains(adj)){
                    continue;
                }else if(adj.equals(end)){
                    return length;
                }else{
                    //将邻居吞进去
                    q.add(adj);
                    visited.add(adj);
                }
            }
        }
    }
    //没找着
    return 0;
}
{% endhighlight %}
               
完整代码请[参考这里]。

你以为这道题到这里就完结了嘛??真是too young too naive! 这道题还有follow up的: ([题目出处]你懂的)

![Image](https://github.com/sophiesongge/sophiesongge.github.io/blob/master/images/Word_Ladder_II.png?raw=true)

如果我们希望把最短的转换序列输出需要怎么办? 这回返回值不再是一个int, 而是一个List\<List\<String\>\>, 也就是说instead of回答5, 我们希望得到下面这样的答案:

\[

    ["hit","hot","dot","dog","cog"],

    ["hit","hot","lot","log","cog"]

  \]
  

看到List\<List\<String\>\>这种形式的return, 直觉告诉我们应该是用backtracing没跑了! 恭喜你, 直觉又一次拯救了你。<font color="red">如果你不知道什么是回溯, 请先回去练这三道题: <font>

* [Combinations]
* [Permutations]
* [Subsets]

每次temps被加入到result中的条件是, temps的长度与前一道题目中返回的最短路径的长度相等。 所以, 我们其实需要准备两样东西, 第一、要搜索的图需要被提前构件好; 第二、最短路的长度需要求出。 我们可以在上一题的广搜中加入两个HashMap,
分别用来存储相应的adjacent list和distance。代码只做稍微改动即可:


{% highlight java %}
	public void wordLadderHelper(String start, String end, Set<String> dict, Map<String, ArrayList<String>> graph, Map<String, Integer> distance){
		Queue<String> q = new LinkedList<String>();
		q.add(start);
		distance.put(start, 0);
		
		for(String s : dict){
			graph.put(s, new ArrayList<String>());
		}
		
		while(!q.isEmpty()){
			String current = q.poll();
			ArrayList<String> adjList = getAdjacent(current, dict);
			
			for(String adj : adjList){
				graph.get(adj).add(current);
				if(!distance.containsKey(adj)){
					distance.put(adj, distance.get(current)+1);
					q.add(adj);
				}
			}
			
		}
	}
{% endhighlight %}


回溯部分的代码如下:

{% highlight java %}
	
	public void backtracing(List<List<String>> result, List<String> temps, String start, String current, 
			Map<String, Integer> distance, Map<String, ArrayList<String>> graph){
		
		temps.add(current);
		
		if(current.equals(start)){
			Collections.reverse(temps);
			result.add(new ArrayList<String>(temps));
			Collections.reverse(temps);
		}else{
			for(String adj : graph.get(current)){
				if(distance.containsKey(adj) && distance.get(current) == distance.get(adj)+1){
					backtracing(result, temps, start, adj, distance, graph);
				}
			}
		}

		temps.remove(temps.size() - 1);
	}
{% endhighlight %}

完整代码请[点击这里可以下载]。

如果您觉得本文有用, 请扫描一下二维码, 随意打赏 =^-^=

<img src="https://github.com/sophiesongge/sophiesongge.github.io/blob/master/images/dashang.JPG?raw=true" width="40" height="40" alt="打赏"/>



[点击这里]: http://www.lintcode.com/zh-cn/problem/word-ladder/
[参考这里]: https://github.com/sophiesongge/LeetCode/blob/master/src/WordLadderI.java
[题目出处]: http://www.lintcode.com/zh-cn/problem/word-ladder-ii/
[Combinations]: http://www.lintcode.com/zh-cn/problem/combinations/
[Permutations]: http://www.lintcode.com/zh-cn/problem/permutations/
[Subsets]: http://www.lintcode.com/zh-cn/problem/subsets/
[点击这里可以下载]:  https://github.com/sophiesongge/LeetCode/blob/master/src/WordLadderII.java