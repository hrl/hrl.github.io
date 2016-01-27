---
layout: post
title: OverTheWire Natas 0-27
modified:
categories: writeup
excerpt:
tags: []
image:
  feature:
share: true
date: 2016-01-26T16:24:00+08:00
---

* ToC
{:toc}

# Level0

查看页面源码，可以看到

{% gist hrl/aaa54d86d8e47242b4c7 %}

# Level1

同Level0,可从菜单内打开开发者工具查看源码

{% gist hrl/c1373e0b0630de86a294 %}

# Level2

源码内原来放密码的地方变成了一张图

{% gist hrl/fe5ea2882076e67221fe %}

图片本身没有什么特殊的，但是访问/files可以看到一个users.txt，里面存着natas3的密码

```
sJIJNW6ucpu6HPZ1ZAchaDtwd7oGrD14
```

# Level3

这次信息变成了

{% gist hrl/49993f0a632c84b8ebb9 %}

注意后一句话，查看/robots.txt可以看到一个被禁止的目录

{% gist hrl/c0827b90922b5c029f23 %}

进去只有一个users.txt，里面存着natas4的密码

```
Z9tkRkWmpt9Qr7XrR5jWRkgOU901swEZ
```

# Level4

提示信息

{% gist hrl/564c9d621b303794cdcf %}

修改Header`Referer: http://natas5.natas.labs.overthewire.org/`之后访问，得到natas5的密码

```
iX6IOfmpN7AYOQGPwtn3fXpbaJVJcHfq
```

# Level5

提示信息

{% gist hrl/643b9f55f2090c0e5ba5 %}

查看Cookie可以看到`loggedin=0`，修改成1后访问，得到natas6的密码

```
aGoY4q2Dc6MgDq4oL4YtoKtyAg9PeHa1
```

# Level6

PHP源码

{% gist hrl/d8b16f4c3cbeb5bb12fb %}

于是访问`/includes/secret.inc`，得到`$secret = "FOEIUWGHFEEUHOFUOIU";`

提交后得到natas7的密码

```
7z3hEENjQtflzgnT29q7wAvMNfZdh0i9
```

# Level7

提示信息

{% gist hrl/33a47d19678b2f15264f %}

注意到主页的uri是`/index.php?page=home`

尝试访问`/index.php?page=/etc/natas_webpass/natas8`，得到natas8的密码

```
DBfUBfqQG69KvJvJ1iAbMoIpwSNQ9bWe
```

# Level8

PHP源码

{% gist hrl/836e7ec4d18b2a4ea551 %}

于是运行以下python代码算出原始secret

{% gist hrl/bee0327be21e3f77d2e8 %}

得到`oubWYf2kBq`，提交后得到natas9的密码

```
W0mMhUcRRnG8dcghE4qvk3JA9lGt8nDl
```

# Level9

PHP源码

{% gist hrl/92b0b5717ff32d3a83f1 %}

参考Level7，猜测可以从`/etc/natas_webpass/natas10`得到密码

于是提交`. /etc/natas_webpass/natas10;`，得到natas10的密码

```
nOpp1igQAkUzaI1GUUjzn1bFVj7xCNzu
```

# Level10

PHP源码

{% gist hrl/4f05498649b911b380c3 %}

可见过滤了`;|&`，于是去掉Level9末尾的`;`，提交`. /etc/natas_webpass/natas11`

只查看`/etc/natas_webpass/natas11`的部分，得到natas11的密码

```
U82q5TCMMQ9xuFoI3dYX61s7OZD9JKoK
```

# Level11

PHP源码核心部分

{% gist hrl/e6bdaa31a7f220f0bc0c %}

根据key做了一个简单的xor加密

加密后的数据为`ClVLIh4ASCsCBE8lAxMacFMZV2hdVVotEhhUJQNVAmhSEV4sFxFeaAw=`

加密前的数据为`{"showpassword":"no","bgcolor":"#ffffff"}`

于是运行以下python代码尝试得到key

{% gist hrl/836d0f39f53434633b00 %}

算出`qw8Jqw8Jqw8Jqw8Jqw8Jqw8Jqw8Jqw8Jqw8Jqw8Jq`，得到key`qw8J`

