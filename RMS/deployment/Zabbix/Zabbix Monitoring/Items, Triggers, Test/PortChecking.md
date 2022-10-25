# Monitoring if certain port is listening

The goal is to notify the user if the port is not listening

**Create Item**

1. Go to Configuration --> Hosts

2. Click on the link Items depending on your host

3. Click "Create item" button

4. Add the following:
 	
    - Name: Tomcat Health Port 443 state

    - Type: Zabbix agent 

    - Key: net.tcp.listen[443]         //Checks if this TCP port is in LISTEN state. Returns 0 - it is not in LISTEN state; 1 - it is in LISTEN state

    - Host interface: ip address of the agent:10050

    - Type of information: Numeric (unsigned)

    - Data type: Decimal

    - Update interval (in sec): 30

    - Applications: None


**Create Trigger**

1. Go to Configuration --> Hosts

2. Click on the link triggers depending on your host

3. Click "Create trigger" button

4. Add the following:

    - Name: Port not listening

    - Expression: {name of host: key content.last()}=0

		For example : {centos-zabbix-agent:net.tcp.listen[443].last()}=0

		name of the host: centos-zabbix-agent

		key content: net.tcp.listen[443]

        last() returns the recent most value


**Test**

1. Disconnect tomcat, and a notification will be sent about the port not listening