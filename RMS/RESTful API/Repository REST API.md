# Repository service

For all the endpoints exposed here, pass userId, ticket, and platformId as header parameters.

- [Get Repositories](#markdown-header-get-repositories)
- [Add New Repository](#markdown-header-add-new-repository)
- [Update Repository](#markdown-header-update-repository)
- [Remove Repository](#markdown-header-remove-repository)
- [Get Access Token](#markdown-header-get-access-token)
- [Get Authorization URL](#markdown-header-get-authorization-url)

## Get Repositories
This API is used to get repositories added by a user in RMS server. 

- **URL**: /rms/rs/repository
- **method**: GET
- **produces**: application/json
```json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1474590650465,
   "results":{
      "repoItems":[
         {
            "repoId":"738805f0-7d9b-455a-b947-4d0cfdbe53b4",
            "name":"google",
            "type":"GOOGLE_DRIVE",
            "isShared":false,
            "accountName":"xxxxxx@gmail.com",
            "accountId":"1xxxxxxxxxxxxxxxxxxxxxx",
            "token":"...",
            "creationTime":1470192537982
         },
         {
            "repoId":"e8eb1c55-c4e8-45e6-a273-5ec140c3cbd2",
            "name":"sharepointonline",
            "type":"SHAREPOINT_ONLINE",
            "isShared":false,
            "accountName":"xxxxxx@nextlabs.com",
            "accountId":"2xxxxxxxxxxxxxxxxxxxxxx",
            "token":"...",
            "creationTime":1470122537982
         }
      ]
   }
}
```

## Add New Repository
This API is used to add a new repository in RMS server. 

Regex for repoName
```
^[\w -]*$
```

- **Repository Type Values**

		DROPBOX
		GOOGLE_DRIVE
		ONE_DRIVE
		SHAREPOINT_ONPREMISE
		SHAREPOINT_ONLINE
		SHAREPOINT_CROSSLAUNCH
		SHAREPOINT_ONLINE_CROSSLAUNCH

- **URL**: /rms/rs/repository
- **method**: POST
- **consumes**: application/json
```json
		{
			"parameters": {
				 "repository": "{
				 	 \"name\": \"My GoogleDrive Repository\",
				 	 \"type\": \"GOOGLE_DRIVE\",
				 	 \"isShared\": false,
				 	 \"accountName\": \"RMS Admin\",
				 	 \"accountId\": \"accountIdFromServiceProvider\",
				 	 \"token\": \"tokenFromServiceProvider\",
				 	 \"preference\": \"\",
				 	 \"creationTime\": 0
				 }"
			}
		}
```
- **produces**: application/json
```json
{
   "statusCode":200,
   "message":"Repository successfully added",
   "serverTime":1474591219931,
   "results":{
      "repoId":"176aa89e-9d75-41ea-929b-40cb832134a1"
   }
}
```
- **Notes**:

**isShared**

The `isShared` field, when set to `TRUE`, will mark a new repository as being a shared one.  RMS would then not make this repo editable in manage repositories for normal users.  Currently, there is no concept of shared repositories in the cloud.  By default, the `isShared` field should be set to `FALSE`.  This is true across the RMS ecosystem (Android, iOS, and web).

**Name Validation**

We have added naming validation where repository name must not exceed 40 characters or contain any special characters. 
Regex expression for this validation is ```^[\\w- ]+$```
This produces two different response when validation failed. 

- ***Name Too Long***: Code 4001

```json
{
  "statusCode": 4001,
  "message": "Repository Name Too Long",
  "serverTime": 1489549303616
}
```
- ***Name Containing illegal special characters***: Code 4003

```json
{
  "statusCode": 4003,
  "message": "Repository Name containing illegal special characters",
  "serverTime": 1489549352358
}
```


- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*  |
> |-------------------------|------------|
> |  200 | Success.|
> |  304 | Repository already exists.|
> |  400 | bad request|
> |  401 | Authentication failed.|
> |  403 | UnAuthorized access. |
> |  405 | Invalid HTTP Method |
> |  409 | Duplicate repository name |
> |  500 | Internal Server Error|


## Update Repository
This API is used to update a repository. 

Regex for repoName
```
^[\w -]*$
```

- **URL**: /rms/rs/repository
- **method**: PUT
- **consumes**: application/json
```json
{
   "parameters":{
      "repoId":"432d8260-d84b-4687-93f8-d195c563de9c",
      "token":"...",
      "name":"..."
   }
}
```
- **produces**: application/json
```json
{
   "statusCode":200,
   "message":"Repository successfully updated",
   "serverTime":1474591551519
}
```

- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*  |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | bad request|
> |  401 | Authentication failed.|
> |  403 | UnAuthorized access. |
> |  405 | Invalid HTTP Method |
> |  409 | Duplicate repository name |
> |  500 | Internal Server Error|


## Remove Repository
This API is used to remove a repository.

- **URL**: /rms/rs/repository
- **method**: DELETE
- **consumes**: application/json
```json
{
   "parameters":{
      "repoId":"432d8260-d84b-4687-93f8-d195c563de9c"
   }
}
```

- **produces**: application/json
```json
{
   "statusCode":204,
   "message":"Repository successfully removed",
   "serverTime":1474591551519
}
```

- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*  |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | bad request|
> |  401 | Authentication failed.|
> |  403 | UnAuthorized access. |
> |  405 | Invalid HTTP Method |
> |  500 | Internal Server Error|

## Get Access Token
This API is used to get access token for given repository.

- **URL**: /rms/rs/repository/{repoId}/accessToken
- **method**: GET
- **produces**: application/json

```json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1474591551519,
   "results":{
      "accessToken":"Znb8BSROLVAAAAAAAAAB3O3PJvPw25xpb13EtNnx6XuPGGVYgFxmYOcur"
   }
}
```

- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Invalid tenant.|
> |  401 | Authentication failed.|
> |  403 | Access denied (This error occurred when trying to access repository that is not belong to that user). |
> |  404 | Repository not found. |
> |  500 | Internal Server Error.|
> |  5001 | Not supported storage provider.|
> |  5002 | Missing storage provider attributes.|
> |  5003 | Storage provider is not configured (Missing appId, appSecret, or redirect URL).|
> |  5004 | Missing Context ID.|
> |  5005 | Not authorized or expired. RMS will send cookie, and client need to use this cookie for launching browser control.|

**Note**
If RMS return status 5005
```json
{
  "statusCode": 5005,
  "message": "Not authorized or expired.",
  "serverTime": 1488338175439,
  "results": {
    "authURL": "https://rmtest.nextlabs.com/rms/json/OAuthManager/DBAuth/DBAuthStart"
  }
}
```
Client need to launch browser for given URL

## Get Authorization URL
This API is used to get url for oAuth server-side flow.

- **URL**: /rms/rs/repository/authURL
- **method**: POST
- **consumes**: application/json
```json
{
   "parameters":{
      "type":"DROPBOX",
      "name":"DBDocs",
      "siteURL":"https://SharepointOnlineSiteURL/site"
   }
}

Supported types are DROPBOX, GOOGLE_DRIVE, ONE_DRIVE & SHAREPOINT_ONLINE

siteURL parameter will be considered only for SHAREPOINT_ONLINE

```

- **produces**: application/json

```json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1474591551519,
   "results":{
       "authURL": "https://rmtest.nextlabs.com/rms/json/OAuthManager/DBAuth/DBAuthStart?name=DBDocs"
   }
}
```

- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*  |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Missing parameters|
> |  5001 | unsupported storage provider.|

#### OAuth Flow

Client should launch the authURL from previous response in a browser window (user_id, ticket, tenant (optional) should be set in cookie or as request params or as headers). Users will be directed to the Service provider's website where they login using their account and authorize our appilication to access their resources. If oAuth flow is successful, server will return the following to client (note that token is missing ). Client can call "Get Access Token" API to get the token when necessary.


- **produces**: application/json

```json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1474591551519,
   "results": {
	   "repository" : {
            "repoId":"e8eb1c55-c4e8-45e6-a273-5ec140c3cbd2",
            "name":"DBDocs",
            "type":"DROPBOX",
            "isShared":false,
            "accountName":"xxxxxx@nextlabs.com",
            "accountId":"2xxxxxxxxxxxxxxxxxxxxxx",
            "creationTime":1470122537982
         }
   }
}
```


- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*  |
> |-------------------------|------------|
> |  200 | Success.|
> |  304 | Repository already exists|
> |  400 | Invalid App/ App not configured/ bad request|
> |  401 | Authentication failed.|
> |  403 | UnAuthorized access. |
> |  405 | Invalid HTTP Method |
> |  409 | Duplicate repository name |
> |  410 | Code Expired. (This means the current request is the same as a previous request processed by the server)|
> |  500 | Internal Server Error. Error during Authorization|
> |  5001 | Not supported storage provider.|