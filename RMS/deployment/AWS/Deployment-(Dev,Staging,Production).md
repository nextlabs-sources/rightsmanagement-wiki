# Steps to deploy in Production(SkyDRM):

1. In production account S3, update **latestbuild** file in ```S3:/skydrm-rc/RMSNG/instance-deployment```
2. Create folder with the build number specified in ```S3:/skydrm-rc/RMSNG/instance-deployment/latestbuild```
3. Place the build zip and *viewer packages* inside the folder created in **step 2**
    - find the build artifacts in ```\\nextlabs.com\share\data\build\release_candidate\SecureCollaboration\10.X``` from TS01
4. login into production bastion server ```cd ~centos/deploy/```
    - trigger suspending auto-scaling script ```sh rms-deploy-suspend-autoscaling.sh```
    - turn off zabbix from zabbix web console
    - trigger build script ```sh rms-production-deploy.sh all```
        - ```all``` Parameter is required when you want to deploy in all instances. Otherwise single instance for each module will be deployed only.
    - trigger resuming auto-scaling script ```sh rms-deploy-suspend-autoscaling.sh```
    - turn on zabbix from zabbix web console
    
5. tagging(Lakshmi)

# Steps to deploy in Staging(TestDRM):

1. In staging account S3, update **latestbuild** file in ```S3:/nxtlbs-rms10.0/RMS/```
2. Create folder with the build number specified in ```S3:/nxtlbs-rms10.0/RMS/latestbuild```
3. Place the build zip and *viewer packages* inside the folder created in **step 2**
    - find the build artifacts in ```\\nextlabs.com\share\data\build\release_candidate\SecureCollaboration\10.X``` from TS01
4. login into staging bastion server ```cd ~/TestDrm/latest-build```
    - trigger build script ```sh deployTest.sh all```
        - ```all``` Parameter is required when you want to deploy in all instances. Otherwise single instance for each module will be deployed only.  
  
Note:   
Build-specific instructions [here](Build/Release Specific Details)

# Steps to deploy in Development(RMTest):

Copy the latest RMS zip from ts01 machine under
 
```
#!shell

\\nextlabs.com\share\data\build\release_candidate\SecureCollaboration\10.1
```
 to TestDRM bastion under 
```
#!shell

/home/ec2-user/TestDrm/latest-build
```


```
#!shell

scp RMS-10.1-0265-201707210115.zip rmtest:/home/centos/latestbuild
ssh rmtest
sudo su -
sh deploy.sh 
```

Troubleshooting in rmtest - if the ec2 instance is rebooted, please ensure the public IP matches after reboot (it should now that we've set it to be a static value) otherwise route 53 will need to be updated. Also note that the centos firewalld will be enabled and so it needs to be disabled (it was manually disabled before) or selinux set to permissive (we already restrict via AWS firewall rules).

NOTE: if using single aws appliance and single tomcat to deploy router, rms and viewer, user /opt/nextlabs/rms/shared/conf/jgroups.xml instead of /opt/nextlabs/rms/shared/conf/jgroups_aws.xml