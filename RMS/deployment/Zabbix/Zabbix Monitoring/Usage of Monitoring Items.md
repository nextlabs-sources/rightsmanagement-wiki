# Usage of Monitoring Items

It is necessary to monitor aspects of the system:

- easier to understand which aspect of it is causing the issue

- attending to those issues become faster

## Requirements:

1) General server health:

- Check ssh log to see any one attacking our server, and setup firewall rule to block IP for 5 minutes, or you can install fail2ban to block hacker attack

    - grep sshd.\\*Failed /var/log/auth.log

    - grep sshd.*Did /var/log/auth.log

- Make sure security patch is up to date.

- Monitor disk space usage (assume AWS will do it for us)

- Monitor memory usage  (assume AWS will do it for us)

- Monitor CPU usage (assume AWS will do it for us)

- Make sure we didn’t’ exceed file handler and i-node (handler leak is a common server issue)


2) Tomcat health:

- Check if 80 and 443 port is listening.

- Scan logs looking for Errors in : $CATALINA_HOME/logs/localhost.*.log

- Scan logs looking for Errors in : $CATALINA_HOME/logs/catalina.out


3) Database:

- Make sure we didn’t max out connection limit.


4) Router

- Basic router API is responding within X milliseconds, You can use [https://xxxx/router/rs/q/tenant/skydrm.com](https://xxxx/router/rs/q/tenant/skydrm.com) See document: [https://r.skydrm.com/router/rs](https://r.skydrm.com/router/rs)

- Scan logs: $CATALINA_HOME/logs/router.*.log


5) RMS:

- Basic RMS API is responding within X milliseconds, You can use [https://xxx/rms/rs/upgrade?tenant=skydrm](https://xxx/rms/rs/upgrade?tenant=skydrm) See document: [https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/Check_Upgrade_REST_API](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/Check_Upgrade_REST_API)

- Scan logs: $CATALINA_HOME/logs/rms.*.log

- Scan audit tail for login failure attempt: (Not available in this version yet)


6) Viewer

- Test viewer's health by checking the availability of viewer's css file

- Scan logs: /opt/nextlabs/viewer/logs/


7) Monitoring system health

- Zabbix need monitor each other to make sure itself is running properly.


## Proposed solutions for the requirements

Detailed steps are provided under the links:

- [https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/browse/RMS/deployment/Zabbix/Zabbix%20Monitoring/Items](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/browse/RMS/deployment/Zabbix/Zabbix%20Monitoring/Items)

- [https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/browse/RMS/deployment/Zabbix/Zabbix%20Monitoring/Triggers](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/browse/RMS/deployment/Zabbix/Zabbix%20Monitoring/Triggers)


1) **Log Monitoring**

- log monitoring requires active zabbix agent which must be enabled in the configuration

- in the frontend of zabbix server, create Log item. Use logrt instead log as the item key because logrt supports log rotation and pattern matching of log file path


2) **Service Checking(aka. certain port is listening)**

- zabbix native supports checking if certain port is in LISTEN state with net.tcp.listen[<port>]

- agent.ping to see if there is a basic connection


3) **Security Patch up-to-date**

- install yum-security (depends on platform)

- add Userparameter and alter Timeout in /var/local/etc/zabbix_agentd.conf

      - UserParameter =  centos.security, yum list updates --security | grep "needed for security" | awk '{print $1}'

      - Timeout = 30

- in the frontend of zabbix server, create item based on centos.security and create trigger if this parameter returns value other then “0”


4) **Fail2Ban** 

- install fail2ban

- change the config file to setup maximum tries and banning time

- follow https://www.youtube.com/watch?v=YJE-lzeeDKw


5) **File Handle** 

- check how many files a process has opened: 

     - ls /<PID>/fd | wc -l

- check the limits of how many files a process can open: 

     - cat /proc/<PID>/limits | grep ^Max\ open\ files

- Alternatively, it can also be taken care by AWS


6) **Response time**

- Follow: https://www.youtube.com/watch?v=EWV8A29wvlk 

- Create an item

     - Key: web.page.perf[Zabbix server]

     - Type: Zabbix agent

- Create a trigger that will send a notification to us when the response time exceeds
            beyond 5s for 1 minute both the rms and router APIs. This notification is sent to us
            only when the status goes from OK to PROBLEM. The average time taken for both 
            These APIs ranges from 980ms to 3.5s

      - {Zabbix server:web.test.time[SKYDRM_ROUTER,Response time 
ROUTER_API,resp].avg(60)}>5 


7) **Monitoring system health** 

- Install another zabbix appliance. Use the ip address of that appliance to access the front-end

- The monitored zabbix appliance is the host

      - Follow steps in 2.

- Alternatively, it can also be taken care by AWS. There exists a template within AWS to conduct self-checking of the zabbix server


8) **Database connection** 

- Follow the steps from: [https://www.zabbix.com/documentation/2.4/manual/config/items/itemtypes/odbc_checks](https://www.zabbix.com/documentation/2.4/manual/config/items/itemtypes/odbc_checks)

- Updates:

      - ODBC connection working on the Ubuntu VM 

      - While trying to add a database item, “out of memory” error comes from MySQL

      - The solution to this is to add more memory to the size of VM disk (already done), and also to increase the size of the partition.  Hopefully it will be functional and this is still in progress

- Alternatively, it can also be taken care by AWS

