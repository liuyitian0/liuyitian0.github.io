---
layout: post
title: "ClickHouse 部署"
subtitle: "ClickHouse 就是快"
author: "裔天"
header-img: "img/post-bg-clickhouse.jpg"
header-mask: 0.4
tags:
  - Bigdata
  - Dw
  - 数仓
  - 大数据工具
---

> ClickHouse 部署

<br>


1.  下载rpm 包:

       wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-server-common-1.1.54383-1.el7.x86_64.rpm
       wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-common-static-1.1.54383-1.el7.x86_64.rpm
       wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-server-1.1.54383-1.el7.x86_64.rpm
       wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-debuginfo-1.1.54383-1.el7.x86_64.rpm
       wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-test-1.1.54383-1.el7.x86_64.rpm
       wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-client-1.1.54383-1.el7.x86_64.rpm

2.  顺序安装

    -- 初始化环境(如果安装指定版本的ck,这个初始化应该是不用做的,这个没有测试)

       curl -s https://packagecloud.io/install/repositories/altinity/clickhouse/script.rpm.sh | sudo bash

    -- 安装指定版本的

       sudo yum install clickhouse-server-common-1.1.54383-1.el7.x86_64
       sudo yum install clickhouse-common-static-1.1.54383-1.el7.x86_64
       sudo yum install clickhouse-server-1.1.54383-1.el7.x86_64
       sudo yum install clickhouse-debuginfo-1.1.54383-1.el7.x86_64
       sudo yum install clickhouse-test-1.1.54383-1.el7.x86_64
       sudo yum install clickhouse-client-1.1.54383-1.el7.x86_64


<br><br><br>

-- 参考资料 

       https://packagecloud.io/Altinity/clickhouse/ 
       https://packagecloud.io/Altinity/clickhouse?page=9

       https://www.jianshu.com/p/20639fdfdc99
       https://blog.csdn.net/qq_18931433/article/details/82494182

<br><br>
-- 官网

       https://clickhouse.yandex/#
