---
layout: post
title: 历史的行程
modified:
categories: life
excerpt:
tags: [history, Lisp, HTTP, XML, PHP]
image:
  feature:
share: true
date: 2016-07-27T19:10:29+08:00
---

实习了一个月，bash、PHP、Go、前端全摸了一遍…

啊，PHP…我实在是找不到词可以描述这个语言，写着写着感觉自己就精分了（

# 稍微说一说PHP

官方文档上称[PHP是一门通用脚本语言](http://php.net/manual/en/intro-whatis.php)，有这样的决心很好，但由于早期设计上的一些问题，以及这门语言几乎是以一种非常随意地把能吸收要素的全部强行吸收进去的方式在发展，整个语言里充满了不一致性、堆凑感与抽象失败。

#### 混乱的命名

**全大写的特殊变量名：**

- `$GLOBALS`
- `$HTTP_RAW_POST_DATA`

**不知为什么就开始全小写的特殊变量名：**

- `$php_errormsg`
- `$http_response_header`

**以及加了开头下划线的特殊变量名：**

- `$_SERVER`
- `$_COOKIE`

**有时候加有时候又不加下划线的函数名：**

- `gettype`, `get_resource_type`
- `stripcslashes`, `strip_tags`

**有时候用to有时候用2的函数名：**

- `strtoupper`, `strtolower`
- `bin2hex`, `hex2bin`

#### 误导性的命名与奇怪的内部处理

**HTTP：**

- 如果你把表单里的`x`变成`x[]`，你会发现你从`$_GET`或是`$_POST`里拿到的key仍然是`x`，只是这个时候`x`对应的value变成了一个array
- `$_GET`跟GET没有关系，从里面拿到的是[Query string](https://en.wikipedia.org/wiki/Query_string)格式化出来的东西
- `$_POST`跟POST也没有关系，从里面拿到的是body格式化出来的东西
- 你肯定会问，我想拿到原始的body怎么办呢，答案是用`file_get_contents("php://input")`
- 你甚至还可以`file_get_contents("compress.zlib://php://input")`来表示你想把输入的body解压一下
- 或者用`readfile("php://filter/read=string.toupper|string.rot13/resource=http://www.example.com")`来读一个网页并应用一些函数

**类型与比较：**

- 几个内置函数/特殊形式：
  - `isset`并不是检测变量有没有被定义，它检测的是变量是否被赋值且不为`NULL`，如果一个值被set为`NULL`，`isset`还是会返回给你假
  - `isset`并不是函数而是语言内置的特殊形式，与之输入一致时输出相反的函数是`is_null`
  - 与`isset`、`is_null`关系相似的还有`empty`与`boolval`，前者是一个特殊形式
  - 话说回来，都有`isset`和`boolval`了，为什么还要加一个`empty`呢？
  - 最后，就算你把这四个函数都用上，你也没有办法检测出一个变量是否已经赋值并且值为`NULL`
- 对相等的比较：
  - `array() == NULL`
  - `NULL == 0`
  - `"1一二三四五" == 1`
  - `"1e1" == 10`
  - `"abcde" == 0`
  - `"0" == false`，`"0" == 0`，`"0e1" == 0`但`"0e1" == true`
  - 最后好像就只能用`===`了
- 对不等的比较：
  - `NULL`似乎小于任何数，比如说`NULL < -12345`（
  - `array()`正常一点，`array() == 0`，`-12345 < array()`
  - 不要把上面两条跟*对相等的比较*下的两条连着看
  - 并没有`==<`或是`==>`这种先确定类型再比较的东西，我也不知道这要怎么办
    - 也许你可以把要比较的值先转成数字再转回去看看值是不是一样的，不一样就放弃比较（
    - 或者写个正则判断下是不是数字吧（

**Error与Exception：**

- `E_ALL`不包含`E_STRICT`
- try-catch不会捕捉到Error，除非你设置一个Error处理函数让这个函数扔一个Exception出来

**惊喜：**

- 总有一天能用上
  - `str_rot13`
- 它保存了什么？
  - array内看上去是数字的字符串索引实际上会被转换成数字索引，比如`"0"`实际上是`0`、`"1"`实际上是`1`、但`"01"`还是`"01"`
  - 具体来说，如果`$a = array("x", "y", "1" => "z", "01" => "a")`，实际上`$a`的值为`array(0 => "x", 1 => "z", "01" => "a")`
  - 当你访问`$a["0"]`时，你实际上是在访问`$a[0]`，但当你访问`$a["01"]`时，你访问的仍然是`$a["01"]`
- 它返回了什么？
  - `current`可以返回array中当前项的值，如果array已经遍历完了，那就返回`false`
  - 如果你的array里有`false`怎么办？
- 哇，我有好多函数可以用
  - `count`可以用来统计array的长度，它有一个可选参数用于让你标记是否递归统计
  - `array_walk`让你指定一个回调函数，它会对每一个array成员都调用一次这个回调函数，但是不支持递归调用
  - `array_walk_recursive`的功能跟`array_walk`类似，但他是递归调用版本
  - 全家桶：`array_intersect`、`array_intersect_assoc`、`array_intersect_uassoc`、`array_intersect_key`、`array_intersect_ukey`，干的事情只是有一些小区别，然而拆出了5个函数

#### 引用与…

PHP默认的变量传递均为传递值，赋值时用`&`可以传递引用，先是看上去比较正常的引用：

{% highlight php %}
$arr_a = ["x" => [1, 2], "y" => 3, "z" => 4];
$arr_b = $arr_a;
$arr_c = &$arr_a;

$arr_b["x"][0] = 11;
$arr_c["x"][1] = 12;
$arr_b["y"] = 13;
$arr_c["z"] = 14;

var_dump($arr_a); // ["x" => [ 1, 12], "y" =>  3, "z" => 14]
var_dump($arr_b); // ["x" => [11,  2], "y" => 13, "z" =>  4]
var_dump($arr_c); // ["x" => [ 1, 12], "y" =>  3, "z" => 14]
{% endhighlight %}

**引用与函数：**

想传递引用到函数内时需要在形参上加上`&`，调用时不用，如`$b = foo($a)`与`function foo(&$a)`。

但当你想返回引用时，你不得不在函数定义与调用处均加上`&`，比如`$b = &foo($a)`与`function &foo($a)`。

对此的解释是函数得愿意返回引用，调用处得愿意接收引用…

好像很有道理，那为什么调用处不能拒绝传入引用呢?

**引用与对象：**

{% highlight php %}
$obj_a = (object)["x" => [1, 2], "y" => 3, "z" => 4];
$obj_b = $obj_a;
$obj_c = &$obj_a;

$obj_b->x[0] = 11;
$obj_c->x[1] = 12;
$obj_b->y = 13;
$obj_c->z = 14;

var_dump($obj_a); // (object)["x" => [11, 12], "y" => 13, "z" => 14]
var_dump($obj_b); // (object)["x" => [11, 12], "y" => 13, "z" => 14]
var_dump($obj_c); // (object)["x" => [11, 12], "y" => 13, "z" => 14]
{% endhighlight %}

看上去对对象用`=`也是在传引用（

对此的解释是，PHP是写时复制的，写object的属性不算写object所以没有复制…

也许很有道理吧，只要别跟array连着看…

#### 别扭的函数式

后来PHP也增加了一些函数式编程的东西，于是我们开开心心地写了个map：

{% highlight php %}
$x = 100;
$arr_a = [1, 2, 3, 4];
$arr_b = array_map(function ($item) {
    return $item + $x;
}, $arr_a);
{% endhighlight %}

然而这么写是跑不起来的（

问题在于PHP既不是词法作用域也不是动态作用域，PHP只有本地作用域与全局作用域（好吧还有一个所谓的超全局作用域，但这东西看起来更像是语言的内置特殊形式）

PHP为了解决这个问题，提供了一个`use`关键字用在匿名函数定义处来给函数打洞，把变量传进去：

{% highlight php %}
$x = 100;
$arr_a = [1, 2, 3, 4];
$arr_b = array_map(function ($item) use ($x) {
    return $item + $x;
}, $arr_a);
{% endhighlight %}

只是一个简单的匿名函数似乎还好，但当你试图嵌套多层匿名函数时，你恐怕得从上到下把每个函数都打个大洞才行了…

顺便一提，如果你在类的方法里使用匿名函数，它会被隐式绑定上`$this`变量（比如这里会指向Foo的实例）…

{% highlight php %}
class Foo {
    public function bar() {
        return function () {
            var_dump($this);
        };
    }
}
{% endhighlight %}

甚至你还可以对匿名函数使用bindTo来改变这个`$this`指向的实例…

过了一个版本号之后，又冒出来了一个静态匿名函数，不会被自动绑定上`$this`

{% highlight php %}
class Foo {
    public function bar() {
        return static function () {
        };
    }
}
{% endhighlight %}

迷得不行…

好了好了不说了，关于PHP的坑先写到这里吧:(

然而纵使有这么多坑，PHP的使用率（[TIOBE](http://www.tiobe.com/tiobe-index/php/)、[IEEE](http://spectrum.ieee.org/computing/software/the-2016-top-programming-languages)）还是很高（

作为模板语言诞生的PHP可能确实比较方便吧，考虑到那些所谓的一键搭建的环境，即使放到今天恐怕也是这样。加上早期的网站功能往往并不复杂，许多刚起步阶段的项目就直接用了起来。到后来项目越来越大，一部分早期代码的编写者可能已经不在组里了，加上需要不断开发新需求，也没什么人有动力去做大的改变，只好在原先的基础上继续写下去（，就这样陷入了路径依赖的死循环里。

随之而来的，还有工作机会的增多与新开发者的涌入。而PHP本身也顺应着这些项目在不断地变化，以至于到了今天的这个状态…

你甚至还能在这个原先是模板语言的社区里找到新的模板语言的轮子，[在模板语言里写模板语言](http://www.smarty.net/)…

可能PHP与相关的社区、项目会一直这么绝望地膨胀下去吧。

# 另一种选择

说回Web与模板语言。

我最开始接触到的模板语言是[Jinja](http://jinja.pocoo.org/)、[mako](http://www.makotemplates.org/)之类，Jinja大概长这样：

{% highlight Jinja %}
<ul>
{% for user in users %}
  <li><a href="{{ user.url }}">{{ user.username }}</a></li>
{% endfor %}
</ul>
{% endhighlight %}

可能是先入为主的原因，我一直以为模板语言就是像上面这样了——用一堆半成品前端字符串配上奇怪的特殊符号来格式化出另一堆前端**字符串**。

直到我后来看到了[这个](http://franz.com/support/documentation/current/doc/aserve/aserve.html#c-location-authorizer)：

{% highlight CommonLisp %}
(publish :path "/local-secret-auth"
    :content-type "text/html"
    :authorizer (make-instance 'location-authorizer
                         :patterns '((:accept "127.0" 8)
                                     (:accept "tiger.franz.com")
                                     :deny))

    :function
    #'(lambda (req ent)
        (with-http-response (req ent)
           (with-http-body (req ent)
               (html (:head (:title "Secret page"))
                     (:body (:b "Congratulations. ")
                       "You made it to the secret page"))))))
{% endhighlight %}

还有[这个](https://common-lisp.net/project/xhtmlambda/)：

{% highlight CommonLisp %}
(with-html-syntax-output (*standard-output* :syntax :standard :print-pretty t)
                  (body (:style "color: red")
                        (p ()
                           "Some text here"
                           (ul () (loop for i below 5
                                        collect (li () (format nil "Line ~D" i)))))))

;;  <body style="color: red">
;;    <p>
;;      Some text here
;;      <ul>
;;        <li>
;;          Line 0
;;        </li>
;;        <li>
;;          Line 1
;;        </li>
;;        <li>
;;          Line 2
;;        </li>
;;        <li>
;;          Line 3
;;        </li>
;;        <li>
;;          Line 4
;;        </li>
;;      </ul>
;;    </p>
;;  </body> 
{% endhighlight %}

直接从代码到代码，而不是从字符串到字符串。同时你也不需要[emmet](http://www.emmet.io/)这类工具帮你生成冗长的HTML代码了。

说回来，对于HTML这种高度结构化的语言，为什么当时会先想着去当字符串处理而不是当成树去处理呢…

甚至更进一步的说，HTML、XML这种结构完全可以转化成更简洁的[S表达式](https://en.wikipedia.org/wiki/S-expression)。S表达式出现得要早得多，但XML这种更加冗余的结构却更流行（

如果当时直接用S表达式表达HTML，浏览器里带上个Scheme解释器而不是十天造出来的JS，恐怕现在就不会有JSX这类东西，也没什么统一前后端语言的问题了（

[![AlienTechnology]({{ site.url }}/images/lisplogo_fancy_256.png)](http://www.lisperati.com/logo.html)

可惜历史并不是这样的（

<del>一种技术的发展啊，当然要靠自我奋斗，但是也要考虑到历史的行程</del>
