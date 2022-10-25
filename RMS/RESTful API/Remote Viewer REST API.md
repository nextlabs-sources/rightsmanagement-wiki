# Remote Viewer REST API
## Use Remote viewer for viewing file
This API is an internal API purely used by RMC.

- [Upload and View local File](#markdown-header-upload-and-view-local-file)
- [View Repository File](#markdown-header-view-repository-file)
- [View Project File](#markdown-header-view-project-file)
- [Supported operations](#markdown-header-supported-operations)
- [Offset](#markdown-header-offset)

###Upload and View local File

userId, ticket, clientId, deviceId and platformId should be passed as header parameters

- **URL**: /rms/rs/remoteView/local
- **method**: POST
- **consumes**: multipart/form-data
      * parameter file should contain the file content
      * parameter API-input should have the following json

```
#!json
{
   "parameters":{
      "userName":"nnaresh@gmail.com",
      "tenantName":"nextlabs.com", // new arch will have tenantName as that's what user logs into
      "fileName":"abc.doc",
      "offset":"-480",
      "operations": 2
   }
}
```
  
-----------------------

** SkyDRM *HoneyBadger* Release Update Proposal  ** 2019-04-12: We propose to change the input JSON as follows:  
  
```
#!json
{
   "parameters":{
      "userName":"nnaresh@gmail.com",
      "fileName":"abc.doc",
      "offset":"-480",
      "operations": 2
   }
}
```
  
The `tenantId` has been removed since they are not required for the Upload + View workflow, and audit log will write tenantName itself.  
  
** end update **   
  

-----------------------
    

- **produces**: application/json


```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1480496502753,
   "results":{
      "owner":true,
      "permissions":513,
      "duid":"E2DA78C06667C97D8A62351D52F47E9F",
      "membership":"m66@t-af77c48063dc42bbac34f348ca28e7b0",
      "cookies":[
         "JSESSIONID_V=5159B9C95D62BB6F7787C710FB3E8A6D;path=/viewer/;Secure;HttpOnly",
         "userId=11;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "ticket=A705B5141CF26C3C11B2258125A02BB6;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "clientId=5480CB4951A359BF28DF07AE47C5A072;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "tenantId=f3133620-2a13-4234-b21e-8522aee08fd1;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "idp=0;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "platformId=1000;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure"
      ],
      "viewerURL":"https://boomerang.nextlabs.com/viewer/DocViewer.jsp?documentid=1480496502732f94dda60-ebb4-4686-8b6f-5aaf3a4232e3&u=nnaresh@gmail.com&t=f3133620-2a13-4234-b21e-8522aee08fd1&s=4fd24a70-4f22-481f-822b-367b6ffcb29c&uid=11&ticket=83F2180F00629DF60D2247E24625C4F5"
   }
}

```

**Client needs to set ALL cookies in cookies field under the domain received in viewerURL (in this case boomerang.nextlabs.com) before trying to launch the webview. If this cookie is not set, file viewing will not be successful**


###View Repository File

userId, ticket, clientId, deviceId and platformId should be passed as header parameters

- **URL**: /rms/rs/remoteView/repository
- **method**: POST
- **consumes**: application/json


```
#!json
{
   "parameters":{
      "repoId":"89bf9353-1426-4f1e-96ee-10916ff93180",
      "pathId":"id:Ee2_t-30uBAAAAAAAAAACQ",
      "pathDisplay":"/testing/hello/RUNNING.txt",
      "offset":-480,
      "repoName":"9090",
      "repoType":"DROPBOX",
      "email":"nnallagatla@nextlabs.com",
      "tenantName":"nextlabs.com",
      "lastModifiedDate":145145141545,
      "operations": 2
   }
}
```


- **produces**: application/json

```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1480496502753,
   "results":{
      "owner":true,
      "permissions":513,
      "duid":"E2DA78C06667C97D8A62351D52F47E9F",
      "membership":"m66@t-af77c48063dc42bbac34f348ca28e7b0",
      "cookies":[
         "JSESSIONID_V=5159B9C95D62BB6F7787C710FB3E8A6D;path=/viewer/;Secure;HttpOnly",
         "userId=11;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "ticket=A705B5141CF26C3C11B2258125A02BB6;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "clientId=5480CB4951A359BF28DF07AE47C5A072;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "tenantId=f3133620-2a13-4234-b21e-8522aee08fd1;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "idp=0;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "platformId=1000;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure"
      ],
      "viewerURL":"https://boomerang.nextlabs.com/viewer/DocViewer.jsp?documentid=1480496502732f94dda60-ebb4-4686-8b6f-5aaf3a4232e3&u=nnallagatla@nextlabs.com&t=f3133620-2a13-4234-b21e-8522aee08fd1&s=4fd24a70-4f22-481f-822b-367b6ffcb29c&uid=11&ticket=83F2180F00629DF60D2247E24625C4F5"
   }
}

```

**Client needs to set ALL cookies in cookies field under the domain received in viewerURL (in this case boomerang.nextlabs.com) before trying to launch the webview. If this cookie is not set, file viewing will not be successful**


###View Project File

userId, ticket, clientId, deviceId and platformId should be passed as header parameters

- **URL**: /rms/rs/remoteView/project
- **method**: POST
- **consumes**: application/json


```
#!json
{
   "parameters":{
      "projectId":1,
      "pathId":"/testing/hello/running.txt.nxl",
      "pathDisplay":"/testing/hello/RUNNING.txt.nxl",
      "offset":-480,
      "email":"nnallagatla@nextlabs.com",
      "tenantName":"nextlabs.com",
      "lastModifiedDate":145145141545,
      "operations": 2
   }
}
```


- **produces**: application/json

```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1480496502753,
   "results":{
      "owner":true,
      "permissions":513,
      "duid":"E2DA78C06667C97D8A62351D52F47E9F",
      "membership":"m66@t-af77c48063dc42bbac34f348ca28e7b0",
      "cookies":[
         "JSESSIONID_V=5159B9C95D62BB6F7787C710FB3E8A6D;path=/viewer/;Secure;HttpOnly",
         "userId=11;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "ticket=A705B5141CF26C3C11B2258125A02BB6;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "clientId=5480CB4951A359BF28DF07AE47C5A072;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "tenantId=f3133620-2a13-4234-b21e-8522aee08fd1;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "idp=0;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure",
         "platformId=1000;Version=1;Comment=;Domain=boomerang.nextlabs.com;Path=/;Max-Age=15931;Secure"
      ],
      "viewerURL":"https://boomerang.nextlabs.com/viewer/DocViewer.jsp?documentid=1480496502732f94dda60-ebb4-4686-8b6f-5aaf3a4232e3&u=nnallagatla@nextlabs.com&t=f3133620-2a13-4234-b21e-8522aee08fd1&s=4fd24a70-4f22-481f-822b-367b6ffcb29c&uid=11&ticket=83F2180F00629DF60D2247E24625C4F5"
   }
}

```

**Client needs to set ALL cookies in cookies field under the domain received in viewerURL (in this case boomerang.nextlabs.com) before trying to launch the webview. If this cookie is not set, file viewing will not be successful**


### Following statusCodes and Operations are applicable for local, repository & project endpoints

- **Supported Response Status Codes**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Invalid request data|
> |  401 | Authentication failed.|
> |  403 | Access Denied|
> |  404 | File not found. Client can download file and then use "Upload and View local file API"|
> |  415 | File type not supported|
> |  500 | Internal Server Error.|
> |  5007 | Invalid/corrupt NXL file.|
> |  5008 | Missing dependencies. Assembly files are not supported as of now|

###Supported operations

> |  *Button*                  |  *Value (decimal)*   |
> |----------------------------|----------------------|
> |  View File Info            | 1                    |
> |  Print                     | 2                    |
> |  Protect                   | 4                    |
> |  Share                     | 8                    |
> |  Download                  | 16                   |

###Offset

The time difference between UTC time and local time, in minutes. Used for generating timestamp for watermarks.
Similar to [Javascript's Date.prototype.getTimezoneOffset()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/getTimezoneOffset)