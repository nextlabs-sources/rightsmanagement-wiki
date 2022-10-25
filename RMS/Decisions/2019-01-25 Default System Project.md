# System Project
  
Discussion date: 2019-01-25    
Decision date: 2019-01-31    
Status: In progress   
Author: Sriram    
Approval: 2019-01-31   
Approver: Keng   

  
## Description  
  
Until February 2019, it was mandatory that encryption tokens used to protect files were associated with projects, or with MyVault. Tokens from projects were meant to be used used for all the enterprise and business use cases, i.e., protecting files for corporate use and collaboration using projects. The use of MyVault is limited to protecting and sharing personal files.   
  
It was challenging to protect files for enterprise uses that were not associated with a project. There are several use cases that required such a design feature.  
  
* Customers integrating SkyDRM with other enterprise applications such as TeamCenter require that files are managed on TeamCenter volumes and not on SkyDRM.
* Day to day use cases in any enterprise are not tied to projects. For example, most corporate environments provide shared network drives or other mechanisms that allow users to store files on storage providers that are hosted or administered by corporate IT.  
  
The system bucket was designed to address these requirements.   
  
* There is one system bucket per tenant. 
* In the context of enterprise use cases excluding MyVault, tokens to protect files can now originate from projects as with the previous SkyDRM release, as well as from the system bucket which is a new feature. More specifically, the encryption and decryption operations can now request and use tokens from the system bucket instead of being restricted to using tokens from projects.
* The behaviour of projects is unchanged, i.e., users can protect files in the context of a project, for the purpose of collaborating and sharing with other members of the project. 
* The system bucket tokens allow files to be protected without mandating their association with a project. This allows the user to protect files without associating it with a project. 
* It enables other NextLabs products such as the Smart Classifier to provide workflows for batch processing and protection of files outside the context of projects. 
  

## Update, discussion with Keng on 2019-02-22:   
  

* Classifications are inherited from the login tenant.
* Policies are deployed at the default tenant level.
* Access to files from the system bucket should be governed by the policies deployed. 
* If the policy denies access to the creator/owner, this behaviour is enforced.
* Ad-hoc policies work as it did earlier, unchanged.
  
  

## MoM between rms-dev-team@nextlabs.com and Sadesh on 18-Feb-2019 ##
No major changes and original requirements stand.
It will be a system project and the file access will be controlled via domain policies to its parent tenant.


Not yet clear - sharing flow, particularly in Rehau case where they already have a project and will be using via dropbox, amidst the edit flow using RMD.

## MoM between rms-dev-team@nextlabs.com on 1-Feb-2019 ##

* Change project upload api to prevent upload to default project.
* No need to include default project in list projects.
* Clients call List Membership and call Get tenant preferences (as per wiki - not yet implemented).
* No need to add column distinguishing default project in project table of db.
* Should we stop this in SAAS (yes, because cannot abac allow everyone in SAAS)? Use isSaasMode in addTenant while creating default project
* Revisit on Monday - What should be the value of the "system tag" and its name ?
*  * tag = tenantname_system
*  * name = tenantname_system
*  tenantname of the parent tenant is already a guid, alternately can use tenantId too if preferred.  


## Decision on 2019-01-31  
  
The requirements are:  
  
* **General requirements**
    * The system project is just another project on the tenant. 
    * This project is created initially to support the local encryption use case.
    * There should be only one system project per tenant.
* **Access Control**
    * Access to this project should be restricted. Project admins should not just be allowed to enter this project and modify it. 
* **Policies:** Project and file access policies to this system project should be created such that:
    * Users are *not required* to be members of the project.
    * Everyone in the enterprise can request to generate and retrieve tokens. 
    * Once a file has been encrypted, only the creator of the file should be permitted to get a token to decrypt the file.   
* **Operations**
    * The tenant admin needs to create it for the first phase of the development / deployment. 
    * In future this project will be automatically created when the RMS launches for the first time.  
