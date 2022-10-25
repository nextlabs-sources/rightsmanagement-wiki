#Install awslogs agent


* curl https://s3.amazonaws.com//aws-cloudwatch/downloads/latest/awslogs-agent-setup.py -O
* sudo python ./awslogs-agent-setup.py --region us-east-1

#Configure agent 


```
#!bash

cd /var/awslogs/etc
vi awslogs.conf
```


Paste the below contents
 
 
```
#!bash

 [opt/tomcat/logs/rms.*.log]
 datetime_format = %Y-%m-%d %H:%M:%S
 file = opt/tomcat/logs/rms.*
 buffer_duration = 1000
 log_stream_name = RMTestLog
 initial_position = end_of_file
 log_group_name = RMTestLog
```

```
#!bash

service awslogs restart
```