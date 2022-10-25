# Classification Change Workflow
  
The caller needs to have admin rights to be able to change the classification of files protected with central policies. The workflows are as follows:  
  
1. System bucket files: These are files that are stored and managed outside SkyDRM. There is no clone of these files stored on SkyDRM. The caller needs to use the token management API to [update the NXL metadata](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Token%20REST%20API#markdown-header-update-nxl-metadata-for-central-policy-files-only).  The purpose of this call is for SkyDRM to internally update the checksum of the file tags.  The next time there is a call to retrieve the token for this file, the checksums for the tags are verified before returning the token. 
2. Project files: these are files that are stored on SkyDRM. Clones of these files could be stored on clients. Changing the classification is done using the [reclassify](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API#markdown-header-reclassify-file) function. This internally calls the [update the NXL metadata](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Token%20REST%20API#markdown-header-update-nxl-metadata-for-central-policy-files-only) function to update the SkyDRM internal database of file metadata checksums. It also updates the .FileTag JSON object in the NXL header of the file to update the classification of the file stored in SkyDRM. Any clients consuming this file should ensure they have the latest file. The server does not push changes to clients today.