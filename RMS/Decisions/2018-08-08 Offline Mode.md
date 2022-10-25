# Offline Mode: SkyDRM server requirements
  
Decision date: 2018-08-08    
Approval: Approved   
Approver: SGDC, CDC   
  
  
The requirements for the client offline mode are specified in the "SkyDRM - iOS offline mode" feature specification document v0.1 released on 2018-06-12.  
  
For now NXL files from the following locations can be marked for offline availability:  

* Project
* MyVault
  
## SkyDRM server side impact
Files cached for offline use will be viewed but not edited. Offline files should be clones of the project / MyVault files, i.e., there should be no change to the DUID.  
  
The REST APIs that will be consumed are:  

* [MyVault: download file](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/MyVault%20REST%20API#markdown-header-download-file)
* [Project: download file](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API#markdown-header-download-file)

The current behaviour of the MyVault download file API conforms with the requirement. The DUID remains unchanged.  
  
The current behaviour of the Project download file API also conforms with the requirement. Client can set the flag "forViewer" as true to clone the file. The platform ID is used to differentiate between web viewing and client offline download. 
  
The current API is defined as follows:  
```
#!json
{
   "parameters":{
      "start":0,
      "length":1000,
      "pathId":"/user-guide.2016-12-25-14-23-14.xlsx.nxl",
      "forViewer":true
   }
}
```