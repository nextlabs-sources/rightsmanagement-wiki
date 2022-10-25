# Policy Management  
  
This page documents the decisions made and captured in April 2016 on policy management using control center. The decisions were recorded in a document called "Centralized Policies in SkyDRM - Design". The information will be made available here to keep it centralised.  
  
# Overview  
  
## Objectives  
  
This document describes the design for supporting Centralized Policies in SkyDRM. Multiple options were considered for the integration and the pros and cons of each approach is discussed here. The requirement is to have a viable solution for both the cloud and on-premise versions of RMS and Control Center.  
  
# Options Considered  
  
## Option 1: One Control Center for each tenant in SkyDRM  
  
![01.png](https://bitbucket.org/repo/dBgzdj/images/1944434812-01.png)  
  
1.	RMS to maintain mapping of CC instance for each tenant.
2.	RMS can’t use Embedded Java PC in this option. RMS to connect to Remote JPC(s) using REST API for policy evaluation.
3.	All RMCs will use RMS to get policies, similar to how it used to work in the old on-premise implementation.
4.	RMS can cache policies to reduce the load on CC.
5.	For Policy creation, there are 2 options:  
a.	Use the existing web based UI of Policy Studio – It is currently not pluggable into any other application. Plugging it into the SkyDRM UI may not be simple.  
b.	Adapt the Policy Studio UI for RM use cases and use the CC API to store the data in the CC database. ->First define the model for the RM Policies and this should drive the UI.  
6.	The main drawback in this approach is that the number of instances to be managed grows with each new tenant.  
  
## Option 2: One Control Center for as many tenants as possible  
  
![02.png](https://bitbucket.org/repo/dBgzdj/images/161976087-02.png)  
  
1.	One CC in the backend to support all tenants in SkyDRM.
2.	CC team will have to build filtering of policies based on Tenant ID.
3.	All RMCs will use RMS to get policies, similar to how it used to work in the old on-premise implementation.
4.	RMS can cache policies to reduce the load on CC.
5.	RMS can use the Embedded Java PC for Policy Evaluation.
6.	For Policy creation, there are 2 options:  
a.	Use the existing web based UI of Policy Studio – It is currently not pluggable into any other application. Plugging it into the SkyDRM UI may not be simple.  
b.	Adapt the Policy Studio UI for RM use cases and use the CC API to store the data in the CC database.  
7.	Lower operational effort since there is only one CC stack to maintain.
8.	Scalability concerns would be more pertinent compared to Option 1.  
  
# Decision  
  
For Policy Creation, RMS will build its own UI and use the APIs exposed by Control Center to store the policies in the Policy Server. We’ll see if we can re-use some of the UI code from the CC code-base. To start with, we’ll not have component creation UI in RMS. This will be taken care of by ‘Operations Management’ team as part of the tenant on-boarding process. Customers will have to tell us what sort of components they want to create and we create it for them. 

Classification information should ideally be stored in the Dictionary – currently, it is configured/stored in RMS. This should be included in the long-term plan. 

Option 1 is non-sustainable in the long-run. It has high operational costs. Option 2 will be used for SkyDRM – One Control Center for as many tenants as possible. When we hit the bottleneck either on the CC side or RMS side, replicate the entire setup (RMS+CC) for the new tenants. 

RMS would perform periodic ‘HeartBeat’ for the tenant specific policies, cache it and provide it to RMC/RMD. Control Center team to provide information on how to get filtered policies as part of the Policy Bundle and also for the Policy Creation UI. This is to avoid returning policies of all tenants for every request. If we use Central Policies for each individual account as well(policies defined based on pre-defined Classification), then it could lead to thousands of policies. Each user will have 6 pre-defined classifications. Keng clarified that this is not a must-have feature in the initial release. 

RMS will use the Embedded Java PC for Policy Evaluation. We need to analyze how this would scale if the number of policies increases considerably.  

For logging/reporting, it was explained that the current Reporter schema is tailored for Policy Evaluation and is not generic enough to accommodate Activity Logs. Trying to fit in the activity logs into the current Reporter schema would not be the right way forward. There are requirements from other products like RMS, Smart Classifier etc for logging/reporting activity logs. It has been agreed that it is best to come up with a new solution with a generic schema that suits the needs of multiple products. This would be built as a service that can be consumed by multiple products within NextLabs.  
  
# Policy Creation   
  
RMS team will build its own UI for creating the Policies. It will use the REST APIs exposed by Control Center for retrieving/storing the policies from/to the Policy Server. The policies created by one tenant should not be visible to another tenant. Since Control Center doesn’t have support for Multi-tenancy yet, we’ll achieve this by using tags. There are two approaches possible – using tags/Delegated Administration or using PBAC(Policy Based Access Control). Based on a discussion with the Control Center Engineering and Product Management teams, it has been decided that RMS will use the Delegated Administration Model for access control, since that is the way forward in Control Center as well. 
  
We can use ‘ATTRIBUTE’ or ‘tags’ to categorize policies based on tenant. ATTRIBUTE is purely internal(stored as part of the PQL) whereas ‘Tags’ are visible to end users. Currently, there is no REST API to filter policies based on ATTRIBUTE, but we can filter policies based on tags. Since ATTRIBUTE goes into PQL, and eventually becomes unstructured in terms of RDBMS, future upgrades or data migrations will be impacted. Whereas, tags are first-class columns in database table and provide lot more control and flexibility. Hence it has been decided that RMS will use tags to filter policies.
  
The Control Center Administrator’s credentials will be configured in RMS and this will be used to created one CC user per tenant/project using REST APIs. This per tenant/project user credential will be used to create policies for each tenant in RMS. This by default will restrict the set of policies returned to the ones created by the same user. Apart from this, RMS can use Delegated Administration model if access to policies needs to be granted to more users. For Authentication for the REST APIs, RMS needs to use CAS. CAS returns a cookie that needs to be sent as part of each REST request.
  
![03.png](https://bitbucket.org/repo/dBgzdj/images/2078369834-03.png)  
  
The policies will be stored in the Policy Server using the standard PQL format:  
  
![04.png](https://bitbucket.org/repo/dBgzdj/images/2604445769-04.png)  
  
The following elements will be present in the Policy Creation UI of SkyDRM:  
  
* Resource  
    + Identify resource based on  
        + Classification
        + Extension  
        + ???
* Subject  
    + Identify subject based on    
        + Group
        + User attributes
        + ???
* Rights  
    + Choose rights
* Effect  
    + Specify watermark text
* Conditions  
    + Time Based/Location Based etc. Show additional options if the user chooses any of these.
  
# Policy Bundles  
  
RMS currently provides Policy Bundles to RMC/D as part of HeartBeat from the clients, acting as a proxy that just forwards the requests from RMC/D to Control Center. This could create a lot of load on Control Center in the context of SkyDRM, since there could be thousands of clients. Hence RMS will now perform Heartbeat on behalf of all clients and cache the Policy Bundle. It would send the Policy Bundles to the clients based on the tenant/project information of the currently logged in user.   
  
![05.png](https://bitbucket.org/repo/dBgzdj/images/2328274267-05.png)  
  
The RMS instances behind the ELB(Elastic Load Balancer) will synchronize amongst themselves to avoid sending Heartbeat requests from each RMS instance. RMS will not be enrolling users in CC. This shouldn’t have any impact on the policy bundle. The ‘UserMap’ would come up empty in the policy bundle and all user attributes would appear as is in the Policy.The Heartbeat frequency is configurable in RMS(default value is 30 minutes). The Policy Bundle returned by the Heartbeat will be cached in RMS(either in database or in EFS that can be accessed by all nodes of RMS). The cached policies will be grouped by Tenant/Project. When a heartbeat request is received from a client, RMS would first determine the memberships of the user based on the ticket in the Heartbeat request. From this, RMS would get the list of tenants/projects that the user has membership for. RMS would get the policies for the relevant tenant/projects and send the same to the RMC.  
  
# Policy Evaluation  
  
The ‘Viewer’ component of SkyDRM will include Embedded Java PC. This JPC will be configured to connect to the CC and retrieve all policies. There will be no filtering done by the JPC. This could mean that the policy bundle could be huge when there are 100s of tenants. But as long as we can have an attribute like ‘TenantId’ in the policy and pass the same during Policy Evaluation, the number of policies applicable for a given evaluation request would be greatly reduced and shouldn’t ideally impact the performance.   
  
As of today, 2 PDPs can’t co-exist on the same host without potential issues. The reason is that the ‘Hostname’(FQDN) is used in the registration. This could lead to discrepancies in the policy bundle updates. The right solution for this would be for CC to use a PDP identifier(which could be specified by the PDP itself during registration) in addition to the hostname. Due to this limitation, we can’t have ‘RMS’ and ‘Viewer’ components of SkyDRM deployed in the same host and expect them to work correctly.  
  
![06.png](https://bitbucket.org/repo/dBgzdj/images/1248558917-06.png)  
  
# Logging and Reporting  
  
A new service is being developed for centralized logging/reporting. Details of this service can be found at -  
 
https://sharepoint.nextlabs.com/sites/engineering/Docs/Engineering%20Documents/RM/Everest/Centralized%20Logging_Reporting%20-%20Requirements%20And%20Analysis.docx?Web=1  
  
# Additional notes and reference information   
  
This section contains additional points related to the design and implementation of the policy creation, management and evaluation on SkyDRM, and details from the users of the products.   
  
* RMS users are not enrolled in the control center, i.e., there is no mapping from RMS users to CC users, whether we authenticate against the internal SkyDRM IDP or against an external IDP using LDAP or SAML.  
* CC does some “pre-processing” of enrolled users in a way that is not useable by RMS. [what exactly is happening here, why can’t RMS use it?]  
* RMS does not create Subject/User components on CC. Even if Subject/User components are created on CC, they are not consumed by RMS. This decision was made because the older CC 8.6 had some issues creating user components via the API but this may be fixed now [what exactly was the issue, and can we use this API now?]
* RMS tags policy with the tenant name of the project tenant. With the architecture update, RMS will tag it with the token group name. The tag is used to filter out the policies that are not required. 
* RMS will store user information + application information + advanced conditions internally [double-check this point]. They are not saved on CC as separate items. RMS creates a single advanced condition using these three (user info + application info + advanced conditions) which is used by CC along with the resource components created in CC. [check how this advanced condition and resource components are used during policy evaluation via RMS and via the RMD]
* RMS document classifications are saved as attributes in the CC policy model. There is one policy model for the project.

----------------      

Feedback from Paritosh 2019-04-18: 

Hi Sriram,

1. The moment you change a policy in CC that was previously deployed using SkyDRM UI, the policy is not visible in the SkyDRM UI going forward. This is because of some differences in how the policy versioning is done between CC and SkyDRM.

2. Even though the policy is still existing in the backend (not displayed in the UI), the Policy Cache seemed to be corrupted as the policies didn’t work as expected. Please note, that I tested this in the context of enrolled attributes, and so it might have corrupted the cache.

3. Please note, I have not tested a simple resource component change from CC for instance in the policy, in which case the policy might still work, but just not displayed in the SkyDRM UI. This needs further testing and confirmation. Also, I don’t know if this would have any other side-impacts. [action: can we make a simple change in a pre-deployed policy, e.g., remove one resource component or action component, check if this can be seen by the RMS UI and used in SkyDRM?]

Thanks,
Paritosh Mehta

----------------     
  
  
Key differences between the way policies are managed on CC vs RMS  
  
* Users: RMS puts this information into advanced conditions, whereas CC can use the Subject/User component which RMS does not use. 
* CC does not support Applications. RMS puts this in the advanced conditions. 
* User attributes: RMS manages the user attribute mapping internally. It does not use any user attributes from CC. 
* RMS does not support Obligations in policies. 
* Digital rights preferences (default watermark, rights validity period) are not managed through CC but in the RMS. 
* [ TBD ]