---
layout: post
title: OverTheWire Natas 0-27
modified:
categories: writeup
excerpt:
tags: []
image:
  feature:
date: 2016-01-26T16:24:00+08:00
---

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