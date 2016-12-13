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

Exception又分为两类, 一类叫RuntimeException, 另一类是非RuntimeException -_-b 

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