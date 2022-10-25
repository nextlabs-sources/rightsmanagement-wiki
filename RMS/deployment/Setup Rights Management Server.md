# Setup Rights Management Server

### Prerequisites
- Ubuntu 14.04.4 LTS
- tomcat7
- PostgreSQL
- JDK8
- JCE Unlimited Strength Jurisdiction Policy

## Initialize PostgreSQL

- Create **router** database

		$ sudo -u postgres psql postgres
		postgres=# CREATE USER router PASSWORD '123next!';
		postgres=# CREATE DATABASE router OWNER router ENCODING 'UTF-8';
		postgres=# \connect router;
		postgres=# SET ROLE router;
		postgres=# CREATE SCHEMA router AUTHORIZATION router;
		postgres=# \q

- Create **rms** database

		$ sudo -u postgres psql postgres
		postgres=# CREATE USER rms PASSWORD '123next!';
		postgres=# CREATE DATABASE rms OWNER rms ENCODING 'UTF-8';
		postgres=# \connect rms;
		postgres=# SET ROLE rms;
		postgres=# CREATE SCHEMA rms AUTHORIZATION rms;
		postgres=# \q

## Config tomcat7
1. Enable authbind, so tomcat can run on port 80 and 443

		$ sudo apt-get install authbind

		# Enable tomcat7 authbind:
		$ sudo vi /etc/default/tomcat7
		# set AUTHBIND=yes

		$ sudo touch /etc/authbind/byport/80
		$ sudo chmod 500 /etc/authbind/byport/80
		$ sudo chown tomcat7 /etc/authbind/byport/80
		$ sudo touch /etc/authbind/byport/443
		$ sudo chmod 500 /etc/authbind/byport/443
		$ sudo chown tomcat7 /etc/authbind/byport/443

2. Config server.xml, copy sample server.xml to tomcat

		$ sudo cp rms/server/conf/server.xml $CATALINA_HOME/conf

3. Config server certificate

		$ sudo cp rms/server/conf/keystore.pfx $CATALINA_HOME/conf
		or create a selfsigned certificate
		$ ca $CATALINA_HOME/conf
		$ sudo keytool -genkey -keyalg RSA -alias tomcat -keystore keystore.pfx -storepass 123next! -storetype PKCS12 -validity 3600 -keysize 2048 -dname "CN=skydrm, OU=Cloud Service, O=skydrm.com, L=San Mateo, ST=California, C=US"

4. Config logging, merge both rms and router logging.properties if you deploy them together.

		$ cp rms/server/conf/logging-tomcat7.properties $CATALINA_HOME/conf/logging.properties

5. Copy system provided libraries to tomcat

		$ cd libraries
		$ sudo cp libs/web/system/bcprov-jdk16-1.46.jar /usr/share/tomcat7/lib
		$ sudo cp libs/web/system/postgresql-9.4-1204-jdbc42.jar /usr/share/tomcat7/lib

6. Create nextlabs conf folder

		$ sudo mkdir -p /opt/nextlabs/conf

7. Config administrative properties file

		$ sudo cp rms/server/conf/admin.properties /opt/nextlabs/conf

8. Config router properties file

		$ sudo cp router/server/conf/router.properties /opt/nextlabs/conf

9. Copy utilities shell script files to tomcat, this is optional

		$ sudo cp rms/server/conf/rms.sh /opt/nextlabs/conf
		$ sudo cp router/server/conf/router.sh /opt/nextlabs/conf

10. Change nextlabs conf folder owner

		$ sudo chown -R tomcat7:tomcat7 /opt/nextlabs

10. Start tomcat

		$ sudo service tomcat7 start

## Download artifact from Jenkin's

