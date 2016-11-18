---
layout: post
title:  "初识Java Properties"
date:   2016-11-18 15:00:00 +0200
categories: learn java
use_math: true
---
同学们好, 今天我们要讨论的话题是Java Properties。

什么是Java Properties
====================

在我们写程序的时候, 有些参数是需要经常改变的。而这些改变是不可预知的。 如: 我写了一段程序用来给指定的邮箱发邮件。 但是这个邮件由哪个邮箱发出, 这个邮箱的smtp服务器是什么, 它的密码是神马????等等一些列参数我希望通过用户来进行配置。
为了保证模块的通用性, 这些信息是不能写死在程序里面的。 通常的做法是通过配置文件来解决。 不同的语言支持的配置文件类型是不同的, 如:

* Python --- .ini
* Java   --- .properties

于是乎, 我们今天的主角登场了。 JDK内置的java.util.Properties类为我们操作.properties文件提供了便利。这个类继承自HashTable, 所以, 不可避免的, Properties类其实就是一个哈希表。 

.properties文件的格式
====================
上文提到, Properties类其实是一个哈希表, 所以.properties文件不可避免的是<key, value>对的形式。 让我们看一个例子, 我们将以下这段代码存储在一个名为config.properties文件中:

{% highlight xml %}
mail.smtp.starttls.enable=true
mail.smtp.port=587
mail.smtp.auth=true
mail.smtp.host=smtp.gmail.com
mail.smtp.username=example@username.com
mail.smtp.password=password
{% endhighlight %}

这就是一个简单的.properties文件。 在这个文件中, "="左边的为key值, key值用来作为我们写的程序中的变量; "="右边的为相应的value值, value值为我们根据实际情况的配置。 在程序中, 我们就可以像操作哈希表一样, 通过key值来得到相应的value, 而进行配置的时候, 我们只需要更改相应的value值。

Properties类的使用
==============

为了使用Properties类, 我们首先需要创建一个Properties对象。 Java提供了两个构造函数, 他们的本质是相同的, 我们一般都用默认的构建函数来创建:

{% highlight java %}
Properties props = new Properties();
{% endhighlight %}

Properties类提供了很多方法, 最常用的是getProperty, load, setProperty, store和clear方法。

- getProperty$\($String key$\)$用来得到key值相应的value
- load$\($InputStream inStream$\)$用来从输入流中读取属性列表
- setProperties$\($String key, String value$\)$用来设定相应的key-value对
- store$\($OutputStream outStream, String comment$\)$与load相反, 它用来将相应的属性列表存储到制定文件中
- clear\(\)清除所有装在过的key-value对

