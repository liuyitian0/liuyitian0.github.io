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


1.
    -- 初始化环境(如果安装指定版本的ck,这个初始化应该是不用做的,这个没有测试)
       
        curl -s https://packagecloud.io/install/repositories/altinity/clickhouse/script.rpm.sh | sudo bash


2.  下载rpm 包

         wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-server-common-1.1.54383-1.el7.x86_64.rpm
         wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-common-static-1.1.54383-1.el7.x86_64.rpm
         wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-server-1.1.54383-1.el7.x86_64.rpm
         wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-debuginfo-1.1.54383-1.el7.x86_64.rpm
         wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-test-1.1.54383-1.el7.x86_64.rpm
         wget https://packagecloud.io/Altinity/clickhouse/packages/el/7/clickhouse-client-1.1.54383-1.el7.x86_64.rpm

3.  顺序安装

    -- 安装指定版本的(可能会因网络报错,重试几次)

        sudo yum -y install clickhouse-server-common-1.1.54383-1.el7.x86_64
        sudo yum -y install clickhouse-common-static-1.1.54383-1.el7.x86_64
        sudo yum -y install clickhouse-server-1.1.54383-1.el7.x86_64
        sudo yum -y install clickhouse-debuginfo-1.1.54383-1.el7.x86_64
        sudo yum -y install clickhouse-test-1.1.54383-1.el7.x86_64
        sudo yum -y install clickhouse-client-1.1.54383-1.el7.x86_64


<br><br>



>  另一种方式

        curl -s https://packagecloud.io/install/repositories/altinity/clickhouse/script.rpm.sh | sudo bash 
        
        
        sudo yum -y install clickhouse-server-common-1.1.54383-1.el7.x86_64
        sudo yum -y install clickhouse-common-static-1.1.54383-1.el7.x86_64
        sudo yum -y install clickhouse-server-1.1.54383-1.el7.x86_64
        sudo yum -y install clickhouse-debuginfo-1.1.54383-1.el7.x86_64
        sudo yum -y install clickhouse-test-1.1.54383-1.el7.x86_64
        sudo yum -y install clickhouse-client-1.1.54383-1.el7.x86_64
        
        sudo yum list installed 'clickhouse*'  
        
        
        
         mkdir -p /data1/clickhouseTmp
         mkdir -p /data1/clickhouseData
        
        
         <tcp_port>9999</tcp_port>
         <listen_host>10.3.8.48</listen_host>
         <path>/data1/clickhouseData/</path>
         <tmp_path>/data1/clickhouseTmp/</tmp_path>
        
         service clickhouse-server start
         service clickhouse-server stop
        
        
        
        vim /etc/clickhouse-server/metrika.xml
        
        
        <yandex>
        <!-- 集群配置 -->
        <clickhouse_remote_servers>
            <!-- 3分片1备份 -->
            <cluster_3shards_2replicas>
                <!-- 数据分片1  -->
                <shard>
                	<weight>1</weight>
                    <internal_replication>true</internal_replication>
                    <replica>
                        <host>hostname1</host>
                        <port>9999</port>
                    </replica>
                </shard>
                <!-- 数据分片2  -->
                <shard>
                	<!--weight是分片权重,数据写入该分片的概率-->
                	<weight>1</weight>
                	<!-- 表示是否只将数据写入其中一个副本，默认为false，表示写入所有副本，在复制表的情况下可能会导致重复和不一致，所以这里一定要改为true -->
                    <internal_replication>true</internal_replication>
                    <replica>
                        <host>hostname2</host>
                        <port> 9999</port>
                    </replica>
                </shard>
                <!-- 数据分片3  -->
                <shard>
                    <weight>1</weight>
                    <internal_replication>true</internal_replication>
                    <replica>
                        <host>hostname3</host>
                        <port>9999/port>
                    </replica>
                </shard>
            </cluster_3shards_2replicas>
        </clickhouse_remote_servers>
        
        
        <!-- zookeeper的配置 -->
        <zookeeper-servers>
          <node index="1">
            <host>hostname1</host>
            <port>2181</port>
          </node>
            <node index="2">
            <host>hostname2</host>
            <port>2181</port>
          </node>
            <node index="3">
            <host>hostname3</host>
            <port>2181</port>
          </node>
        </zookeeper-servers>
        
        </yandex>




-- 参考资料 

       https://packagecloud.io/Altinity/clickhouse/ 
       https://packagecloud.io/Altinity/clickhouse?page=9

       https://www.jianshu.com/p/20639fdfdc99
       https://blog.csdn.net/qq_18931433/article/details/82494182

<br><br>
-- 官网

       https://clickhouse.yandex/#
