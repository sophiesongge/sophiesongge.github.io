---
layout: post
title:  "Design Pattern 之 Singleton (单例模式)"
date:   2016-11-18 15:00:00 +0200
categories: design pattern
use_math: false
---
Singleton是最简单也最常用的设计模式之一。 它的用途非常广泛, 思想也非常简单, 但是并不是每个程序员都能正确的写好一个单例模式。 今天就让我们来讨论一下Singleton。

为什么要用Singleton
==============

有时候, 系统只需要拥有一个全局变量, 因为这样有助于我们统一协调系统整体的行为。 比如: 某个发邮件的程序中, 该程序的配置文件(smtp地址, 用户名, 密码, etc.)存放在
一个文件中, 这些配置数据最好由一个对象统一读取, 然后系统服务进程中的其他对象再通过这个对象获取这些配置信息。 这样的应用场景还有很多, 例如: web中只能有一个计数器; 
一个系统中只能有一个计时工具, 等等。 

那么, 如何才能保证一个类只被创建了一个instance, 并且这个instance可以轻易的被其他类访问到呢? 你可能首先会想到创建一个全局变量, 因为这样可以确保该变量可以随时被访问到。
问题是, 这样仍然无法避免我们实例化多个对象, 然而, 有时候系统中创建一个新的对象会消耗很多资源(比如I/O访问, 数据库连接等)。 

理想的办法是让这个需要只存在一个实例的类自己去保存它的这个实例并提供一个访问该实例的方法, 并且这个类需要确保没有其他类能够创建它的实例。 具有以上性质的类就被称为Singleton类。

Singleton的实现思路
==============

上文书说到: Singleton类需要阻止其他类实例化它, 保存自己的实例并提供访问该实例唯一的方法。 阻止它被实例化的方法非常简单, 就是私有化它的构建器(constructor), 这样就没有人能
通过constructor来构建它了, 由于constructor是private的, 这就意味着没有其他类可以通过它的constructor来构建一个它的实例, 唯一能生成它的实例的只有它自己。 保存它自己唯一的
实例也很简单, 提供一个static的全局变量, 这个变量是它本身的实例即可。 但是, 由于别人不能实例化它, 那么也就意味着也无法访问它的全局变量, 所以为了让其他类访问到这个唯一的实例, 
我们还需要写一个public的方法, 来将这个instance返回。 所以你看我说过它很简单吧, 总结起来Singleton的实现思路为以下三个部分:

* 私有化constructor --- 确保别人无法创建该类的实例
* 提供一个自身实例的静态变量 --- 保存自身的实例
* 提供一个方法, 判断自身的实例是否存在, 如果不存在则创建, 存在则返回该实例 --- 唯一的访问接口

Singleton的优缺点
=============

Singleton模式的优点非常明显。 首先, 由于内存中只存在一个实例, 这样减少了内存的开销, 尤其是对于一些需要频繁创建和销毁的对象, 它极大程度的提高了系统的性能。 其次它可以避免对资
源的多重占用(如写文件的操作)。 最后, 它提供了对唯一实例的访问, 从而严格控制了用户访问它的方式以及时间。 

然而它也是有一些缺点的。 比如, 它没有接口, 不能被继承。 它没有抽象层, 因此它也很难被扩展。 因此, Singleton并不是任何情境下都适用的。 滥用Singleton模式会带来一些问题。 比如, 
用Singleton实现连接数据库的功能, 虽然避免了系统开销, 但是它也会导致共用该实例的对象过多导致溢出。 另一个隐忧是, Java提供自动垃圾回收机制, 如果Singleton实例长时间不被利用, 则
会被系统误认为是垃圾而被回收掉, 这样下次使用的时候会重新对该类进行初始化, 对于一些计数计时功能的类, 会导致状态的丢失。

Singleton的使用场景
===============

一般情况下, 我们在面试中遇到系统设计的问题尤其是OOD的问题的时候, 需要我们根据正确的场景选择正确的设计模式。 Singleton也是比较容易被考到的一个设计模式, 一般遇到以下场景的时候
基本是需要用Singleton没跑儿了:

* 系统只需要一个实例。 如配置文件, 序列号生成, 计时, 计数或者创建对象的开销过大的时候。
* 用户只被允许通过一个公共访问点访问这个类。

Singleton的实现
============

Singleton的主要实现方式有两种, 第一种是懒汉式, 就是啥时候用, 啥时候构建, 不要空耗内存; 第二种是饿汉式, 在第一次加载类到内存中的时候, 就初始化这个实例。 
 
以下这段代码是懒汉式的实现方法。 考虑到, 当多个线程调用getInstance( )方法时, 会创建多个实例, 导致线程不同步, 所以在多线程的环境下使用懒汉式的时候一般会加上synchronized关键字。

{% highlight java %}
public class Singleton{
    private static Singleton instance;
    
    private Singleton(){
    
    }
    
    public static synchronized Singleton getInstance(){
        if(instance == null){
            instance = new Singleton();
        }
        return instance;
    }
}
{% endhighlight %}

对于饿汉式来说, 由于实例是在类加载的时候就进行初始化的(通过static和final关键字完成), 所以它不存在线程不安全的问题。 以下是饿汉式的实现方法:

{% highlight java %}
public class Singleton{
    private static final Singleton instance = new Singleton();
    
    private Singleton(){
    
    }
    
    public static synchronized Singleton getInstance(){
        return instance;
    }
}
{% endhighlight %}

用例
===

在另一篇讲java properties的[博文]中, 我们在最后展示了一个, 自动发邮件的小程序。 在本文的最后, 我们对这个小程序进行了完善, 从而实现了本文开头提到的例子中的功能。
以下是这个例子的代码, 在这段代码中, 我们将email configuration设置为了Singleton模式, EmailConfiguration类保存了关于configuration的唯一实例, 并提供了getInstance( )方法, 对这个实例进行访问。 完整代码请点击[这里]。


{% highlight java %}
public class EmailConfiguration{
	private String path;
	
	private static EmailConfiguration configuration;
	
	private EmailConfiguration(){
		path = "config.properties";
	}
	
	public static synchronized EmailConfiguration getInstance(){
		if(configuration == null){
			configuration = new EmailConfiguration();
		}
		return configuration;
	}
	
	public String getConfigPath(){
		return path;
	}
}
{% endhighlight %}


[博文]: https://sophiesongge.github.io/learn/java/2016/11/18/java-properties.html
[这里]: https://github.com/sophiesongge/EmailNotification/blob/master/src/main/java/email/notification/EmailConfiguration.java