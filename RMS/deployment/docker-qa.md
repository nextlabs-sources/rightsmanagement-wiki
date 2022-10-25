# Clean/First time deployment #


## Clean Environment ##
*
```
#!java

  docker stack rm 
  docker volume rm 
  docker container rm
  docker image rm
```



## Download ##


```
#!java

SkyDRM-setup-10.6-0080-201801250605.zip 
RightsManagementServer-DB-DockerImage-10.6-0080-201801250605.tar
RightsManagementServer-RMS-DockerImage-10.6-0080-201801250605.tar
RightsManagementServer-Router-DockerImage-10.6-0080-201801250605.tar
RightsManagementServer-Viewer-DockerImage-10.6-0080-201801250605.tar
```

	## 		 
Pre-Setup ##

```
#!java

unzip SkyDRM-setup-10.6-0080-201801250605.zip 
cd setup
sh setup.sh
cd /opt/nextlabs/rms/shared/conf/
Edit admin.properties, router.properties, viewer.properties add existing values.
(Please add below line)
web.inbuilt_service_provider=S3 in admin.properties
```


## Load docker images ##

```
#!java

docker load -i RightsManagementServer-DB-DockerImage-10.6-0080-201801250605.tar
docker load -i RightsManagementServer-RMS-DockerImage-10.6-0080-201801250605.tar
docker load -i RightsManagementServer-Router-DockerImage-10.6-0080-201801250605.tar
docker load -i RightsManagementServer-Viewer-DockerImage-10.6-0080-201801250605.tar
```


## Run Deploy
 ##
docker stack deploy --compose-file <composefilename> skydrm

## Upgrade image ## 


```
#!java

Download new images 
Stop docker stack (docker stack rm skydrm)
Remove old images 
Load new images 
Deploy docker again (docker stack deploy --compose-file <composefilename> skydrm)
```