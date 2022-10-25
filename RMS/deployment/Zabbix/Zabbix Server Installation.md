# Installation Methods #

There are 3 ways of getting zabbix:

1. Install it from the distribution packages
2. Download the latest source archive and compile it yourself
3. Download virtual appliance or zabbix appliance

Installation method 1 and 3 are introduced in detail as follows.

# Install it from the distribution packages
In this method introduction, we are deploying Zabbix server in a machine with CentOS 7. Before Installing Zabbix Server, two prerequisites must be fulfilled. 

* a sudo non-root user for Zabbix
* Apache, MySQL, and PHP installed

Follow the steps below to set up these two. If all of them are already installed, steps 1 and 2 can be skipped.

### Step 1: Set up a sudo non-root user for Zabbix ##

The root user is the administrative user in a Linux environment that has very broad privileges. Because of the heightened privileges of the root account, you are actually discouraged from using it on a regular basis. This is because part of the power inherent with the root account is the ability to make very destructive changes, even by accident. That is why Zabbix needs its own user access.

```
ssh root@SERVER_IP_ADDRESS
```

Complete the login process by accepting the warning about host authenticity, if it appears, then providing your root authentication (password or private key). If it is your first time logging into the server, with a password, you will also be prompted to change the root password.

**Create a New User**

Once you are logged in as root, we're prepared to add the new user account that we will use to log in from now on. Execute the command below to create a user named zabbix

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

### Step 2: Install Apache, MySQL, PHP stack on CentOS 7 ##

The server will use a MySQL database to record monitoring data and use Apache to serve the web interface. It also provides a web interface so you can view data and configure system settings. 

**Install Apache**

We can install Apache easily using CentOS's package manager, yum. A package manager allows us to install most software pain-free from a repository maintained by CentOS. For our purposes, we can get started by typing these commands:

```
sudo yum install httpd
```

Since we are using a sudo command, these operations get executed with root privileges. It will ask you for your regular user's password to verify your intentions.
Afterwards, your web server is installed.
Once it installs, you can start Apache on your CentOS:

```
sudo systemctl start httpd.service
```

The last thing you will want to do is enable Apache to start on boot. Use the following command to do so:

```
sudo systemctl enable httpd.service
```

**Install MySQL (MariaDB)**

Now that we have our web server up and running, it is time to install MariaDB, a MySQL drop-in replacement. MariaDB is a community-developed fork of the MySQL relational database management system. Basically, it will organize and provide access to databases where our site can store information.
Again, we can use yum to acquire and install our software. This time, we'll also install some other "helper" packages that will assist us in getting our components to communicate with each other:

```
sudo yum install mariadb-server mariadb
```

When the installation is complete, we need to start MariaDB with the following command:

```
sudo systemctl start mariadb
```

Now that our MySQL database is running, we want to run a simple security script that will remove some dangerous defaults and lock down access to our database system a little bit. Start the interactive script by running:

```
sudo mysql_secure_installation
```

The prompt will ask you for your current root password. Since you just installed MySQL, you most likely won’t have one, so leave it blank by pressing enter. Then the prompt will ask you if you want to set a root password. Go ahead and enter Y, and follow the instructions:

```
Enter current password for root (enter for none):
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorization.

New password: password
Re-enter new password: password
Password updated successfully!
Reloading privilege tables..
 ... Success!
```

For the rest of the questions, you should simply hit the "ENTER" key through each prompt to accept the default values. This will remove some sample users and databases, disable remote root logins, and load these new rules so that MySQL immediately respects the changes we have made.

The last thing you will want to do is enable MariaDB to start on boot. Use the following command to do so:

```
sudo systemctl enable mariadb.service
```

At this point, your database system is now set up and we can move on.


**Install PHP**

PHP is the component of our setup that will process code to display dynamic content. It can run scripts, connect to our MySQL databases to get information, and hand the processed content over to our web server to display.

We can once again leverage the yum system to install our components. We're going to include the php-mysql package as well:

```
sudo yum install php php-mysql
```

This should install PHP without any problems. We need to restart the Apache web server in order for it to work with PHP. You can do this by typing this:

```
sudo systemctl restart httpd.service
```

