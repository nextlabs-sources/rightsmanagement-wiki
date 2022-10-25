# Central policies for Master Tenant

Decision date: 2018-07-27    
Updated: 2018-09-24  
Approval: Approved   
Approver: Keng   
  
## Clarification on 2018-09-24    
  
On 2018-09-24 there was a clarification with the PS team (Sadesh, Paritosh, Kent) and Tapas on the behaviour. 
    
* The enterprise policy administrator should be able to create a policy at the master tenant level.  

* When access to a file is requested, the client (web, desktop, mobile, other) triggers a policy evaluation. The project level policy, and the master tenant policy (if it exists) are used for the policy evaluation.  

* Access to the resource should be granted if:
    + Both master tenant policy and the project policy allow access (or)
    + Any one of the master tenant policy or the project policy allow access
  
* This behaviour should be a part of the SkyDRM Web App, all other client apps, and plugins or applications using the client or server SDK.
  
All data below this should be considered historic and for reference. 

-------
  
## Clarification on 2018-08-03  
  
The enterprise application administrator should be able to create and deploy central policies for the top level tenant. This is the Master Tenant for enterprise deployments, and the SkyDRM public tenant for the SaaS product.   
  
For enterprise deployments, the enterprise master tenant contains all other tenants and projects under it. Central policies deployed to the master tenant can be used by users, scripts or other applications like the smart classifier to do batch protection of files.  
  
The following policies should apply by default when protecting files outside or inside projects:  
    
| Domain     |  Default policy                        |
| -----------|----------------------------------------|
| Projects   | Based on the policy evaluation result  |
| MySpace    | Master tenant central policy           |
   
Project-specific policies may override or conflict with master tenant central policies, and the final decision is made during policy evaluation by the policy engine. 
  
   
## Policy phases  
  
The following phases must be considered:
  
1. Policy creation and deployment
2. Applying the policy (protecting files)
3. Policy evaluation (accessing files)  

  
### Policy creation and deployment  
This is the job of the person with the administrator role, with rights to create policies. The administrator creates central policies for the master tenant, and is able to deploy them in the enterprise SkyDRM installation. Once these policies are deployed, they are available to users of the enterprise SkyDRM system.  
  
### Applying the policy  
Once the central policy is deployed, SkyDRM users can make use of the policy. The user can choose to protect files and apply either an ad-hoc policy or the pre-defined central policy. The user's workflow to protect files outside the projects would be the same or close to the workflow he follows when protecting files inside projects. He would be presented with a similar user interface, be allowed to define an ad-hoc policy or choose the central policy and protect the file.  
  
### Policy evaluation   

Policy evaluation is performed when access to a resource is requested. There is no change in this process.    
  
## Other Points  
  
Centrally defined policies can also be applied to files in linked accounts, e.g., Google drive, Box, and so on.  

-------
  
## Old data from 2018-07-27  

Central policies are needed for MySpace. This is meant for enterprise deployments. The enterprise administrator can create central policies that are deployed to the SkyDRM installation on-premises.  
  
The team has completed the initial work. SkyDRM now allows the administrator to define central policies for MyDrive.   
   
The use cases need to be clearly defined. Initial thoughts around the use cases are:   

+ Batch processing, e.g., using smart classifier, that can use central policies for files where ad-hoc policies are not in force. Maybe the central policy could even override the ad-hoc policy.
+ Using non-project central policies for Teamcenter or SAP integration use cases. 
  
## Next Steps

+ Refine the use cases for central policy for MyDrive
+ Define the SkyDRM + TeamCenter integration strategy: usage + workflows, APIs needed, etc.
+ Define the SkyDRM + SAP integration strategy: ditto
+ Files protected in the Teamcenter context should be accessible in the SAP context if needed. Decryption tokens should not be restricted to one application (e.g., Teamcenter).