将data修改为`{"showpassword":"yes","bgcolor":"#ffffff"}` 

用算出的key加密得到新的cookie`ClVLIh4ASCsCBE8lAxMacFMOXTlTWxooFhRXJh4FGnBTVF4sFxFeLFMK`

用新的cookie访问得到natas12的密码

```
EDXp0pS26wLKHZy1rDBPUZk0RKfLGIR3
```

# Level12

PHP源码核心部分

{% gist hrl/8f6f24b83b3abbc90b37 %}

未对扩展名做检测，于是将表单中 `filename` 项改为.php结尾，上传如下文件

{% gist hrl/f97a80f493c627002e76 %}

之后访问上传上去的php页面得到natas13的密码

```
jmLTY0qiPZBbaKc9341cqPQZBJv7MQbY
```

# Level13

PHP源码，与Level12不同的部分

{% gist hrl/6ab95d932e81a5f12e09 %}

这次加了一个文件头的检测，于是我们加一个假的文件头，变成

{% gist hrl/a4975dfd6c1a2bb45489 %}

之后访问上传上去的php页面得到natas14的密码

```
Lg96M10TdfaPyVBkJdjymbllQ5L6qdl1
```

# Level14

PHP源码

{% gist hrl/119d2b028d63ed07d018 %}

只需要这个SQL语句有结果就行了，于是username里填上`" OR 1=1;#`，让这个语句返回所有结果，password随便打点什么上去。得到natas15的密码

```
AwWj0w5cvxrZiONgZ9J5stNVkmxdk39J
```

# Level15

PHP源码

{% gist hrl/ba1fe0d2d990da114ba5 %}

总之先测试一下用户名，发现`natas16`确实存在；从代码里可以看出，我们只能知道这个语句返回的结果是否大于0，那么下一步只有盲注密码了…

看了看数据库定义，password最长为64，字符范围根据之前的密码应该是[0-9A-Za-z]，然后写个脚本慢慢跑吧…

{% gist hrl/b216dc46fbaf8fc3b155 %}

最后拿到natas16的密码

```
WaIHEacj63wnNIBROHeqi3p9t0m5nhmh
```

# Level16

PHP源码

{% gist hrl/241217da12b1eb9a96db %}

把大部分特殊字符都过滤了，不过$key是包在双引号里的，所以可以用$(statements)的方式来执行语句返回一个结果到$key里，其余的思路跟上一题差不多

{% gist hrl/3b5eafb9f09f43f5f2f5 %}

最后拿到natas17的密码

```
8Ps3H0GWbn5rd9S7GmAdgQNdkhPkq9cw
```

# Level17

PHP源码

{% gist hrl/f4c3114304f51ab51ac0 %}

基本思路跟Level15一样，但是不能靠返回的内容判断了，所以只有靠sleep来盲注了，碰到网络波动就比较尴尬了…保险一点的话sleep的时间得设长一点

先试试`natas18" AND SLEEP(10)#`，确定用户名确实是`natas18`，剩下的部分就跟Level15大同小异了，因为二分在这里太慢，这里就直接判断了，并且各个位并行请求

{% gist hrl/3edf35c657bb970006bc %}

最后拿到natas18的密码

```
xvKIqDjy4OPv7wCRgDlmj0pFsCsDjhdP
```

# Level18

PHP源码核心部分

{% gist hrl/9dff53292c3db22a04e3 %}

大概就是读了下cookie里的PHPSESSID，然后就当你是这个session对应的用户了，猜测这640个id里肯定有一个是admin的，于是直接穷举吧…

{% gist hrl/92b01d1764f48c7cdace %}

最后拿到natas19的密码

```
4IwIrekcuZlA9OsjOkoUtwU6lhokCPYs
```

# Level19

没有给源码

{% gist hrl/c1ef69c936a536c93e1b %}

总之先弄几个ID看看吧，拿到了

{% highlight text %}
3433302d61646d696e
3130302d61646d696e
3237342d61646d696e
{% endhighlight %}

大概是`3x3x3x2d61646d696e`的感觉，剩下的部分跟上一题一样了

{% gist hrl/f796d44f07ef5fb28c0a %}

最后拿到natas20的密码

```
eofm3Wsshxc5bwtVnEuGIlr7ivb9KABF
```

