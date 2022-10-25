# Setup Docker Swarm Cluster #

## Introduction ##

Docker swarm is a group of machines that are running Docker and joined into a cluster. Its an open source container orchestration platform.
 
In Docker Swarm Cluster, one node can take part as Worker and at the same time it can take responsibility of the Master node as well. 

![HA.PNG](https://bitbucket.org/repo/dBgzdj/images/1607199749-HA.PNG)

These are the following steps to setup cluster for SkyDRM. This set up follows closely with deployment on-premises.
 
1. Prepare 3 servers with fixed ip address which will take part in Docker Swarm cluster.
2. Prepare a database server to install SkyDRM database.
3. Prepare a proxy server to install NGINX and a directory which can be mounted and used for local storage.
4. Make sure all the servers can ping each other by adding ip address and hostname in /etc/hosts files.
5. Install Docker required version in all servers except database and proxy server.
6. Install NGINX in proxy server.
7. Mount local storage path in all the servers having read and write permission.
8. Make sure following ports are open.
 
```
#!sh
   $ sudo firewall-cmd --zone=public --add-port=8443/tcp --permanent
   $ sudo firewall-cmd --zone=public --add-port=8444/tcp --permanent
   $ sudo firewall-cmd --zone=public --add-port=8445/tcp –permanent
   $ sudo firewall-cmd --permanent --add-port=2377/tcp
   $ sudo firewall-cmd --permanent --add-port=7946/tcp
   $ sudo firewall-cmd --permanent --add-port=7946/udp
   $ sudo firewall-cmd --permanent --add-port=4789/udp
   $ sudo firewall-cmd --permanent --add-port=5432/tcp
   $ sudo firewall-cmd --permanent --add-port=445/tcp
   $ sudo firewall-cmd --permanent --add-port=5672/tcp
   $ sudo firewall-cmd --permanent --add-port=15672/tcp
   $ sudo firewall-cmd --permanent --add-port=465/tcp
   $ sudo firewall-cmd --permanent --add-port=11222/tcp*
   $ sudo firewall-cmd –reload
```
9. Load all the images except database image. If you are using external database, make sure it has right configuration, it’s up and running.
```
#!python
   $ sudo docker load -i RightsManagementServer-Viewer-DockerImage-10.7-0171-201901020017.tar
   $ sudo docker load -i RightsManagementServer-RMS-DockerImage-10.7-0171-201901020017.tar
   $ sudo docker load -i RightsManagementServer-RabbitMQ-DockerImage-10.7-0171-201901020017.tar
   $ sudo docker load -i RightsManagementServer-InfiniSpan-DockerImage-10.7-0171-201901020017.tar
   $ sudo docker load -i RightsManagementServer-Router-DockerImage-10.7-0171-201901020017.tar 
   $ sudo docker load -i RightsManagementServer-DB-DockerImage-10.7-0171-201901020017.tar
```
10. Verify all the images are uploaded.
```
#!python
  $ sudo docker images
```
![1.png](https://bitbucket.org/repo/dBgzdj/images/3573757736-1.png)
11. Start Docker Swarm Cluster
```
#!python
  $ sudo docker swarm init --advertise-addr <ip-address-of the lead master node>
```
![2.png](https://bitbucket.org/repo/dBgzdj/images/1127680603-2.png)
12. Get the token manager, so that other server can join the cluster as master nodes
```
#!python
  $ sudo docker swarm join-token manager
```
![3.png](https://bitbucket.org/repo/dBgzdj/images/4114852276-3.png)
13. Copy the value of the previous command and run in the server two join the cluster
```
#!python
  $ sudo docker swarm join --token SWMTKN-1-5dyesu5rf0fds1b82fpjw5qlm01llq8pwm5pjiske1bi0q9w8o-0md7dr43icznrvgc2zvbos4gw 10.23.58.208:2377
```
![4.png](https://bitbucket.org/repo/dBgzdj/images/1846361676-4.png)
14. Verify the docker Swarm Cluster
```
#!python
  $ sudo docker node ls
```
![5.png](https://bitbucket.org/repo/dBgzdj/images/3635045597-5.png)
15. Run the set up script for the application configuration. Make sure you enter correct values.
16. Verify all the properties file and move into local storage location so that all the servers can use it.
17. Modify the compose file as required.
18. Deploy the application using this command in one of the master node.
```
#!sh
  $ sudo docker stack deploy –c <compose-file-name> skydrm
```
To check the deployment,
```
#!sh
  $ sudo docker stack ps skydrm –no-trunc
```
![6.png](https://bitbucket.org/repo/dBgzdj/images/514753849-6.png)
19. To scale up/down services, use the command
```
#!
docker service scale SERVICE-NAME=REPLICAS
```
Before you setup proxy server, make sure your services are running in Docker Swarm Cluster. 
**SkyDRM service will run only with host name given in configuration file. To remove this constraint we will use proxy server.**

# Update docker-compose file #
RMS replicas can not be launched together because of the following reasons as only one rms should get access to it. 
1. Policy Model Creation
2. Database Creation

Similarly viewer replicas should not be launched together.
1. Unzipping the CAD/SAP/Doc viewer zips to same location 


```
#!python

version: "3"
services:
  infinispan:
    image: jboss/infinispan-server:9.4.0.Final
    command: standalone -c skydrm/infinispan_server.xml
    environment:
      - APP_USER=skydrm
      - APP_PASS=123next!
      - MGMT_USER=skydrm
      - MGMT_PASS=123next!
    ports:
      - "9990:9990"
    volumes:
      - "/opt/nextlabs/rms/shared/cache/:/opt/jboss/infinispan-server/standalone/configuration/skydrm/"
      - "/opt/nextlabs/rms/shared/cachestore/:/var/tmp/"
    networks:
      - webnet

  rabbitmq:
    image: rabbitmq:3-management
    ports:
      - "5672:5672"
      - "15672:15672"
    volumes:
      - "rabbitmq_data:/var/lib/rabbitmq/data"
    networks:
      - webnet
  
  router:
    image: skydrm-router:10.7.0206
    deploy: 
      replicas: 1
    ports:
      - "8443:8443"
    volumes:
      - "/opt/nextlabs/rms/shared/:/var/opt/nextlabs/rms/shared/"
    networks:
      - webnet

  rms:
    image: skydrm-rms:10.7.0206
    deploy: 
      replicas: 1
    ports:
      - "8444:8443"
    volumes:
      - "/opt/nextlabs/rms/shared/:/var/opt/nextlabs/rms/shared/"
    networks:
      - webnet

  viewer:
    image: skydrm-viewer:10.7.0206
    deploy: 
      replicas: 1
    ports:
      - "8445:8443"
    volumes:
      - "/opt/nextlabs/rms/shared/:/var/opt/nextlabs/rms/shared/"
      - "/opt/nextlabs/rms/shared/viewerPackages/:/opt/nextlabs/rms/viewer/viewers/"
    networks:
      - webnet

volumes:
   rabbitmq_data:

networks:
   webnet:

```
To scale up services 

$ docker service scale skydrm_rms=2

$ docker service scale skydrm_viewer=2
rms and viewer services can be increased and decreased as needed. 1 router should be enough for SkyDRM application. If required,i t can be increased as well.

Similarly grep the logs 

```
#!python

$ grep –Hrni “Deployed CAD Viewer.”  /opt/nextlabs/rms/shared/logs
$ grep –Hrni “Deployed SAP Viewer.”  /opt/nextlabs/rms/shared/logs
$ grep –Hrni “Deployed Doc Viewer.”  /opt/nextlabs/rms/shared/logs

```


# Setup NGINX for Reverse Proxy #

Make sure to open and enable port 80 and 443 using the firewall-cmd command:
```
#!python
$ sudo firewall-cmd --permanent --zone=public --add-service=http
$ sudo firewall-cmd --permanent --zone=public --add-service=https
$ sudo firewall-cmd --reload
```
1. Follow the instruction to install NGING in the link. 
[Install NGINX ](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/)

**Some useful NGINX commands** 

Start Nginx command

```
#!python
$ sudo systemctl start nginx
$ sudo service nginx start

```
Stop Nginx command

```
#!python
$ sudo systemctl stop nginx
$ sudo service nginx stop
```
Restart Nginx command
```
#!python
$ sudo systemctl restart nginx
```
Find status of Nginx server command

```
#!python
$ sudo systemctl status nginx
```
These are the useful directories used in NGINX server

* Config dir – /etc/nginx/
* Master/Global config file – /etc/nginx/nginx.conf
* Port 80 http config file – /etc/nginx/conf.d/default
* TCP ports opened by Nginx – 80 (HTTP), 443 (HTTPS)
* Document root directory – /usr/share/nginx/html

**Configure NGINX to use SSL**

Create a self-signed key and certificate pair with OpenSSL in a single command. Make sure /etc/ssl/certs and /etc/ssl/private directories are created.

```
#!python
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt
```

Create a file rms.conf in /etc/nginx/conf.d directory.

```
#!python

$ sudo vim rms.conf 

```
copy below contents to the file and change as per your configuration.
```
#!
upstream rms {
  server 10.23.58.203:8444 weight=5  max_fails=3 fail_timeout=30s;
  server 10.23.58.203:8443 weight=2  max_fails=3 fail_timeout=30s;
  server 10.23.58.213:8443  max_fails=3 fail_timeout=30s;
}

upstream router {
  server 10.23.58.208:8443 weight=5  max_fails=3 fail_timeout=30s;
  server 10.23.58.203:8443 weight=2  max_fails=3 fail_timeout=30s;
  server 10.23.58.213:8443  max_fails=3 fail_timeout=30s;
}

upstream viewer {
  server 10.23.58.208:8446 weight=5  max_fails=3 fail_timeout=30s;
  server 10.23.58.203:8446 weight=2  max_fails=3 fail_timeout=30s;
  server 10.23.58.213:8446  max_fails=3 fail_timeout=30s;
}

upstream rabbitmqapi{
  server 10.23.58.208:15672 weight=5  max_fails=3 fail_timeout=30s;
  server 10.23.58.203:15672 weight=2  max_fails=3 fail_timeout=30s;
  server 10.23.58.213:15672 max_fails=3 fail_timeout=30s;
}

upstream rabbitmqqueue{
  server 10.23.58.208:5672 weight=5  max_fails=3 fail_timeout=30s;
  server 10.23.58.203:5672 weight=2  max_fails=3 fail_timeout=30s;
  server 10.23.58.213:5672 max_fails=3 fail_timeout=30s;
}


# This server accepts all http traffic to port 80 and https traffice to 443 
# and passes it to the upstream.
# Notice that the upstream name and the proxy_pass need to match.

server {
   listen 80;
   listen 443 ssl;
   server_name 10.23.58.244;

   ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
   ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;

   location /rms/ {
                proxy_pass https://rms;
                proxy_set_header Host $host;
   }

   location /router/ {
                proxy_set_header Host $host;
                proxy_pass https://router;
   }

   location /viewer/ {
        proxy_set_header Host $host;
        proxy_pass https://viewer;
   }

   location /rabbitmq/api {
        proxy_set_header Host $host;
        proxy_pass http://rabbitmqapi;
   }

  location /rabbitmq/queue {
        proxy_set_header Host $host;
        proxy_pass http://rabbitmqqueue;
   }

}
```
Verify the   configuration with command 

```
#!python

sudo nginx -t
```
**Note: Before you start the nginx server, make sure you purge the database and change properties file RMS, ROUTER, VIEWER and RABBITMQ URL to proxy server URL. Re-deploy SkyDRM in docker swarm cluster. This is a limitation in the design of the system.**

Restart the nginx server.

```
#!python

sudo srevice nginx restart
```

You can access skydrm with https://proxyserver/rms