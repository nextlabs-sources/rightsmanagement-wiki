# Tenant Model and Hierarchy
  

Decision date: 2018-08-03    
Approval: Approved   
Approver: Keng  
   
The tenant model proposed today is described in this page. We consider both the SaaS and enterprise on-premises deployment models.  
  
## SaaS Model  
  
The overview of the SaaS tenant model is shown in the picture below.
![Slide2.PNG](https://bitbucket.org/repo/dBgzdj/images/1694580304-Slide2.PNG)
The SaaS model is deployed and managed by NextLabs. While it was initially designed for individual users, the decision was made today to open the SaaS version to businesses as well. Each business would be able to pay for an account on the SkyDRM SaaS. The business would not need to install any server software on-premises. This is suitable for small to medium sized businesses that do not want to operate their own IT infrastructure.  
  
The service would be maintained by NextLabs. At the SaaS top level would be present the SkyDRM master tenant. This would be managed by NextLabs. Central policies for this tenant would be defined and managed by NextLabs.  
  
Under the SkyDRM master tenant would be the SkyDRM public tenant. All individual subscribers would come under this tenant. Central policies defined for this tenant would only apply to individual subscribers.  
  
Businesses purchasing an account on SkyDRM SaaS would have their own tenants. Central policies defined for these tenants would be managed by the administrator for __each business tenant__. These administrators are different from the SkyDRM public tenant administrator or the SkyDRM master tenant administrator. The business tenant administrator roles must be created when the business first purchases an account on SkyDRM SaaS.  
  
## Enterprise Model  
  
The overview of the enterprise model is shown in the picture below.  
![Slide3.PNG](https://bitbucket.org/repo/dBgzdj/images/3337142738-Slide3.PNG)  
The enterprise model is conceptually the same as the SaaS model in terms of how the tenants are structured. With the enterprise deployment there is one enterprise master tenant, that plays the same role as the SkyDRM master tenant in the SaaS model.  
  
Under the enterprise master tenant, the administrator can create multiple tenants as required, e.g., a tenant per department or a tenant per function. This structure is completely controlled by the enterprise SkyDRM administrator.  
  
In this model, the enterprise central policy can be defined by the administrator and deployed on the system. This central policy can be used to protect individual files, for batch protection or by other enterprise applications.  
  
The enterprise SkyDRM administrator can either choose to be the administrator for sub-tenants, or assign others to perform administrative roles for each tenant. This is controlled by the enterprise.  
  
Here, as with the SaaS model, the enterprise SkyDRM administrator can define and deploy the central policy. Per-project policies can also be defined and deployed. The policy evaluation process will see no change, and will behave as it does today. This is specially important when there may be a conflict between a central policy deployed at the enterprise master tenant and the project-specific policy. 
  
\- END -