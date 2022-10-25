# SkyDRM 2019 requirements: draft 1
  
-----------------------  
    
## SaaS business accounts  
  
Backend: CloudAZ integration [connect it, configure and validate]  
  
* [Jira story](https://nextlabs.atlassian.net/browse/SC-2484)
* Why: We need central policy support in SaaS. CloudAZ/CC needs to work with SkyDRM SaaS (testdrm & rmtest for testing, and skydrm for production).
* Integrate CloudAZ 8.6 with SkyDRM SaaS to be able to deploy central policies.   
  
Backend: Single tenant managed SaaS service [2019-05-27]    
  
* The requirement is to have a single-tenant service for small business customers.  
* This will be a managed service that the customer does not need to deploy on-premises.   
* The product to be deployed on the cloud will be the same as the enterprise version of the software, it will be deployed on AWS and managed by NextLabs for the customer.  
  
See the screenshot below for the details in the mail:  
  
![requirements-02.png](https://bitbucket.org/repo/dBgzdj/images/163159890-requirements-02.png)  
  
Tasks to be completed are:  
  
* Make the GA release using the new architecture codebase in mid-June 2019   
* Deploy the new code on RMTEST, ensure it works with CloudAZ   
* Internal e2e testing using the RMTEST server and CloudAZ  
* Release to QA, e2e tests on RMTEST and TESTDRM  
* Publish the code to Skydrm.com and to other domains required by the customer.  
  

  
Backend: Multi-tenant support [architecture update]  
  
* [Jira story](https://nextlabs.atlassian.net/browse/SC-2406)
* Why: Today there is no real multi-tenant support. Our implementation of multi-tenant only allows multiple DNS domains to exist. Administration, user management, access to projects and files happen across tenants today, e.g., a user logged in to tenant1.skydrm.com can see files and projects from tenant2.skydrm.com.  This cannot work for a multi-tenant business SaaS offer.  
* Separation of projects and files across tenants. Admins and users from tenant1.skydrm.com should have no access to users, projects or files from other tenants e.g., tenant2.skydrm.com.  
    

Backend: Domain user management [design change]  
  
* Users should be managed at the SkyDRM.com SaaS domain level.  
* When a request to create a business account is received, the super admin can create a tenant and assign a tenant admin.  
* The tenant admin can start enrolling new users.  
    + The new users automatically become users of the domain.  
    + The new users are members of this new tenant, but not of other tenants.  
* This means that all users are domain users, and each tenant will allow a subset of the domain users.  
* A domain user may be a member of multiple tenants. For example, tenant1's admin and tenant2's admin can allow the user to join tenant1 and tenant2. However, there is only one record for this user at the domain level. 
  

User management front end: registration, email settings and approvals  

* [Jira story](https://nextlabs.atlassian.net/browse/SC-2320)
* Registration settings: account creation (self-enroll, admin creates account, self-enroll with approval)
* Email settings: welcome email, approval requirements, password recovery, account blocked/deleted, 
* Password rules: password properties and enforcement
* Pending approvals: view, approve, reject, reply
  
Sharing of documents across tenants and domains [design change]  
  
* This feature allows a document or a collection of documents to be shared across two tenants in a single domain. The goal is to enable sharing of documents between a small business and their partners.  
* Extending this concept, sharing should also be allowed across different instances of SkyDRM, i.e., different domains.  
   
Bring your own storage [design update]  
  
* Both personal and business users should be able to choose their "own" storage to store all the files managed by SkyDRM. For example, business or personal users can choose a google drive, onedrive or dropbox account to store their files. With this approach SkyDRM is not responsible for physically storing the files.   
* This opens up a tiered pricing strategy, e.g.,   
    + BYOS (bring your own storage) personal account at $9.95
    + We store your data, $19.95
    
The three types of rights / policies  [terminology update]  
  
* Personal policy : used to be called ad-hoc rights.
* Project policy : policies defined at the project level. 
* Company policy : policies defined at the tenant level.
* Domain policy : policies defined at the domain level, e.g., on skydrm.com, or at the top level domain of an enterprise installation  
  
Personal accounts : policy and project constraints [ongoing discussion]  
   
* Options are: 
    + only support personal policies (ad-hoc, per file)
    + only support 1 project, and the user can define a project policy  
    + (or) don't support projects at all, just allow the user to be able to access files that were shared with him/her by someone else through the "shared with me" option
  

Payment services integration [on hold]  
  
* Payment mechanism
* Payment gateway integration
* [2019-03-13 decision: we will invoice customers and they will pay us directly for now]  
  
-----------------------  
  
## Web application  
### Management console: Super-Admin  
  
Super-admin: view and query audit logs  
  
* This is the super admin user interface to the audit & logging module update
* The super admin can filter, view and run queries against audit logs across the deployment  
  
System overview  
  
* Implement the system overview UX/UI as discussed and designed on Figma. 
* Specific features are: 
     + Router settings
     + General settings
     + Tenant settings
     + Download URLs
     + IDPs
     + User attributes
     + Email server settings
     + Database settings
     + Default storage
     + File sharing service provider
     + CC settings  
  
User management front-end requirements  
  
* This is the user management system designed on figma. This ties in with the user management backend to be implemented on the server. The user management UI we have today is very minimal. It only lists the users and allows the super-admin to assign API user role. 
* Requirements are:
    + User management : List, add, lock, unlock users. Filter users.
    + Add new user flow includes: password and security, roles
    + Role management: view, edit, add new role, view users in a role, delete role
* Registration settings (details in the SaaS section)  
  
Disable admin dashboard on mobile screens
  
* Disable the dashboard on mobile screens. The dashboard is very heavy in terms of content, and better suited for viewing on a desktop browser  
  
### Tenant management console  
  
IDP configuration [done]  
  
* This is a requirement for SaaS business accounts. 
* The tenant admins should be able to configure & connect to IDPs and manage users per tenant  
  
Per-tenant User management interface  
  
* Per-tenant user management for SaaS business customers
* User attribute mapping -> unique per business tenant
* This depends on the implementation of per-tenant user management at the backend. 
  
### User web app  
  
Ability to update classifications [done]  
  
* The UX should allow the authorised user to update classifications on files stored on RMS
  
  
Add central policy file to project  
  
* The user should be able to add a central policy protected file into a project. 
* The file can be (a) on a local drive / user's workstation or (b) in another project
  

Share central policy files through sharing transaction  
  
* The UX should allow the user to share a central policy protected file. This behaviour should be identical to what will be implemented on the clients.   
  

Tenant workspace  
  
* Provide an interface to the tenant workspace.
* The user will be able to list files, view files, and perform all the operations that he can on his MyVault [to be confirmed]
* The user can share files from within the tenant workspace to other projects or to other users. 
  
  
Soft-delete  
  
* "Delete" files to a recycle bin instead of permanently deleting them.  
  
-----------------------  
  
## RMS Server  
Token management API security implementation [done]  
  
* [Jira story](https://nextlabs.atlassian.net/browse/SC-2445)
* Why: Today the token management APIs have inadequate authorisation checks. For example, we return tokens to callers (subjects) who should not have access to a certain file because the policy evaluation results in a "deny access".
* Generate token API : pre-fetch and immediate use tokens
* Retrieve token: policy check for central policy file, rights check for ad-hoc file.
* New Update NXL metadata API  
   
Policy creation to allow sharing of central policy files [done]    
  
* Allow the admin to create policies to enable sharing of central policy files.
* This will enable the client feature to allow a user to share a file that was protected using central policy.  
  
Design requirement: design update to decouple the project / tenant / system bucket logic [done]     
  
* [Jira story](https://nextlabs.atlassian.net/browse/SC-2469)
* Why: Today the tenant and project management logic is too tightly coupled. We are referring to tenantId and tenantName for projects as well as tenants due to some legacy design decisions. Projects are implemented as tenants. Any change to tenant or project code breaks the other. 
* The "system bucket" logic was a kludge derived from the project code. This will also break if we don't refactor it now.
* Decouple the design of the SkyDRM project and tenant management logic.
* Update the design to be modular and extensible to cater to new requirements, for example the "system bucket" requirement that came in Jan which required us to reuse the project code for token management.
* Design an abstract base class and interfaces for tenant, project and system bucket with extensibility in mind.   
  
Role management system  
  
* [Jira story](https://nextlabs.atlassian.net/browse/SC-2407)
* Why: 
    + Today our roles (super admin, tenant admin, project admin, API user, normal user) have their logic hard-coded into our RMS code. This is very bad design, is not extensible, is very fragile. 
    + Exceptions like the reclassify privilege, API user, etc., can be managed in a structured way through the role management system. For example, we can have a role such as "tagManager" that allows the person with this role to change tags. The users with this role need not be the tenant admin or project admin but some selected users with this specific privilege. 
* Design a role management system. 
* Create role - action mappings: 
    + A role means a certain allowed set of actions
    + The role - action set mapping should be managed through the super admin console
    + Each operation on RMS needs to check if the action is allowed - the caller's role should be checked against the action set, and we need an OK or NOK before performing the operation.
* Authorisation for REST API calls
    + Why: Today any client can call our REST APIs.
    + Each REST API should have the same level of protection as has been implemented in CC. On the CC they now have per-API authorisation using a "delegation user".  
  
User management backend to support the new UI functionality at the domain and tenant level   
  
* Include the role management functionality in the user management module
* Make the Super-admin, tenant admin, project admin, normal user, API user just roles and not rules implemented in our code.   
  
Enterprise tenant workspace  
  
* This is a per-tenant workspace that belongs to the company
* It is a space for users to upload and share files   
  
Sharing central policy files using transaction workflows  
  
* Support for transaction workflow to share files in projects or to the enterprise tenant workspace  
  
CC 8.7   
  
* Why: 
    + Teamcenter uses CC 8.7 to store and retrieve policies. 
    + Rehau's security policy requires special characters in passwords. CC 8.7 supports it, not CC 8.6.
    + CC 8.7 fixes a privilege escalation issue identified in CC 8.6
    + CC 8.7 has about 150 bugfixes
* If we support CC 8.7 the customer will not need to deploy both CC 8.6 for RMS and CC 8.7 for Teamcenter DCE  
* [Jira story](https://nextlabs.atlassian.net/browse/SC-2479)
  
Rights expire obligation  

* Rights expiry for central policy files. Today rights expiry is supported only for ad-hoc files. 
  
Notification system  
  
* Notifications for more event types -> to be discussed  
  
Distributed locking mechanism  
  
* Today we use a simple DB-based lock
    + It is very heavy
    + We need to maintain the logic to manage the lock
    + We don't support re-entrancy
+ Move to an off-the-shelf, lightweight distributed locking system that supports re-entrancy.

Support other document sources  
  
* Box for business
* Dropbox for business (validation was done, there are some gaps, the users cannot list their dropbox for business files and folders, dropbox personal accounts work)
  
Soft-delete  
  
* "Delete" files to a recycle bin instead of permanently deleting them.  
  
RMS server on Windows 2019 / 2016 docker host  
  
* Support the SkyDRM server, viewer, router, infinispan cache, RabbitMQ and DB installation on Windows server docker hosts. 
  
RMS server auto discovery by clients  
  
* Auto discovery feature: allows the client to automatically discover the details of the RMS server deployed on-premises.
* The user will not need to manually enter the router URL during setup of the client.  
  
Code base update to support Java 8 + features  
  
* Why: Today our codebase uses Java 7. Java 8 - 11 introduces local variable support, functional programming, functional interfaces, collections updates and collection streams. 
* Update the codebase to use Java 8+ features in those specific modules that will benefit, e.g., the project & tenant modules.
  
Dependency updates  
  
* Update our external dependencies to current versions.
  
Audit and logging system  
  
* Admins should be able to filter, view, and query logs
* Export logs to external formats  
  
Azure AD IDP integration  
  
* Why: companies are moving to Azure AD (AAD) for user management. 
* Support Azure AD (AAD) as IDP  
  
AD single sign-on  
  
* SSO with AD servers / GC / ...  
  
Deprecate TLS 1.0 and 1.1  
  
* Why: TLS 1.0 and 1.1 have security issues.
* It is recommended to upgrade to TLS 1.3. (1.2 at least).    
  
FIPS compliance for crypto libraries  
  
* Today we use the BouncyCastle crypto libraries on the server. The version we use is v1.57. The library we use is **not** FIPS 140-2 compliant.  
* If we want to achieve FIPS 140-2 compliance, we will need to start using the [FIPS compatible](https://www.bouncycastle.org/fips-java/) BouncyCastle library that implements the FIPS APIs.  
* This is not a simple replacement of the existing library with the FIPS version. The API and behavioural differences require API and code flow changes to be made to use the FIPS APIs. For example, the initialisation should comply with FIPS specifications, i.e., by calling a `FipsStatus.isReady()` method, and by reading status messages using the `FipsStatus.getStatusMessage()` method.  
* Details are in the document [here](https://www.bouncycastle.org/fips/BCFipsDescription.pdf). Based on the documentation, changes need to be made to :   
    + Startup 
    + Configuration of FIPS approved / non approved modes
    + Module self verification 
    + Setting the default SecureRandom
    + Provider configuration and use with JSSE
    + Configuration and use of the FIPS compliant keystore
* Reference documents:  
    + [BC FIPS Java API](https://www.bouncycastle.org/fips/BCFipsDescription.pdf)
    + [BC FIPS Java API usage and examples](https://www.bouncycastle.org/fips-java/BCFipsIn100.pdf)
  
  
Clean download / upload workflows  
  
* (a) downloading a file re-encrypts using system bucket token, (b) the server allows the user to upload a system bucket protected file into a project  
    
View rights    
  
* This is a new right.  
* It is similar to a right available on Microsoft's Azure RMS.  
* This new right, `RIGHT_VIEWRIGHTSDATA` allows the caller to view the rights or classifications for an NXL file, i.e., that information which is considered sensitive.   
  

  
-----------------------  
  
## Server SDK  

Token management system security implementation [done]   
  
* Why: To align with the security updates being implemented for token management on the server.
* Implement the new APIs that were designed for the enhanced token management module.  
  
Other features [done]    
  
* Changes related to the architecture enhancements   
  


-----------------------    
  
## Feature Gap with RMS 8.X  
  
* Support 3rd business repo: DB for business, OneDrive for business,  SPOL, server provide APIs to return business repositories setting  
* Enforced on file attribute: file type or file extension  
* Capture RMD policy evaluation logs to server, then server send to CC and log in CC DB for Reporter view  
* Support assembly files (asm, CATProduct, iam) from view Locally file and view from Repository(SP, DB)  
* Support obligation, application with “Name” setting in central policy  
* Support DB type: MySQL  
* Support AD type: OpenLDAP  
* Restricted user from access NXL based on location (set IP address in policy)  
* And features that compared with client:  
    + Support add any nxl file to Project
    + Download nxl file from project/MyVault/3rd party repository, file is re-encrypted with system bucket token    
* Support user group when setting policy, compatible with CC 8.7    
  
-----------------------  
    
## Common Requirements from Keng  
  
* Adding support for Edit / Cut / Copy / Extract Content / etc.  
* RMS user authentication – supporting external IdP such as Azure AD,  SAP ERP identity managed by SAP ERP / SAP IdM, or Teamcenter IdP. That means RMS will not store user and authenticate locally, instead the user is managed and authenticated by an external IdP such as AD, AAD, Okta etc. I think this is what we are doing for Rehau?  
* Logging all actions : open, edit, save, save as, download, etc.  
* Reporting and tracking by policy, by document, by user or group of users etc.   
* Improve CAD file format support – viewing with good response time
* Allow NXL or result of the rights protected file (NXL) to be stored anywhere or where the native file located.  
* 100% parity on the functionality Central Policy = Ad-hoc policy
    + expiration on central policy
* Improve logging and reporting of access: document tracking – log when a user is denied access to an NXL file and/or send an alert  
* Consolidate MyDrive/MyVault/MySpace to just MySpace
     + MySpace – for User
     + WorkSpace – for account / tenant
* Bring Your Own Key (BYOK)
* Enhance / Add / Improve Central Policy Support – more attributes – support any attributes enrolled / defined by the Admin instead of the predefined one
* We need to add support for:
    + number of use policy (number of time open/use policy) – can only use 10 times for example
    + expiration policy
    + policy configuration per user profile
    + use the user profile as the 1st use case for central policy
* Upload any NXL file into Project
* User Profile Management and using user profile as attribute for policy
* Authorization Federation support between SkyDRM enterprise edition and SaaS edition and federation of rights between 2 SkyDRM domains or between 2 companies each running SkyDRM Enterprise Edition.
* Support multiple files inside 1 NXL file + Support Folder – protect a folder
* Access denied – allow user to requests access - Access Request Workflow
* Support policy based on “content” such as keyword in the document – content analysis?
* Extend and certify more file type support – especially the CAD file types and SAP VE file type (RH, VDS, 3D PDF), and MathCAD
![clip_image002.gif](https://bitbucket.org/repo/dBgzdj/images/1905284119-clip_image002.gif)
* Policy to support non-User subject – grant access device or application regardless of user
* Location based Policy
* Content analysis for automatic rights protection
  
In-place protection for 3rd party document sources  
  
* [Jira story](https://nextlabs.atlassian.net/browse/SC-2518)  
* Documents originating from 3rd party repositories should be protected and stored in-place without uploading them to MyVault.  
  
Download operation re-encrypts file with system bucket token  
  
* Today the download option uses a token from the project to protect the file which is returned to the caller.  
* In future, any save operation that returns an NXL file to the user should re-encrypt the file with a token from the system bucket.  
  

Management console  
  
* Client management (client accounts, billing / invoicing, etc).
* Client user management  

Scalability  
  
* One management server, multiple RMS servers  
![clip_image004.gif](https://bitbucket.org/repo/dBgzdj/images/2702834930-clip_image004.gif)
* Multiple RMS servers syncing with each other  
![clip_image006.gif](https://bitbucket.org/repo/dBgzdj/images/1228502569-clip_image006.gif)

Simplify the file name behaviour for projects  
  
* Today we have a file auto renaming system that adds the date / time stamp at the end of the file names. This is confusing to users. 
* Change this behaviour to retain the original file name. The user can be shown an option to date-stamp the file if needed, but the default behaviour should be to retain the original file name (e.g., abc.xls --> abc.xls.nxl)
  

File recovery  
  
* If the DB that stores the keymaterial used to generate tokens has crashed, we should be able to recover files.   
  
  
  
-----------------------  
  
## Operational requirements  
  
Documentation  
  
* Updates to documentation for each release as required. 
* The admin guide, release notes, and Server SDK documentation should be updated.  
  
Setup scripts cleanup  
  
* Clean up the setup scripts to account for re-installation of SkyDRM. It deletes some lines from the docker compose files if run twice. There is no easy way to add docker configurations with the setup script today.  
  
Installer  
  
* The installer needs to be greatly simplified. Minimise the number of inputs required from the user before SkyDRM can launch.   
* Move most of the configuration details out of the setup script. Allow SkyDRM to be installed with only the following details provided by the installer:  
    + super admin user details   
    + database server details  
* SkyDRM should be able to launch in a "setup" mode where the super admin can login and enter the rest of the details on the management console.  
  
Source code auto checks  
  
* PMD: static code analyser. Update this to version 6.12.0. PMD is used to find common programming flaws like unused variables, empty catch blocks, unnecessary object creation, and so on. 
* Checkstyle: update this to the current version 8.18. Used to check for class design problems, method design problems, code layout and formatting issues. 
  
-----------------------  
  
## Customer Requests  
AD Global catalog support [Rehau: on hold, they will try with AD-FS]    
  
* [Jira story](https://nextlabs.atlassian.net/browse/SC-2339)
* Design limitation today. We have not designed the server to talk to a Global Catalog DC. We force the user to choose the domain. 
* What needs to be done: 
    + Test: send requests to the GC without the domain, check if it works. 
    + Update the (a) setup scripts, (b) Login page
    + Validate  
  
Sharepoint HTTPS support [Rehau: this is a key requirement][done, build 218]       
  
* Connect to sharepoint on premises document source via HTTPS. We only support HTTP connections today.
* [Jira story](https://nextlabs.atlassian.net/browse/SC-2475)  
  
Using hostnames to communicate between RMS and router [Rehau]   
  
* [Jira story](https://nextlabs.atlassian.net/browse/SC-2476)
  
Miscellaneous features [Rehau]  
  
* Support to delete or deactivate project
* Assign AD group user as a super admin, now we support multiple users as super-admin through a comma separated list, but we do not support user group  
* Support project admin to assign people as project admin too. Today only the tenant admin can assign project admins.  

-----------------------  
  
## TeamCenter Requirement Gap  
  
* User attributes from Teamcenter should be available in SkyDRM   
    + User attributes assigned in the teamcenter IDP are required in SkyDRM  
    + Important attributes are users' group memberships, project memberships, role
    + This may require a generic solution, e.g., a small plugin system that allows Jim's team to write a plugin to connect with teamcenter IDP  
* No reporter tool or no UI for activity logs – several issues  
    + Some logs are in the CC database, majority logs are in the SkyDRM database  
    + No Reporting tool unlike CC Reporter to browse SkyDRM logs  
    + Teamcenter DRM uses only “System Bucket” and there is no UI to view logs for System bucket  
*  Watermark  
    + This is available only at the web app for ad-hoc sharing. Needs to be available for central policy files.    
* IP or host based policy: Need to be able to write policy based on IP address or host.  
* Policy enforcement based on location.  
* Time-based expiry of policy. This is already covered in another section.  
* Cut / copy / paste rights. This is already covered in another section.  
  
![tc-gap-1.png](https://bitbucket.org/repo/dBgzdj/images/3472681503-tc-gap-1.png)  
   
![tc-gap-2.png](https://bitbucket.org/repo/dBgzdj/images/4199716923-tc-gap-2.png)  
  
  
-----------------------  
  
## Technical and Design Requirements  
  
* Allow developers to build plugins for SkyDRM  
    + The first requirement is to allow SkyDRM to consume user attributes from the Teamcenter IDP.  
    + The workflow is:   
        + User authenticates to SkyDRM with a pre-configured IDP
        + RMS gets attributes from another (different) IDP like teamcenter
        + These attributes are used for policy evaluation 
    + Points to resolve:  
        + How to map users across IDPs? For example, here we will authenticate the SkyDRM user against IDP-1 (e.g., a pre-configured AD server), then SkyDRM gets the attributes for this user from another IDP (e.g., Teamcenter's IDP).  
        + Maybe this can also be covered in the plugin. The plugin will need to perform the user mapping internally, then query the teamcenter IDP to get the attributes. Then the workflow will be : 
            + The user logs in to the RAC  
            + The user authenticates with the SkyDRM desktop application  
            + RMS calls the plugin with the userID that SkyDRM understands  
            + The plugin maps the SkyDRM userID to the userID used by the other IDP which stores the attributes (e.g., teamcenter IDP)  
            + The plugin retrieves the user attributes and returns it to RMS  
            + RMS can now use these attributes for policy evaluations for this user  
  
Requirements from the call with Keng, SE and customer-facing teams on [14 May 2019] :  
  
* SkyDRM will be using standard JPC and therefore activity logs will be pushed to Control Center database. Subsequently we will be able to use Control Center Reporter for audit reports.  
* SkyDRM is going to adopt the Control Center policy model. This will address several issues:  
    + Policies can be managed centrally from Control Center, even though for simpler SkyDRM deployment, SkyDRM related policies can be still managed from SkyDRM console.   
    + We will be able to use attributes enrolled at the Control Center in the SkyDRM policies. That also mean, we will be able to use IP address or host as one of the controls after CC 8.7 support.  
* Watermark & Time based expiry enabling through central policy will be added to the roadmap.  
* Watermark to be viewed only in NextLabs Web and Desktop Viewer – not in the native app (with RMX). This is by design as of now unless we see strong need to change in future.
  
Refer to the screenshot below.    

![requirements-01.png](https://bitbucket.org/repo/dBgzdj/images/3073889921-requirements-01.png)
    
  
Notes on this approach to deploying policies on CC:   
  
* Re-think the way we are creating and deploying policy models, users, policies, resource components. The goal is to (a) understand the exact recommendations from CC on how to create and manage these components, for example we are using advanced conditions for users and applications today, (b) identify the gap, and (c) propose, design and implement this approach that CC recommends.  
* [Action] Proposed workshop with Alan Morgan, Dhanraj, CC team face to face in SG for 1 week.     

  
-----------------------  
  
## References  
  
* Detailed technical discussions on open points are on a separate wiki page [here](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/Technical/Behaviours%20and%20Requirements%20for%20Clarification)