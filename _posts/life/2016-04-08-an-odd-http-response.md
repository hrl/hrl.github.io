---
layout: post
title: 岛沉过后奇特的HTTP Response
modified:
categories: life
excerpt:
tags: [HTTP, TCP]
image:
  feature:
share: true
date: 2016-04-08T08:13:35+08:00
---

昨天在群里看到一个A岛的链接，于是点进去看看，发现Chrome里链接已经打不开了，看讨论说岛又沉了（

正好当时手头的事情告一段落，就拿burp试了试，发现也是没法打开，然后又尝试了下telnet，发现居然能连上…也能返回400…到目前为止感觉是后台服务器炸了…

然后就开始比较迷的事情了…我在telnet里手动输了一下HTTP请求，大概长这样：

{% highlight http %}
GET / HTTP/1.1
Host: h.nimingban.com
{% endhighlight %}

嗯…结果居然返回正常…看来不太像后台服务器的问题

然而一样的请求丢burp里也是一直出不来response…

然后我试着抓包看了看，telnet发出的请求跟burp发出的请求，唯一的区别只在：

- telnet在我每次换行后就发出一次tcp包，也就是每一行请求头都发一个包
- burp一次发完整个请求头

为了验证这个想法，我写了段小脚本模拟这两种发送请求的方式：

{% highlight python3 %}
#!/usr/bin/env python
# encoding: utf-8

import socket

HOST = "h.nimingban.com"
PORT = 80
REQ_LINES = (
    b"GET / HTTP/1.1\r\n",
    b"Host: h.nimingban.com\r\n",
    b"\r\n"
)


def send_one_by_one(HOST, PORT, REQ_LINES):
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((HOST, PORT))
    for line in REQ_LINES:
        s.sendall(line)

    data = None
    try:
        data = s.recv(1024)
    except OSError as e:
        print(e)
    finally:
        s.close()
    return data


def send_all(HOST, PORT, REQ_LINES):
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((HOST, PORT))
    s.sendall(b"".join(REQ_LINES))

    data = None
    try:
        data = s.recv(1024)
    except OSError as e:
        print(e)
    finally:
        s.close()
    return data


if __name__ == '__main__':
    print("Send one by one:")
    print(send_one_by_one(HOST, PORT, REQ_LINES))
    print("Send all:")
    print(send_all(HOST, PORT, REQ_LINES))
{% endhighlight %}

结果也是一行一行发就可以…一次发完就拿不到response

迷…\_(:з」∠)\_…这是为什么…
