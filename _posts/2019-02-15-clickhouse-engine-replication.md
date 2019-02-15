---
layout: post
title: "Clickhouse 表引擎和表复制模式的探索"
subtitle: "分片 复制模式 表引擎"
author: "et"
header-img: "img/post-bg-halting.jpg"
header-mask: 0.4
tags:
  - clickhouse
---


>  目前使用 3台集群部署了 click house集群.    
>  node1 / node2 / node3    
>  配置方式采用了  remote_servers   独立文件配置的方式，独立成  metrika.xml    
>  目前 metrika.xml 的 配置了  clickhouse_remote_servers  /  zookeeper-servers /  macros    
>  具体如下:   

       <yandex>
       <!-- 集群配置 -->
         <clickhouse_remote_servers>
         <!-- 3分片1备份 -->
         <cluster_3shards_1replicas>
            <!-- 数据分片1 -->
         <shard>
            <weight>1</weight>
            <internal_replication>true</internal_replication>
            <replica>
               <host>node1</host>
               <port>9999</port>
            </replica>
         </shard>
         <!-- 数据分片2 -->
         <shard>
            <weight>1</weight>
            <internal_replication>true</internal_replication>
            <replica>
               <host>node2</host>
               <port>9999</port>
            </replica>
         </shard>
         <!-- 数据分片3 -->
         <shard>
            <weight>1</weight>
            <internal_replication>true</internal_replication>
            <replica>
              <host>node3</host>
              <port>9999</port>
            </replica>
         </shard>
        </cluster_3shards_1replicas>
        </clickhouse_remote_servers>
       
       </yandex>   


#  把metrika.xml 同步到各节点,然后重启各节点   

>  场景一    测试
>  目前采用了  3个 分片 1个副本的方式  ,测试用表 如下:    
>    准备采用    MergeTree + Distributed + 集群复制  的方式    

----- 创建测试表
       create table default.test \
       (dt Date,id UInt8,name String) \
       ENGINE = MergeTree(dt, (id, dt), 8192);
       
       create table default.test_local \
       (dt Date,id UInt8,name String) \
       ENGINE = MergeTree(dt, (id, dt), 8192);       

------ 插入测试数据    
       insert into test (dt,id,name) values('2019-01-10',1001,'aaa');
       insert into test (dt,id,name) values('2019-01-10',1002,'aaa');
       insert into test (dt,id,name) values('2019-01-10',1003,'aaa');
       insert into test (dt,id,name) values('2019-01-10',1004,'aaa');
       insert into test (dt,id,name)values('2019-01-10',1005,'aaa');    

--- 创建 分布式试图表 , 一定要把所有的 分布本地表创建完成后在创建  路由表(试图表/总表) ，不然 本地表没有数据   

       CREATE TABLE test_all AS test \
       ENGINE = Distributed(cluster_3shards_1replicas, default, test_local, rand())    

------ 把数据插入 试图表，以便 clickhouse 自己同步数据到 各分片本地表 lym_test    

       INSERT INTO test_all SELECT * FROM test;
       select * from  test_all;











![](/img/in-post/post-nextgen-web-pwa/PWAR-007.jpeg)
