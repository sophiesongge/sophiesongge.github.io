---
layout: post
title:  "Java Exception"
date:   2016-12-13 15:25:55 +0200
categories: learn java
use_math: false
---

Hi guys, welcome back, today's topic is Java Exception.

Execption的原因
============

顾名思义, Exception就是异常, 而引起程序异常的原因无非就是以下几种:

* 用户输入了非法数据
* 要打开的文件路径不存在
* 网络通信中断
* JVM内存溢出

Java把以上这些不太严重的问题都称为异常。

Exception的结构
============

说到Java Exception的结构, 就要从一个古老的父类Throwable来说起。 从名字来理解, 这个词表示"可抛出"的。 Java中左右的Error和Exception都继承自这个类。

Exception又分为两类, 一类叫RuntimeException, 另一类是非RuntimeException。 -_-b 

RuntimeException翻译为中文是"运行时异常", 即这类异常不会在编译的时候被检测, 而是在程序运行的时候才会被处理, 一般像除数为零(ArrithmeticException), 零指针(NullPointerException),
数组索引越界(ArrayIndexOutofBoundsException)等都属于这一类异常。 由于这类异常不是程序逻辑的异常, 而一般都是没有考虑全边界条件引起的, 所以Java并不强制程序去处理这类异常。 所以我们的
程序中既可以检查这类异常, 也可以不检查直接留给JVM处理。所以这类异常又被称为"un-checked"异常。 有人把它翻译成"不可查异常", 我觉得这个翻译有很大程度的误导, 更准确的翻译是"可不查异常"。

除RuntimeException之外的所有异常都是"checked exception", 既程序中必须对这些异常进行处理, 否则无法通过编译。 Checked异常很大程度上是由于代码逻辑不完善引起的, 所以Java本着"不完善的代码
不执行"的严谨设计哲学, 强制代码对可能出现的异常进行处理, 否则编译出错。 

总结一下, Java的异常的"家谱"如下图所示:

                Throwable
                /\     /\             
               //       \\         
              //         \\    
            Error      Exception
                      /\        /\
                     //          \\
                    //            \\
               IOException   RuntimeException
                  (etc.)
                  
Exception的处理机制
==============

那么问题来了, 既然除RuntimeException外的所有异常都需要在程序中进行处理, 那么怎么处理呢?? Java给出了两种处理机制: (1) 抛出异常, (2) 捕获异常。

异常的抛出通常有两个地方, 首先我们可以在方法的签名中声明该方法可能抛出的异常, 通过**throws**关键字。 或者我们也可以使用**throw**关键字来抛出一个具体的异常对象,
 throw语句可以单独使用。


在捕获异常方面, 为了增加代码的可读性, 减少维护的难度。 Java提出了try-catch的处理机制。 如下图所示:

{% highlight java %}
try{
    //程序
}catch(Exception e1){
    //要处理的Exception
}catch(Exception e2){
    //需要处理的Exception
}
{% endhighlight %}

其中正常程序需执行的代码被放在try block中, 而这段程序可能会出现的异常被放在catch模块中。 当然, 在捕获异常的过程中也需要先将异常抛出, 比如在执行try block中如果代码出现异常
系统会自动生成一个异常对象, 这个对象被提交给JVM, 这个过程就是"抛出"的过程。 当JVM接收到异常对象时, 会寻找能处理该异常对象的相应catch block, 如果找到, 则把该异常对象交给
这个catch block进行处理, 这个过程就是异常的捕获过程。 --- 当然如果JVM找不到相应的catch block, 则运行环境终止, 程序退出。

Catch Block的执行顺序
================
既然我们可以捕获多个异常, 那自然就涉及到捕获的顺序。 请问下面这段代码能通过编译嘛:

{% highlight java %}
try {  
            //Do something  
        } catch (Exception e) {  
              
        } catch (IOException e) {  
              
        }  
{% endhighlight %}

答案是: 不能。 因为, catch block是按顺序执行的, 并且java一旦捕获了一个异常, 就会忽略后面所有的异常。 由于IOException是Exception的子类, 所以IOException将永远不会被捕获。 所以在
我们自己写代码捕获异常的时候, 一定要注意多个异常捕获的顺序。

finally
=======

关于finally, 我们最熟悉的就是这个block无论如何都会被执行到, 即便try或者catch中有return, continue, break等, finally仍然会被执行。 当然, 如果你将JVM终止掉, 那自然finally也不会被执行, 
所以如果你在try或catch中写了System.exit(0), 那么算你狠, finally不会被执行。

另外一个需要提的是, 为什么要写finally块, 当然你可以说, 为了保证有一部分永远被执行到。 但其实finally的作用远远不止这些。 

故事还得从头说起。 大家都知道java的垃圾回收机制是自动进行回收的, 这样极大程度的方便了程序员。 问题是java的垃圾回收机制不会回收任何物理资源, 它只会回收堆内存中的对象所占用的内存。那么如果我们试图
在try中打开一些物理资源, ---比如连接了数据库, 打开了磁盘中的文件, 网络连接等, 这些资源是不会被java自动回收的, 需要我们手动进行处理。 而这些物理资源的回收一般会被放到finally中。 因为, 如果try
中的某一语句引起了异常, 则位于这个语句后面的回收资源语句将不会被执行到; 如果将回收放到catch中, 则很大程度它不会被执行, 因为我们suppose正常情况下程序是不会有异常的。 所以为了保证资源回收被顺利执行, 
最佳的做法是将其放到finally中。

还有一点需要注意的是, 一般不会在finally中使用return或者throw等会导致方法终止的语句。 因为, 一旦使用了这样的语句, 会导致try, catch中的return、throw语句失效。 举个简单的栗子:

{% highlight java %}
try{
    return true;
}finally{
    return false;
}
{% endhighlight %}

这段代码永远都会return false。