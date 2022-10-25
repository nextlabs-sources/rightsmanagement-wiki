# General Information and Basic Monitoring of CPU Load, Email Configuration

## Introduction
Zabbix is enterprise open source monitoring software for networks and applications. It is designed to monitor and track the status of various network services, servers, and other network hardware. 

Zabbix uses MySQL, PostgreSQL, SQLite, Oracle or IBM DB2 to store data. Its backend is written in C and the web frontend is written in PHP. Zabbix offers several monitoring options:

 - Simple checks can verify the availability and responsiveness of standard services such as SMTP or HTTP without installing any software on the monitored host.   
 - A Zabbix agent can also be installed on UNIX and Windows hosts to monitor statistics such as CPU load, network utilization, disk space, etc.
 - As an alternative to installing an agent on hosts, Zabbix includes support for monitoring via SNMP, TCP and ICMP checks, as well as over IPMI, JMX, SSH, Telnet and using custom parameters. Zabbix supports a variety of near-real-time notification mechanisms, including XMPP.

## Features

- High performance, high capacity (able to monitor hundreds of thousands of devices)
- Auto-discovery of servers and network devices
- Low-level discovery
- Distributed monitoring with centralized web administration
- Support for both polling and trapping mechanisms
- Native high performance agents (client software for Linux, Solaris, HP-UX, AIX, FreeBSD, OpenBSD, OS X, - - - - Tru64/OSF1, Windows 2000, Windows Server 2003, Windows XP, Windows Vista, Windows Server 2008, Windows 7)
- Agent-less monitoring
- JMX monitoring
- Web monitoring
- Secure user authentication
- Flexible user permissions
- Web-based interface
- SLA, and ITIL KPI metrics on reporting
- Flexible e-mail notification on predefined events
- High-level (business) view of monitored resources through user-defined visual console screens and dashboards
- Audit log


## Functionalities

- Data Collection
    - Zabbix agent
    - SNMP and IPMI agents
    - Agentless monitoring
    - Custom monitoring
    - Virtual machine monitoring
    - Web scenarios
    - Java applications
    - Database monitoring
    - Calculated and aggregate information
    - Internal performance
- Problem Detection
- Visualization
    - Zabbix web frontend
    - Global Dashboard
    - Graphs
    - Maps
    - Screen & slide shows
    - Access to raw data
    - Event & notification details
    - Nice little things
- Notification
    - Receive a message
    - Execute a command
    - Escalate response to problems
