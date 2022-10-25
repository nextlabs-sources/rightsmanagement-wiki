# DevOps Workflow 

Mail from Kava on 2018-06-25:   

## Monitoring  

Xabbix APM monitoring tool (Hosted in AWS) has been configured to alert CloudOps and members of SkyDRM engineering team in the event of application and performance issues.  
 
CloudOps team will be handling those tickets according to the escalation process document.  

##  Hardening and Compliance for Prod Account  

* We follow least privilege access to ensure security of prod environment.
* We have our internal CIS hardening done.
* Production account is fully owned by CloudOps team , 
* Application deployment must be handled by CloudOps team.  


## Build Process  

* Build artifacts(WAR/Docker images) should be pushed to staging account for security and compliance reasons.
* CloudOps will take care of pulling artifacts and deploy in production account.

  
## Support process  

* Helpdesk ticket should be raised by engineering team if they need any help in production account.


We managed to get listed under SOC2 free listing , we have to put above processes in place in order to achieve full SOC2 compliance for SaaS applications (SkyDRM).  
  
Please include **@Pattabhiram** in the release schedule he will be taking care of deployment.  
  
He will test the new deployment in ServiceOps account (DevSecOps) then deploy in production. This is the process we have been following for every CloudAz release.  

Let us know your availability this week so that we can go over the CloudOps/DevSecOps process workflow with our team.