# Monitor for any critical data in the log files

The aim of log monitoring is to ensure that the user is notified of any "error" keyword appearing in the log file.

NOTE: Each error lines will be sent in a separate mails. For example if there are 100 error lines in the log file, it means we will receive 100 emails. This is an open issue of zabbix.

**Create Item**

1. Go to Configuration --> Hosts

2. Click on the link Items depending on your host

3. Click "Create item" button

4. Add the following:
 	
    - Name: Catalina Log Error Scan

    - Type: Zabbix agent (active)

    - Key: log[/root/test/test.log,"ERROR",,10,skip,]

	  check the following to understand the parameters: 

	[https://www.zabbix.com/documentation/2.0/manual/config/items/itemtypes/zabbix_agent](https://www.zabbix.com/documentation/2.0/manual/config/items/itemtypes/zabbix_agent)

    - Type of information: Log

    - Update interval (in sec): 60

    - Applications: None

**Create Trigger**

1. Go to Configuration --> Hosts

2. Click on the link triggers depending on your host

3. Click "Create trigger" button

4. Add the following:

    - Name: Catalina Log error Found

    - Expression: {name of host: key content.regexp(ERROR)}=1

		For example : {centos-zabbix-agent:log[/root/test/test.log,"ERROR",,10,skip,].regexp(ERROR)}=1

		name of the host: centos-zabbix-agent

		key content: log[/root/test/test.log,"ERROR",,10,skip,] 

**Test**

1. Create a sample test.log:

     20160906:062251 INFO: good

     20160906:062251 ERROR: bad

2. You will receive a mail informing about the occurence of new error as:

     20160906:062251 ERROR: bad
