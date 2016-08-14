---
layout: post
title: SICP 第二章笔记 数据抽象导引
modified:
categories: notes
excerpt:
tags: [SICP, Scheme, Lisp]
image:
  feature:
share: true
date: 2016-03-24T21:59:47+08:00
---

因为这样那样的事情…这篇总结拖了两周才写…惭愧（

第二章大部分的[习题答案](https://github.com/hrl/SICP/tree/master/ch2)，图形那一章没搭环境题目就跳了，再往后的章节因为暂时没有put之类的函数支持没有实际跑过（

这一章内容比较多，会拆成几个章节来写_(:з」∠)_

* ToC
{:toc}

# 构造数据抽象

第一章的重点是计算过程，以及过程在程序中所扮演的角色。通过对过程的组合我们可以形成复合过程，同样的，通过对数据的组合我们可以形成*复合数据*。

我们需要这些复合数据的理由同需要复合过程的理由一样：

- 提升设计程序时的概念层次
- 提高设计的模块性
- 增强语言的表达能力

# 数据抽象导引

参考上一章的过程抽象概念，过程抽象定义了过程的使用方式，同时将这一过程的使用方式与该过程的实现细节分离。

针对复合数据的类似概念被称为*数据抽象*，为了实现数据抽象，我们需要：

- 隐藏起复合数据的实现细节
- 提供*选择函数*与*构造函数*作为接口，选择函数提取出复合数据内的更基本数据，构造函数提供组合起复合数据的方法

随后，我们需要使 使用复合数据对象的程序的操作 在抽象层面上完成，而不对具体使用的数据做任何多余的假设。

#### 抽象屏障

通过复合数据所定义的接口与一组操作函数，使用数据抽象的程序与实现数据抽象的程序可以隔离开来，系统可以分为相互隔离不同的层次，这种隔离被称为抽象屏障。

抽象屏障使得数据使用部分与实现部分可以各自独立变化，让程序得以模块化，便于维护与修改。

#### 序对

Scheme提供了称为*序对*的复合结构，这种结构由`cons`定义，由`car`与`cdr`取出内部数据

{% highlight scheme %}
(define x (cons 1 2))
(car x) ; 1
(cdr x) ; 2
(define y (cons 3 4))
(define z (cons x y))
(car (car z)) ; 1
(car (cdr z)) ; 3
{% endhighlight %}

#### 数据意味着什么

一般而言，我们总可以将数据定义为一组适当的选择函数和构造函数，以及为使这些过程成为一套合法表示，它们所必须满足的一组特定条件。

考虑序对的概念，如果我们能实现`cons`、`car`、`cdr`三个操作，使得`(define z (cons x y))`后，`(car z)`就是`x`，`(cdr z)`就是`y`，那么我们就可以说我们实现了Scheme里原有的序对基础。

然而，我们可以不用任何数据结构，只使用过程便实现序对：
{% highlight scheme %}
(define (cons x y)
  (define (dispatch m)
    (cond ((= m 0) x)
          ((= m 1) y)
          (else (error "Argument not 0 or 1 -- CONS" m))))
  dispatch)

(define (car z) (z 0))

(define (cdr z) (z 1))
{% endhighlight %}
这里由`cons`返回的是一个过程`dispatch`而不是“真正的”数据结构，它通过调用的参数来决定返回的值是x或y，只是通过调用这里定义的`cons`、`car`、`cdr`的话，我们没有办法将这一实现与“真正的”数据结构区分开。

进一步地，在一个可以对过程做各种操作的语言里，我们完全可以没有数（至少是非负整数）：
{% highlight scheme %}
(define zero (lambda (f) (lambda (x) x)))

(define (add-1 n)
  (lambda (f) (lambda (x) (f ((n f) x)))))

; (add-1 zero)
(define one (lambda (f) (lambda (x) (f x))))
; (add-1 one)
(define two (lambda (f) (lambda (x) (f (f x)))))

(define (add a b)
  (lambda (f) (lambda (x) ((a f) ((b f) x)))))
{% endhighlight %}
这一表示形式称为*Church计数*。

对数的加被表示为了函数的调用，可以通过下面的过程将之转换为数：
{% highlight scheme %}
(define (inc n) (+ n 1))

(define (church-numeral->int church-numeral)
  ((church-numeral inc) 0))

(church-numeral->int (add one two)) ; 3
{% endhighlight %}

数据的过程性表示将在我们的程序设计宝库里扮演一种核心角色，有关的程序设计风格通常称为*消息传递*。

# 层次性数据和闭包性质

见：[层次性数据和闭包性质]({% post_url /notes/2016-07-23-sicp-chapter-2-notes-2 %})

# 符号数据

见：[符号数据]({% post_url /notes/2016-07-24-sicp-chapter-2-notes-3 %})

# 抽象数据的多重表示

见：[抽象数据的多重表示]({% post_url /notes/2016-07-26-sicp-chapter-2-notes-4 %})

# 带有通用型操作的系统

待续
