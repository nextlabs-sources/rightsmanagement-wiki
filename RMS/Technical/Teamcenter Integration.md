# TeamCenter Integration with SkyDRM
## Install Process
  
This page describes the process of setting up SkyDRM to work with TeamCenter today. The steps are: 
  
* SkyDRM Installation Team:
    * Install SkyDRM.
    * Set up an enterprise applications project, and record the `tenantName` for this project.
  
* SkyDRM Administrator:
    * Use the `AccessKeyMgmt.bat` or `AccessKeyMgmt.sh` utility to register a trusted application with SkyDRM. This utility allows the administrator to list, create and revoke trusted applications. 
    * This utility will result in an `appId` and `appKey`, similar to the application ID and key used by a calling application to authenticate itself with Google or other services.
    * Send the `tenantName`, `appId` and `appKey` to the DCE install team. 
  
* DCE Installation Team: 
    * Install the DCE. 
    * During the DCE install process, the admin will be asked to enter the three parameters provided by the SkyDRM team: `tenantName`, `appId` and `appKey`. This is an additional step not present in the current DCE 4.X install process. 
    * DCE will save these parameters, and will use them in calls to the Server SDK. Technically, the  parameters are used in the `RightsManager` class, for example: `RightsManager manager = new RightsManager(routerURL, appId, appKey);`, which will allow the Server SDK to authenticate the caller with SkyDRM. 
  
After these steps are performed, DCE should be able to encrypt and decrypt files, read and write NXL file tags using the Server SDK API. Note that the Server SDK uses the NXL 2.0 file format and the functions will not work with the older NXL 1.0 files.