---
layout: post
title: "net.ipv4.tcp_timestamps 系统参数遇到的坑"
subtitle: "linux"
author: "et"
header-img: "img/post-bg-halting.jpg"
header-mask: 0.4
tags:
  - OP
  - linux
---


   最近在对接腾讯的EMR 弹性计算平台,遇到了一个比较坑的地方。关于腾讯EMR的文章后续会陆续更新。今天说下因为一个系统内核参数排查了一上午问题的坑   --> Hue 。   


   在腾讯的EMR平台上部署了一个小规模的集群，给组内同事开通了hue的访问权限。给了一个公网IP和端口号去访问hue使用hive。发现打开网页很慢。有时候甚至根本打不开。刚开始认为是公司网络差 又加上Hue 本身会加载很多js 导致访问太卡的。 经过一番排查后发现 hue 默认的外网出口带宽是5M 这个地方也是个瓶颈，随后调到了20M。但是跳过之后问题依旧，通过外网访问hue 时不时的打不开。排查了安全组的进出规则也没有问题,telnet 也不通了。 后面突然想到由于是新机器一些系统参数使用的默认的。就想起了 tcp_timestamps 这个参数。net.ipv4.tcp_timestamps 默认设置了为1，即启用TCP时间戳。阻塞了tcp 3次握手，表现在web 端就是访问页面超时 打不开。 下面直接关掉 就回复了正常。   


     修改 /etc/sysctl.conf 中:   

       net.ipv4.tcp_timestamps = 0
       sysctl -p