# Level20

PHP源码核心部分

{% gist hrl/3f7df014402240e49795 %}

注意到读session的函数里是直接按照`\n`切割，而保存的时候没有对value做任何检查，于是在value里插一个换行符就可以了，拿到natas21的密码

```
IFekPyrQXftziDEsUr3x21sYuahypdgJ
```

# Level21

按他的指示去隔壁，PHP源码核心部分

{% gist hrl/e85c84200758995d0f45 %}

直接在表单里加个admin项，填1就行了…拿到natas22的密码

```
chG9fbe1Tq2eWVMgjYYD1MsfIvN461kJ
```

# Level22

PHP源码

{% gist hrl/39cd91cea9b536cbcfe1 %}

url里加个?revelio=1就行，Postman/Burp或者写脚本不自动跟随重定向就可以看到密码。拿到natas23的密码

```
D0vlad33nQF0Hz2EP255TP5wSW9ZsRSE
```

# Level23

PHP源码

{% gist hrl/b7c565ac3a7193b8855b %}

尴尬的隐式类型转换…填`11iloveyou`就好。拿到natas24的密码

```
OsRmXFguozKpTZZ5X14zNO43379LZveg
```

# Level24

PHP源码

{% gist hrl/b0c6a1bc847a70d68afd %}

看看[文档](http://php.net/manual/en/function.strcmp.php)，发现这么一句`strcmp("foo", array()) => NULL + PHP Warning`，然后就把passwd改成passwd[]，不填内容提交，会出现Warning和密码。拿到natas25的密码

```
GHF6X7YwACaYYssHVY05cFq83hRktl4c
```

# Level25

PHP源码

{% gist hrl/78e203d684bcdcae01ab %}

注意到网页内容是include进来的，而且logRequest内有一个可以让我们填任意字符的地方，那么下一步就是想办法让它include进来这个log文件。

检查目录遍历的函数里只替换了一次`../`，所以如果我们输入`....//`的话，替换之后就变成了`../`，多重复几次就可以到达根目录，然后访问到log文件了。

最后在UA里插一段`<?php include('/etc/natas_webpass/natas26') ?>`就可以拿到密码了。

拿到natas26的密码

```
oGgWAJ7zcGT28vYazGo4rkhOPDhBu34T
```

# Level26

PHP源码核心部分

{% gist hrl/850d503df8b251bc9e9d %}

发现用到了`unserialize`，代码里还有个奇怪的Logger。于是自己试了试，发现`serialize`会把实例内部属性给dump出来，然后`unserialize`的时候会去读属性，这样构造函数不一定能用上，不过析构函数是肯定可以用得上的…于是写了下面的php代码来试图往img目录里写一个php文件

{% gist hrl/91ceeee64d25ca620c1a %}

然后访问`img/yoooo.php`，拿到natas27的密码

```
55TBjpPZUUJgVP5b3BnbG6ON9uDPVzCJ
```

# Level27

PHP源码

{% gist hrl/ee1fdabe2a7bd01c5f54 %}

大概是看一下有没有这个用户名，有的话检测用户名和密码这一对数据是否存在，如果存在就去拿用户名对应的数据；如果没有这个用户名就创建一个新的。

那么下一步要做的应该是自己插一个`natas28`用户进去了…然而这代码并没有可以让我们注入的地方…尴尬…

看了看开头的注释，`username`长度只有64，然后翻了翻MySQL的[文档](https://dev.mysql.com/doc/refman/5.0/en/char.html)，注意到

> Before MySQL 5.0.3, trailing spaces are removed from values when they are stored into a VARCHAR column; this means that the spaces also are absent from retrieved values.

也就是说后面的空格会被删掉，于是尝试令`username`=`natas28`+57个空格，发现此时还是会当`natas28`处理。

然后尝试令`username`=`natas28`+57个空格+1个任意字符，`password`留空，这时`validUser`返回了`False`，于是系统会尝试插入这个用户，首先末尾超出的字符被截断，只留下`natas28`+57个空格，然后末尾空格被删掉，只留下`natas28`。这样就成功插入了一个新的`natas28`用户。

最后拿到`natas28`的密码

```
JWwR438wkgTsNKBbcJoowyysdM82YjeF
```