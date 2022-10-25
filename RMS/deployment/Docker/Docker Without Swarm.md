Minor changes to the .yml file (like removing the double quotes and changing the posix-style paths to Windows style for my machine) can be made to deploy using docker-compose.

This way they can run as containers (not services) but cannot be orchestrated. The number of instances and ports would need to be determined/tracked beforehand.

Changes:

•	Docker compose – attached file (removed double quotes in volume paths and changed to Windows style slashes)

•	Admin/router/viewer.properties – replace the db:5432 line with hibernate.hibernate.connection.url=jdbc:postgresql://sudong.nextlabs.com:5433/rms
(note that if in your compose file, for the database the port mapping is p1:p2 then here it would have to use hostname:p1, whereas in swarm mode we’d still use servicename:p2).



```
#!sh

D:\repos\rmsng>docker swarm leave --force

D:\repos\rmsng>docker-compose -f docker/compose/docker-compose-non-swarm.yml up -d
Starting compose_router_1 ... done
Starting compose_db_1     ... done
Starting compose_rms_1    ... done
Starting compose_viewer_1 ... done

D:\repos\rmsng>docker ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                                            NAMES
31b72ab20d27        skydrm-rms:LATEST      "/entrypoint.sh run"     4 minutes ago       Up 6 seconds        0.0.0.0:8000->8000/tcp, 0.0.0.0:443->8443/tcp    compose_rms_1
a3daff2d3094        skydrm-db:LATEST       "docker-entrypoint.s…"   4 minutes ago       Up 6 seconds        0.0.0.0:5433->5432/tcp                           compose_db_1
d0a1a8334a6c        skydrm-router:LATEST   "/entrypoint.sh run"     4 minutes ago       Up 6 seconds        0.0.0.0:8001->8001/tcp, 0.0.0.0:8444->8443/tcp   compose_router_1
9e90187cb27e        skydrm-viewer:LATEST   "/entrypoint.sh run"     4 minutes ago       Up 6 seconds        0.0.0.0:8002->8002/tcp, 0.0.0.0:8445->8443/tcp   compose_viewer_1

D:\repos\rmsng>docker service ls
Error response from daemon: This node is not a swarm manager. Use "docker swarm init" or "docker swarm join" to connect this node to swarm and try again.

D:\repos\rmsng>
```



Docker Compose for Non Swarm mode .yml:

```
#!YAML

version: "3"
services:
  db:
    image: skydrm-db:LATEST
    volumes:
      - dbdata:/var/lib/postgresql/data
      - C:\Users\pkumar\DockerMapped\rmsng\docker\compose\datafiles\db\:/docker-entrypoint-initdb.d/
    ports:
      - "5433:5432"
    networks:
      - webnet
  
  router:
    image: skydrm-router:LATEST
    ports:
      - "8444:8443"
      - "8001:8001"
    volumes:
      - C:\Users\pkumar\DockerMapped\rmsng\docker\compose\datafiles\:/var/opt/nextlabs/rms/shared/
    networks:
      - webnet
  rms:
    image: skydrm-rms:LATEST
    ports:
      - "443:8443"
      - "8000:8000"
    volumes:
      - C:\Users\pkumar\DockerMapped\rmsng\docker\compose\datafiles\:/var/opt/nextlabs/rms/shared/
    networks:
      - webnet
  viewer:
    image: skydrm-viewer:LATEST
    ports:
      - "8445:8443"
      - "8002:8002"
    volumes:
      - C:\Users\pkumar\DockerMapped\rmsng\docker\compose\datafiles\:/var/opt/nextlabs/rms/shared/
      - C:\Users\pkumar\DockerMapped\rmsng\docker\rms\shared\viewerPackages/:/opt/nextlabs/rms/viewer/viewers/
    networks:
      - webnet

volumes:
   dbdata:

networks:
   webnet:
```