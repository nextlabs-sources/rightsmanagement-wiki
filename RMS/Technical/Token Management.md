# SkyDRM support for Partner Enterprise Applications (Teamcenter, etc.)  
  
## Problem Statement   
  
The SkyDRM enterprise solution needs to work with partner enterprise applications such as Siemens Teamcenter and SAP. These enterprise applications have their own user management system which may not be linked to the corporate LDAP/AD, e.g., Teamcenter has its own user management system with a different set of permissions than AD. 
   
As part of this support for third party enterprise applications, SkyDRM needs to support token management for files that are not stored on the SkyDRM server itself. This is in addition to the default use case with files stored within the SkyDRM server. The solution needs to support the different modes of operation of the partner enterprise applications, e.g., Teamcenter can operate in managed and unmanaged modes. 
  
NextLabs DCE for Teamcenter requires the following functions:   

* Decrypt NXL file
* Encrypt file to NXL
* Write tags
* Check if the fie is NXL
* Partial decrypt
* Generate token
* Retrieve token
   
The client SDK requires the following functions:  

* Generate token
* Retrieve token
  
## Proposed Solution   
  
In the proposed solution, the partner enterprise applications deployed on premises are mapped into a “partner enterprise applications” project instead of the default tenant per user as was the case with the previous proposal. 
  
As part of this process, the administrator registers the enterprise application (trusted application) with SkyDRM using the SkyDRM server registration script (AccessKeyManagement.sh / .bat). This outputs an AppID and AppKey that must be used by the trusted application while calling SkyDRM crypto functions through the RMJavaSDK.
  
The “partner enterprise applications” project generates a tenant name which needs to be used during function calls from Teamcenter and other enterprise applications. 
  
User management is a complex issue. Teamcenter has its own user management system which may or may not be connected to the corporate AD service. For example the current Teamcenter EDRM deployment at Bajaj does not connect to the Bajaj corporate AD. In order to simplify the solution, we proposed to reuse the RMC 9.1 concept. Users of Teamcenter and other partner enterprise applications would still need to be members of the “partner enterprise applications” project in SkyDRM, i.e., they would be able to login to SkyDRM even if they have a separate account managed by Teamcenter. RMC 9.1 solves this problem by providing an RMS login mechanism on the user’s workstation. This concept can be reused. 
   
Pre-requisites and assumptions:  

1. Teamcenter and other enterprise applications may have their own user management mechanism not connected to the corporate AD (example: currently the case with Bajaj).
2. The enterprise applications map to a default “partner enterprise applications” project in SkyDRM. 
3. SkyDRM is deployed in the enterprise. Users of Teamcenter and other enterprise applications must be enrolled in SkyDRM, and are members of the “partner enterprise applications” project with a tenantName specified by the enterprise SkyDRM administrator.
4. Other enterprise applications deployed in the enterprise will also be mapped to this tenantName to enable sharing of files across enterprise applications.
  
## Server side: DCE with Teamcenter using the RMJavaSDK  
  
Registration  

1.	SkyDRM is installed on premises. 
2.	The SkyDRM administrator creates a project for the partner enterprise applications. 
3.	This project has a tenant Name (tenantName).
4.	The admin registers the trusted client with SkyDRM, which outputs an AppID and AppKey. 
5.	The tenantName, AppID and AppKey are provided during the DCE installation. 
6.	The admin installs the TeamCenter DCE, and inputs the tenantName, AppID and AppKey. DCE records these three parameters. 
  
Encryption / Decryption Requests  

1.	DCE uses the RMJavaSDK encrypt/decrypt/other functions. 
2.	In the function call, it provides the AppID, AppKey and tenantName. 
*Note: The current RMJavaSDK APIs do not take the tenantName as a parameter. The SDK uses the default tenant and is not aware of projects. This code needs to be updated to support tenant name. 
3.	The RMJavaSDK communicates with the SkyDRM RMS server to perform the relevant operation (generate token, retrieve token). The REST API from SkyDRM returns the DUID and Token. 
https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Token%20REST%20API
  