### Step Three: Installing the Zabbix Server ##

Now with Zabbix user created and LAMP stack installed properly, we can move on to install Zabbix Server 

**Install Zabbix Server Packages **

Zabbix isn't available in our package manager by default, so we will install a repository configuration package using the official Zabbix repository for CentOS:

```
sudo rpm -ivh http://repo.zabbix.com/zabbix/3.0/rhel/7/x86_64/zabbix-release-3.0-1.el7.noarch.rpm
```

You will see the following output:

```
Retrieving http://repo.zabbix.com/zabbix/3.0/rhel/7/x86_64/zabbix-release-3.0-1.el7.noarch.rpm
warning: /var/tmp/rpm-tmp.qLbOPP: Header V4 DSA/SHA1 Signature, key ID 79ea5ed4: NOKEY
Preparing...                          ################################# [100%]
Updating / installing...
   1:zabbix-release-3.0-1.el7         ################################# [100%]
```

Now you can run the following command to install the Zabbix server and web frontend with MySQL database support:

```
sudo yum install zabbix-server-mysql zabbix-web-mysql
```

During the installation process you will be asked about importing a GPG key. Confirm it so the installation can complete.


**Configuring the MySQL Database For Zabbix**

We need to create a new MySQL database and populate it with some basic information in order to make it suitable for Zabbix. Log into MySQL as the root user using the root password that you set up during the MySQL server installation:

```
mysql -uroot -p
```

First, create the Zabbix database with UTF-8 character support:

```
create database zabbix character set utf8;

```

Next, create a user that the Zabbix server will use, give it access to the new database, and set the password:

```
grant all privileges on zabbix.* to zabbix@localhost identified by '123next!';
```

Then apply these new permissions:

```
flush privileges;
```

That takes care of the user and the database. Exit out of the database console.

```
quit;
```

Next we have to import the initial schema and data. The Zabbix installation provided us with a file that sets this up for us. We just have to import it. Navigate to the directory:

```
cd /usr/share/doc/zabbix-server-mysql-3.0.4/
```

Run the following command to set up the schema and import the data into the zabbix database. We'll use zcat since the data in the file is compressed.

```
zcat create.sql.gz | mysql -uzabbix -p zabbix
```

Enter the password for the zabbix user that you configured when prompted.
This command will not output any errors if it was successful. If you see the error ERROR 1045 (28000): Access denied for user 'zabbix'@'localhost' (using password: YES) then make sure you used the password for the zabbix user and not the root user.

In order for the Zabbix server to use this database, you need to set the database password in the Zabbix server configuration file.

```
sudo vi /etc/zabbix/zabbix_server.conf
```

Look for the following section of the file:

```
### Option: DBPassword                           
#       Database password. Ignored for SQLite.   
#       Comment this line if no password is used.
#                                                
# Mandatory: no                                  
# Default:                                       
# DBPassword=
DBPassword=123next!
```

That takes care of the Zabbix server configuration, but we have to make some modifications to our PHP setup in order for the Zabbix web interface to work properly.

**Configuring PHP For Zabbix**

The Zabbix web interface is written in PHP and requires some special PHP server settings. The Zabbix installation process created an Apache configuration file that contains these settings. It is located in the directory /etc/httpd/conf.d/ and is loaded automatically by Apache. We need to make a small change to this file, so open it up.

```
sudo vi /etc/httpd/conf.d/zabbix.conf
```

The file contains PHP settings that meet the necessary requirements for the Zabbix web interface. The only change you need to make is to set the appropriate timezone, which is commented out by default.

```
<IfModule mod_php5.c>
    php_value max_execution_time 300
    php_value memory_limit 128M
    php_value post_max_size 16M
    php_value upload_max_filesize 2M
    php_value max_input_time 300
    php_value always_populate_raw_post_data -1
    # php_value date.timezone Europe/Riga
</IfModule>
```

Uncomment the timezone line, highlighted above, and change it to Asia/Singapore. You can use this list of supported time zones to find the right one for you. Then save and close the file.

Now restart Apache to apply these new settings.

```
sudo systemctl restart httpd
```

You can now start the Zabbix server.

```
sudo systemctl start zabbix-server
```

