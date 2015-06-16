---
layout: post
title: [Notes] Working With Unix Processes 01
category:
- unix
- processes
- ruby
permalink: working-with-unix-processes-01
meta_description: Working With Unix Processes
browser_title: Working With Unix Processes
comments: true
---

### 前言

自从工作的项目越做越大之后发现编程中对系统的理解越发重要，调试程序问题的时候不能只看到 It works fine on my computer，
也需要考虑到部署到服务器后可能发生的各种问题；

> 对工作原理有了深入的了解之后，不仅可以更快地理解并诊断出现的问题，还能对那些对看代码依然让人摸不着头脑的问题进行排查。

### 进程皆有标识

系统中的所有进程都有一个唯一标识，称为PID； Ruby中通过执行`Process.pid`可获取当前进程的PID，此外全局变量`$$`也保存着当前的PID值。

### 进程皆有父

系统中运行的每一个进程都有一个父进程。每个进程都知道其父进程的PID。
Ruby中获取父进程的PID：
{% highlight ruby%}
puts Process.ppid
{% endhighlight %}

### 进程皆有文件描述符

UNIX哲学中万物皆为文件，包括设备、管道、套接字，当然普通的文件也视为文件；
