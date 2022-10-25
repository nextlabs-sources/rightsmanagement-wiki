# Zabbix Agent Installation

## Introduction

In the machines that we are going to monitor, we need to install Zabbix Agent to actively monitor local resources and applications (hard drives, memory, processor statistics etc). 

 The agent gathers operational information locally and reports data to Zabbix server for further processing. In case of failures (such as a hard disk running full or a crashed service process), Zabbix server can actively alert the administrators of the particular machine that reported the failure.

Zabbix agents are extremely efficient because of use of native system calls for gathering statistical information. 

### Passive and active checks

Zabbix agents can perform **passive** and **active** checks.

In a passive check the agent responds to a data request. Zabbix server (or proxy) asks for data, for example, CPU load, and Zabbix agent sends back the result.

Active checks require more complex processing. The agent must first retrieve a list of items from Zabbix server for independent processing. Then it will periodically send new values to the server.

Whether to perform passive or active checks is configured by selecting the respective monitoring item type. Zabbix agent processes items of type 'Zabbix agent' or 'Zabbix agent (active)'.

## Installation
First, it is required to create a dedicated user called zabbix for Zabbix Agent. This user should have permission to perform sudo commands with a password.

**Create a New User**

Once you are logged in as root, we can to add the new user account that we will use to log in from now on. Execute the command below to create a user named zabbix.

```
adduser zabbix

```

Next, assign a password to the new user

```
#!bash

passwd zabbix
```

Enter the password twice to set it.

**Root Privileges**

we can set up what is known as "super user" or root privileges for our zabbix account. This will allow our zabbix user to run commands with administrative privileges by putting the word sudo before each command. To add these privileges to our zabbix user, we need to add zabbix user to the "wheel" group. By default, on CentOS 7, users who belong to the "wheel" group are allowed to use the sudo command.
As root, run this command to add zabbix user to the wheel group

```
#!bash

gpasswd -a zabbix wheel
```

Now switch to user Zabbix and perform the following steps.
```
sudo su zabbix
```

Since we are requiring Zabbix to run remote commands(such as restarting tomcat service), we have to disable 'requiretty' for user Zabbix and allow zabbix to run in SELinux permissive mode. 

First, run ```visudo``` to bring out the sudoers profile. add these two lines. 

```
Defaults:zabbix !requiretty

zabbix    ALL=NOPASSWD: ALL
```

Then, add zabbix domain under SELinux permissive list.

```
semanage permissive -a zabbix_agent_t
```

Check the permissive list using command ```semanage permissive -l```.


### Step 1 — install a repository configuration package
Zabbix isn't available in our package manager by default, so we will install a repository configuration package using the official Zabbix repository for CentOS.
```
sudo rpm -ivh http://repo.zabbix.com/zabbix/3.0/rhel/7/x86_64/zabbix-release-3.0-1.el7.noarch.rpm
```

You will see the following output:

```
Output
Retrieving http://repo.zabbix.com/zabbix/3.0/rhel/7/x86_64/zabbix-release-3.0-1.el7.noarch.rpm
warning: /var/tmp/rpm-tmp.qLbOPP: Header V4 DSA/SHA1 Signature, key ID 79ea5ed4: NOKEY
Preparing...                          ################################# [100%]
Updating / installing...
   1:zabbix-release-3.0-1.el7         ################################# [100%]
```

Run the commands to install the Zabbix agent:

```
sudo yum install zabbix-agent
```

### Step 2 - Configuring Zabbix Agent

Make sure the following are present in `/etc/zabbix/zabbix_agentd.conf`:

```
#!conf
LogFile = /tmp/zabbix_agentd.log (or the default place)
EnableRemoteCommands=1
LogRemoteCommands=1
Server = zabbix.testdrm.com
ServerActive = zabbix.testdrm.com (depends on the real zabbix server address)
Hostname = #(the host name created in the front-end)
UserParameter = centos.security, yum list updates --security | grep "needed for security" | awk '{print $1}'
Timeout = 60
StartAgents=5
```

### Step 3 - Start Zabbix Agent
Run the commands below to start Zabbix Agent:
```
sudo systemctl start zabbix-agent
```

You might as well enable Zabbix Agent to start at boot time:
```
sudo systemctl enable zabbix-agent
```

# Database Connectivity Check

If the agent is supposed to check on the database connection status in the monitored node, please go through the following steps to install the necessary packages and make the proper configurations. 

### Install Postgres Client Libraries

Type the command below to install the client libraries for PostgresSQL.
```
sudo yum install postgresql-libs
```

### Install libzbxpgsql
Download the libzbxpgsql package for your operating system and Zabbix version from [here](http://cavaliercoder.com/libzbxpgsql/download/). 

Install with the native package manager.
```
sudo rpm -i libzbxpgsql-1.0.0-1.el7.x86_64.rpm
```

### Validate installation
Check the installed version with:
```
cat /var/log/zabbix/zabbix_agentd.log | grep loaded
 4552:20161010:075129.198 loaded modules: libzbxpgsql.so
```

# Communication Port
To enable communication between Zabbix Agent and Zabbix Server, port 10050 and 10051 must be opened for inbound traffic on both sides. Please check with system administrator to ensure such inbound policies. 

# Reference
1. [How To Install and Configure Zabbix to Securely Monitor Remote Servers on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-zabbix-to-securely-monitor-remote-servers-on-centos-7)
2. [PostgreSQL monitoring for Zabbix](http://cavaliercoder.com/libzbxpgsql/documentation/)
3. [SELinux Permissive versus enforcing](https://wiki.gentoo.org/wiki/SELinux/Tutorials/Permissive_versus_enforcing)