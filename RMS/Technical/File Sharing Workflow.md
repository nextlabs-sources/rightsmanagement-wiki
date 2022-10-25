# Current Sharing Workflow

## Workflow 1

### Assumptions
1. This is an on-prem deployment.
2. SkyDRM is configured to authenticate users with the corporate AD server.
3. The user attributes are managed on the AD server by the IT department.
4. Smart classifier is not used to batch protect files in-place.
5. Project admins create projects, members upload files to projects.
6. People outside the company who need to access files will be enrolled in the corporate AD server with appropriate attributes (e.g., the current Bajaj deployment).
  
### Sharing workflow
1. The project admin creates a new project specifically for sharing files with a third party.
2. The project admin selects tags for the project, or manually invites members through email. 
3. Access control to the project can be through ABAC or member invites. 
4. If the admin uses ABAC, it is his responsibility to ensure that the appropriate attributes and tags are managed properly. 
5. Users from within the company with access to this new project would now upload files into it. 
6. Users from the vendor company will be able to access these files. 
7. Project policies could be created to restrict rights to the vendor, e.g., deny "share" or "download", based on their AD attributes.

### Limitations
1. The file owners need to have unprotected copies of the original files somewhere.
2. Files and folders can't be shared directly from existing projects. 
  
## Workflow 2
Similar to workflow 1. External users are not on the corporate AD. Their accounts are managed on the SkyDRM IDP. The sharing workflow is similar to workflow 1 but since the SkyDRM IDP does not manage attributes, ABAC access to projects will not work, and external users need to be enrolled individually into the project. 

### Limitations
1. No attributes for external users, they need to be enrolled individually. 
2. Does not scale.
  

# Proposed Sharing Workflow
## Requirements
1. Go to a project, select a bunch of files, or folders from a project, right click and share. (need to refine this).
2. Users should not be required to have the original unprotected files. 
3. Sharing should work for files protected using smart classifier, that are not physically inside a SkyDRM project.
  
## Technical and operational issues to resolve
1. NXL files belong to a project tenant today. The [Retrieve decryption token](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Token%20REST%20API#markdown-header-retrieve-decryption-token) API needs the tenant, owner and DUID in the REST API call. Simply copying the file into a new project tenant will not work since the tenant would have changed.
2. Ideally, we should be able to share files without making copies of the original files. If we need to make copies, sharing a file with 10 vendors will make 9 copies which can get out of sync very soon. Think of something like the Unix symlink. A file can have any number of symlinks or hard links to it, but they all point to the same physical bunch of bytes on disk. A change to the file will be seen by everyone accessing it through any of the symlinks or hard links. Can we create a sharing workflow where the "sharing project" has symlinks to the original NXL files instead of having to make a copy of the file?
3. What technical changes are needed to support these flows? 
    * NXL file format / header (No need to change nxl file format)
    * schema change: add two new tables **project_sharing_transaction(** id, duid, sharing_user_id, creation_time, status, comments **)** and **project_sharing_recipient(** duid, project_id, transaction_id, last_modified, status **)**

    * Backend:
      TokenMgmt, check project sharing transaction before granting token
      Project sharing management
    * Frontend:
      New UI to show/manage shared project files

4. Problem: 
    * Who can revoke/delete sharing transaction? file owner/the person who shared the file/project admins
    * What pre-requisites do I need to share a file? sharing right is must, do I need to be member of target project?
    * Do we allow all projects to be target shared projects? Or should we select a few projects in management console which can be shared with?
    * What policy do we need to use for the shared files? the policy in original project or in new shared project?