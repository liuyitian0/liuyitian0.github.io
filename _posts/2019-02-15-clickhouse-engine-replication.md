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


| 把metrika.xml同步到各节点,然后重启各节点   

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
       insert into test (dt,id,name) values('2019-01-10',1005,'aaa');    

--- 创建分布式试图表,一定要把所有的分布本地表创建完成后在创建路由表(试图表/总表),不然本地表没有数据    
 
       CREATE TABLE test_all AS test \    
       ENGINE = Distributed(cluster_3shards_1replicas, default, test_local, rand())    

------ 把数据插入 试图表，以便 clickhouse 自己同步数据到 各分片本地表 lym_test    

       INSERT INTO test_all SELECT * FROM test;    
       select * from  test_all;    

![](/img/in-post/clickhouse-engine-replication.jpg)    
  在第一台机器上查 本地表:    
![](/img/in-post/clickhouse-engine-replication-1.jpg)    
  在第二台上 查询 本地表:    
![](/img/in-post/clickhouse-engine-replication-2.jpg)    
  在第三台上 查询 本地表:    
![](/img/in-post/clickhouse-engine-replication-3.jpg)    
  对于分布式表来说 ，只要有一个分片节点不可用 整个 分布式表，就是带 _all 的 路由表 就是不可用的，下面是把第三个分片停掉后，在查询 路由表 报错    
![](/img/in-post/clickhouse-engine-replication-4.jpg)    



>  场景二   测试    
>  目前采用了  1分片 2 个副本的集群方式  ，配置如下:    
>  在第一台和第三台上:    

       <yandex>
       <clickhouse_remote_servers>
          <!-- 1分片2备份 -->
          <cluster_1shards_2replicas>
          <shard>
             <internal_replication>false</internal_replication>
             <replica>
               <host>test-ai-etl-c1-1</host>
               <port>9999</port>
             </replica>
             <replica>
                <host>test-ai-etl-c1-3</host>
                <port>9999</port>
             </replica>
          </shard>
          </cluster_1shards_2replicas>
       </clickhouse_remote_servers>
       </yandex>    

---- 测试用表 如下:    
       create table default.test_local_s1r2 \    
       (dt Date,id UInt8,name String) \    
       ENGINE = MergeTree(dt, (id, dt), 8192);    
       
       CREATE TABLE test_local_s1r2_all AS test \    
       ENGINE = Distributed(cluster_1shards_2replicas, default, test_local_s1r2, rand());    
       
       INSERT INTO test_local_s1r2_all SELECT * FROM test;    

  查询 第一台上的本地表:    
![](/img/in-post/clickhouse-engine-replication-5.jpg)    
  可以看到 是单分片的全量数据    
  查询 第三台上的本地表:   
![](/img/in-post/clickhouse-engine-replication-6.jpg)    
  也是 单分片 的全量数据     




**   画外话 && FAQ (引用)

> 四种复制模式:    
>非复制表，internal_replication=false。插入到分布式表中的数据被插入到两个本地表中，如果在插入期间没有问题，则两个本地表上的数据保持同步。我们称之为“穷人的复制”，因为复制在网络出现问题的情况下容易发生分歧，没有一个简单的方法来确定哪一个是正确的复制。    
>复制表，internal_replication=true。插入到分布式表中的数据仅插入到其中一个本地表中，但通过复制机制传输到另一个主机上的表中。因此两个本地表上的数据保持同步。这是推荐的配置。    
>非复制表，internal_replication=true。数据只被插入到一个本地表中，但没有任何机制可以将它转移到另一个表中。因此，在不同主机上的本地表看到了不同的数据，查询分布式表时会出现非预期的数据。显然，这是配置ClickHouse集群的一种不正确的方法。    
>复制表，internal_replication=false。数据被插入到两个本地表中，但同时复制表的机制保证重复数据会被删除。数据会从插入的第一个节点复制到其它的节点。其它节点拿到数据后如果发现数据重复，数据会被丢弃。这种情况下，虽然复制保持同步，没有错误发生。但由于不断的重复复制流，会导致写入性能明显的下降。所以这种配置实际应该是避免的，应该使用配置2。    

*  目前clickhouse 对底层的复制模式不够清楚,不建议用于生产环境
