# Monitor for EFS Availability

The aim of  EFS availability monitoring is to ensure that the EFS system is always accessible to the current node hence service can be properly restarted when needed.


**Create Item**

1. Click "Create item" button

2. Add the following:
 	
    - Name: EFS Availability Check

    - Type: Zabbix agent

    - Key: vfs.file.exists[/mnt/efs/nextlabs/conf/admin.properties]

	  check the following to understand the parameters: 

	[https://www.zabbix.com/documentation/2.0/manual/config/items/itemtypes/zabbix_agent](https://www.zabbix.com/documentation/2.0/manual/config/items/itemtypes/zabbix_agent)

    - Type of information: Numeric (unsigned)

    - Update interval (in sec): 60

    - Applications: EFS Availability Check

### Create Trigger

1. Click "Create trigger" button

2. Add the following:

    - Name: EFS NOT Available for 5 min

    - Expression: {rmsng-s2:vfs.file.exists[/mnt/efs/nextlabs/conf/admin.properties].last(5m)}<1
    
    - Multiple PROBLEM events generation: tick
    
    - Severity: High

**NOTE** 
This will check if current system can access the admin.properties file in EFS every minute. The trigger will be fired if EFS is not accessible for consecutive 5 mintues. 
