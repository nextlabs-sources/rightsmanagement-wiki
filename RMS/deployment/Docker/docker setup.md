# Documentation for deploying container #

NOTE: *In this documentation, we have used values like: 10.23.58.178, /home/admin/Documents/rmsng/docker, /opt/nextlabs/rms/shared etc. Depending on your machine's address and project path, these values might change as well.*

1. Install docker CE 18.03.1 in your centOS VM by following the steps in https://runnable.com/docker/install-docker-on-linux (Refer to the section ‘Install with the Docker Installation Script’. You can try other methods as well).  Verify by typing: **docker --version** or **docker info**. If docker info is producing an error try doing **sudo docker info**

2. Create the following mounts:

      NOTE: If you are using **Docker Toolbox for Windows**, Docker daemon has limited access to the file system. The easiest solution is to update the mount path `/opt/nextlabs/rms/shared` in `{rmsng}/docker/compose/docker-compose.yml` to a directory under `C:\Users\` - **please note that if your windows login credentials (AD in this case) ever change, you need to go into Docker settings and reauthorize access to the drive, otherwise it will not be able to mount the host's drive**. You can also change the port settings in `docker-compose.yml`. For e.g. use port 443 instead of 8444 for rms. You can skip steps (a) and (b) after doing this. For example, 


        rms:
          image: rms:LATEST
          depends_on:
            - "db"
          ports:
            - "443:8443"
          volumes:
            - "/c/Users/......./rmsng/docker/compose/datafiles/:/var/opt/nextlabs/rms/shared/"
          networks:
            - webnet



        a.	In opt directory create a folder called ‘/opt/nextlabs/rms/shared/conf’:

                mkdir -p cd /opt/nextlabs/rms/shared/conf

                NOTE: You can mention any path name but mentioning conf is important otherwise the service will not run. The pathname for volume has to be same as the paths (before ‘:’) mentioned in the compose file.  

        b.	In ‘/opt/nextlabs/rms/shared/conf , add the following property files: router.properties, viewer.properties, admin.properties. We need to mount these files as these configuration can be different for different customers. 

                In the above example, these 3 property files should be under '/c/Users/......./rmsng/docker/compose/datafiles/conf'

        c.	Change the following in admin.properties:

		        web.router _url=https://<ip address>:8443/router
		        web.viewer_url=https://<ip address>:8445/viewer
		        hibernate.hibernate.connection.url=jdbc:postgresql://db:5432/rms?ApplicationName=RMS
		        web.public_tenant_admin=admin@nextlabs.com
                        web.idp.rms.attributes={}

                NOTE: e.g. ip address is the hostname of the docker machine, for e.g., could be 10.23.58.178. For Docker Toolbox it is normally 192.168.99.100 (run "docker-machine ip"). You can also use your hostname if you have mapped the ip to your hostname in etc/hosts)

        d.	Change the following in router.properties:

		         web.rms_url=https://10.23.58.178:8444/rms
		         hibernate.hibernate.connection.url=jdbc:postgresql://db:5432/router?ApplicationName=ROUTER


        e.	Change the following in viewer.properties:

		         RMS_URL=https://10.23.58.178:8444/rms
		         ROUTER_URL=https://10.23.58.178:8443/router

        f. Add the 3 viewer packages RightsManagementServer-CADViewer, DocViewer, SAPViewer in the path, mentioned under the 2nd volume mount of viewer service. 


3. In /home/admin/Documents create a directory called ‘docker’ . ‘docker’ directory should have the following structure:

     a.	
          
         ‘base’ folder

            |_____________tomcat
                          
                          |_____________conf

                                        |_____________context.xml

                                        |_____________server.xml

                                        |_____________websslcert.jks

            |_____________Dockerfile


     b.	

       ‘db’ folder

          |_____________Dockerfile

          |_____________scripts

      		              |_____________init_setup.sql

     c.	
    
       ‘rms’ folder

          |_____________Dockerfile

          |_____________rms.war

          |_____________datafiles

		                |_________rms-keystore.jks

		                |_________watermarkConfig

     d.	

       ‘router’ folder

           |_____________Dockerfile

           |_____________router.war

           |_____________datafiles

		                 |_________rms-keystore.jks	
	
     e.	
      
       ‘viewer’ folder

           |_____________Dockerfile

           |_____________entrypoint.sh

           |_____________viewer.war


     f.	

       ‘compose’ folder

          |_____________docker-compose.yml

4. Run the images:

     Prerequisties:
     
     Create folder 'xlib' under RMSNG
     Copy files from \\semakau\Share\Teams\Secure Collaboration\RMSNG\xlib  to xlib

     a.	Go to RMSNG  directory. **cd /home/admin/Documents/rmsng/**

     b.	**docker build --rm -t skydrm-base:LATEST   -f docker/base/Dockerfile .** 

     c. **docker build --rm -t skydrm-db:LATEST -f docker/db/Dockerfile .**

     d.	**docker build --rm -t skydrm-router:LATEST -f docker/router/Dockerfile .**

     e.	**docker build --rm -t skydrm-rms:LATEST -f docker/rms/Dockerfile .**

     f. **docker build --rm -t skydrm-viewer:LATEST -f docker/viewer/Dockerfile .**   (NOTE: in docker/viewer/entrypoin.sh, you may need to change **cd linux/intel-64** to **cd windows/intel-64** depending on the platform you are working on - although at present we are using linux based containers in Docker so no change required. Also change the EOL conversion to Unix for all entrypoint.sh scripts using for e.g. dos2unix utility).

If the build throws errors while resolving some mirrorlists for the "yum install" commands in Dockerfiles, please consider editing docker's daemon.json and check the dns server mapping. The build applies cached intermediate containers, so ideally for release builds we should be building with the --no-cache argument in the above.

     g.	Check the list of images by typing: **docker image ls**

     NOTE:  Ordering matters. First base image must be built followed by db, then router, rms. Specify '.' if you want to specify the current path as the context

5.	After running the images, run the compose file:** docker stack deploy --compose-file docker/compose/docker-compose.yml skydrm**. This will create all the containers. ‘skydrm’ is the service name.   You may need to initialise the swarm node: **docker swarm init --advertise-addr 10.23.58.178**

6.	To view the services, type: **docker service ls**. 1/1 means the service is running and hence we can access the container. 0/1 means there is some issue while starting the service.

7.	To check service logs, type: **docker service logs skydrm_db** . skydrm_db is the service name for database.

8.	You can view the containers by typing: **docker container ls**. This will give mainly the container id and the image name.

9.	To access the container type: **docker exec  -it <container_id> /bin/bash**

10.	To exit the container just type: **exit**

11.	Then open a firefox browser to issue the URL: **https:// 10.23.58.178:8444/rms**. You will see the login page.

12.	Also you will note that the admin.properties, router.properties will have another entry called ‘web.publicTenant’ when tenant is created. You can view the changes in the property files in the container by going to path : ‘/var/opt/nextlabs/rms/shared/conf’

13.	Commonly used commands:

    a.	Force remove all images: **docker rmi $(docker images -q)**

    b.	Remove a particular image: **docker rmi <tagname>:<tagnumber>**

    c.	Remove services: **docker service rm <all the service names>** 

    d.	Remove all services: **docker stack rm skydrm** 

    e.	Force remove all containers:  **docker rm –f $(docker ps -aq)**

    f. SSH into a container: **docker exec -it <container_id> /bin/bash**

14.	Other helpful commands on docker can be found in https://www.docker.com/sites/default/files/Docker_CheatSheet_08.09.2016_0.pdf

NOTE: If you get the error "Cannot connect to the docker daemon at unix:///var/run/docker.sock.... ", just type the command **service docker start**