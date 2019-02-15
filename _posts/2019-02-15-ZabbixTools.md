---
layout: post
title: "Zabbix Tools 监控,玎钉通知"
subtitle: "python 实现"
author: "et"
header-img: "img/post-bg-halting.jpg"
header-mask: 0.4
tags:
  - OP
  - python 
  - zabbix
---


>   直接上脚本


       #!/usr/bin/env python
       #coding:utf-8
       import json
       import urllib2
       from urllib2 import URLError
       import sys
       import os
       
       reload(sys)  
       sys.setdefaultencoding('utf8')   
       
       
       zabbix_addresses=['zabbix-web-address,账号,密码']
       
       class ZabbixTools:
           def __init__(self,address,username,password):
       
               self.address = address
               self.username = username
               self.password = password
       
               self.url = '%s/api_jsonrpc.php' % self.address
               self.header = {"Content-Type":"application/json"}
       
       
       
           def send_alarm(self,mess):
               alarm_mess = ''' curl 'https://oapi.dingtalk.com/robot/send?access_token=玎钉的tokenID'  \
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
           #print alarm_mess
               os.system(alarm_mess)
           #(stat,res_v) = commands.getstatusoutput(alarm_mess)
           #return stat
       
           def user_login(self):
               data = json.dumps({
                                  "jsonrpc": "2.0",
                                  "method": "user.login",
                                  "params": {
                                             "user": self.username,
                                             "password": self.password
                                             },
                                  "id": 0
                                  })
       
               request = urllib2.Request(self.url, data)
               for key in self.header:
                   request.add_header(key, self.header[key])
       
               try:
                   result = urllib2.urlopen(request)
               except URLError as e:
                   print "Auth Failed, please Check your name and password:", e.code
               else:
                   response = json.loads(result.read())
                   result.close()
                   #print response['result']
                   self.authID = response['result']
                   return self.authID
       
           def trigger_get(self):
               data = json.dumps({
                                  "jsonrpc":"2.0",
                                  "method":"trigger.get",
                                  "params": {
                                             "output": [
                                                       "triggerid",
                                                       "description",
                                                       "priority",
                                                       "group"
                                                       ],
                                             "filter": {
                                                        "value": 1
                                                        },
                                             "sortfield": "priority",
                                             "sortorder": "DESC",
                                             "min_severity": 4,
                                             "skipDependent": 1,
                                             "monitored": 1,
                                             "active": 1,
                                             "expandDescription": 1,
                                             "selectHosts": ['host'],
                                             "selectGroups": ['name'],
                                             "only_true": 1
                                           },
                                  "auth": self.user_login(),
                                  "id":1              
               })
       
               request = urllib2.Request(self.url, data)
               for key in self.header:
                   request.add_header(key, self.header[key])
       
               try:
                   result = urllib2.urlopen(request)
               except URLError as e:
                   print "Error as ", e
               else:
                   response = json.loads(result.read())
                   result.close()
                   issues = response['result']
                   content = ''
                   if issues:
                       for line in issues:
                           content = content + "%s,%s\r\n" % (line['groups'][0]['name'],line['description']) 
                   return content
       
       if __name__ == "__main__":
           for zabbix_addres in zabbix_addresses:
               address,username,password = zabbix_addres.split(',')
               z = ZabbixTools(address=address, username=username, password=password)
               mess = z.trigger_get()
           if mess:
               print mess
               z.send_alarm(mess)    


     #  上面的 zabbix_addresses 的值要替换成自己的信息,zabbix web 地址和对应的账密
     #  上面的 玎钉的tokenID  要替换掉用玎钉机器人申请到的群组 ID
     然后定时执行即可



*  zabbix api 可以参考官方:    
    https://www.zabbix.com/documentation/3.2/manual/api    -- 结合自己zabbix的版本   