- Templating & Import/Export
- Auto Discovery
- Distributed Monitoring
- Security and Authentication
- System Management
- Zabbix API
see [functionality of Zabbix for detailed descriptions.](http://www.zabbix.com/functionality.php)

## Architecture

![](http://4.bp.blogspot.com/-2EOxyQ5fUiw/VMnw348EXOI/AAAAAAAAA2k/kprwDhTlDaE/s1600/Complete%2BArchitecture.bmp)

- **Proxy:** All the monitored devices send the data to the proxy (an interface between devices and Zabbix Server). Proxy collects the monitoring data and if something bad happens, proxy informs Zabbix Server
- **Database:** MySQL, Microsoft SQL Server, Oracle etc for storing monitoring data
- **GUI:** Web interface for configuring and working with zabbix
- **Media Server:** email, sms etc for sending alarm to Admin
- **Agent:** Zabbix agents can perform passive and active checks.
    - In a passive check the agent responds to a data request. Zabbix server (or proxy) asks for data, for example, CPU load, and Zabbix agent sends back the result.
    - Active checks require more complex processing. The agent must first retrieve a list of items from Zabbix server for independent processing. Then it will periodically send new values to the server.




## Monitor CPU Load of the agent

**Create a Host** (the device which you want to monitor)

Go to Configuration --> Hosts --> Create host

- Provide the host name

- Put only "Linux servers" in groups

- Put the IP address of zabbix agent (CentOS). You can find the ip address by typing ifconfig. Check that it is connected to IP and the port is 10050

- Update it


### Create an Item(the aspect of the host you want to monitor)

Go to Configuration --> Select a host created --> Items --> Create Item

- Name your item such as "CPU Load" if you want to monitor the CPU load for the particular host

- Type: Zabbix Agent

- Key: system.cpu.load (unique for monitoring CPU loading)
   
   For more information about key go through [Zabbix Item Key documentation.](https://www.zabbix.com/documentation/3.0/manual/config/items/item/key)

- Host interface: < ip address of zabbix agent>:10050

- Type of information: Numberic (float) as the data received by the server will be in float

- Update interval (in sec): 2

- History storage period (in days): 90

- Trend storage period (in days): 365

- Make sure "enabled" is checked

- Update it

### Create a graph(to see the data in a visualised format)

Go to Configuration --> Select a host created --> Graphs --> Create graph

- Input the name of the graph
- Choose graph type as normal
- Enable "show legend" and "show working time"
- Add the item (created in the previous step)
- Update it

### Create a tigger(watches the data and evaluates by a condition provided by us)

Go to Configuration --> Select a host created --> Triggers --> Create Trigger

- Name the trigger
- Expression: for example {Centos-remote-agent:system.cpu.load.avg(60)} > 0.05

   "Centos-remote-agent" is the host name which is provided while creating a host.
   If the cpu load for the host exceeds 5% for the past 1 minute, then there will be a trigger

   For more trigger expressions go through: [https://www.zabbix.com/documentation/3.0/manual/config/triggers/expression](https://www.zabbix.com/documentation/3.0/manual/config/triggers/expression)
- Update it

## User groups
Administration --> User groups --> Create user group

Select the "User groups" tab

- Provide "Group name" eg. Zabbix administrators
- Select in group as "Admin (Zabbix Administrator)"
- Update it

Select the "Permissions" tab

- Under "Composing permissions", grant Discovered hosts, Linux servers, Zabbix servers the read-write permission
- Under "Calculated permissions --> Host groups", grant Discovered hosts, Linux servers, Zabbix servers the read-write permission
- Under "Calculated permissions --> Host", grant **insert host name**, New template, Zabbix servers the read-write permission
- Update it

## Users

Administration --> Users --> Create user

Select the "User" tab

- Alias: Admin
- Name: Zabbix
- Surname: Administrator
- Groups: Zabbix Administrators
- Password: Change password
- Update it

Select the "Media" tab and add it (The notification will be sent to this email address if any problem occurs)
 
- Type: Email
- Send to : rmsuser0@gmail.com
- Make sure all the checkboxes are selected  for "use if severity"

Select the "Permissions" tab

- Grant all the Host groups and Hosts read-write permission
- Update it


## Media Types (Email as example)

Administration --> Media Types --> Create media type

- Name: Email
- Type: Email
- SMTP server: smtp.gmail.com
- SMTP server port: 25
- SMTP helo: gmail.com
- SMTP email: rmsuser0@gmail.com
- Connectivity security: STARTTLS
- Authentication: Normal password
- Username: rmsuser0@gmail.com
- Password: 123next!
- Update it

**Please configure the following for the rms gmail account:**

These steps are necessary as gmail by default does not trust outside applications like the zabbix. As a result, gmail notifications will not reach us. in order to get the notifications, the following configurations need to be done

- Turn on the access for less secure apps [https://www.google.com/settings/security/lesssecureapps](https://www.google.com/settings/security/lesssecureapps) 
- Allow access when signing into a new device or application [https://accounts.google.com/DisplayUnlockCaptcha](https://accounts.google.com/DisplayUnlockCaptcha) 

**Create Actions** (If something goes wrong then the admin must be notified via sms, email or script)

Go to Configuration --> Actions --> Create action

- Actions-->Actions --> Enter Name of the action and check "enabled"
- Actions-->Operations --> "New" in action operations and enter the details
  - Operation type: send message
  - Send to Users --> Choose the Admin
  - Send only to --> Email
  - Select "Default message"
- Update it


**Test**

- Run an infinite loop in the host. (Create a cpp file with infinte loop in zabbix agent)
- Go to graphs tab, select group as "linux servers", host as "Centos-remote-agent" (for example), graph as "CPU remote load graph"
- Observe the graph spikes to 1.0 or 100% because of running the infinte loop
- Under "Monitoring --> triggers", select the group as "Linux servers" and the select the appropriate host for eg. "Centos-remote-agent" the status will change to "PROBLEM" after 1 minute according to the condition created in triggers: {Centos-remote-agent:system.cpu.load.avg(60)} > 0.05
- Check the mail in rmsuser0@gmail.com, you will receive a notification about this problem where the CPU load has exceeded the condition
- You can stop the infinite loop in the host, you will observe that the graph will display the CPU load going down. Once it reaches below 5% (according to the condition), the trigger's status will turn to "OK"

**Run Script**

Instead of sending notification with email, we can also run a script when something we want to monitor is triggered.

1) Go to Administration -> Media types -> Create media type
      
   Create a media type with 'Script' type.

   - Name: Script
   - Type: Script
   - Script name: sendemail.sh (for example)
   - Script parameters: add thrice to put the parameters - {ALERT.SENDTO}, {ALERT.SUBJECT}, {ALERT.MESSAGE}
   - Update it

2) Go to Users -> Create User -> "Media" tab

   Add the media type you just created

    - Type: Script
   - Send to : rmsuser0@gmail.com
   - Make sure all the checkboxes are selected  for "use if severity"
   
3) Go to Configuration -> Actions

   - Create a new action
   - Under 'Action' tab give the name as "Test Script" (for example) and fill in the other details if required
   - In 'Operations' tab, change 'Send only to field' to the media type you created. Other inputs remain the same as mentioned under the topic "Create Actions"

4) Go to Administration -> Scripts

   This script is created in the front-end. It gets executed when the admin addresses the issue in the monitored agent by going to monitoring -> triggers -> clicking on a host -> script options

   - Create a script
   - Fill in the details as:
     - Name: sendmail.sh (for example)
     - Type: script
     - Execute on: zabbix server
     - Commands: (anything)
     - User group: All
     - Host group: Selected -> Linux servers
     - Required host permission: Write
   - Update it

5) Create a script in the zabbix_appliance (zabbix_server)
   
   - Go to the directory /usr/lib/zabbix/alertscripts
   - create a script called sendemail.sh
   - Write the command echo "problem" | sendmail rmsuser0@gmail.com
   - Run the command ./sendemail.sh. (So every time there is a problem, this script will get executed and will send a mail to us)


Other useful commands:
- mailq

- mail -bp

- sudo service sendemail status

- sudo service sendemail restart

- mv /usr/sbin/mail /usr/sbin/sendemail (to figure out the correct path)


After doing the above steps, you are able to run the script.

**Location of useful files:**

- zabbix_server.log : /var/log/zabbix/

- zabbix_server.conf : /etc/zabbix/

# Recommended videos and sites

To understand zabbix in depth please follow these videos:

1. [Zabbix for beginners - Introduction](https://www.youtube.com/watch?v=uqFaz2HyxVM)

2. [Zabbix for beginners - Installing Zabbix](https://www.youtube.com/watch?v=0k6sDBqb35s)

3. [Zabbix for beginners - Working with Zabbix - Part 1](https://www.youtube.com/watch?v=Nk-MY7MWNmE)  **(most important)** 

4. [Zabbix for beginners - Working with Zabbix - Part 2 - Templates with SNMP](https://www.youtube.com/watch?v=uL2ifZwYRnA)

5. [Zabbix for beginners - Working with Zabbix - Part 3 - Templates with Zabbix Agent](https://www.youtube.com/watch?v=_b7qszaK2BA)

6. [Zabbix for beginners - Working with Zabbix - Part 4 - Auto Discovery](https://www.youtube.com/watch?v=TEqOcg-Cf3o)

7. [Zabbix for beginners - Monitoring Tips](https://www.youtube.com/watch?v=_c8CO4XW2xs&index=7&list=PL1NQDf5SnnFXEPjzrDoeeQVK3CJoabAMD)

8. [https://devopstrailer.wordpress.com/2015/06/11/zabbix-aws-and-auto-registration/](https://devopstrailer.wordpress.com/2015/06/11/zabbix-aws-and-auto-registration/)

9. [Zabbix documentation](https://www.zabbix.com/documentation/3.0/start)

10. [https://www.ibm.com/support/knowledgecenter/SSVV6P/Monitoring/startandstopzabbixagentinlinuxos.html](https://www.ibm.com/support/knowledgecenter/SSVV6P/Monitoring/startandstopzabbixagentinlinuxos.html)

11. [https://www.zabbix.com/forum/showthread.php?t=19693](https://www.zabbix.com/forum/showthread.php?t=19693)

12. [https://www.zabbix.com/forum/showthread.php?t=21599](https://www.zabbix.com/forum/showthread.php?t=21599)

13. [https://www.zabbix.com/forum/showthread.php?t=20530](https://www.zabbix.com/forum/showthread.php?t=20530)

14. [http://stackoverflow.com/questions/39340106/zabbix-log-monitoring-how-to-get-notifications-such-that-all-error-logs-appear](http://stackoverflow.com/questions/39340106/zabbix-log-monitoring-how-to-get-notifications-such-that-all-error-logs-appear)

15. [http://stackoverflow.com/questions/11990708/error-cant-connect-to-local-mysql-server-through-socket-var-run-mysqld-mysq](http://stackoverflow.com/questions/11990708/error-cant-connect-to-local-mysql-server-through-socket-var-run-mysqld-mysq)

16. [https://talk.plesk.com/threads/no-logs-for-fail2ban.329868/](https://talk.plesk.com/threads/no-logs-for-fail2ban.329868/)

17. [http://www.cyberciti.biz/faq/iptables-connection-limits-howto/](http://www.cyberciti.biz/faq/iptables-connection-limits-howto/)

18. [https://javapipe.com/iptables-ddos-protection](https://javapipe.com/iptables-ddos-protection)

19. [http://www.thegeekstuff.com/2011/06/iptables-rules-examples/](http://www.thegeekstuff.com/2011/06/iptables-rules-examples/)

20. [https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sect-System_Monitoring_Tools-Net-SNMP-Configuring.html](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sect-System_Monitoring_Tools-Net-SNMP-Configuring.html)