# Compliance and Technical Details for Government Projects  
  
This page documents answers to common questions related to government projects.  

## 2019-04-11 request from PS 
   
* Describe how your DRM product encrypts information  
    + Certifications (FIPS 140-2 Level xxx): We use the [bouncycastle](https://www.bouncycastle.org/) crypto library version 1.57 for our crypto operations. Bouncycastle also has a [FIPS library](https://www.bouncycastle.org/fips-java/) available. However we do not use this set of APIs in our current design. FIPS 140-2 certification needs to be conducted through an approved lab. SkyDRM has not gone through this process. 
    + Protocols (AES 256 or better): We use AES-256 to encrypt files.   
  
* Describe the type of Key Management available for your product  
    + Key backup/recovery: SkyDRM can be deployed in one of seven different operation modes. Today only one operation mode is supported, the Default mode (multi-tenant). The root CA can be used to recover the encryption token for files. The other six operation modes are not implemented. The key recovery for the default mode should work in theory but we have not developed tools or a web app to automate this process. 
    + Ownership (end-user vs. administrator): This is an evolving domain in SkyDRM. The concept of roles and role-based privileges is being developed. This will allow fine grained control over authorisations to perform specific actions, e.g., re-classify files, and so on. The first version of this feature is available. It provides control over classification change operations. With this feature, only the user with a specific right is allowed to perform classification change operations, and the super-admin and tenant admin is denied this right by default. This concept is being extended to other sensitive operations such as deleting files or folders.
    + Two-person controls: This is the continuous surveillance and control of positive control material at all times by a minimum of two authorized individuals, each capable of detecting incorrect or unauthorized procedures with respect to the task being performed and each familiar with established security requirements. Also called TPC. SkyDRM does not implement TPC today.