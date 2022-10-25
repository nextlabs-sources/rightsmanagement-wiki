# Active agent auto-registration

It is possible to allow active Zabbix agent auto-registration, after which the server can start monitoring them. This way new hosts can be added for monitoring without configuring them manually on the server. Auto registration can happen when a previously unknown active agent asks for checks.

The feature might be very handy for automatic monitoring of new Cloud nodes, in our case, new instances brought up by auto-scaling in AWS. As soon as you have a new node in the Cloud Zabbix will automatically start the collection of performance and availability data of the host. This requres the AMI launched by auto-scaling should have Zabbix agent properly configured and the service needs to be started by the system. Server, when adding the new auto-registered host, uses the received IP address and port to configure the agent. If no IP address value is received, the one used for the incoming connection is used. If no port value is received, 10050 is used. 

Currently in our configuration, IP address is used to distinguish node identity. 

## Configuration

### Specify server

Make sure you have the Zabbix server identified in the agent configuration file - zabbix_agentd.conf

```
ServerActive=zabbix.skydrm.com
```

Since the instance brought up by auto scaling will have the same zabbix_agentd.conf, the hostname is used to differentiate the type of the instance such as rms, viewer or router. Depending on the hostname, we can decide which template should be linked during auto-regisration.

Restart the agent after making any changes to the configuration file.

### Action for active agent auto-registration

When server receives an auto-registration request from an agent it calls an action. An action of event source “Auto registration” must be configured for agent auto-registration.

![Capture.PNG](https://bitbucket.org/repo/dBgzdj/images/3499575530-Capture.PNG)

In the Zabbix frontend, go to Configuration → Actions, select Auto registration as the event source and click on Create action:

* In the Action tab, give your action a name
* In the Conditions tab, optionally specify conditions.
  * hostname like rms or viewer or router 
* In the Operations tab, add relevant operations, such as - 'Add host', 'Send message to user groups' (for example, Zabbix Admin), 'Link to templates' (for example, TemplateRMS), etc.

# References

* [Auto Registration](https://www.zabbix.com/documentation/3.0/manual/discovery/auto_registration)