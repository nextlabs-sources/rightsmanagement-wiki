## Issue description:
Right now the concepts of 'Tenant' and 'Project' are highly coupled. When a project is created, a tenant is implicitly created to manage project tokens. Same for the tenant creation flow, when a tenant is created, a project is also implicitly created. 
These dependencies were brought by historical design issues. Tenant has to be created in order to manage tokens for files. This design incurs many problems:

1. Coupling among different modules. -- Tenant and project code are coupled.
2. Difficult for integration, it's difficult to understand the relationship between tenant and project when third party system integrate with SkyDRM.
3. Hard to scale. When we start to think about implementing sub-tenant features, it will be messy to implement on top of the current design, because project is also considered as sub-tenant under root level tenant now.

## Propose to:

1. Update architecture to support sub-tenants.
2. Break creating tenant as a pre-condition to manage tokens.
3. Introduce a new way to manage token groups and memberships.

The current flow of creating project is: create keystore for tenant -> create tenant -> create project.

After refactoring, the flow would be: create keystore -> create project

## Impacted scope of changes:

### Schema changes:
1. membership
	* use type to distinguish member of tenant/member of project, change the way we manage membership name, change from memberId@tenantName to memberId@token_group_name. Drop NOT NULL constraint for project_id and tenant_id because user can either be a member of a project or a tenant. If user member of project then tenant becomes NULL
2. tenant_classification
	* rename to classification, add type and project_id column, so classification table can be used for both tenant and project.
3. policy_component
	* add type, project_id so policy_component can also be used for both tenant and project.
4. nxl_metadata
	* rename tenant_id column to token_group_name
5. key_store_entry
	* rename tenant_name column to token_group_name, key_store_entry will be used as foreign keys in tenant and project table.
6. tenant
	* add parent_tenant column to support sub-tenant hierarchy, add key_store_entry_id as foreign key to key_store_entry
7. project
	* currently there's a 'tenant_id' column, which is used to reference the tenant created for this project, can rename it to parent_tenant_id so this can be used to identify under which tenant the project is created.
	* Change keystore(Blob type) to key_store_entry_id(a foreign key to key_store_entry)

### Token group management:

We want to manage tokens by groups. There are three types of groups now: tenant, project and system bucket.

The name for token group can be defined as:

| tenant     |          project                 | system bucket     | 
|------------|----------------------------------|-------------------|
| tenantName | tenantName_projectName_ownerId | tenantName_system | 

Token group name needs to be unique across the domain.

Membership name will be changed to memberId@token_group_name, for static members the format is **m**Id@token_group_name, for dynamic members the format is **u**Id@token_group_name




### API changes:
 
* Router APIs: The **definition/response** of the lookup APIs in router remain the same, its just the endpoint has changed:


```
#!python

Before: https://dockerhost:8443/router/rs/q/defaultTenant
        https://dockerhost:8443/router/rs/q/tenant/{tenantName}
```


Empty tenantName (not token_group_name) will continue to return defaultTenant.


```
#!python

Now: https://dockerhost:8443/router/rs/q/defaultTenant
     https://dockerhost:8443/router/rs/q/tokenGroupName/{token_group_name}
```



The token_group_name would be available in the membership record of nxl file (after @), and the defaultTenant remains the same.

