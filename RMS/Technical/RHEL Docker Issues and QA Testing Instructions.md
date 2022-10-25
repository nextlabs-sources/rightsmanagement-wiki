**Bug**

[[Docker] rms and viewer caches are not forming a cluster using Infinispan's jgroups](http://bugs.cn.nextlabs.com/show_bug.cgi?id=48944)

[[Docker] Some images (db) are not starting due to SELinux](http://bugs.cn.nextlabs.com/show_bug.cgi?id=48942)

Issues happen on RHEL v7.4 and Docker CE v1.13.1 with SELinux enforcing.

**Solution**

This is a known [issue](https://github.com/moby/moby/issues/31137) for docker version 1.13.1. It's fixed in release 17.03.2.

On RHEL 7.4 Docker CE 18.03 (latest) – which is in Docker’s repo, everything works fine – selinux is enforcing, db and all images startup, cluster formation also happens (I tested with 2 RMS, 1 Router, 2 Viewer just to
scale).

**QA Testing Instructions**

Deploy using the same wiki instructions using latest build https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/deployment/Docker/docker-qa

1. Install RHEL 7.4 and activate its subscription (IT can).
2. Install Docker CE 18.03 (Community Edition) as per instructions (they are the same as for centos) at https://docs.docker.com/install/linux/docker-ce/centos/
The Docker that is available in RedHat’s own repository is 1.13.1 which is old and our product cannot work on it, so (as per link above) Docker’s repo has to be added so that `$yum install docker`, fetches 18.03 latest version and its dependencies.
3. Ensure selinux is set to enforcing (`vi /etc/sysconfig/selinux` and ensure `SELINUX=enforcing` and then reboot – verify using command `sestatus`).


Not necessary each time, but necessary in at least one/first round of QA testing:
1. Edit the docker compose file manually to add replicas for rms and viewer (the default compose file does not have this so there’s only one rms, viewer, router now) but the replicas allow docker to increase their count which it will load balance automatically. https://docs.docker.com/compose/compose-file/#replicas (sample attached and the addition is the deploy: replicas: 2). Note that editing your yaml using notepad++/Windows might add CR/LF which might not be valid yaml.
2. Before deploying new build’s containers, delete (`$docker volume rm –f skydrm_dbdata`) the database volume to ensure the containers apply the db schema migrations from scratch each time (if volume is deleted, manually remove line web.publicTenant= from admin and router.properties). Doing this means that deployment will lose all its data in the database.
3. Check caches are forming the cluster a few minutes after deploying docker stack 
Get the container logs (which in future builds will be inside docker host) but here get from within container:

Get container ID of any rms or viewer using commands
```
$docker container ls
$docker exec –it containerId /bin/bash
```

```
$cd $CATALINA_HOME/logs
$cat catalina-xx-earliest.log | grep receiveClusterView
```

The last line printed should look like:
```
06-Jul-2018 09:50:29.967 INFO [jgroups-15,dd044cd972e8-53376] org.infinispan.remoting.transport.jgroups.JGroupsTransport.receiveClusterView ISPN000094: Received new cluster view for channel RMS_JGROUPS_296068fb-1233-49d3-a7ea-4dd30fe2f99a: [61f7aa588dc1-28674|3] (4) [61f7aa588dc1-28674, dd044cd972e8-53376, 810beb5380c1-59224, b02b74d11328-23034]
```

Note the count is 4 followed by 4 container IDs which matches the rms and viewer container IDs (here there were 2 rms + 2 viewer). With standard docker compose without replicas the count will be 2. This is used to verify that rms and viewer caches are forming cluster.

**Sample yml File**

```
version: "3"
services:
  db:
    image: skydrm-db:10.7.0129
    volumes:
      - "dbdata:/var/lib/postgresql/data"
      - "/opt/nextlabs/rms/shared/db/:/docker-entrypoint-initdb.d/"
    ports:
      - "5432:5432"
    networks:
      - webnet
  
  router:
    image: skydrm-router:10.7.0129
    ports:
      - "8443:8443"
    volumes:
      - "/opt/nextlabs/rms/shared/:/var/opt/nextlabs/rms/shared/"
    networks:
      - webnet
  rms:
    image: skydrm-rms:10.7.0129
    ports:
      - "8444:8443"
    volumes:
      - "/opt/nextlabs/rms/shared/:/var/opt/nextlabs/rms/shared/"
    networks:
      - webnet
    deploy:
        replicas: 2
  viewer:
    image: skydrm-viewer:10.7.0129
    ports:
      - "8445:8443"
    volumes:
      - "/opt/nextlabs/rms/shared/:/var/opt/nextlabs/rms/shared/"
      - "/opt/nextlabs/rms/shared/viewerPackages/:/opt/nextlabs/rms/viewer/viewers/"
    networks:
      - webnet
    deploy:
      replicas: 2

volumes:
   dbdata:

networks:
   webnet:
```