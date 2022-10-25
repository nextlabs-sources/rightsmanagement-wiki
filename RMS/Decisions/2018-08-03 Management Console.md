# Separation of management console from user webapp

Decision date: 2018-08-03    
Approval: Approved   
Approver: Keng  
  
Today the decision was made to separate the management functionality from the normal user webapp. The details are as follows:  

* The URLs for the normal user login and admin login would be different.  
* Admins with a valid management login would be presented with a management user interface (management console).  
* The management console will only contain controls that allow the admin to manage SkyDRM, e.g., create policies and so on. This management console would not present any of the options shown on the normal web app, e.g., projects, MySpace and so on. 
* Admin users are assumed to be different from regular users.  
* When a normal user logs in to the web app, none of the admin (configuration) options will be shown.
  
## Actions  
  
1. Define the details of what would be shown in the management console. 
2. Evaluate the effort.  
  
## Notes  
  
* The management console is __not__ a part of the August 2018 release. We will complete the August 2018 release with the existing functionality, and plan to build the management console based on the effort estimates after this release.
* Look at how this works for the Box enterprise account we (should?) have.