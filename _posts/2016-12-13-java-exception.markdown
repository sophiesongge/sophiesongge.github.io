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