[http://nxt-build24.nextlabs.com:9031/view/RMSNG/job/RMSNG-master-nightly/](http://nxt-build24.nextlabs.com:9031/view/RMSNG/job/RMSNG-master-nightly/)

## Deploy war file to tomcat
```
$ cp rms.war $CATALINA_HOME/webapps/
$ cp router.war $CATALINA_HOME/webapps/
$ cp viewer.war $CATALINA_HOME/webapps/
```

### Add tenant 

Add tenant to router by running the following command

```
./router.sh --cmd=Tenant --operation=add --name=skydrm --server=https://rmtest.nextlabs.solutions/rms

2016-06-13 11:23:50:129:  Tenant: skydrm created, OTP:4864563C0259A1BC62B4891FEB45D070
```

Use the otp from above result to register tenant by invoking the rest API at https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Tenant%20REST%20API#markdown-header-register-a-new-tenant-on-rms

The otp for a tenant can also be reset using ```router.sh``` by setting ```--operation=reset-otp```.  Here is description of the options which can be used with ```router.sh```:

```
usage: java com.nextlabs.router.Main --cmd=Tenant
-d,--displayName <arg>      Tenant display name
-desc,--description <arg>   Tenant description
-e,--email <arg>            Contact email address
-n,--name <arg>             Tenant name
-o,--operation <arg>        Operation: add/reset-otp
-s,--server <arg>           Server name
```

## Deploy Viewer

1. Update RMS_URL, VIEWER_DATA_DIR and VIEWER_INSTALL_DIR properties in viewer/conf/viewer.properties. (OS user running the Tomcat process must have write permissions on these directories)
2. Copy updated viewer.properties and viewer/conf/viewer-log.properties to $CATALINA_HOME/conf

		$ sudo cp viewer/conf/viewer*.properties $CATALINA_HOME/conf

3. Update the web.viewer_url in {tomcat}/conf/admin.properties.
		
4. Create a folder license in <VIEWER_DATA_DIR> and copy libraries/license/license.jar into <VIEWER_DATA_DIR>/license/

5. Place license.dat (get it from peer developers) file along with license.jar 
6.  Create a folder viewers in <VIEWER_INSTALL_DIR> and copy the following zip files into it <VIEWER_INSTALL_DIR>/viewers from \\nextlabs.com\share\data\releases\SecureCollaboration\9.0.0.0-SAASOnly\4  

        RightsManagementServer-CADViewer-*.zip
        RightsManagementServer-DocViewer-*.zip
        RightsManagementServer-SAPViewer-*.zip

7. Add <VIEWER_INSTALL_DIR>/external/perceptive to PATH environment variable. (external/perceptive will be created when viewer packages are extracted). For LINUX set the following environment variables

```
#!shell

export LC_ALL="en_US.UTF-8" 
export DISPLAY=:0 
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:<VIEWER_INSTALL_DIR>/external/perceptive:<VIEWER_INSTALL_DIR>/external/RMSCADCONVERTER/bin/linux64
export ISYS_FONTS=<VIEWER_INSTALL_DIR>/external/perceptive/fonts
```
 
8. Start the tomcat server. You can see the logs at VIEWER_DATA_DIR/logs/viewer.log. If you see the following statements in logs, then viewer packages have been deployed correctly and you should be able to launch the viewer

```
06-23-2016 15:16:30 INFO  LicenseManager: - Licensed Viewers: CAD Viewer, SAP 3D Viewer, Secure Viewer
06-23-2016 15:16:30 DEBUG ViewerInitializationManager: - Overwriting  with RightsManagementServer-CADViewer-8.3.0.0-295PS-main-201603310515.zip
06-23-2016 15:16:30 DEBUG ViewerInitializationManager: - Unzipping C:\NextLabs\RMS\INSTALL_DIR_NG\viewers\RightsManagementServer-CADViewer-8.3.0.0-295PS-main-201603310515.zip to C:\NextLabs\RMS\INSTALL_DIR_NG\external\RMSCADCONVERTER
06-23-2016 15:16:48 INFO  ViewerInitializationManager: - Deployed CAD Converter.
06-23-2016 15:16:49 INFO  ViewerInitializationManager: - Deployed CAD Viewer.
06-23-2016 15:16:49 DEBUG ViewerInitializationManager: - Overwriting  with RightsManagementServer-SAPViewer-8.3.0.0-14-201604250658.zip
06-23-2016 15:16:49 DEBUG ViewerInitializationManager: - Unzipping C:\NextLabs\RMS\INSTALL_DIR_NG\viewers\RightsManagementServer-SAPViewer-8.3.0.0-14-201604250658.zip to C:\Users\nnallagatla\apache-tomcat-8.0.35\webapps\viewer\ui\app\viewers
06-23-2016 15:17:18 INFO  ViewerInitializationManager: - Deployed SAP Viewer.
06-23-2016 15:17:18 DEBUG ViewerInitializationManager: - Overwriting  with RightsManagementServer-DocViewer-9.0.0.0-4-201605270119.zip
06-23-2016 15:17:18 DEBUG ViewerInitializationManager: - Unzipping C:\NextLabs\RMS\INSTALL_DIR_NG\viewers\RightsManagementServer-DocViewer-9.0.0.0-4-201605270119.zip to C:\NextLabs\RMS\INSTALL_DIR_NG\viewers\perceptive
06-23-2016 15:17:24 INFO  ViewerInitializationManager: - Deployed Doc Viewer.
```
### Troubleshooting on windows
If you are unable to view CAD files, you should install:
```
https://www.microsoft.com/en-sg/download/details.aspx?id=40784 
https://www.microsoft.com/en-sg/download/details.aspx?id=30679 
```

### Troubleshooting on linux

If you are unable to view CAD files, check if the following files are present


```
#Redhat/fedora/centos
/usr/lib64/libXext.so.6
/usr/lib64/libXmu.so.6
/usr/lib64/libGLU.so.1

#debian/ubuntu
/usr/lib/x86_64-linux-gnu/libXext.so.6
/usr/lib/x86_64-linux-gnu/libXmu.so.6
/usr/lib/x86_64-linux-gnu/libGLU.so.1
```

If above files are missing, run the following commands to install them

```
#Redhat/fedora/centos
yum install libXext-1.3.3-3.el7.x86_64
yum install libXmu-1.1.2-2.el7.x86_64
yum install mesa-libGLU-9.0.0-4.el7.x86_64

#debian/ubuntu
sudo apt-get install libXext6
sudo apt-get install libXmu6
sudo apt-get install libGLU1
```