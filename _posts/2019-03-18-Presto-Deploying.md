---
layout: post
title: " Presto 部署 "
subtitle: "分布式快速查询"
author: "et"
header-img: "img/post-bg-halting.jpg"
header-mask: 0.4
tags:
  - Presto
  - Kafka
---



Presto  部署:   
可以参照官网的部署，已经参数配置:   
> [https://prestodb.github.io/docs/current/installation/deployment.html](https://prestodb.github.io/docs/current/installation/deployment.html "With a Title")

简单说下注意点:   
    1.  安装目录和 数据目录要分开，便于升级   
    2.  如果要连接hive的话，在编辑 catalog/hive.properties  文件的时候 connector.name=hive-hadoop2 参数一定要带上hadoop 的版本号，便于连接器选择不同的版本连接。不配置或配置有问题的话 presto 服务不会启动或启动异常，在操作hive的时候会报 连接异常。 hive.config.resources  配置参数指定的core-site.xml 和hdfs-site.xml hadoop 配置文件要可以访问到。   
    3.  在配置 config.properties 文件到时候注意端口是否被占用？其他按照官网配置即可   
    4.  jvm.config 配置文件，模版按照官网的配置。具体大小可以根据自己机器的配置来调整   
    5.  node.properties /log.properties  配置文件没什么好说的   
    
 

下载 Presto    

      wget https://repo1.maven.org/maven2/com/facebook/presto/presto-server/0.217/presto-server-0.217.tar.gz

Presto 0.217 需要 jdk1.8.0_15 在以上   
下载 jdk   

       wget https://download.oracle.com/otn-pub/java/jdk/8u201-b09/42970487e3af4f5aa5bca3f542482c60/jdk-8u201-linux-x64.tar.gz   

下载 Presto CLI(可以结合server版本)     

       wget https://repo1.maven.org/maven2/com/facebook/presto/presto-cli/0.188/presto-cli-0.188-executable.jar   

将 resto-cli-0.188-executable.jar  重命名为 presto   
      
       mv presto-cli-0.188-executable.jar  presto    
       chmod 777 presto   
执行    
       ./presto --server localhost:8001 --catalog kafka --schema default     
 
由于我本机端口8081 被占用,我在上面的config.properties 文件中改成了 8001,所以这里写8001    

 
最终目录文件和配置情况如下:     

![](/img/in-post/Presto-Deploying-1.jpg)      
![](/img/in-post/Presto-Deploying-2.jpg)      
![](/img/in-post/Presto-Deploying-3.jpg)      
![](/img/in-post/Presto-Deploying-4.jpg)      
![](/img/in-post/Presto-Deploying-5.jpg)      
![](/img/in-post/Presto-Deploying-6.jpg)      
![](/img/in-post/Presto-Deploying-7.jpg)      


