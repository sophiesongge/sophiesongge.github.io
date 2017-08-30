---
layout: post
title:  "Spring笔记之依赖注入和控制反转"
date:   2017-08-22 17:36:00 +0200
categories: backend
use_math: false
---

Spring 最认同的技术是控制反转Inversion of Control（IoC）的依赖注入dependency injection（DI）模式。控制反转是一个通用的概念，它可以用许多不同的方式去表达，依赖注入仅仅是控制反转的一个具体的例子。

依赖注入（Dependency Injection）
=========

* 作用：

当编写一个复杂的Java程序时，应该让这个程序尽可能独立于其他的Java类来增加这些类的可重用性。这样一来，进行单元测试的时候，也可以使它们独立于其他类进行测试。

Dependency Injection有助于将这些类粘合在一起，并且在同一时间让它们保持独立。


* 依赖：

在Class A中有Class B的实例，则称Class A对Class B有一个依赖。例如：

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

实现依赖注入有很多种方式，在Java中，最常使用的方式是通过注解来实现。通过在字段的声明前添加@Inject注解进行标记，来实现依赖对象的自动注入。例如：

{% highlight java %}
public class Human {
    ...
    @Inject Father father;
    ...
    public Human() {
    }
}
{% endhighlight %}

但是实际上，如果只写了一个@Inject注解，Father对象并不会被自动注入，因为它还需要依赖一个注入框架，比如Spring。

控制反转
=======

为了更好的理解控制反转的作用，让我们看一个现实生活中的例子。某公司作为一个电器制造商需要把自己的商品分销到全球各地。经过研究他们发现，不同的分销渠道有不同的规则，于是他们派出了不同的销售代表来执行不同的规则。
后来，销售渠道越来越多，他们发现，每增加一个销售渠道就要新增一批人和一个新的流程，因为每个渠道都依赖于（耦合）它相应的规则。

后来，系统过于庞大，于是他们决定制定业务标准，开发分销信息化系统，只有符合这个标准的渠道商才能成为该公司的分销商。这样，分销的过程就由先前的该公司适应分销商，变成了分销商反过来依赖自己的标准，反转了控制，倒置了依赖。

如果我们把该公司和各分销商当做软件工程中的对象，那么该公司开发的分销信息化系统则被称为IoC容器。在没有引入分销信息化系统（IoC容器）之前，分销商就想齿轮一样，增加一个齿轮就要增加多钟依赖在其他齿轮上，最终的结果就是导致
系统越来越复杂。而引入分销系统后，各分销商只依赖于分销系统，可以很方便的增加和删除齿轮。


在一个软件系统中，各对象就像齿轮一样，是相互耦合协同工作的。在一个强耦合的系统中，如果一个零件不能正常工作了，整个系统就崩溃了。然而，对象之间的耦合关系是不可避免的也是非常必要的，这是协同工作的基础。
随着应用系统规模的庞大，对象之间的依赖关系也变得越来越复杂，经常会出现对象之间存在多重依赖的关系。

为了解决对象间耦合度过高的问题，控制反转的理论就应运而生了。它被设计来实现对象之间的解耦。控制反转是一种面向对象的设计原则，它的基本思想是：借助于"第三方"实现具有依赖关系的对象之间的解耦。

由"第三方"（IoC容器）来代替强耦合的各个齿轮。这样，情况就由之前的齿轮间的依赖转换成了齿轮与"第三方"的依赖，每一个齿轮都依赖"第三方"，但是齿轮与齿轮之间是独立的。在这里IoC容器起到了"粘合剂"的作用，把系统中的
所有对象粘合在了一起。

在引入IoC容器前，如果对象A依赖于对象B，那么对象A在初始化或者运行的过程中，需自己主动去创建对象B或使用已经被创建的对象B。但是无论是创建还是使用，控制权都在A手上。
而在引入IoC容器之后，情况完全改变了，由于IoC容器的加入，对象A与对象B之间失去了直接的联系， 所以，当对象A需要用到对象B的时候，IoC容器会主动创建一个对象B注入到对象A需要的地方。
在后者中，对象A获得依赖对象B的过程，由一个主动行为变成了一个被动行为，控制权颠倒了过来，所以这个过程被称为"控制反转"。


控制反转和依赖注入的关系
====================

简单的说，控制反转是一种思想，依赖注入是一种设计模式。

IoC框架用依赖注入来实现控制反转。当然控制反转还有其他的实现方式，所以，不能讲控制反转等同于依赖注入。

Spring中的依赖注入
================

让我们看一下这个例子：

{% highlight java %}
class MovieLister{
    ...
    private MovieFinder finder;
    public void setFinder(MovieFinder finder) {
        this.finder = finder;
    }
    ...
}
class ColonMovieFinder{
    ...
    private String filename;
    public void setFilename(String filename) {
        this.filename = filename;
    }
    ...
}
{% endhighlight %}

