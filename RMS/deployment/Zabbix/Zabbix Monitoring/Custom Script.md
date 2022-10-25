# Run Remote commands 

The goal is to run some customized commands to restore service when certain situation happens. A specific case would be immediately bringing back tomcat service once it crashed. 

**Create Item**

1. Go to Configuration --> Actions

2. Click on the Action panel and add the following:
 	
    - Name: Tomcat Restore

    - Default subject: Tomcat service failed at host {HOST.HOST} {HOST.IP}. Zabbix is trying to restart the service

    - Default message: put in host's information and trigger's information using Zabbix's macros. 

3. Click on the Conditions panel and add the following:
    
    - A: New Condition: Maintenance status not in maintenance
    - B: New Condition: Trigger value = PROBLEM
    - C: New Condition: Trigger = Template Tomcat Health: Tomcat Health: Port 80 NOT Listening
    - D: New Condition: Trigger value = Template Tomcat Health: Tomcat Health: Port 443 NOT Listening
    - Type of Calculation: A and B and (C or D)

3. Click on the Conditions panel and add the following:
    
    - Default operation step duration: 60
    - Action operations: 
    - send message to Zabbix administrators
    - Run remote commands
        - step duration: 60
        - operation type: Remote command
        - target list: current host
        - Type: Custom Script 
        - Execute on: zabbix agent
        - Commands
```
sudo /bin/systemctl stop tomcat;
sudo rm -f /opt/tomcat/temp/tomcat.pid;
sudo /bin/systemctl start tomcat
```