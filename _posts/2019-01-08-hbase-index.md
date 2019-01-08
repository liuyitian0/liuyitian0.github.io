---
layout: post
title: "HBase 二级索引"
subtitle: "hbase  二级索引参数调整"
author: "et"
header-img: "img/post-bg-waterdrop.jpg"
header-mask: 0.4
tags:
  - HBase
  - CDH
---


> HBase  二级索引参数调整优化,CDH参数配置


<br>

> Master

      <property>
        <name>hbase.regionserver.wal.codec</name>
        <value>org.apache.hadoop.hbase.regionserver.wal.IndexedWALEditCodec</value>
      </property>
      <property>
        <name>hbase.master.loadbalancer.class</name>
        <value>org.apache.phoenix.hbase.index.balancer.IndexLoadBalancer</value>
      </property>
      <property>
        <name>hbase.coprocessor.master.classes</name>
        <value>org.apache.phoenix.hbase.index.master.IndexMasterObserver</value>
      </property>


<br>


>  Region Server

      <property>
        <name>hbase.regionserver.wal.codec</name>
        <value>org.apache.hadoop.hbase.regionserver.wal.IndexedWALEditCodec</value>
      </property>
      <property>
         <name>hbase.region.server.rpc.scheduler.factory.class</name>
         <value>org.apache.hadoop.hbase.ipc.PhoenixRpcSchedulerFactory</value>
         <description>Factory to create the Phoenix RPC Scheduler that usesseparate queues for index and metadata updates</description>
      </property>
      <property>
         <name>hbase.rpc.controllerfactory.class</name>
         <value>org.apache.hadoop.hbase.ipc.controller.ServerRpcControllerFactory</value>
         <description>Factory to create the Phoenix RPCScheduler that uses separate queues for index and metadataupdates</description>
      </property>
