---
layout: post
title:  "Spring笔记之依赖注入和翻转控制"
date:   2017-08-22 17:36:00 +0200
categories: backend
use_math: false
---

Spring
======

Spring 最认同的技术是控制反转的依赖注入dependency injection（DI）模式。控制反转Inversion of Control（IoC）是一个通用的概念，它可以用许多不同的方式去表达，依赖注入仅仅是控制反转的一个具体的例子。

依赖注入（Dependency Injection）
=========

* 作用：

当编写一个复杂的Java程序时，应该让这个程序尽可能独立于其他的Java类来增加这些类的可重用性。这样一来，进行单元测试的时候，也可以使它们独立于其他类进行测试。

Dependency Injection有助于将这些类粘合在一起，并且在同一时间让它们保持独立。


* 依赖：

在Class A中有Class B的实例，则成Class A对Class B有一个依赖。例如：

{% highlight java %}
public class Human {
    ...
    Father father;
    ...
    public Human() {
        father = new Father();
    }
}
{% endhighlight %}

Class Human中有Class Father，则称类Human对类Father有一个依赖。

上一段代码中存在的问题：

（1）如果现在要改变father的生成方式，如果需要用new Father(String name)初始化father， 则要改变Human的代码。
（2）如果想测试不同的Father对象对Human的影响很困难，因为father的初始化被写死在了Human的构造函数中。
（3）如果new Father()的过程非常缓慢，单测时我们希望用已经初始化好的father对象Mock掉这个过程也会很困哪。

* 依赖注入：

在上一例中，将依赖在构造函数中直接初始化是一种Hard Init方式，这种方式的弊端在于两个类不够独立，不方便测试。
当然我们还有另外一种Init方式，例如：

{% highlight java %}
public class Human {
    ...
    Father father;
    ...
    public Human(Father father) {
        this.father = father;
    }
}
{% endhighlight %}

在这种方式中，我们将father对象作为构造函数的一个参数传入。这样，在调用Human的构造方法之前，需要外部初始化好一个Father对象。像这种非自己主动初始化依赖，而是通过外部来传入依赖的方式被称之为依赖注入。

依赖注入的好处在于：

（1）将依赖之间解耦
（2）因为已经解耦，所以方便做单元测试，尤其是Mock测试。

* Java中的依赖注入

实现依赖注入有很多种方式，在Java中，最常使用的方式是通过注解来实现。通过在字段的声明前添加@Inject注解进行标记，来实现依赖对象的自动注入。

但是实际上，如果只写了一个@Inject注解，Father对象并不会被自动注入，因为它还需要依赖一个注入框架，比如Spring。

翻转控制
=======

在一个软件系统中，各对象就像齿轮一样，是相互耦合协同工作的。在一个强耦合的系统中，如果一个零件不能正常工作了，整个系统就崩溃了。然而，对象之间的耦合关系是不可避免的也是非常必要的，这是协同工作的基础。
随着应用系统规模的庞大，对象之间的依赖关系也变得越来越复杂，经常会出现对象之间存在多重依赖的关系。

为了解决对象间耦合度过高的问题，翻转控制的理论就应运而生了。它被设计来实现对象之间的解耦。