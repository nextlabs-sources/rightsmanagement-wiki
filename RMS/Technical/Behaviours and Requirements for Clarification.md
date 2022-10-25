# Open Points    
  
This page is to document the open points, the discussions around the various issues and the decisions made with regards to technical design and user experience.  
  
-----------------------  
  
## User Experience  
   
### Delete file / folder permissions  
  
* Who should be able to delete files or folders in projects? 
    + Today the project *owner* can delete them, not even other project *admins*
    + File owners cannot delete anything.
* Should the delete right be available to those users with `ADMIN` rights? Or should this be a new right? 
* This presents a weird user experience, where I as a normal user can create a folder, realise it's a mistake and cannot delete it. This may be an issue if the folder has files in it, but it is still weird for empty folders I have created.  
  
-----------------------  
  
## System Behaviour  
  
### Domain, Tenant  
  
* On both SaaS and enterprise on-prem deployments, we introduce the concept of a domain. 
* The concept came about during discussions on user management for Saas multi-tenant business accounts. 
* A domain is equivalent to one instance of SkyDRM, and it comprises information managed across its sub-tenants. 
* Requirements for the domain are to be defined. However, for now, here are the high level requirements: 
    + Users on SaaS business accounts will be registered at the SkyDRM.com domain. 
    + SkyDRM should provide a mechanism where subsets of domain users are allowed to access different tenants.
    + In other words, user management for SkyDRM business accounts (or enterprise accounts) is done at the domain level, not at the tenant level. 
    + This provides options for the future where users from one tenant can be allowed access to other tenants. 
    + There was some discussion on policy deployment at the domain level, but this topic was not concluded. 
    
### Ad-Hoc files  
  
* The behaviour of ad-hoc files (more specifically, who can get the token and when - triggered by the issue of validity) needs to be defined. 
* More details in its own page [here](Ad-Hoc Files)  
  
### Admin Rights  
  
* What is the scope / requirement for admin rights? 
    + Today the scope is limited to the classification change feature. 
    + Should this also apply to delete file / delete folders? 
    + What else should be covered under admin rights? 
* Separate page [here](Admin Rights)  
    
###  View Rights Permissions  
  
* This is a new requirement that came up on 2019-04-04.  
* Today any member of a project can view file classifications even if they have no rights to perform any action on the resource.  
* In [Microsoft AIP ](https://docs.microsoft.com/en-us/azure/information-protection/) there is a separate right (**`VIEWRIGHTSDATA`**) that is checked before showing file rights to a user. 
![aad-1 - viewrights.png](https://bitbucket.org/repo/dBgzdj/images/3066746408-aad-1%20-%20viewrights.png)  

* The proposal is to follow this behaviour for SkyDRM. This is the requirement for the *long term*.
* For the *short term*, we have agreed to show file classifications to anyone with at least **view rights**. 
   
### Two categories of information about resources  
  
* On the call with Keng on 2019-04-04 it was agreed that we would have two categories of information about the files  
    + Basic file information: for example, file name, file size, last modified, creation date, and so on. This information should be viewable by anyone.  
    + More sensitive information: this is information that is security sensitive, e.g., file classifications/tags, rights, and should not be accessible by just anyone. 
* [*To be done*] Need to define exactly when and how this information will be displayed. The requirements are pending. This applies across all client - web, desktop and mobile. 


### Download workflow  
  
* Downloading a file should re-encrypt the file with a token from the system bucket.
* This will allow the file to function as a system bucket protected file.
* The file will no longer be protected with a token from the project from which it was downloaded. 
  
### Add / Upload file  
  
* Adding a file to a project will now expect a file protected with the system bucket token, or a token from the project itself (the second part is to be confirmed).  
* Adding a file to the project will re-encrypt the file using a token from the project if the file was protected using a system bucket token. The file will not be re-encrypted if it was already protected with a token from this project.
    
-----------------------  
  
# Closed Topics  
  
## API User  
  
* Documentation [here](API User)  
  
### Viewer -> Download File  
  
* The current experience with downloading a file through the viewer is cumbersome. The web app displays a warning to the user, allowing the user to accept and proceed, or cancel the operation.  
* It was agreed on 2019-03-19 that this workflow will change. 
* Clicking on a `DOWNLOAD` link will simply start the download. The user is presented with a file save dialog box by the OS. The user can choose to cancel it here.