---
layout: post
title: SICP 第二章笔记 符号数据
modified:
categories: notes
excerpt:
tags: [SICP, Scheme, Lisp]
image:
  feature:
share: true
date: 2016-07-24T11:41:18+08:00
---

这一小节东西并不多，主要是一些帮助理解的实例。不过第一次接触符号数据可能会觉得有点绕（

还是惯例的[习题答案](https://github.com/hrl/SICP/tree/master/ch2)

* ToC
{:toc}

# 数据抽象导引

见：[数据抽象导引]({% post_url /notes/2016-03-24-sicp-chapter-2-notes-1 %})

# 层次性数据和闭包性质

见：[层次性数据和闭包性质]({% post_url /notes/2016-07-23-sicp-chapter-2-notes-2 %})

# 符号数据

先说点别的。

自然语言里，`说出你的名字`和`说出"你的名字"`表达了两种截然不同的意思。被加上引号的"你的名字"表达的是这个单词本身，而不是它的意义。

#### 引号

回到Scheme，为了描述出符号本身，将符号与符号的意义区分开来，我们就需要一种加*引号*的能力。

举例来说，如果你想得到表`(a b)`，你不能使用`(list a b)`，因为这样会构造出a和b的值的表，而不是a和b这两个符号本身的表。

为了得到被引用的a，我们使用`(quote a)`这一特殊形式，为了方便简写为`'a`。`quote`也可用于复合对象，如`(quote (a b))`或`'(a b)`。

{% highlight scheme %}
(define a 1)
(define b 2)

(list a b)    ; (1 2)
(list 'a b)   ; (a 2)
(list 'a 'b)  ; (a b)
(quote (a b)) ; (a b)
'(a b)        ; (a b)

(car '(a b)) ; a
(cdr '(a b)) ; (b)
{% endhighlight %}

基本过程`eq?`可用于判断两个符号参数是否为同一个符号。

三个实例就不在这里赘述了…直接[看书吧](https://mitpress.mit.edu/sicp/full-text/book/book-Z-H-16.html#%_sec_2.3.2)。

# 抽象数据的多重表示

见：[抽象数据的多重表示]({% post_url /notes/2016-07-26-sicp-chapter-2-notes-4 %})

# 带有通用型操作的系统

待续
