# Official support for RedHat Enterprise Linux  

Date: 2018-07-06  
Approval status: Approved  
Approver: Keng   
  
Today the team uses CentOS for development. However enterprise customers running Linux servers normally use an enterprise Linux distribution such as RHEL.   

Going forward, we may choose to continue working on CentOS but **must** ensure our builds for the Docker containers can run on RHEL docker hosts.   

**Keng confirmed on 2018-07-17 that we need to and will support RHEL as an officially supported platform for Docker deployment.   **  

For developers who want to set up a new RHEL VM:   
   

* Log in to SG-TS or SG-TS01 or SG-TS02    
* Use the RMS RHEL images available at : `\\10.23.58.58\share\OVF\Linux\rms-rhel74 `   
* Or use the RMS RHEL ISO from `\\10.23.58.58\share\software\Linux ` 
* Deploy the OVF or install RHEL from the ISO  
* Change the hostname and get an IP address  
* Email helpdesk with the IP and login, ask to activate the RHEL subscription  
* IT should login to the VM and activate the subscription  
* Now we should be able to install and update software from the official RHEL repos