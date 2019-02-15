---
layout: post
title: "简易的系统监控"
subtitle: "python 实现"
author: "et"
header-img: "img/post-bg-hbase-index.jpg"
header-mask: 0.4
tags:
  - OP
  - python 
---


>  方式一 


<br>

       #! /usr/bin/env python
       #coding:utf-8
       import json
       import sys
       import os
       import commands
       import time,datetime
       
       
       reload(sys)  
       sys.setdefaultencoding('utf8')
       
       
       daytime = datetime.datetime.now()
       
       while True:
            
           now = datetime.datetime.now()
           if now > daytime:
               daytime = now
               prefix = "topfile_%s" % now.strftime("%Y%m%d")
           else:
               prefix = "topfile_%s" % daytime.strftime("%Y%m%d")
       
           filepath = "/data/%s.log" % prefix
           #print filepath
           now = datetime.datetime.now()
           currtime = now.strftime("%Y-%m-%d_%H:%M:%S")
           #prefix += currtime
           fenge ="==========================%s=================================" %  currtime
           exec_ = """ echo %s >> %s """ % (fenge,filepath)
           os.system(exec_)
       
           exec_ = """  top -b -n 1 |head -20  >> %s """ % filepath
       
           os.system(exec_)
       
           time.sleep(60)  





>  方式二   



