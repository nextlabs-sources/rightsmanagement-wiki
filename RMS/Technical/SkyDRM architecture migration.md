# SkyDRM Deployment Migration

  
  
# Migration Process  
  
This section describes the deployment migration process. The purpose of this process is to migration an existing deployment (build <= 212) to the product with the new architecture. Broadly, we will migrate the SkyDRM DB and what RMS deploys on CC.  
    
The migration process is:  
  
* Take the system offline for all users. The administrator needs to do this manually today since we don't have a "maintenance mode" for SkyDRM today.  
* Migration trigger point:
    + Add a new table which will store the build and version details of the system. If the version details is not present or its value is less then current version, migration process will start.
* SkyDRM migration:  
    + Flyway does the DB updates
    + Our java code handles the data migration for the tenant, project, and system bucket
    + Add a new column Status to tenant table with a default ACTIVE value. This status will be INACTIVE for project tenants. The benefit of doing this to remove the restriction on tenant names.
* Control center migration:  
    + Create new users based on token groups. These are per-tenant or per-project-tenant users.  
    + Configure RMS to start using the new users that were created in the step above.  
    + Create and deploy new policy models.   
    + Create and deploy new policies. Resource components created part of creating policy
    + Delete old policies.  
    + Delete old resource components.  
    + Delete old policy models.  
    + Delete old users.  
  
## Notes related to CC:  
  
* ~~For now we will use the schema version to check if we need to migrate the system.~~
* The way we deploy policies today: 
    + Today, we create one policy model per project or per tenant. 
    + Action components are created once and reused across the whole domain and all tenants. 
    + Resource components are created per policy. They are created by RMS during the policy creation process. 
    + Resource components are not reused across policies today. 
* Today, while creating and deploying a policy model, we create one user in CC which is used by the webapp while listing, editing and viewing policies. The sample name format is shown in the screenshot below. This will change to the token group name for the new architecture. The format is `token_group_name ADMIN` instead of the old format `T_TENANTID_ADMIN`.  
  
![01.png](https://bitbucket.org/repo/dBgzdj/images/285466115-01.png)  
  
The `Username` is used in RMS while making requests.  
  
![02.png](https://bitbucket.org/repo/dBgzdj/images/1114166533-02.png)  
  
While creating a project, a policy model is created in CC.  
  
![03.png](https://bitbucket.org/repo/dBgzdj/images/2725445241-03.png)  
  
The short name of the policy model is used as a reference.  
  
![04.png](https://bitbucket.org/repo/dBgzdj/images/3928277239-04.png)  
  
The policy model contains any document classifications defined in the project on RMS.  
  
![05.png](https://bitbucket.org/repo/dBgzdj/images/3847470766-05.png)  
  
![10.png](https://bitbucket.org/repo/dBgzdj/images/4176275413-10.png)  
  
The project admin can create a policy in RMS.  
  
![11.png](https://bitbucket.org/repo/dBgzdj/images/3776834699-11.png)  
  
The conditions are defined in the policy. These are shown below. Later screenshots show how these map to the resource components used by the policy deployed on CC.   
  
![20.png](https://bitbucket.org/repo/dBgzdj/images/585339467-20.png)  
  
![21.png](https://bitbucket.org/repo/dBgzdj/images/2925420476-21.png)  
  
This policy defined on RMS is deployed as a policy in CC.  t-<id> is the tenant name here
  
![30.png](https://bitbucket.org/repo/dBgzdj/images/3542153420-30.png)  
  
The policy name and tag matches the project that created it.  
  
![31.png](https://bitbucket.org/repo/dBgzdj/images/1681461894-31.png)  
  
While this policy is created, a resource component is created, which is referred to in the policy.  
  
![35.png](https://bitbucket.org/repo/dBgzdj/images/1431621488-35.png)  
  
You can list the resource components in CC.  
  
![40.png](https://bitbucket.org/repo/dBgzdj/images/1825162645-40.png)  
  
The resource type refers to the policy model created.  
  
![41.png](https://bitbucket.org/repo/dBgzdj/images/924504577-41.png)  
  
The conditions in the resource component correspond to the conditions defined in the SkyDRM policy.  
  
![42.png](https://bitbucket.org/repo/dBgzdj/images/643427485-42.png)  
  
  
## CC Limitations  
  
* The policy model only has one "User" resource type in CC. There is no separation per tenant or per project. Anything defined here applies across the entire SkyDRM domain.  
  
![50.png](https://bitbucket.org/repo/dBgzdj/images/3681895668-50.png)  
  
The "User" resource type can be used to specify user attributes.  
  
![51.png](https://bitbucket.org/repo/dBgzdj/images/3132222772-51.png)  
  
These user attributes can be used if you want to create a new Subject (type = User) component, as part of the conditions you want to define.  
  
![52.png](https://bitbucket.org/repo/dBgzdj/images/1400314619-52.png)  
  
However these will now apply across all tenants across the domain. Separation across multiple tenants will not be possible, and this solution will not work for the SaaS deployments.    
  
More details are available on the [page dedicated](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/Technical/Control%20Center%20Usage) to CC related investigations. 
  
## Clarifications related to CC  
  
* We have one delegation policy per deployment.  
* Today we use one policy model per tenant or per project tenant to store attributes. Check how we can create and use one policy model for the whole tenant or for the whole domain. How can this be done? How can this be reused across all projects?   
* SkyDRM does not use Subject/User components because of the issues described above.
  
  
## Reference documents  
  
* [New architecture](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/Technical/SkyDRM%20architecture%20update%20proposal)