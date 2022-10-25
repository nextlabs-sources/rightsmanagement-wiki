# PLM Requirements  
  
This page contains requirements from the PLM team.    
  
## TeamCenter User Attributes  
  
Date: 2019-04-01  
  
The TeamCenter user attributes should be available in SkyDRM for use in SkyDRM policies.  
Important user attributes are user’s groups membership, projects membership, role.  
  
**Update: 2019-05-08: ** Summary of the call with Keng today:  
  
* It was decided that the proper approach is to manage the user attributes on the control center itself. The CC should utilize the "User" resource type in the policy models to specify which user attributes are allowed. The Subject component of type User should be used on the CC instead of the RMS creating PQL advanced conditions for policy evaluation from the user attributes it obtains from an IDP.   
* It was also decided that we should not create a mechanism on SkyDRM to consume user attributes from the teamcenter IDP or another IDP, since this would continue to require RMS to create advanced conditions in the PQL which contains user attributes for policy evaluation.     
  
  
## Reporter tool or UI for activity logs  
  
Date: 2019-04-01  
  
There needs to be a central location for logs.  
Some logs are in the CC database, majority of the logs are in the SkyDRM database.  
There needs to be a reporter tool like the one on CC to browse SkyDRM logs.   
TeamCenter uses only the System Bucket. There needs to be a UI to view logs for the system bucket.  
  
  
## Watermark  
  
Date: 2019-04-01  
  
There needs to be a watermark capability for central policies.  
  
## IP or host based access control  
    
Date: 2019-04-01  
  
Need IP or host based control.  
  
  
## Time based policy expiry, policy validity  
  
Date: 2019-04-01  
  
Central policies need to allow validity like ad-hoc policies. 
    
  
## Cut, copy, paste rights  
  
Date: 2019-04-01  
  
Cut, copy and paste rights need to be available.  
  
  
## Partial decryption function in the Server SDK  
  
Date: 2019-05-07  
  
The requirement details were provided by Jim today.   
  
1. Two APIs are required:
    + Capability to return original file length.
    + Capability to decrypt part of a protected file instead the whole file.
    + You can define any parameters required for these APIs, e.g. RMS server address.
Design your exception handling mechanism, We will use the exception based on what the RMJavaSDK provide.  
2. Both APIs are required to be thread-safe since it will be called in multiple threads context.  
3. From performance stand view,  General speaking, it should not broke any application, which use partial download of FCC, to function correctly. Following is the performance data we got with RMJavaSDK in RMS 8.4. Considering the fact that the new SkyDRM is using different algorithm to get the key/token, the performance requirement is not beyond +/- 10% comparing with the RMJavaSDK in 8.4. Following table is the performance data with RMJavaSDK 8.4 for different file size.     
  
