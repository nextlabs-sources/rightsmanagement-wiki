# API User  
  
## Background  
  
The "API User" role originated due to the earlier complexity of generating valid credentials to use the server SDK. In the past, a shell script had to be run to create and register an API user. Now the super admin console allows the admin to assign an "API User" role to a user.  
  
## Scope and Description  
  
* The API user is a privileged role. 
* Only the super-admin can assign API user rights to a user. 
* The API user role is valid across all tenants in the domain. 
* The API user obtains an appId and appKey from the super admin which are needed to use the server SDK.
* Using the appId and appKey the API user can perform all the operations supported by the Server SDK. This includes listing and creating projects, encrypting and decrypting files on different tenants, partial decryption, and update tags. 
* Policies deployed at the domain, tenant or project level do not apply to API users.
* This means that the API user is allowed to make calls such as change classification without the API user being a member of the project due to the privileges provided by this role.