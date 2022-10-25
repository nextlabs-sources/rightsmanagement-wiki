##Download zabbix repostory for Ubuntu 14.04 LTS
```
$ wget http://repo.zabbix.com/zabbix/2.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_2.4-1+trusty_all.deb
$ sudo dpkg -i zabbix-release_2.4-1+trusty_all.deb
$ sudo apt-get update
```
>You can find all versions from: [http://www.zabbix.com/download.php](http://www.zabbix.com/download.php)

##Install database server
Make sure postgres is installed
```
$ sudo apt-get install postgresql
$ sudo -u postgres psql

postgres=>alter user postgres password 'postgres';
```

##Install apache2 server with SSL enabled


##Install zabbix server with postgres
```
$ sudo apt-get install zabbix-server-pgsql
$ sudo service zabbix-server start
```

* zabbix server configuration file will be installed in `/etc/zabbix/`
* zabbix server will listening on port **10051** by default.

##Install zabbix frontend
```
$ sudo apt-get install php5-pgsql
$ sudo apt-get install zabbix-frontend-php
```

##Editing PHP configuration for Zabbix frontend
Apache configuration file for Zabbix frontend is located in `/etc/apache2/conf-enabled/zabbix.conf`

>Uncomment the **date.timezone** setting and set the correct timezone:

```
$ sudo vi /etc/apache2/conf-enabled/zabbix.conf

php_value date.timezone America/Los_Angeles

$ sudo service apache2 restart
```

## Setup Virtual host for zabbix
```
$ sudo cp /etc/apache2/sites-available/default-ssl.conf /etc/apache2/sites-available/zabbix.conf
$ sudo vi /etc/apache2/sites-available/zabbix.conf
```

/etc/apache2/sites-available/zabbix.conf should looks like:
```
<IfModule mod_ssl.c>
        <VirtualHost _default_:443>
                ServerName zabbix.nextlabs.com
                ServerAdmin admin@nextlabs.com

                DocumentRoot /usr/share/zabbix
                ErrorLog ${APACHE_LOG_DIR}/error.log
                CustomLog ${APACHE_LOG_DIR}/access.log combined

                #Include conf-available/serve-cgi-bin.conf
                SSLEngine on
                SSLCertificateFile     	/opt/nextlabs/keys/gerrit.nextlabs.com.crt
		SSLCertificateKeyFile 	/opt/nextlabs/keys/gerrit.nextlabs.com.key
                SSLCertificateChainFile	/opt/nextlabs/keys/CA.crt
                <FilesMatch "\.(cgi|shtml|phtml|php)$">
                                SSLOptions +StdEnvVars
                </FilesMatch>
                <Directory /usr/lib/cgi-bin>
                                SSLOptions +StdEnvVars
                </Directory>

                BrowserMatch "MSIE [2-6]" \
                                nokeepalive ssl-unclean-shutdown \
                                downgrade-1.0 force-response-1.0
                # MSIE 7 and newer should be able to use keepalive
                BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown
        </VirtualHost>
</IfModule>
```

```
$ sudo a2ensite zabbix
$ sudo service apache2 restart
```

##Config zabbix
open [https://zabbix.nextlabs.com](https://zabbix.nextlabs.com)

Default acout: Admin/zabbix

##Install zabbix agent
This step is optional, Install agent on the server that we'd like to monitor.
```
$ sudo apt-get install zabbix-agent
```