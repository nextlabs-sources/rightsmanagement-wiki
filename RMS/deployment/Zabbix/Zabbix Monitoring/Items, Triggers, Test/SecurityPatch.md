# Monitor any updates in security

The goal is to notify the user through email of any updates in security package

**Installation**

- install yum-security (depends on platform)

- add Userparameter and alter Timeout in /var/local/etc/zabbix_agentd.conf

      - UserParameter =  centos.security, yum list updates --security | grep "needed for security" | awk '{print $1}'

      - Timeout = 30     //the time the server waits for a response. 

**Create Item**

1. Go to Configuration --> Hosts

2. Click on the link Items depending on your host

3. Click "Create item" button

4. Add the following:
 	
    - Name: Security Patch update

    - Type: Zabbix agent (active)

    - Key: User Parameter.security OR host name.security

 	  For example: User Parameter = centos, which is defined in the zabbix_agentd.conf therefore key = centos.security

    - Type of information: Text

    - Update interval (in sec): 60

    - Applications: None

**Create Trigger**

1. Go to Configuration --> Hosts

2. Click on the link triggers depending on your host

3. Click "Create trigger" button

4. Add the following:

    - Name: Centos Security Patch Needs Update

    - Expression: {name of the host: key content.str(No)}=0

		For example : {centos-zabbix-agent:centos.security.str(No)}=0

		name of the host: centos-zabbix-agent

		key content: centos.security


**Test**

1. Go to Monitoring --> Latest data

2. Search for the host name on the top right corner

3. Under "Hosts", select the appropriate Latest data depending on your host name

4. Check for the security update patch data. If the value is 0, then it means there is no security update needed. If it is not 0, then a notification will be sent to the user suggesting the number of updates needed.