在这里我们先定义了两个类，它们都使用了依赖注入的方式从外部传入依赖，而不是自己创建依赖。问题是，谁创建了finder和filename，并把finder传给了MovieLister，把filename传给了ColonMovieFinder？答案是：Spring IoC容器。
对象的依赖由对象的构造函数的参数、工厂方法的参数和属性来定义，然后由Spring IoC容器在创建bean的时候将依赖注入对象。

Spring IoC的基础包是：org.springframework.beans 和 org.springframework.context

BeanFactory接口就是所谓的Spring IoC容器，它可以管理任何类型的对象。ApplicationContext是BeanFactory的子接口，它添加了更丰富的特性。Spring IoC容器通过读取配置文件来实例化bean并装配bean之间的依赖关系。一旦Spring IoC容器的初始化完成，我们就可以使用它来管理所有的bean了。
Spring中的bean是指被Spring IoC容器管理的各种对象，bean由Spring IoC容器负责实例化、装配、管理，bean本身以及bean之间的互相依赖由配置元数据定义。配置元数据也就是Spring的配置文件，它的作用是告诉Spring IoC容器应该如何实例化、装配和管理bean。

配置文件可以通过xml、java代码和注解等形式来进行定义。

假设我们有如下一个bean：

{% highlight java %}
public class HelloWorld {
    public void sayHello(String name) {
        System.out.println("Hello " + name);
    }
}
{% endhighlight %}

* 基于xml方式来定义bean，需要在services.xml中写入：
{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="helloWorldBean" class="study.spring.bean.HelloWorld">
    </bean>
</beans>
{% endhighlight %}

其中bean id是这个bean的唯一标识，可以用这个id在其他bean中引用这个bean，而class用来指定该bean的全类名。

* 基于java代码和注解的 bean 定义：

{% highlight java %}
@Configuration
public class HelloWorldConfig {
    @Bean(name="helloWorldBean")
    public HelloWorld helloWorld() {
        return new HelloWorld();
    }
}
{% endhighlight %}

在这里，我们使用@Configuration注解来定义配置类，使用@Bean注解来定义bean，使用@Bean的name属性来定义bean id

实例化Spring IoC容器非常简单，只需要告诉Spring IoC容器配置文件的位置即可。

* 实例化基于xml配置的Spring IoC容器：

{% highlight java %}
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml");
{% endhighlight %}

* 实例化基于java代码和注解配置的Spring IoC容器：

{% highlight java %}
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(HelloWorldConfig.class);
{% endhighlight %}

同样，我们可以从Spring IoC容器中获取bean：
{% highlight java %}
public static void main(String[] args) {
    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(HelloWorldConfig.class);
    HelloWorld helloWorld = applicationContext.getBean(HelloWorld.class);
    helloWorld.sayHello("test");
}
{% endhighlight %}

通过上面这个简单的例子我们明白了Spring IoC的配置过程，当然，我个人认为，上面这个简单的例子是没有必要使用bean来返回实例的，可以简单的初始化一个实例，达到的效果和使用IoC容器管理几乎是一样的（如果你不需要单例模式）。

真正需要Spring IoC来进行管理的是像段首这样有依赖的类。回到段首我们举的MovieLister和ColonMovieFinder的例子中，我们同样可以使用xml和代码注解两种方式配置这个IoC容器。xml配置的方式如下：
{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<beans>
    <bean id="MovieLister" class="spring.MovieLister">
        <property name="finder">
            <ref local="MovieFinder"/>
        </property>
    </bean>
    <bean id="MovieFinder" class="spring.ColonMovieFinder">
        <property name="filename">
            <value>movies1.txt</value>
        </property>
    </bean>
</beans>
{% endhighlight %}

每个bean代表一个对象的实例（默认是[Singleton模式]，即在程序的生命周期内，所有的对象都只有一个实例，进行重复使用），通过配置bean，IoC容器在启动的时候会根据配置生成bean实例。这里Spring IoC容器会根据配置创建MovieFinder，
在运行的时候把MovieFinder赋值给MovieLister的finder属性，完成依赖注入的过程。

在对这段代码进行测试时，需要首先根据配置生成Spring IoC容器（既：ApplicationContext），然后从容器中获取MovieLister的实例。
{% highlight java %}
public void testWithSpring() throws Exception {
    ApplicationContext apc = new FileSystemXmlApplicationContext("services.xml");
    MovieLister lister = apc.getBean("MovieLister");
    Movie[] movies = lister.moviesDirectedBy("Yimou Zhang");
    asertEquals("Hong Gaoliang", movies[0].getTitle());
}
{% endhighlight %}

具体配置语法请参考Spring Documentation的[Dependencies]章节。


[Singleton模式]: https://sophiesongge.github.io/design/pattern/2016/11/18/singleton-pattern.html
[Dependencies]: https://docs.spring.io/spring/docs/current/spring-framework-reference/html/beans.html#beans-dependencies