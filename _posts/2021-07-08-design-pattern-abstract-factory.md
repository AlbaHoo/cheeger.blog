---
layout: post
title:  "[DP] Factory Pattern"
lang: 中文
category: develop
tags: design-pattern
comments: true
---

# 介绍
准备做一起设计模型的介绍，主要的参考是一本书：Design Patterns Elements of Reusable Object-Oriented Software
作者是4位作者，Erich，Richard，Ralph，John，也叫四人帮, GoF(Gang of Four)。

所有的模型都是基于一个目的：
> Coding to interfaces, not implementation

# 第一期 工厂模型：Factory Pattern

## 背景介绍

    假设你是一个老板，你要翻译很多份文件，怎么处理。

    工厂模式的目的就是统一标准，老板只需要和经理Factory Class交流，再由经理来进行重新分配工作。


1. 招募Class作为团队成员。
2. 组建一个工厂模式的团队
3. 团队领导就是Factory Class
4. 成员就是Member Class

我们来编一个短的话剧吧，出场人物，

    老板：只会中文
    小F：经理，了解小黑和小红
    小黑：会(英语)和[法语]
    小红：会中文和(英语)

低效率的团队：

    老板：小黑, 翻译一份法语文件
    小黑：(你在说什么)
    小黑：(小红，老板说什么)
    小红：(他让你翻译法语文件)
    小黑：(你和他说，我明白了)
    小红：他知道了
    老板：葡萄牙语谁会，去招人吧。
    小红：谁去，以后感觉说话好乱。
    老板： #\%@#&*。。。

工厂模式的团队：

    老板: 小F，翻译法语文件。
    小F把文件递给小黑
    老板：小F，翻译一项英文文件。
    小F把文件递给了小红。
    老板：葡萄牙语谁会，去招人吧。
    小F招了一个会葡萄牙语言的，并且和他约定好，以后把文件递给他就直接翻译成英文不要说别的。
    老板：小F，翻译葡萄牙文件。
    小F把文件递给了新招的人。

# 具体的例子

写一个画板程序，这个程序可以画圆形，长方形，三角形， 准备工作：
1. 给圆形，长方形，三角形各写一个class。
2. 不同形状画法不一样，每个class注入画的方法。
3. 写一个画版Canvas的class，分别给给圆形，长方形，三角形创建一个对象，然后使用这三个工具画画吧。

# Factory 方案

![Factory]({{"/design_pattern/factory.jpg" | prepend: site.image_root }})

# 分析

1. Canvas的逻辑已经足够复杂了，不同形状画法的逻辑被完全的抽离，这样可以让Canvas这个class更加关注它自己的工作，代码管理更加有效。比如需要画圆的时候，调用shapeFactory.getShape('circle').draw()即可。

2. 添加新的形状（五角星），需要写一个新的五角星的class，然后在ShapeFactory里面注册这个class。

3. 便于扩展，方便管理，画图软件里的画笔类型可以有成百上千种，添加新的类型可以基本不更改Canvas的代码。

4. Factory的扩展Abstract Factory Pattern

    沿袭Factory Pattern的思路，形状有shapeFactory，颜色有ColorFactory，画笔有penFactory等等，这么多Factory本身也是同类型的东西，但是因为Factory的功能是不同的，用继承的思路并没有什么优势，所以一般的处理方法是通过添加一个抽象的class，其他的思路不变。

        abstract class factory
          abstract Shape getShape;
          abstract Color getColor;
          abstract Pen getPen;
