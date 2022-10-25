# SkyDRM Admin Roles and Scope  
  
SkyDRM will have three types of administrators  

* Super-admin
* Tenant admin
* Project admin
  
There are differences between the roles and scope in the SaaS version and the enterprise version. 
  

The roles and scope are described in this page.

  
## Super Admin
### Functions

* Create and manage tenants
* Assign and manage tenant admins
* Create user and assign attributes for SkyDRM IDP
* Configure IDPs
* User attribute mapping
* Assign project admin (project created under public tenant)
* Document classification
* Manage service providers
* Policy administration

  
## Tenant Admin

* Configure IDPs
* User attribute mapping
* Assign project admin (project created under this tenant)
* Document classification
* Manage/Enable Service Providers
* Policy administration
* Note
    * If some tenant settings are missing, will inherit from system settings
  
## Project Admin

* Create project
* Document classification for project
* Create project policies
* Note
    * Now only project owner can remove members, anyone can invite members
    * Super admin and tenant admin also have project admin permissions

### Note

On Prem:  

System admin will also be the tenant admin (there is only one login tenant).
The permission for system admin will be the same as tenant admin in SAAS.
other login tenant cannot be created.
  

Private Cloud:  

Some customers may want to deploy in their private cloud and enable multi-tenancy feature, then the hierarchy/roles/permissions will be the same with public cloud