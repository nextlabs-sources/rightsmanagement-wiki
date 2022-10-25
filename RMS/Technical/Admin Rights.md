# Admin Rights
  
## Background  
  
The idea originated when designing the RECLASSIFY feature. This is a security sensitive operation. It was decided in mid March 2019 on calls with Keng that the ability to change classification should not be driven by policies, but that it should be a new role. This evolved into what is now called "Admin rights".  
  
## Scope  
  
Today the scope of Admin rights is limited to the ability to change the classification of files. There may be other uses in future. The full scope and requirement is summarised.  
  
* Admin rights today only applies to central policy files.  
* Someone with admin rights is allowed to change the classifications of files.  
* Inside a project, the people with admin rights are:  
    + ~~The creator of the file, i.e., the person who first created the NXL protected file. ~~
    + All the administrators of this project. This is not just the creator of the project but any project admin who is a member of this project. 
    + Tenant admins.  
    + The API users
* In the system bucket, the following people have admin rights:   
    + The tenant admin 
    + ~~The creator of the file~~
    + The API users
  
##  Update: 2019-03-28  
  
* Discussion with Keng, approved on 2019-03-28  
* Admin rights can apply to **both ad-hoc as well as central policy** protected files
* Ad-hoc (under discussion, not final):
    + If a user creates an ad-hoc protected file locally, he gets admin rights
    + By virtue of the creator getting admin rights, the creator can change rights
    + If this file is added to a project, the creator no longer has admin rights. Only the project admin and tenant admin will now have admin rights. 
* Central policy - admin rights granted to:
    + In a Project
        + Project admins
        + Tenant admins (should we allow this? what if the tenant admin is not a member of the project?)
    + In the System bucket
        + Tenant admins
* API users get all rights including admin rights    
* Today the admin right only allows change classification.

  
## Update: 2019-03-29  
  
* Discussion with Keng, approved on 2019-03-29  
* There are two possible scenarios:   
    + Implicit rights: Tenant admin has all the rights of a project admin or user
    + Explicit rights: Tenant admin should be granted explicit rights, e.g., project admin or user
* We go with the second scenario. The tenant admin should not have any special rights in a project by virtue of his being a tenant admin.
* Four sub-scenarios: 
    + S1: tenant admin has no project membership: tenant admin does **not** have admin rights
    + S2: tenant admin is a member of the project: tenant admin does **not** have admin rights
    + S3: tenant admin is an admin of a project: this is the only scenario where the tenant admin **has** admin rights to change classification.
    + S4: system bucket: tenant admin **has** admin rights to change classification.

The implication on the admin rights :    
  
    
| Project/System Bucket        | User Type      | Admin rights?           | Note  |
|-------------|----------------|------------------------------------------|--|  
| Projects    |  project admin | **has** admin rights                     | Project admin needs to be a member of the project  |
| Projects    |  tenant admin  | no check needed, this is irrelevant now, tenant admin has **no** special rights, **no** admin rights unless he is also a project admin  |  |
| Projects    |  normal user   | **does not** have admin rights           |  |
| Projects    |  super admin   | **does not** have admin rights           |  |
| Projects    |  API user      | **has** admin rights                     | API user does not have to be a member of the project  |
| System bucket | tenant admin | **has** admin rights                     |  |
| System bucket | normal user  | **does not** have admin rights           |  |
| System bucket |  super admin   | **does not** have admin rights         |  |
| System bucket | API user     | **has** admin rights                     |  |
   
  

## Behaviour
    
Admin rights allow authorised users to perform classification changes without needing access to the token used to protect the file. This is a key point, since there may be no policy in place that allows access to users who need to change classification. However, if they have admin rights, they can still perform a classification change without obtaining the token used to protect the file.  
  
Note that policy checks do not apply to API users due to the fact that API users are explicitly created by the super-admin and are considered trusted users that invoke the Server SDK APIs.  
  
Behaviours such as the one below are by design:
  
* User A protect a Policy file in server side
* then User A (file owner) and User B (other project member)  all download the
file from server side
* User A (file owner) call update function :update tags with User A' APPId and
APPkey but with the file downloaded by User A (file owner) : this works
* User A (file owner) call update function: update tags with User A' APPId and
APPkey but with the file owned by User B (other Project member) : this fails because only user B should have the admin right to update classifications for this file.