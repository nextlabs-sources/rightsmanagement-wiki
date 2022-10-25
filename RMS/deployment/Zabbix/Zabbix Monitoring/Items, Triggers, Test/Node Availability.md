# Monitor for Agent Node Availability

The aim of  Agent Node availability monitoring is to ensure that the whenever a node is down for 5 minutes, support team will be notified.


**Create Item**

1. Click "Create item" button

2. Add the following:
 	
    - Name: Agent Availability

    - Type: Zabbix agent

    - Key: agent.ping

	  check the following to understand the parameters: 

	[https://www.zabbix.com/documentation/2.0/manual/config/items/itemtypes/zabbix_agent](https://www.zabbix.com/documentation/2.0/manual/config/items/itemtypes/zabbix_agent)

    - Type of information: Numeric (unsigned)

    - Update interval (in sec): 60

    - Applications: Agent Availability Check

### Create Trigger

1. Click "Create trigger" button

2. Add the following:

    - Name: Agent Unavailable for 5 min

    - Expression:{rmsng-s2:agent.ping.nodata(5m)}=1
    
    - Multiple PROBLEM events generation: tick
    
    - Severity: Disaster