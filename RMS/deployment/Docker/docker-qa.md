# Clean/First time deployment #

This has been done in CentOS VM.   
It is currently also being tested on RHEL.    
In windows paths like /opt/nextlabs.. may not work and hence services will not run.

## Clean Environment   

```
#!java

  docker stack rm 
  docker volume rm 
  docker container rm
  docker image rm
```



## Download  

Get the latest SkyDRM artifacts from here:   
[http://nxt-build18-apache.nextlabs.com:9052/release_candidate/SecureCollaboration/10.7/](http://nxt-build18-apache.nextlabs.com:9052/release_candidate/SecureCollaboration/10.7/)

    
![01.png](https://bitbucket.org/repo/dBgzdj/images/2075539705-01.png)


  

## Pre-Setup   

```
#!java

unzip SkyDRM-setup-*.zip 
cd setup
sh setup.sh
cd /opt/nextlabs/rms/shared/conf/
Edit admin.properties, router.properties, viewer.properties add existing values.
(Please add below line)
web.inbuilt_service_provider=S3 in admin.properties
```


## Load docker images  

```
#!java

docker load -i RightsManagementServer-DB-DockerImage-10.*.tar
docker load -i RightsManagementServer-RMS-DockerImage-10.*.tar
docker load -i RightsManagementServer-Router-DockerImage-10.*.tar
docker load -i RightsManagementServer-Viewer-DockerImage-10.*.tar
```


## Run Deploy  

~~~
docker stack deploy --compose-file <composefilename> skydrm  
e.g.,
docker stack deploy --compose-file docker-compose-10.7-0129-201807100159.yml skydrm
~~~


## Upgrade image   


```
#!java

Download new images 
Stop docker stack (docker stack rm skydrm)
Remove old images 
Load new images 
Deploy docker again (docker stack deploy --compose-file <composefilename> skydrm)
```