* **Design and Code**  
    * We will use *tags* to identify the system project in the DB. This will allow the flexibility to define access control rules later. 
    * We will add a section in the tenant admin console to create a system project. This is actually two steps, (a) create a project and (b) tag it as the system project. They should be completed as a transaction. 
    * We want to add a new *create system project* API instead of changing the [create project](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API#markdown-header-create-project) API. Only the tenant admin should be allowed to access this API. 
    * We will update the [create project](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API#markdown-header-create-project) API to check if the caller has specified any `projectTags`, and if one of them is `SYSTEM`. This should not be allowed. 
    * The [get tenant preferences](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Tenant%20REST%20API#markdown-header-get-tenant-preferences) REST API should be updated to query the system project from the DB and return its tenant ID. 
    * The [list projects](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API#markdown-header-list-projects) API should be updated to include a tag for the system project. create a new field `projectType: SYSTEM_PROJECT` that is returned for a system project. 
    * The web app should be updated so that users never see the system project when they login. 
* **Constraints and other notes**
    * The *system project* in this implementation is no different from any other project, and files protected this way are subject to the same constraints as files protected for other projects.
    * In the first phase, the user cannot simply upload or copy the NXL file protected for the system project into another project. 
    * With the upcoming implementation of the *DOWNLOAD DECRYPTED* workflow, the user can download the cleartext version of the file and upload it into another project that way. This flow requires a few manual steps, but the user is not locked out of his files or prevented from transferring the files into another project or sharing them.
    * The user could email or share the file with someone else, and this flow is not managed by SkyDRM. 
    * For the future, the workflow of moving or copying files into another project can be automated. This requires updates to the webapp and the client applications. 
    * The system should not allow the deletion of the *system* project. 
    * The system should not allow multiple *system* projects, i.e., when tagging projects, RMS should check if any other projects are tagged `SYSTEM`.
    * Users should not be allowed to upload files to the *system* project.
    * The solution proposed here does not support the use case to upload a file into a project. The workaround is for the client applications to provide a decrypt option. 
  
## Future Directions and Implementation

* **Sharing / Adding files to projects**  
    * If the user wants to add a local protected NXL file into a project, SkyDRM should provide an API to allow this. 
    * This API would decrypt the NXL file after verifying access rights, and re-encrypt it for the target project so that the token is managed in the context of the target project. 
    * If the local protected NXL file needs to be added to multiple projects, the operation above is repeated multiple times, once per project, i.e., if the file was added to 10 projects, there are 10 copies of the file, each with a different DUID and a unique token associated with each of the 10 projects. 
* **Downloading protected files from projects**
    * Today, when a file is downloaded from a project, the process creates a copy of the file with a new DUID, and retains its association with the project. 
    * Now if the user tries to upload this file into another project, it will fail because the project in the NXL file does not match the target project. 
    * In future, this process should work seamlessly, i.e., when downloading a file from a project, the file should not retain a connection with the source project. It should switch to the system project. 
    * When uploading a file from a system project into any other project, SkyDRM should perform a transparent re-encryption after verifying access to the file. 

***
## Past Discussions   
  

This feature was first suggested by Keng on **2019-01-18** in the context of local file encryption, and the scope was refined on **2019-01-25** for precisions on the concept of the SYSTEM project. The information on this page reflects the last update as of 2019-01-25.
  
The discussions on 2019-01-18 were focused on the local file encryption use case. The original workflow is illustrated in the screenshots of the desktop application below. 
  
The first screenshot shows the first step of the local file protection workflow. Since the token for each NXL file is associated with a project, the user is asked to select a project in the protection workflow.
  
![s1.png](https://bitbucket.org/repo/dBgzdj/images/2306172011-s1.png)  
  
However, the idea proposed by Keng is to remove the requirement to tag the protected file to a project. 

![s1 - Copy.png](https://bitbucket.org/repo/dBgzdj/images/4126451311-s1%20-%20Copy.png)  
  
Proceeding to the next step the user will choose a company-defined or ad-hoc policy and proceed to protect the file. 

![s2.png](https://bitbucket.org/repo/dBgzdj/images/1243979958-s2.png)  
  
The protected NXL file would then be available on his desktop workstation local drive.
  
During this discussion it was agreed that :  

  * The user will be able to EDIT and VIEW the local NXL file.
  * The user will not SHARE the file.
  * The user will not UPLOAD the file into a SkyDRM project. 
  * The user may choose to email the NXL file to someone, but this is considered out of scope of SkyDRM.  
  
  
The initial workflow proposal from Keng on 2019-01-18 was :   
  
  * The RMS starts up for the first time. 
  * It creates the "default system project" for each tenant. 
  * The client applications can query the [get tenant preferences REST API](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Tenant%20REST%20API#markdown-header-get-tenant-preferences) to retrieve the default system project tenant ID from `SYSTEM_PROJECT_TENANTID`.
  * If the `SYSTEM_PROJECT_TENANTID` value is not `NULL`, the client applications will use this project tenant for all file protection operations. 
  * If the `SYSTEM_PROJECT_TENANTID` is `NULL`, the client application will ask the user to select a project for the protection operation. 
  

On the 2019-01-25 discussion with the team and Keng the following requirements were proposed:  
  
  * Users must always have the ability to protect a file without having to choose a project to associate the protected file with.
  * No one except the file owner should be able to get the token to decrypt the file protected for the system project. 
  * Tokens for all local protected files should be stored in the system project.  
  * The system project should never be deleted. 
  * *to be clarified:* In future, when protecting local files, the system should not allow a project to be selected. It should default to the system project. 
  * This "system project" should not require users to be members of the project.  
  * One of the goals for the "system project" is to allow files to be copied or moved across projects. The user should be able to protect a file, load it into project-A, copy it into project-B, and so on.
  * It should be possible to use the default system project to protect all files on SkyDRM. 
  
The implications of the requirements are :   
  
  * If there is no membership requirement to the default system project, anyone can request token access to decrypt any files. 
  * The system project should not be visible to users of the webapp, desktop or mobile apps. 
  * Rights / security :  
      + Who should have the rights to administer the system project? It cannot be any project admin, because any change to the system project will affect all files across the system. 
      + Access to the system project should be a new role / right. 
      + We need to add this code in the role management subsystem of the super-admin application, to be able to manage the system project admin role and rights. 
      + We could choose to create a new role like the tenant or project admin, __(or)__ choose an existing user and apply the rights to manage the system project
  * User management, policies, access
      + If no users need to be enrolled in this project or have membership in this project, how will access to tokens work? Will there be no checks? Can any user ask for a decryption token and read the files? 
      + Since we will use this to share or move or copy files across projects, how will that work? 
      + When moving files across projects, how should SkyDRM ensure that classifications across projects are compatible? How to ensure that policies across projects will not suddenly deny access to a whole lot of files after they were moved from one project to another? 
      + When moving a file from project-A to project-B there may be security/access holes. Project-A can have a restrictive policy and Project-B could be more permissive, e.g., Project-B may allow `PRINT` rights while Project-A does not. Who is responsible for managing these security issues, to manage policies across projects? 
  * Operations
      + The admin for the system project still needs to create classifications and policies. This cannot be automated. 
      + If classifications are not created, the local file encryption flow will not work because the clients will not know what classifications / tags can be applied to the files to be protected. 
      + If policies are not defined, there is no way for clients to tell if they would suddenly be locked out from accessing their protected files.
      + How will this work when the user wants to now move files into a project, or move files from project-A to project-B? 
  * Feature / code updates   
      + The super-admin application should be updated to manage the system project admin role / rights. 
      + The tenant admin application should be updated to allow the tenant admin to select the default system project.
      + The web front-end code should be updated so that users do not see the system project when they login to the web app.
      + The REST APIs should be updated so that the system default project does not appear in the list of projects. 
      + File uploads to the system project should be denied. 
  
Points to resolve (work in progress, this is a very early draft):  
  
  * Update the [get tenant preferences REST API](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Tenant%20REST%20API#markdown-header-get-tenant-preferences) to return the `SYSTEM_PROJECT_TENANTID`
  * Define the workflow to create the system project for each tenant.
  * Specify the workflow to define the classifications and policies for the system project. 
  * Define how the system project admin rights are to be managed.
  * Define the sharing workflows. 
  * Define all the workflows where files can be moved or copied across projects. 
  * Specify the "view" concept where there is just one physical NXL file which can be accessed through views across different projects. In other words, if `file1.txt.nxl` is accessible through project-1 and project-2, and someone makes a change to the file through project-1 the updates are seen when the file is accessed through project-2 as well. This is like the Unix hard link or symbolic link concept. 
  * Define how to manage the consistency of classifications / tags and policies when copying or moving files across projects, or creating "views" of files from multiple projects. 

  
  
2019-01-29  
## Phase-1  
Discussing with Tarun this morning, we can consider the following set of features for phase-1.  
  
### Requirements   
  
* __OPS:__ The system project is just another project on the tenant. The tenant admin needs to create it.
* __OPS:__ All the enterprise users are enrolled in this project. The admin needs to ensure this. _Update:_ We can deploy an "allow all" ABAC policy so that the admin does not need to create it. 
* __CODE:__ The tenant admin console should be updated to allow the tenant admin to choose the default system project. _Update:_ Specifics in the section below. The create + assign default process should be atomic (transaction). 
* __CODE:__ Once the tenant admin creates + sets the default system project, this information is stored in the DB.
* __CODE:__ The [get tenant preferences](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Tenant%20REST%20API#markdown-header-get-tenant-preferences) REST API should be updated to query the default system project from the DB and return its tenant ID.  
* __CODE:__ The [list projects](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API#markdown-header-list-projects) API should be updated to include a tag for the system default project. Check if the `accountType` field can be used. Or create a new field `projectType: SYSTEM_PROJECT` that is returned for a system project. 
    * Using `projectType: SYSTEM_PROJECT` would allow for multiple system projects. 
    * The client app can query the get tenant preferences API for the default system project. 
    * This will allow the client app to hide all the system projects in case the deployment has multiple system projects. 
* __CODE:__ The web app should be updated so that users never see the default system project when they login. 
* __OPS:__ There is no change to the project management / admin roles. 
  
### Constraints and Notes  
  
The "system default project" in this implementation is no different from any other project, and files protected this way are subject to the same constraints as files protected for other projects. Specifically:  
  
* __FEATURE:__ Protecting a file for the system project should not result in files becoming inaccessible. 
* __CONSTRAINT:__ The user cannot simply upload or copy the NXL file protected for the system project into another project.  
* __CONSTRAINT:__ With the upcoming implementation of the "DOWNLOAD DECRYPTED" workflow, the user can download the cleartext version of the file and upload it into another project that way. This flow requires a few manual steps, but the user is not locked out of his files or prevented from transferring the files into another project or sharing them.
* __FEATURE:__ The user could email or share the file with someone else, and this flow is not managed by SkyDRM. 
  
## Future Options  
  
For the future, the workflow of moving or copying files into another project can be automated. This requires updates to the webapp and the client applications.  
  
  
## Engineering Design Status & Decisions  
  
There was a meeting with the team on 2019-01-29. The following points were discussed and agreed.  
  
* __PRECISION:__ The concept of a SYSTEM project is different than the DEFAULT project. The workflow above describes the DEFAULT project.  
* __DESIGN:__ We will use tags to identify the DEFAULT project instead of the project TYPE field in the DB. This will allow flexibility to define access control rules.   
* __DESIGN + CODE:__ We will add a section in the tenant admin console to create a default project. This is actually two steps, (a) create a project and (b) tag it as the default. They should be completed as a transaction. 
* __DESIGN + CODE:__ The default project access policy should allow access to all users. This can be created automatically. 
* __CODE:__ We want to add a new "create default project" API instead of changing the [create project](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API#markdown-header-create-project) API. Only the tenant admin should be allowed to access this API. 
* __CODE:__ We will update the [create project](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API#markdown-header-create-project) API to check if the caller has specified any projectTags, and if one of them is DEFAULT. This should not be allowed. 
* __Constraints :__   
    * The system should not allow the deletion of the DEFAULT project. 
    * The system should not allow multiple DEFAULT projects, i.e., when tagging projects, RMS should check if any other projects are tagged DEFAULT.
    * Users should not be allowed to upload files to the DEFAULT project.
    * The solution proposed here does not support the use case to upload a file into a project. The workaround is for the client applications to provide a decrypt option. 


-- END --