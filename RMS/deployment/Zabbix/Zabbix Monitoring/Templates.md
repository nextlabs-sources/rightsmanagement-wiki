# Templates

A template is a set of entities that can be conveniently applied to multiple hosts. 

The entities may be:

* items
* triggers
* graphs
* applications
* screens (since Zabbix 2.0)
* low-level discovery rules (since Zabbix 2.0)
* web scenarios (since Zabbix 2.2)


As many hosts in real life are identical or fairly similar so it naturally follows that the set of entities (items, triggers, graphs,…) you have created for one host, may be useful for many. Of course, you could copy them to each new host, but that would be a lot of manual work. Instead, with templates you can copy them to one template and then apply the template to as many hosts as needed. Another benefit of using templates is when something has to be changed for all the hosts. Changing something on the template level once will propagate the change to all the linked hosts. For example, every instance needs checking tomcat service availability and monitoring catalina.out log file. All these tomcat related monitoring tasks can be encapsulated into one template and apply to each instance. 


To support auto-registration of new instances brought up by auto-scaling in AWS, templates are necessary for configuring the monitoring tasks of the new instance automatically. During auto-registration, you can set up which template(s) to link to the new instance and  all the monitoring tasks will be set up upon registration. 

## Creating a template

Configuring a template requires that you first create a template by defining its general parameters and then you add entities (items, triggers, graphs etc.) to it. 

To create a template, do the following:

* Go to Configuration → Templates
* Click on Create template
* Edit template attributes

![template.png](https://bitbucket.org/repo/dBgzdj/images/114998894-template.png)

After editing all the necessary attributes, you can link existing templates to the current template in the Linked Templates tab. For example, now we are creating the template for an RMS node. We have already created templates for tomcat health check, EFS availability check and all the other rms-related checks in several independent templates. We can link all the related templates to this template and once a new RMS node registers to Zabbix Server, we only need to link this template to the new instance instead of linking templates one by one.

![Capture.PNG](https://bitbucket.org/repo/dBgzdj/images/2890380502-Capture.PNG)

The Macros tab allows you to define template-level user macros. You may also view here macros from linked templates and global macros if you select the Inherited and template macros option. That is where all defined user macros for the template are displayed with the value they resolve to as well as their origin. This is used in our template for checking database reachability from each node. 

# References 

* [Configuring a template](https://www.zabbix.com/documentation/3.0/manual/config/templates/template)