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

```
Access disallowed. You are visiting from "" while authorized users should come only from "http://natas5.natas.labs.overthewire.org/"
```

修改Header `Referer: http://natas5.natas.labs.overthewire.org/` 之后访问，得到natas5的密码

```
iX6IOfmpN7AYOQGPwtn3fXpbaJVJcHfq
```