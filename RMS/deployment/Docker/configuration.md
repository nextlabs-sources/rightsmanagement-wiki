# Configuration SkyDRM #

## IDPs in Docker ##
IDPs can be configured by using the docker\setup\setup.sh script which is an interactive wrapper script to seek the values for configuring IDP values from the admin.

The script will populate the values in admin/router/viewer.properties file and this can still be done manually (like now) as at [IDP templates in properties file](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/deployment/IDP%20templates%20in%20properties%20file)

If you are using LDAP IDP in an environment where the AD has self signed certificates (like in dev/QA/test systems), then along with configuring LDAP as above, it is also necessary to import those self-signed certs for the docker rms container to be able to connect to, as documented below.

## Enrolling certs for LDAP IDP login or Sharepoint https in RMS ##
First export the AD's self signed certs as before (can refer to either RMS 8.4 Admin Guide or Google). The only restriction is that the exported certificate file MUST be in the .cer file format, and the exported .cer file must NOT be password protected.

The entrypoint.sh script for the rms docker container will look for .cer files in a particular folder that the customer/deployment would have mounted at /var/opt/nextlabs/rms/shared/enroll/cer. Note that enroll/cer folder structure is inside /var/opt/nextlabs/rms/shared/ which contains the conf folder with admin, router, viewer.properties as usual. 

The docker-compose.yml file must reflect the mounted path to /var/opt/nextlabs/rms/shared/ correctly so it contains both conf/ and enroll/cer inside it (for example, "/c/Users/pkumar/DockerMapped/rmsng/docker/compose/datafiles/:/var/opt/nextlabs/rms/shared/")

[~/DockerMapped/rmsng/docker/compose/datafiles]>ls

enroll/  conf/  db/  temp/

The entrypoint.sh script for rms docker container will pickup each file in the enroll/cer folder and check if it already exists in the $JAVA_HOME/lib/security/cacerts folder under the filename's alias (for example AD 01.cer will be looked up or added under alias "AD 01"). If its present then no changes will be made, if its missing it will be imported under the alias of its filename. This will be done for each .cer file in that folder. Importing .cer files is necessary for ADs with self-signed certificates, and can be used for Sharepoint https communication too.

## Default storage provider in Docker ##
Default storage provider can be configured by using the docker\setup\setup.sh or by manually modifying the [admin.properties](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/deployment/Default%20Storage%20Provider%20in%20properties%20file).

## Database options ##
Users can choose inbuilt database or choose to select their external databases mainly: PostgreSQL and Microsoft SQL Server. For external databases, users have to specify their hostname (e.g. 10.63.0.125) and port number (e.g. 5432)

NOTE: If external database is selected, then the db service from the docker-compose.yml file will be deleted as well

## Custom data directory ##
Customer can provide their own custom absolute data directory apart from the default (/opt/nextlabs/rms/).

NOTE: the path specified is absolute and not relative

## Icenet Configuration ##
User can specify the icenet url to establish a connection between RMS and CC in order to acquire policies

## Customization scripts ##
Customer may choose to include their bash scripts. In that case please follow the steps:

- Keep these files in the same place as conf/ folder where the other property files like admin.properties, router.properties are present

- Create 3 versions of the same script files and rename them as follows: custom_script_rms.sh, custom_script_router.sh, custom_script_viewer.sh as these files will be used in 3 different modules

Our code will run your custom script files

## Choosing OneDrive for Business as a default storage provider ##

The bucket names for 'MySpace' and 'Project' should start with 'Documents/'. It is a folder path.

OneDrive for Business site URL is the sharepoint site that needs to be provided