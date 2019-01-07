---
layout: post
title: "WaterDrop 水滴"
subtitle: "海量数据处理的产品"
author: "et"
header-img: "img/post-bg-waterdrop.jpg"
header-mask: 0.4
tags:
  - DW
  - 数仓
---



> waterdrop  安装部署

<br>

   
1.  创建目录
> mkdir -p  /opt/waterdrop

2.  下载waterdrop 压缩包
> wget https://github.com/InterestingLab/waterdrop/releases/download/v1.1.2/waterdrop-1.1.2.zip -O waterdrop-1.1.2.zip

3.  解压zip 
> unzip waterdrop-1.1.2.zip 

  创建软连接
> ln -s  unzip waterdrop-1.1.2  waterdrop

  下载spark2,并创建软连接 
>  wget http://archive.apache.org/dist/spark/spark-2.2.0/spark-2.2.0-bin-hadoop2.6.tgz 
>  ln -s spark-2.2.0-bin-hadoop2.6 spark2

 7.  scp -P34222 root@ai-etl-c2-25:/opt/jdk-8u144-linux-x64.tar.gz  ./  
     ----cp jdk；配置 javahome

 8.  配置java Home  
>      tar -zxvf jdk-8u144-linux-x64.tar.gz  -C  /usr/local/
>      vim /etc/profile
>      export JAVA_HOME=/usr/local/jdk1.8.0_144
>      export PATH=$JAVA_HOME/bin:$PATH
>      export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

 9. cp  hive-site.xml   到 spark conf 目录

> scp -P34222 root@hostname1:/etc/hive/conf/hive-sitm.xml   /opt/spark/spark2/conf/

 10. 修改hive-site.xml 文件增加修改(host 文件要配置)

>        <property>
>           <name>hive.metastore.local</name>
>           <value>false</value>
>        </property>
>        <property>
>           <name>hive.metastore.uris</name>
>           <value>thrift://hostname1:9083,thrift://hostname2:9083</value>
>        </property>

 11. 配置 waterdrop-env.sh

>    SPARK_HOME=${SPARK_HOME:-/opt/spark/spark2}

 12. 配置application.conf

>    spark {
>        --  Waterdrop defined streaming batch duration in seconds
>        spark.streaming.batchDuration = 5
>        spark.app.name = "Waterdrop"
>        spark.ui.port = 13000
>         }
>
>     input {
>        socketStream {}
>      }
>
>     filter {
>             split {
>                fields = ["msg", "name"]
>                delimiter = ","
>        }
>      }
>
>     output {
>        stdout {}
>      }

 13. 启动 waterdrop

>  /bin/start-waterdrop.sh --master yarn --deploy-mode client --config ./config/application.conf 
