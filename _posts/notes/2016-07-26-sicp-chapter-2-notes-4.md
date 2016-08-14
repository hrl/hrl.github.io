---
layout: post
title: SICP 第二章笔记 抽象数据的多重表示
modified:
categories: notes
excerpt:
tags: [SICP, Scheme, Lisp]
image:
  feature:
share: true
date: 2016-07-26T10:38:49+08:00
---

惯例的[习题答案](https://github.com/hrl/SICP/tree/master/ch2)

* ToC
{:toc}

# 数据抽象导引

见：[数据抽象导引]({% post_url /notes/2016-03-24-sicp-chapter-2-notes-1 %})

# 层次性数据和闭包性质

见：[层次性数据和闭包性质]({% post_url /notes/2016-07-23-sicp-chapter-2-notes-2 %})

# 符号数据

见：[符号数据]({% post_url /notes/2016-07-24-sicp-chapter-2-notes-3 %})

# 抽象数据的多重表示

数据抽象使我们能够通过隔离数据的实现与使用，来将一个复杂的大任务分割为许多简单的小任务。由于外界不必关心数据的内部表示，各个系统都可以在这一确定的抽象上同步开发，不必互相等待。数据内部的表示与实现也可以自由变更。

但数据抽象的威力不仅仅在于允许数据表示的变化与实现的延缓，更在于它使我们能在一个系统内支持多种不同的数据表示。有的数据本身就没有确定的“基本表示”，比如复数可以通过实部与虚部表示，也可以通过模与幅角表示。而在复杂的系统中，由于开发者的流动与需求的变化，数据表示的不统一也几乎是不可避免的事情。

这一节主要讲的就是如何构造能够在多种表示上操作的*通用型过程*，以提供数据抽象。

以复数为例，我们对外至少需要`real-part`、`imag-part`、`magnitude`、`angle`以提供基本的获取值的操作，同时实现过程`make-from-real-imag`与`make-from-mag-ang`来以不同的表示构造复数。

![通用型复数算术系统的结构]({{ site.url }}/images/SICP/ch2-Z-G-62.gif)  
图1 通用型复数算术系统的结构[^1]

在内部，我们可以用对数据加上标签的方式来区分数据的类型，比如`'data`可以加上标签变成`(cons 'tag 'data)`，并提供`attach-tag`用于加标签、`type-tag`用于取出标签、`contents`用于取出数据。

有了以上这些东西我们就可以开始具体实现了。

#### 显示分派

假定复数的直角坐标表示已经有了基本的实现函数，比如`real-part-rectangular`，相应的极坐标表示的函数为`real-part-polar`，并提供两个判断函数来根据标签判断数据类型，定义为`rectangular?`与`polar?`

那么最简单的显示分派的过程可以直接实现为下面的样子：

{% highlight scheme %}
(define (real-part z)
  (cond ((rectangular? z)
         (real-part-rectangular (contents z)))
        ((polar? z)
         (real-part-polar (contents z)))
        (else
         (error "Unknown type -- REAL-PART" z))))
{% endhighlight %}

这样实现很直观，但缺点很多。首先是你每添加一种类型，都得去修改一次对应的通用型过程；其次是还存在潜在的命名冲突问题。当系统中类型较少时可能还好，一旦类型多起来系统就会变得难以维护。

这些问题导致这种方法不具有*可加性*。

#### 数据导向

为了解决上面这些问题，我们需要一种能将系统设计进一步模块化的方法。

在之前的方法中，导致上面问题的根源在于，我们直接在代码里判断了类型然后做了显示的分派。

但我们可以通过另一种方式来得到具体过程，比如在这里，实际上我们做的事情是从一个`(通用过程 类型)`对得到一个`具体过程`，这个过程很像查找一个表格：

![复数系统的操作表]({{ site.url }}/images/SICP/ch2-Z-G-63.gif)  
图2 复数系统的操作表[^2]

数据导向的程序设计就是一种使程序能直接利用这种表格工作的程序设计技术。在通用过程中，我们用操作名与参数类型的组合去查表格，得到应当调用的具体过程，最后将这一过程应用到参数内容上。

而为了实现它，我们只需要两个处理表格的过程——`(put <op> <type> <item>)`与`(get <op> <type>)`——来帮我们存表与查表。

然后我们可以实现一个通用过程，以操作名与参数来访问表格，并应用参数到具体的过程上：

{% highlight scheme %}
(define (apply-generic op . args)
  (let ((type-tags (map type-tag args)))
    (let ((proc (get op type-tags)))
      (if proc
          (apply proc (map contents args))
          (error
           "No method for these types -- APPLY-GENERIC"
           (list op type-tags))))))

(define (real-part z) (apply-generic 'real-part z))
{% endhighlight %}

之后添加新类型就很容易了，以直角坐标表示为例：

{% highlight scheme %}
(define (install-rectangular-package)
  ;; internal procedures
  (define (real-part z) (car z))
  ;; ... others
  ;; interface to the rest of the system
  (define (tag x) (attach-tag 'rectangular x))
  (put 'real-part '(rectangular) real-part)
  (put 'make-from-real-imag 'rectangular
       (lambda (x y) (tag (make-from-real-imag x y))))
  ;; ... others
  'done)
{% endhighlight %}

完全不用修改系统的其他部分，也不用考虑命名冲突的问题。

这很像某些语言内提供的函数重载功能。

#### 消息传递

上面这种解决显示分派导致的问题的方法，实际上是将图2中的操作表按行拆分，每一行都是一个“智能操作”，能根据输入参数找到对应的具体操作。

解决这些问题的另一种方法是，将图2中的操作表按列拆分，返回一种“智能数据对象”，以自行应对各种各样的操作。

比如说，对于直角坐标表示，我们可以这么定义：

{% highlight scheme %}
(define (make-from-real-imag x y)
  (define (dispatch op)
    (cond ((eq? op 'real-part) x)
          ;; ... others
          (else
           (error "Unknown op -- MAKR-FROM-REAL-IMAG" op))))
  dispatch)
{% endhighlight %}

与之对应的`apply-generic`只需要简单地传递进去一个操作名即可：

{% highlight scheme %}
(define (apply-generic op arg) (arg op))
{% endhighlight %}

<del>再次发明了面向对象（逃</del>

# 带有通用型操作的系统

待续

#### References

[^1]: https://mitpress.mit.edu/sicp/full-text/book/ch2-Z-G-62.gif  
[^2]: https://mitpress.mit.edu/sicp/full-text/book/ch2-Z-G-63.gif  