* [Login APIs](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Login%20REST%20API).  
  
    * Return memberships for tenant/project/system bucket, and distinguish different memberships by membership type. tenantId, projectId are not always returned depends on membership type.
    * The [basic / google / facebook login](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Login%20REST%20API#markdown-header-basic-login) API response will change. We will now include the token group in the response.  
```
Current membership list
"memberships":[
         {
            "id":"m17@skydrm.com",
            "type":0,
            "tenantId":"21b06c79-baab-419d-8197-bad3ce3f4476",
            "projectId":1
         },
         {
            "id":"m28@t-5948bf18ba1749e8a6a99b66edba429d",
            "type":0,
            "tenantId":"8360d053-9f7d-4bcc-a265-dab4a31a92fd",
            "projectId":9
         }
      ],

New format
"memberships":[
         {  
            "id":"m836@tenantName",
            "type":0,
            "tenantId":"abd298ff-f3ab-4f1a-a856-e0cd7d4166f7",
            "tokenGroupName": "tokenGroupOne",
         },
         {  
            "id":"user22@tenantName_projectNova_88",
            "type":1,
            "tokenGroupName": "tokenGroupTwo",
            "projectId":88
         },
         {  
            "id":"user22@tenantName_system",
            "type":2,
            "tokenGroupName": "tokenGroupThree",
         }
      ],
```
* [Membership management](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Membership%20REST%20API)
    * membershipId is passed in the request, need add logic to check membership for tenant/project/system bucket (no change in response)
    * The membershipId should now use the new format described above, i.e., the token group (`memberId@token_group_name`)
  
* [Tenant management](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Tenant%20REST%20API#markdown-header-get-project-admin)  
    * We will split the "get project admin" API into two APIs: 
        * get tenant admin: this will return only the list of tenant admins. We will create a new endpoint: **URL:** /rms/rs/tenant/{tenant_id}/tenantAdmin
        * get project admin: this will return the list of project admins. We will continue to use the same endpoint: **URL:** /rms/rs/tenant/{tenant_id}/projectAdmin  
    * `SYSTEM_DEFAULT_PROJECT_TENANTID` in [Get Tenant Preferences API](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Tenant%20REST%20API#markdown-header-get-tenant-preferences) will be changed to `SYSTEM_BUCKET_NAME`
  

* [Token management](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Token%20REST%20API)
	* in retrieve token API, change the way we check membership, change tenant parameter to 'token_group' (need change tenant parameter in request to token_group, no change in response)
    
* [User management](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/User%20REST%20API)  
  
    + Retrieve [full profile](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/User%20REST%20API#markdown-header-retrieve-full-profile), [list memberships](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/User%20REST%20API#markdown-header-list-memberships) will be updated. The response will now contain the token group name for the projects.  
    + The [register new user](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/User%20REST%20API#markdown-header-register-a-new-user) API will now return the login tenant as well as the default tenant in the response array.   
  
* [Heartbeat API](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Heartbeat%20REST%20API)  
  
    +  You will now be able to send a heartbeat request for tenants and projects. 
  

* [Classification management](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Classification%20REST%20API)
     + should be able to manage classifications for both tenant and project 
     + add new APIs to manage project classifications
  
* Policy management
    + **Note:** These functions are not consumed by the client SDK or apps. These are internal to the RMS server. Changes here will not impact the client software. 
    + should be able to manage policy for both tenant and project, add new APIs to manage project policies
    + Naming conversion
        + Tag: tokenGroupName `6ed9474a-7fad-4f1d-bde3-76137743ad0a_aasda_1`, and `SkyDRM`
        + Policy: tokenGroupName + space + project/tenant namme `6ed9474a-7fad-4f1d-bde3-76137743ad0a_aasda_1 aaa`
        + Policy model: "t_" + tokenGroupName `t_6ed9474a-7fad-4f1d-bde3-76137743ad0a_aasda_1`
        + Resource component: policy name + '_' + tokenGroupName `aaa_6ed9474a-7fad-4f1d-bde3-76137743ad0a_aasda_1`
        + Delegate user name: "t_" + tokenGroupName `t_6ed9474a-7fad-4f1d-bde3-76137743ad0a_aasda_1`

  
* [Project APIs](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API)
	* Get Membership & Get Member Details, update to remove tenantId, change membership type to 1
	* List Projects & Get Project Metadata, replace tenantId and tenantName with parentTenantId and parentTenantname
  
* [Remote viewer](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Remote%20Viewer%20REST%20API)
  
    + Upload and view local file API may be changed. We are considering whether to remove the tenantId and tenantName parameters.  
```
{
   "parameters":{
      "userName":"toor@gmail.com",
-- considering stripping out the two lines below --
      "tenantId":"f3133-2a13-4234-b21e-8522aee08fd1",
      "tenantName":"nextlabs.com",
-- end of change --
      "fileName":"abc.doc",
      "offset":"-480",
      "operations": 2
   }
}
```  
  
### Membership types  
  
The membership `type` field is interpreted as follows:  
  
| type | interpretation |
|------|----------------|
|  0   | tenant         |
|  1   | project        |
|  2   | system bucket  |

  

### SDK changes:

Right now to encrypt/decrypt files under project, we need call 'get project metadata' API and get tenantName for the project first.

1. The flow can be simplified as: pass tenantName and projectName when encrypt/decrypt files at one shot(tenantName is not the tenantName for project anymore, but under which tenant the project is created)
2. encrypt/decrypt methods can also be overloaded to encrypt/decrypt files for system bucket

### Other changes:
1. Change the way to get domain policies (the way we find parentTenant of project need update).  
  
### Supporting slides.  
Slides are on the network share:   
```
\\semakau\share\Teams\Secure Collaboration\Presentations\Architecture
```  
  
## Reference documents  
  
* [Deployment migration proposal](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/Technical/SkyDRM%20architecture%20migration)