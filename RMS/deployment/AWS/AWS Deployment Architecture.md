**Architecture**

![skydrm.png](https://bitbucket.org/repo/dBgzdj/images/2957204943-skydrm.png)

**Access**

You need to login to the production bastion server bastion.skydrm.com (If you do not have access,Please contact DevOps team)

     cat .ssh/config to list the host name.

     ssh <hostname> to access the instance

**Upload latest build to s3**

* Upload the release build to this bucket 
  https://console.aws.amazon.com/s3/home?region=us-east-1#&bucket=skydrm-rc&prefix=RMSNG/instance-deployment/ 

* update the latestbuild file with the latestbuild number

**Deployment**

If it is a new instance ,follow the below steps. If it is a AMI ,skip this step  

    devops-aws-scripts / RMS-AWS-SCRIPTS / RMSNG / RMSNG-instance-setup-steps.txt

**Router:**

* Copy the folder devops-aws-scripts / RMS-AWS-SCRIPTS / RMSNG / RMS-AWS-SCRIPTS / to /etc/RMS-AWS-SCRIPTS 
   
       cd /etc/RMS-AWS-SCRIPTS 
       vi rms.server.roles   
       change the role to router 
       bash rms-get-latest-build.sh

**RMS:**

* Copy the folder devops-aws-scripts / RMS-AWS-SCRIPTS / RMSNG / RMS-AWS-SCRIPTS / to /opt/nextlabs
* Edit rms.server.roles file  and change it to rms
* Run rms-get-latest-build.sh

**Viewer:**

* Copy the folder devops-aws-scripts / RMS-AWS-SCRIPTS / RMSNG / RMS-AWS-SCRIPTS / to /opt/nextlabs
* Edit rms.server.roles file  and change it to viewer
* Run rms-get-latest-build.sh



**Troubleshoot:**

*** To enable http to https redirect in Load balancer need to add the below valve in server.xml** 


<Valve
className="org.apache.catalina.valves.RemoteIpValve"
remoteIpHeader="x-forwarded-for"
remoteIpProxiesHeader="x-forwarded-by"
protocolHeader="x-forwarded-proto"/>



** CloudWatch:**

•	Memory

•	Disk space

•	File handles

•	CPU utilization

**Backups**

The logs are archived everyday and are backed up in S3 bucket skydrm-data.
The script can be found at devops-aws-scripts / RMS-AWS-SCRIPTS / RMSNG / RMS-AWS-SCRIPTS / rms.BackupTomcatLogsToS3.sh

