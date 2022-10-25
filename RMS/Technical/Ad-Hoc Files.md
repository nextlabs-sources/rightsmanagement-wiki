# Ad-Hoc Rights for Files
  
## Ad-Hoc rights for files in projects  
  
This discussion was triggered by two issues raised by Michelle's team:   
http://bugs.cn.nextlabs.com/show_bug.cgi?id=54096  
http://bugs.cn.nextlabs.com/show_bug.cgi?id=54034  

This point was discussed on the call on 2019-04-04 with Keng and approved. The requirement is as follows:  
  
* When a project member adds a file to a project, giving it ad-hoc rights, he relinquishes ownership of the file once it goes into the project, i.e., he is no longer considered an "owner" of the file. This is different than the case where he adds the file to his MyVault. In that workflow, this user is still considered the owner of the file.   
* Once the file is inside the project, protected with ad-hoc rights, the original file owner is subject to the same constraints as any other user with respect to validity. 
* For example, if the validity period is from 2019-01-01 to 2019-02-01 and the original file owner (or any other user) tries to access the file after 2019-02-01, they should not get the token.
* This constraint applies to files with a future validity date. If the file validity is from 2020-01-01 to 2020-02-01 and I as the original owner try to access the file today on 2019-04-04, I should have no access.  
* Note: Today in the RMD, the owner of the ad-hoc file in a project continues to have access to the token outside the validity period.
  
## Ad-Hoc rights for files in MyVault  
  
The files added to MyVault are treated differently. The user who uploaded the file is still considered to be the owner and admin of the file.   
  
* This means the owner can obtain a token and have all rights on the file even outside the validity period.   
  
## Ad-Hoc Rights for files protected with system bucket tokens  
  
* Earlier it was agreed that the file creator would have all the rights, i.e., the behaviour for ad-hoc files from the system bucket would be similar to the behaviour of ad-hoc files in MyVault. In other words, the file creator would be able to perform actions with the file that are not explicitly defined in the ad-hoc rights, e.g., share the file. 
* Now this is not so clear. If only the tenant admin has ADMIN rights, how does this affect ad-hoc files? 
     + Does the file creator no longer have access to the file outside of the rights specified in the .FilePolicy section? 
     + Does he "lose" ownership of the file, so that the behaviour is identical to ad-hoc files in a project? 
  
## Points to clarify  
  
* On the call on 2019-04-04 Keng briefly spoke about admin rights in connection to ad-hoc files. For example, I add a file to a project, with only view rights. This implies I lose other rights to the file, and only the admin has full rights on the file. 
* The exact details of the scope of the admin rights in the context of ad-hoc files in projects and system bucket need to be clearly defined. This scope is not clear today.
* How should validity expiry behave for system bucket files? I suggest it should behave the same way as for projects, since files in the system bucket are not considered "my" files but company property, i.e., the rules that apply to projects should also apply to the system bucket.

[Raymond]
For adhoc file, even in project, we always said file creator is the owner, and will have the full rights. So even it is expired, as the file owner, creator still can have access to the file. This is different as we described as above. Need more discussion.