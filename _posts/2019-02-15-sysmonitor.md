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


>  方式一, 简单粗暴,利用系统命令Top      


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





>  方式二  ,通过 aha 生成html 当时镜像图，要先安装 aha   

       yum install aha   

       #! /usr/bin/env python
       #coding:utf-8
       import json
       import sys
       import os
       import commands
       import time,datetime
       
       
       reload(sys)  
       sys.setdefaultencoding('utf8')   
       
       
       def send_alarm(self,mess):
           alarm_mess = ''' curl '原本想通过玎钉进行监控(预留接口)'  \
                       -H 'Content-Type: application/json'  \
                       -d '
                           {
                               "msgtype": "text", 
                               "text": {
                               "content": "%s"
                               },
                           "isAtAll":true
                           }'
           '''  % mess
       
       
       
       while True:
       
           prefix = "htop_"
           now = datetime.datetime.now()
           currtime = now.strftime("%Y-%m-%d_%H:%M:%S")
           prefix += currtime
           print prefix
           exec_ = """ 
           echo q | htop | aha --black --line-fix >> ./%s.html
           """  %  prefix
       
       
           (status,res_data) = commands.getstatusoutput(exec_)
           if (status != 0):
               print  "command exec Failure."
       
           exit(1)