Generate Token: Result   
~~~
{
   "statusCode":200,
   "message":"OK",
   "results":{
       "tokens":{ "AD3D7A52634AFC99CCD35D0413CEEC5D":"78D270A56750AE5FAF7261B73A20F5ADA8F786036D402DC50EAF6558584A61B",
             "AB2D7A22634AAC11AED35D0413CEAC2B":"..."
           },
       "ml":"0"
    }
}
~~~   
Retrieve Token: Result   
~~~
{
   "statusCode":200,
   "message":"OK",
"results":{"token":"78D270A56750AE5FAF7261B73A20F5ADA8F786036D402DC50EAF6558584A61BB"   
   }
}
~~~   
   
4.	The DCE uses the token to encrypt or decrypt files, and to change tags. Tag changes require the HMAC checksum to be re-computed and will need the token from the SkyDRM server. 
5.	Note that since these requests come from previously trusted applications, SkyDRM relies on the requesting application to perform any authentication or access control required, and is only responsible to generate and retrieve tokens. 
  
## Client Side   
  
For this solution to work on the client side, the following points need to be considered:  

1.	The solution should work whether the SkyDRM user management uses the internal IDP, AD, or other IDPs.
2.	Not all enterprise users who can authenticate to SkyDRM are necessarily in the Teamcenter or other partner enterprise application ecosystems. 
3.	The list of users in the partner enterprise application ecosystem should be members of this SkyDRM project for the solution to work. This step should be performed as part of the Teamcenter + DCE deployment process in the enterprise.
4.	The client SDK needs a connection with the SkyDRM RMS server each time it needs to retrieve a token for a specific file. It may choose to cache tokens offline but this functionality is out of scope of the SkyDRM RMS server or server SDK.
5.	 The solution needs to work with the Teamcenter use cases where the SCF is used to share files with external partners through the SCF framework and the Briefcase Browser (BB). 
  
Registration  

1.	All users requiring this functionality are expected to be pre-enrolled in the “partner enterprise applications” project in the enterprise SkyDRM deployment, i.e., the result of the [“List Memberships”](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/User%20REST%20API#markdown-header-list-memberships) API call for each user should contain the tenant Name of the “partner enterprise applications” project. The solution does not work if the user is not a member of this project. 
2.	Note that once the user is enrolled in this project, this functionality will work for other enterprise applications as well, and is not limited to Teamcenter alone.
3.	The client SDK need to set tenant Name for the partner enterprise applications project during the initialization process.
  
Encryption  
  
1.	The client SDK provides a mechanism for the user to [login to SkyDRM](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Login%20REST%20API). 
2.	The user logs in with his credentials.
3.	SkyDRM responds with an OK / NOK. 
4.	If the user is authenticated, SkyDRM responds with UserID and ticket which will be used by the Client SDK later. 
5.	Optionally, if needed, the user authenticates with Teamcenter or any other application. 
6.	The client SDK sends a request to SkyDRM to generate token using the token [REST API](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Token%20REST%20API). As part of this request, the user ID, ticket and tenant name are sent. 
7.	The SkyDRM server processes the request, generates a DUID and token and returns it to the Client SDK. 
8.	The Client SDK protects the file using the token. 
9.	The file is stored locally or is loaded to Teamcenter. The file is not stored on SkyDRM. 
  
Decryption  

1.	The client logs in to SkyDRM. 
2.	The client requests the token using the REST API. 
3.	As part of this request, the client SDK sends the user ID, ticket, tenant name and DUID. 
4.	The SkyDRM server processes the request and returns the token. 
5.	The client SDK decrypts the file. 

## Actions  

The RMJava SDK needs to be updated (work in progress). The functions should accept a tenant Name as a parameter. The RightsManager class needs to be updated to accept a Tenant Name. We currently call this function to instantiate the object: 
~~~
RightsManager manager = new RightsManager(routerURL, appId, appKey);
~~~
After the update, it should look like this: 
~~~
RightsManager manager = new RightsManager(routerURL, appId, appKey, tenantName);
~~~
  
Expose the “Check if NXL” API in the RMJavaSDK for Jim.
  
Check the “[Generate new token](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Token%20REST%20API)” API to ensure we can specify a tenant. 
  
No changes are required to the NXL file format.
  
## Outcome  
The two problems are solved:  

1.	Teamcenter DCE (and other enterprise applications) + SkyDRM integration: encrypt, decrypt, set tag, check if NXL file, partial decrypt.
2.	The Client SDK can retrieve tokens.