Finally, enable the Zabbix server to start at boot time:

```
sudo systemctl enable zabbix-server
```

**Configuring Settings for the Zabbix Web Interface**

The web interface lets us see reports and add hosts that we want to monitor, but it needs some initial setup before we can use it. Launch your browser and go to the address http://your_zabbix_server_ip_address/zabbix/. On the first screen, you will see a welcome message. Click Next step to continue.

On the next screen, you will see the table that lists all of the prerequisites to run Zabbix. 

![3wJ78k4.png](https://bitbucket.org/repo/dBgzdj/images/2554956014-3wJ78k4.png)

All of the values in this table must show OK, so verify that they do. Be sure to scroll down and look at all of the prerequisites. Once you've verified that everything is ready to go, click Next step to proceed.

The next screen asks for database connection information.

![fGJhZo1.png](https://bitbucket.org/repo/dBgzdj/images/1929565442-fGJhZo1.png)

We told the Zabbix server about our database, but the Zabbix web interface also needs access to the database to manage hosts and read data so it can display it to us. Enter the MySQL credentials you configured in Step 2 and click Next step to proceed.

On the next screen, you can leave the options at their default values.

![OwORmZh.png](https://bitbucket.org/repo/dBgzdj/images/3569007720-OwORmZh.png)

The Name is optional; it is used in the web interface to distinguish one server from another in case you have several monitoring servers. Click Next step to proceed.

The next screen will show the pre-installation summary so you can confirm everything is correct.

![X0POG7S.png](https://bitbucket.org/repo/dBgzdj/images/3697679164-X0POG7S.png)

Click Next step to proceed to the final screen.

The web interface setup is complete! This process creates the configuration file /etc/zabbix/web/zabbix.conf.php which you could back up and use in the future. Click Finish to proceed to the login screen. You can request login credentials from any dev-ops team members. 

If seeing zabbix server is not running issue, execute the commands below to fix the issue.
```
getsebool -a 
setsebool -P zabbix_can_network=true 
setsebool -P httpd_can_network_connect=true
```

### Zabbix Server Configuration File
Make sure the following are set in `/etc/zabbix/zabbix_server.conf`. The rest parameters can leave as default.

```
#!conf
LogFile = /var/log/zabbix/zabbix_server.log
LogFileSize = 10 # 10MB of log file
DebugLevel = 4 # debugging mode
PidFile = /var/run/zabbix/zabbix_server.pid
DBName = zabbix (already present)
DBUser = zabbix (already present)
DBPassword = 123next!
UnreachablePeriod=900
UnreachableDelay=300
```

Restart the Zabbix Server to make sure the setting in `/etc/zabbix/zabbix_server.conf` is realized. Whenever the configuration file is changed, restart the service to apply the changes. 

# Method 3: Download virtual appliance or zabbix appliance #

As an alternative to setting up manually or reusing an existing server for Zabbix, users may download a Zabbix appliance or Zabbix appliance installation CD image. The latest version of Appliance is based on Ubuntu Linux with MySQL back-end. Zabbix software is pre-installed and pre-configured for trouble free deployment.

To get started, boot the appliance and point your browser at the IP it has received over DHCP: http://<host_ip>/zabbix

It has Zabbix server configured and running on MySQL, as well as frontend available.

The appliance has been built using standard Ubuntu/Debian feature called Preseed files.


# Communication Port
To enable communication between Zabbix Agent and Zabbix Server, port **10050** and **10051** must be opened for inbound traffic on both sides. Please check with system administrator to ensure such inbound policies.

Zabbix Server will send out notification emails out to people in charge via smtp services. Hence, port 25 must be open on Zabbix Server. 

Currently in production, a dedicated security group called SKYDRM-ZABBIX-SG has all the inbound and outbound policies configured.  

# References #

1. [Initial Server Setup with CentOS 7](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-centos-7)
2. [How To Install Linux, Apache, MySQL, PHP (LAMP) stack On CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-centos-7)
3. [How To Install and Configure Zabbix to Securely Monitor Remote Servers on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-zabbix-to-securely-monitor-remote-servers-on-centos-7)
4. [Zabbix appliance](https://www.zabbix.com/documentation/3.0/manual/appliance)