![01.png](https://bitbucket.org/repo/dBgzdj/images/3935360317-01.png)
    
The above list is the original requirement. Current requirement is the new RMJavaSDK should work same as RMJavaSDK of RMS 8.4 which can work with TcVis/NX 12/VisServer correctly.

## Partial decryption performance  
  
Date: 2019-04-23  
  
Context: Siemens TcViz application  

This relates to the SkyDRM Server SDK.  
The `RightsManager.decryptPartial()` function should return within 20ms.  
  
**Note:** As of 2019-05-01 it is not clear that this is indeed a requirement. The 20ms deadline came about because of the assumption that the `decryptPartial()` response time was causing a crash in TcViz. Now this appears not to be the case.   
  
**Update: 2019-05-07:** Jim confirmed that the Server SDK should still allow *caching of token material* to improve the response time for the partial decryption function. This is included in [Build 213](http://nxt-build18-apache.nextlabs.com:9052/release_candidate/SecureCollaboration/10.7/213/) and can be switched on or off at deployment time as documented in the user guide. However, the 20ms response time for `RightsManager.decryptPartial()` is *not included* in the requirement. 
  
### Supporting material  
   
 
Historically, each request to the decryptPartial() function would trigger a token fetch from the RMS server. An update was made to the Server SDK that temporarily caches the token. The Server SDK uses the [Google guava](https://github.com/google/guava) library for caching with timeouts. Please note that the cache operations do some cleanups as it does not spawn a separate thread for expiry of keys (https://github.com/google/guava/wiki/CachesExplained#when-does-cleanup-happen).
  
Before the caching mechanism was implemented, the typical performance for the partial decrypt + download operation was as follows:  
  
```
57430: 2019-04-17 07:08:58,392 INFO:PartialDownload TokenMgmt getToken in total took time (ms) 131
57431: 2019-04-17 07:08:58,392 INFO:PartialDownload TokenMgmt getToken in total took time (ms) 131
57432: 2019-04-17 07:08:58,392 INFO:PartialDownload TokenMgmt getToken in total took time (ms) 131

```  
  
After the implementation of the cache, the performance is:  
  
```
2019-04-23 14:40:23.714 [DecryptUtil][INFO] - PartialDownload prefetchDuidInCache put token in cache for duid 08F75DC6024177E2B59E2F75BDFB0022
2019-04-23 14:40:23.716 [DecryptUtil][INFO] - PartialDownload prefetchDuidInCache took time (ms) 103
2019-04-23 14:40:23.728 [DecryptUtil][INFO] - PartialDownload decryptPartial took time (ms) 8
2019-04-23 14:40:23.734 [DecryptUtil][INFO] - PartialDownload decryptPartial took time (ms) 1
2019-04-23 14:40:23.742 [DecryptUtil][INFO] - PartialDownload decryptPartial took time (ms) 2
2019-04-23 14:40:23.749 [DecryptUtil][INFO] - PartialDownload decryptPartial took time (ms) 1

```  
  
The first operation which is the `prefetchDuidInCache() ` still takes around 100+ms because of the security checks done on the RMS server, i.e., membership check, tags/rights corruption check, and policy evaluation. However subsequent partial decrypt operations are well within the 20ms required by the Siemens TcViz application.  
  
### Known Issues  
  
As of 2019-04-23 there is no way for the `prefetchDuidInCache()` function to complete in less than 20ms due to the checks done on the server before returning the token to the caller. This means that in order to achieve the 20ms limit for the caller, the `prefetchDuidInCache()` function must be called well ahead of the first call to `decryptPartial()` that returns data to TcViz.   
  
Trying to achieve a significant improvement in the performance of this function will require a deeper analysis and significant refactoring of the token management logic.  At a high level the `TokenMgmt.getToken()` method does the following:  
  
* Parse the parameters sent to the function  
* Perform basic sanity checks to the parameters  
* Check for missing (unverified) NXL metadata
* Check if the DUID is inactive or revoked
* Check if the file protection type is correct
* Check that the owner is valid
* Check that the file policy is not tampered
* Check the file tags are not tampered 
* Check the `KeyManager` can retrieve the correct private key
* Create session criteria for the request 
* Obtain the certificate chain from the key manager
* Obtain the certificate root
* Derive the root public key and agreement key
* Create the agreement (`uek`) and get the secret key (`hsk`) 
* Create the token from the `uek` and `hsk`
  
If the caller is an API user (as is the case here) the policy evaluation step is skipped. The other logic in the `getToken` method is still required.  
  
Going deeper, even if we can bypass all the checks, the basic token generation logic (below) itself takes about 50ms. This includes the calls to database since the server keys are stored there, but even if it were optimized by storing those in server memory instead of db - it would limit the design which allows BYOK or retrieval from KMS servers depending on requirements, and their response time would vary.
```
Certificate[] chain = km.getCertificateChain(ownerTokenGroupName, dhAlias);
Certificate root = chain[chain.length - 1];
PublicKey rootPubKey = KeyUtils.readPublicKey(root.getPublicKey().getEncoded(), "DH");
PublicKey agreeKey = KeyManager.createDHPublicKey(new BigInteger(agreement, 16));
SecretKey uek = KeyManager.createAgreement(privKey, rootPubKey, agreeKey);
SecretKey hsk = km.getSecretKey(ownerTokenGroupName, IKeyStoreManager.PREFIX_HSK + ownerTokenGroupName);
byte[] token = KeyManager.createToken(uek, hsk, Hex.toByteArray(duid), maintanenceLevel);
```
This function internally calls a BouncyCastle MAC function: 
```
        MessageDigest md = MessageDigest.getInstance(IKeyStoreManager.ALG_SHA256, "BC");
```
Based on the current implementation, it is not possible to achieve 20ms for the token retrieval function.  
  
## Gaps in the requirements  
  
TBD.