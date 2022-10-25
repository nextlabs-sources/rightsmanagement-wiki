# RMS Projects REST API

RMS exposes the following web services to access Projects for the following operations:

* [List projects](#markdown-header-list-projects)
* [Get project metadata](#markdown-header-get-project-metadata)
* [Create project](#markdown-header-create-project)
* [Update project](#markdown-header-update-project)
* [Upload file](#markdown-header-upload-file)
* [Get ticket](#markdown-header-get-ticket)
* [Upload large file](#markdown-header-upload-large-file)
* [File listing](#markdown-header-file-listing)
* [Create folder](#markdown-header-create-folder)
* [Delete file/folder](#markdown-header-delete-filefolder)
* [Get file metadata](#markdown-header-file-metadata)
* [Download file](#markdown-header-download-file)
* [Project Invitation](#markdown-header-project-invitation)
* [Send Invitation Reminder](#markdown-header-send-invitation-reminder)
* [Revoke Invitation](#markdown-header-revoke-invitation)
* [Accept Invitation](#markdown-header-accept-invitation)
* [Decline Invitation](#markdown-header-decline-invitation)
* [List Members](#markdown-header-list-members)
* [Remove Member](#markdown-header-remove-member)
* [Get Membership](#markdown-header-get-membership)
* [Get Member Detail](#markdown-header-get-member-details)
* [List Pending Invitations For a Project](#markdown-header-list-pending-invitations-for-a-project)
* [List Pending Invitations For a User](#markdown-header-list-pending-invitations-for-a-user)
* [Delete Project (Command line)](#markdown-header-delete-project)
* [Folder Metadata](#markdown-header-folder-metadata)
* [Reclassify File](#markdown-header-reclassify-file)


This API is intended to be consumed by RMC/RMD.


## List projects

 - **URL:** /rms/rs/project     
 - **method:** GET
 - **headers** Pass userId and ticket as header parameters
> |  *Parameter*                  |  *Value*   |
> |-------------------------|------------|
> |  page	    | Page number (start from 1)| 
> |  size     | Number of records to be returned. Positive number. You can use page and size fields to support pagination on client.|
> | orderBy | A comma-separated list of sort keys. Supported fields ***lastActionTime***, ***name***. Each key sorts ascending by default, but may be reversed with the '-' modifier. Example usage: orderBy=***-lastActionTime,name***.
>|ownedByMe (true/false)|If not specified, will return all projects. If true, return projects owned by this user. If false, return projects user has joined. |
 - **produces:** application/json; 
```
#!json 
{
  "statusCode": 200,
  "message": "OK",
  "serverTime": 1484537953900,
  "results": {
    "totalProjects":2,
    "detail": [
	     {
	        "id": 13,
	        "tenantId": "99406fce-dcde-4ab2-be66-17e2d1406bd1", // will be changed to "parentTenantId"
	        "tenantName": "t-1578639918fa481080701faf9301a58e", // will be changed to "parentTenantName"
                // will add a new parameter "tokenGroupName"
	        "name": "Project Nova",
	        "description": "Collaborate in project nova",
	        "displayName": "Project Nova",
	        "creationTime": 1483605615817,
	        "totalMembers": 1,
	        "totalFiles": 0,
	        "ownedByMe": true,
	        "owner": {
	          "userId": 2,
	          "name": "admin@nextlabs.com",
	          "email": "admin@nextlabs.com"
	        },
	        "accountType": PROJECT_TRIAL,
	        "trialEndTime": 148706243714,
	        "projectMembers": {
	          "totalMembers": 6,
	          "members": [
	            {
	              "userId": 57,
	              "displayName": "althaf nextlabs",
	              "email": "rmsuser720@gmail.com",
	              "creationTime": 1496886387439
	            },
	            {
	              "userId": 4,
	              "displayName": "althaf.ashraf@nextlabs.com",
	              "email": "althaf.ashraf@nextlabs.com",
	              "creationTime": 1496885208735
	            },
	            {
	              "userId": 56,
	              "displayName": "althaf.rms1@gmail.com",
	              "email": "althaf.rms1@gmail.com",
	              "creationTime": 1496886126044
	            },
	            {
	              "userId": 7,
	              "displayName": "mohammed althaf",
	              "email": "althaf1088@gmail.com",
	              "creationTime": 1496886413244
	            },
	            {
	              "userId": 55,
	              "displayName": "rmsuser0@gmail.com",
	              "email": "rmsuser0@gmail.com",
	              "creationTime": 1496886176971
	            }
	          ]
	        }
	      },
	      {
	        "id": 14,
	        "tenantId": "33138865-ed68-4645-8365-57386a63c719",
	        "tenantName": "t-8f3ecc2e206c46a7839b7cf39c523b37",
	        "name": "Project SkyDRM",
	        "description": "Share your documents securely",
	        "displayName": "Project SkyDRM",
	        "creationTime": 1483606243712,
	        "totalMembers": 1,
	        "totalFiles": 0,
	        "ownedByMe": true,
	        "owner": {
	          "userId": 2,
	          "name": "admin@nextlabs.com",
	          "email": "admin@nextlabs.com"
	        },
	        "accountType": PROJECT_TRIAL,
	        "trialEndTime": 148706243714,
	        "projectMembers": {
	          "totalMembers": 6,
	          "members": [
	            {
	              "userId": 57,
	              "displayName": "althaf nextlabs",
	              "email": "rmsuser720@gmail.com",
	              "creationTime": 1496886387439
	            },
	            {
	              "userId": 4,
	              "displayName": "althaf.ashraf@nextlabs.com",
	              "email": "althaf.ashraf@nextlabs.com",
	              "creationTime": 1496885208735
	            },
	            {
	              "userId": 55,
	              "displayName": "rmsuser0@gmail.com",
	              "email": "rmsuser0@gmail.com",
	              "creationTime": 1496886176971
	            }
	          ]
	        }
	      }
	    ]
  }
}
```

## Get project metadata

 - **URL:** /rms/rs/project/{projectId}     
 - **method:** GET
 - **headers** Pass userId and ticket as header parameters
 - **produces:** application/json; 

NOTE: Maximum length of invitationMsg is 250 characters. It does not have any regular expression as one can type any characters for invitationMsg. 

```
#!json 
{
  "statusCode": 200,
  "message": "OK",
  "serverTime": 1484538182215,
  "results": {
    "detail": {
      "id": 14,
      "tenantId": "33138865-ed68-4645-8365-57386a63c719", // will be changed to "parentTenantId"
      "tenantName": "t-8f3ecc2e206c46a7839b7cf39c523b37", // will be changed to "parentTenantName"
      // will add a new parameter "tokenGroupName"
      "name": "Project SkyDRM",
      "description": "Share your documents securely",
      "displayName": "Project SkyDRM",
      "creationTime": 1483606243712,
      “invitationMsg” : “This is the default message”,
      "totalMembers": 1,
      "totalFiles": 0,
      "ownedByMe": true,
      "owner": {
        "userId": 2,
        "name": "admin@nextlabs.com",
        "email": "admin@nextlabs.com"
      },
      "accountType": PROJECT_TRIAL,
      "trialEndTime": 148706243714
    }
  }
}
```

- **Supported Account Type**: 
    
   	PROJECT_TRIAL
  
	PROJECT

	ENTERPRISE

## Create project

 - **URL:** /rms/rs/project     
 - **method:** PUT
 - **consumes:** application/json; Pass userId and ticket as header parameters


NOTE: Maximum length of invitationMsg is 250 characters. It does not have any regular expression as one can type any characters for invitationMsg. 

```
#!json 
{
   "parameters":{
      "projectName":"RMC",
      "projectDescription":"RMC project",
      “invitationMsg” : “This is the default message”,
      "emails":[
         "user1@nextlabs.com",
         "user2@nextlabs.com"
      ],
      "projectTags":[1,2,3,4]
   }
}
```
      
 - **produces:** application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1477637785817,
   "results":{
      "projectId":"47",
      "membership":{  
         "id":"m146@t-6abfc8bfe32a451a9fe6e4e54be0c733", // membership will be sth like "m14@6ed9474a-7fad-4f1d-bde3-76137743ad0a_asdasdasdasd_1
         "type":1,
         "tenantId":"50ff2895-aa30-4ffb-a2d6-7467732de424", // will be removed, and add "tokenGroupName"
         "projectId":47
      }
   }
}
```

- **Note**: Name Validation

We have added naming validation where repository name must not exceed 50 characters.

Project name can contain:

1) 'a' to 'z'

2) 'A' to 'Z'

3) '0' to '9'

4) special characters: single quotes, double quotes, hashes, hyphens, commas, underscores, spaces. 

Regex expression for this validation is ```^[\\u00C0-\\u1FFF\\u2C00-\\uD7FF\\w \\x22\\x23\\x27\\x2C\\x2D]+$``` 
```\x22 double quote, \x23 hash, \27 single quote, \x2C comma, \x2D hyphen, and underscore is included in \w```

- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Missing required parameters (projectName, projectDescription).|
> |  4001 | Project Name Too Long.|
> |  4002 | Project Description Too Long.|
> |  4003 | Project Name containing illegal special characters.|
> |  4009 | Invalid email.|
> |  401 | Authentication failed.|
> |  403 | Access denied (This error occurred when trying to upload NXL file and current user is not document steward). |
> |  500 | Internal Server Error.|
> |  5005 | Project name already exists.|


## Update project

 - **URL:** /rms/rs/project/{projectId}
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters, EITHER "projectName" OR "projectDescription" has to be present


NOTE: Maximum length of invitationMsg is 250 characters. It does not have any regular expression as one can type any characters for invitationMsg. 

```
#!json 
{
   "parameters":{
      "projectName":"Project SkyDRM", // will be removed, do not allow to edit project name
      "projectDescription":"Share your documents securely",
      “invitationMsg” : “This is the changed message”
   }
}
```
      
 - **produces:** application/json
```
#!json
{
  "statusCode": 200,
  "message": "OK",
  "serverTime": 1484538182215,
  "results": {
    "detail": {
      "id": 14,
      "name": "Project SkyDRM",
      "description": "Share your documents securely",
      "displayName": "Project SkyDRM",
      "creationTime": 1483606243712,
      "totalMembers": 1,
      "totalFiles": 0,
      "ownedByMe": true,
      "owner": {
        "userId": 2,
        "name": "admin@nextlabs.com",
        "email": "admin@nextlabs.com"
      },
      "accountType": PROJECT_TRIAL,
      "trialEndTime": 148706243714
    }
  }
}
```



## Upload file

 - **URL:** /rms/rs/project/{projectId}/upload
 - **method:** POST
 - **consumes**: multipart/form-data
      * parameter **file**. It should contain the file content
      * parameter **API-input**. It should contain information about the file metadata.
      * parameter **type** in API-input presents the flag overwrite. If value is 1, you are overwriting an existing file *if* you have EDIT rights. If value is 0, it will upload the file and if same file exists it will display a message "file already exists".


NOTE: Clients should protect the native file locally and send the NXLfile to the server.

For NXL files:
```
#!json
{
   "parameters":{
      "name":"test.js.nxl",
      "parentPathId":"/new3/",
      "type" : 1
   }
}
```

For Native files:
```
#!json
{
   "parameters":{
      "name":"test.js",
      "rightsJSON":"[VIEW,SHARE]",
      "tags" : {
         "Confidentiality" : ["SECRET", "TOP SECRET"]
       },
      "parentPathId":"/new3/"
   }
}
```

 - **produces:** application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1483930368801,
   "results":
    {
      "entry":
       {
           "pathId":"/new3/test-2017-12-31-21-01-01.js.nxl",
           "pathDisplay":"/new3/test-2017-12-31-21-01-01.js.nxl",
           "size":31,
           "name":"test.js",
           "folder":false,
           "lastModified":1483930369000
       }
   }
}

```

- ** Supported Response Code **:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Missing required parameters (tags, pathId, file).|
> |  403 | Access Denied. |
> |  404 | Parent folder missing. |
> |  4001 | Unknown platform.|
> |  4002 | Invalid project. |
> |  4003 | Project folder not found. |
> |  4004 | Invalid expiry. |
> |  4005 | Invalid file name. |
> |  4006 | File already exists. |
> |  4007 | Another User is editing this file. |
> |  5001 | Empty files are not allowed to be uploaded. |
> |  5002 | The nxl file does not belong to this project. |
> |  5003 | Unsupported NXL version. |
> |  5004 | Invalid NXL format. |
> |  5005 | Classification or Rights required. |
> |  6001 | Project Storage Exceeded. |
> |  500 | Socket timeout, IO Error, File Upload Error, Internal Server Error. |

## Get ticket

  - __URL:__ /rms/rs/project/{projectId}/uploadTicket
  - __method:__ GET
  - __produces:__ application/json
    
```
#!json
{
  "statusCode":200,
  "message":"OK",
  "results":{
    "exchange":"mydrive-upload",
    "routing_key":"62-2cd80388-fe5d-410a-b927-805296c392a6"
  }
}
```

## Upload large file

 - **URL:** /rms/rs/project/{projectId}/uploadLargeFile/{routing_key}
 - **method:** POST
 - **consumes**: multipart/form-data
      * parameter **file**. It should contain the file content
      * parameter **API-input**. It should contain information about the file metadata.

NOTE: Clients should protect the native file locally and send the NXLfile to the server.

For NXL files:
```
#!json
{
   "parameters":{
      "name":"test.js.nxl",
      "parentPathId":"/new3/"
   }
}
```

For Native files:
```
#!json
{
   "parameters":{
      "name":"test.js",
      "rightsJSON":"[VIEW,SHARE]",
      "tags" : {
         "Confidentiality" : ["SECRET", "TOP SECRET"]
       },
      "parentPathId":"/new3/"
   }
}
```

 - **produces:** application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1483930368801,
   "results":
    {
      "entry":
       {
           "pathId":"/new3/test-2017-12-31-21-01-01.js.nxl",
           "pathDisplay":"/new3/test-2017-12-31-21-01-01.js.nxl",
           "size":31,
           "name":"test.js",
           "folder":false,
           "lastModified":1483930369000
       }
   }
}
```

## File listing

Search API and fileList API has been merged into fileList as the response are same for both. It will return the usage and quota metadata as well as the file listing metadata

 - **URL:** /rms/rs/project/{projectId}/files?page=1&size=10&orderBy=creationTime&pathId=/{pathId}/ &q=name&searchString=zabbix    
 - **method:** GET

**Supported values for Query parameters**
> |  *Parameter*                  |  *Value*   |
> |-------------------------|------------|
> |  page	    | Page number (start from 1)| 
> |  size     | Number of records to be returned. Positive number. You can use page and size fields to support pagination on client.|
> | orderBy | A comma-separated list of sort keys. Supported fields ***creationTime***, ***name***. Each key sorts ascending by default, but may be reversed with the '-' modifier. Example usage: orderBy=***-creationTime,name***.
> |pathId| The path where you want to list the file. You can use '/' for root folder. If you want to go inside a folder name then put it as /folderName/ (optional - refer to recent file section below)|
> |q| The value will always hold "name" as searching is done based on the file name|
> |searchString| Enter any string here. Search will be done based on name |

 - **Recent files**


If you want to get recent files across folder, you can call the URL without pathId in query parameter

folderLastUpdatedTime in the response will be only available when you pass pathId


     https://rmtest.nextlabs.solutions/rms/rs/project/72/files?page=1&size=6&orderBy=-lastModified


 - **produces:** application/json
```
#!json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1487902124167,
    "results": {
        "usage": 322560,
        "quota": 26843545600,
        "folderLastUpdatedTime": 1499743566000,
        "detail": {      
            "totalFiles": 2,     
            "files": [
                 {
                    "id": "a0725c74-cbd6-4c08-889f-2a2f9336f719",
                    "duid": "BEE96295E8AD9AA51459CFD559BAA0BE",
                    "pathDisplay": "/zabbix_mail-2017-02-23-13-52-28.PNG.nxl",
                    "pathId": "/zabbix_mail-2017-02-23-13-52-28.png.nxl",
                    "name": "zabbix_mail-2017-02-23-13-52-28.PNG.nxl",
                    "lastModified": 1487829150000,
                    "creationTime": 1487829150000,
                    "size": 28672,
                    "folder": false,
                    "fileType": "jpg",
                    "lastModifiedUser": {
                        "userId": 1,
                        "displayName": "rupali.choudhury@nextlabs.com",
                        "email": "rupali.choudhury@nextlabs.com"
                    },
                    "owner": {
                        "userId": 1,
                        "displayName": "rupali.choudhury@nextlabs.com",
                        "email": "rupali.choudhury@nextlabs.com"
                    }
                },
                {
                    "id": "acadf108-ec7c-42ca-aeaf-7b4ed783cd7c",
                    "duid": "3D9C27F90FE34ED7C4643D98BF1A1A3E",
                    "pathDisplay": "/ZabbixArchitecture-2017-02-23-13-52-35.PNG.nxl",
                    "pathId": "/zabbixarchitecture-2017-02-23-13-52-35.png.nxl",
                    "name": "ZabbixArchitecture-2017-02-23-13-52-35.PNG.nxl",
                    "lastModified": 1487829157000,
                    "creationTime": 1487829157000,
                    "size": 293888,
                    "folder": false,
                    "fileType": "jpg",
                    "lastModifiedUser": {
                        "userId": 1,
                        "displayName": "rupali.choudhury@nextlabs.com",
                        "email": "rupali.choudhury@nextlabs.com"
                    },
                    "owner": {
                        "userId": 1,
                        "displayName": "rupali.choudhury@nextlabs.com",
                        "email": "rupali.choudhury@nextlabs.com"
                    }
                }
            ]
        }
    }
}
```

## Create Folder

 - **URL:** /rms/rs/project/{projectId}/createFolder
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters

```
#!json
{
   "parameters":{
      "parentPathId":"/Test/",
      "name":"NewFolder",
      "autorename":"false"
   }
}
```

Note that autorename is not implemented yet.

 - **produces:** application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1477637785817,
   "results":{
               "entry":{
                        "pathId":"/Test/NewFolder/", 
                        "pathDisplay":"/Test/NewFolder/",
                        "size":0,
                        "name":"NewFolder",
                        "folder":true,
                        "lastModified":1489646032122
                       }
          }
}
```

- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Missing required parameters.|
> |  400 | Missing request.|
> |  400 | Invalid tenant.|
> |  400 | Malformed request.|
> |  401 | Missing login parameters.|
> |  401 | Authentication failed.|
> |  4001 | Invalid folder name. |
> |  4002 | Folder already exists.|
> |  4003 | Folder name length limit of 127 characters exceeded.|
> |  500 | Internal Server Error.|

## Delete File/Folder
 - **URL:** /rms/rs/project/{projectId}/delete
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters
```
#!json
{
   "parameters":{
      "pathId":"/folder/"
   }
}
```
 - **produces:** application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1484107685187,
   "results":{
      "pathId":"/folder/",
      "name":"folder"
   }
}
```

## File Metadata
 - **URL:** /rms/rs/project/{projectId}/file/metadata
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters
```
#!json
{
   "parameters":{
      "pathId":"/folder/draft.doc.nxl"
   }
}
```

 - **produces:** application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1484201956960,
   "results":{
      "fileInfo":{
         "pathDisplay":"/folder/draft.doc.nxl",
         "pathId":"/folder/draft.doc.nxl",
         "name":"draft.doc.nxl",
         "size":52736,
         "lastModified":1484104394000,
         "rights":[
            "VIEW"
         ],
         "owner":true,
         "nxl":true,
         "protectionType":0,
         "createdBy": {
            "userId": 1,
            "displayName": "rupali.choudhury@nextlabs.com",
            "email": "rupali.choudhury@nextlabs.com"
         },
         "creationTime": 1484104394000,
         "fileType": "jpg",
         "lastModifiedUser": {
            "userId": 1,
            "displayName": "rupali.choudhury@nextlabs.com",
            "email": "rupali.choudhury@nextlabs.com"
         }
      }
   }
}
```

## Download File

### v2 ###
Pass userId and ticket as header parameters

 - **URL:** /rms/rs/project/{projectId}/v2/download
 - **method:** POST
 - **consumes**: application/json
 - **type**： *0* for normal download, *1* for download for viewer(same with forViewer:true in v1) and *2* for download for offline. Option *0* results in a copy of the file with a new DUID. Options *1* and *2* result in a clone of the file with the same DUID.
 - Server will handle re-encryption. It will use public membership.Server will handle activity log for this operation
```
#!json
{
   "parameters":{
      "start":0,
      "length":1000,
      "pathId":"/user-guide.2016-12-25-14-23-14.xlsx.nxl",
      "type":0
   }
}
```
 
 - start and length are optional   
 - **produces:** application/octet-stream

     **HTTP Response Header**:
   > |  *Key*               | Example              |  *Description*   |
   > |----------------------|----------------------|------------------|
   > |  x-rms-last-modified | `1477637785817`  | File last modified time in Unix format.|
   > |  Content-Disposition | attachment; filename*=UTF-8''user-guide.2016-12-25-14-23-14.xlsx.nxl | Indicating the file content is expected to be displayed as attachment.| 
   > |  x-rms-file-size | 1000 | File size.|

     **Response body**:
	
     File content as stream


### ~~v1~~ (deprecated) ###
Pass userId and ticket as header parameters

 - **URL:** /rms/rs/project/{projectId}/download
 - **method:** POST
 - **consumes**: application/json
 - set *forViewer* property to true if client is downloading this file for rendering in viewer (user is not downloading the file)
 - Server will handle re-encryption. It will use public membership.Server will handle activity log for this operation
```
#!json
{
   "parameters":{
      "start":0,
      "length":1000,
      "pathId":"/user-guide.2016-12-25-14-23-14.xlsx.nxl",
      "forViewer":true
   }
}
```
 
 - start and length are optional   
 - **produces:** application/octet-stream

     **HTTP Response Header**:
   > |  *Key*               | Example              |  *Description*   |
   > |----------------------|----------------------|------------------|
   > |  x-rms-last-modified | `1477637785817`  | File last modified time in Unix format.|
   > |  Content-Disposition | attachment; filename*=UTF-8''user-guide.2016-12-25-14-23-14.xlsx.nxl | Indicating the file content is expected to be displayed as attachment.| 
   > |  x-rms-file-size | 1000 | File size.|

     **Response body**:
	
     File content as stream


## Project Invitation
 - **URL:** /rms/rs/project/{projectId}/invite
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters


NOTE: Maximum length of invitationMsg is 250 characters. It does not have any regular expression as one can type any characters for invitationMsg.   

```
#!json
{
    "parameters":
	{
		"emails": ["example@gmail.com"],
        “invitationMsg” : “This is the new message for my new user”
	}
}
```
 - **produces:** application/json
```
#!json
{
	"statusCode": 200,
	"message": "OK",
	"serverTime": 1484060079827,
	"results": {
		"alreadyInvited": [],
		"nowInvited": [
		"example@gmail.com"
		],
		"alreadyMembers": []
	}
}
```

## Send Invitation Reminder
 - **URL:** /rms/rs/project/sendReminder
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters
```
#!json
{
    "parameters":
	{
		"invitationId": 123
	}
}
```
 - **produces:** application/json
```
#!json
{
	"statusCode": 200,
	"message": "OK",
	"serverTime": 1484060079827
}
```

**Other Status codes in case of failure:**

> |  *statusCode*                  |  *Explanation*   |
> |-------------------------|------------|
> |  400	    | Invalid request parameters or parameters missing|
> |  401	    | Authentication failure|
> |  401	    | user forbidden from sending reminder|
> |  404	    | Invitation not found|
> |  500	    | Internal server error|
> |  4001	    | Invitation expired|
> |  4002	    | Invitation already declined|
> |  4005	    | Invitation already accepted|
> |  4006	    | Invitation already revoked| 


## Revoke Invitation
 - **URL:** /rms/rs/project/revokeInvite
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters
```
#!json
{
    "parameters":
	{
		"invitationId": 123
	}
}
```
 - **produces:** application/json
```
#!json
{
	"statusCode": 200,
	"message": "OK",
	"serverTime": 1484060079827
}
```

**Other Status codes in case of failure:**

> |  *statusCode*                  |  *Explanation*   |
> |-------------------------|------------|
> |  400	    | Invalid request parameters or parameters missing|
> |  401	    | Authentication failure|
> |  401	    | user forbidden from sending reminder|
> |  404	    | Invitation not found|
> |  500	    | Internal server error|
> |  4001	    | Invitation expired|
> |  4002	    | Invitation already declined|
> |  4005	    | Invitation already accepted|
> |  4006	    | Invitation already revoked|


## Accept Invitation
 - **URL:** /rms/rs/project/accept?id={invitation_id}&code={code}
 - **method:** GET
 -  Pass userId and ticket as header parameters
 - **produces:** application/json
```
#!json
{
	"statusCode": 200,
	"message": "OK",
	"serverTime": 1484060079827,
	"results": {
		"projectId":1234,
		"membership":{
		    "id":"m146@t-6abfc8bfe32a451a9fe6e4e54be0c733", // will be changed to sth like "m43@6ed9474a-7fad-4f1d-bde3-76137743ad0a_asdasdasdasd_1"
		    "type":1,
		    "tenantId":"50ff2895-aa30-4ffb-a2d6-7467732de424", // will be changed to "tokenGroupName"
		    "projectId":47
		}
	}
}
```

**Other Status codes in case of failure:**
Incase of failure, projectId will not be returned to client.
> |  *statusCode*                  |  *Explanation*   |
> |-------------------------|------------|
> |  400	    | Invalid request parameters or parameters missing|
> |  401	    | Authentication failure|
> |  404	    | Invitation not found|
> |  500	    | Internal server error|
> |  4001	    | Invitation expired|
> |  4002	    | Invitation already declined|
> |  4003	    | logged in email does not match with invitee email| 
> |  4005	    | Invitation already accepted|


## Decline Invitation
 - **URL:** /rms/rs/project/decline
 - **method:** POST
 - **consumes:** application/x-www-form-urlencoded
 
         id={invitation_id}
         code={code}
         declineReason={reason_max_250_char}  /*optional parameter*/
 
 - **produces:** application/json
```
#!json
{
	"statusCode": 200,
	"message": "OK",
	"serverTime": 1484060079827,
	"results": {
	}
}
```

**Other Status codes in case of failure:**
Incase of failure, projectId will not be returned to client.
> |  *statusCode*                  |  *Explanation*   |
> |-------------------------|------------|
> |  400	    | Invalid request parameters or parameters missing|
> |  404	    | Invitation not found|
> |  500	    | Internal server error|
> |  4001	    | Invitation expired|
> |  4002	    | Invitation already declined|
> |  4005	    | Invitation already accepted|
> |  4006	    | Invitation already revoked|
> |  4007	    | Decline reason too long|


### List Members

 - **URL:** /rms/rs/project/{projectId}/members?page=1&size=10&orderBy=creationTime&q=name&searchString=abraham
 - **method:** GET
 - **consumes:** Pass userId and ticket as header parameters. 

**Supported query parameters are listed below.**


> |  *Parameter*                  |  *Value*   |
> |-------------------------|------------|
> |  page	    | Page number (start from 1)| 
> |  size     | Number of records to be returned. Positive number. You can use page and size fields to support pagination on client.|
> | orderBy | A comma-separated list of sort keys. Supported fields ***creationTime***, ***displayName***. Each key sorts ascending by default, but may be reversed with the '-' modifier. Example usage: orderBy=***-creationTime,displayName***.
> |picture| A boolean value which Indicates where clients need server to return the profile picture of the user or the link to the profile picture |
>|q|Comma Seperated search field . Currently supported are "name" and "email". Example usage: q=name,email |
>|searchString|String which the user searches for. |
      
 - **produces:** application/json
```
#!json
{
  "statusCode": 200,
  "message": "OK",
  "serverTime": 1484905488932,
  "results": {
    "detail": {
      "totalMembers": 2,
      "members": [
        {
          "userId": 1,
          "displayName": "fengchao1993@gmail.com",
          "email": "fengchao1993@gmail.com",
          "creationTime": 1470290028965
        },
        {
          "userId": 2,
          "displayName": "FENG CHAO",
          "email": "chao.feng@nextlabs.com",
          "creationTime": 1470292723663
        }
      ]
    }
  }
}
```

## Remove Member
 - **URL:** /rms/rs/project/{projectId}/members/remove
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters
```
#!json
{
    "parameters":
	{
		"memberId": 10
	}
}
```
 - **produces:** application/json
```
#!json
{
   "statusCode":204,
   "message":"Member successfully removed",
   "serverTime":1484060079827
}
```

- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  204 | Success.|
> |  400 | Missing request.|
> |  400 | Malformed request.|
> |  401| Missing login parameters (userId, ticket).|
> |  401| Authentication failed.|
> |  404| Member not found.|
> |  500| Internal Server Error.|
> |  5001 | Only Project owner can remove a member.|
> |  5002 | Project owner cannot be removed .|

## Get Membership
 - **URL:** /rms/rs/project/{projectId}/membership
 - **method:** GET
 - **consumes:** application/json; Pass userId, ticket, clientId, platformId as header parameters
https://sudong.nextlabs.com/rms/rs/project/5/membership

This also checks for dynamic abac membership and if so, the id will be like: user0@t-c10c9c579a834125b07a2d659a002fc8
```
#!json

{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1524115844781,
    "results": {
        "membership": {
            "id": "m10@t-d0fb1eabcfc94b20984c5816071bc004", // will be changed to sth like "m43@6ed9474a-7fad-4f1d-bde3-76137743ad0a_asdasdasdasd_1"
            "type": 1,
            "tenantId": "5994bdab-880c-4704-8a84-2f90d884d572", // will be change to "tokenGroupName"
            "projectId": 5
        }
    }
}

```

Otherwise returns not found (so clients check status code)
https://sudong.nextlabs.com/rms/rs/project/500/membership

```
#!json

{
    "statusCode": 404,
    "message": "Not Found.",
    "serverTime": 1524060313212
}

```

### Get Member Details

 - **URL:** /rms/rs/project/{projectId}/member/{memberId}
 - **method:** GET
 - **consumes:** Pass userId and ticket as header parameters. 
 - **produces:** application/json
```
#!json
{
  "statusCode": 200,
  "message": "OK",
  "serverTime": 1484905488932,
  "results": {
    "detail": {
      "userId": 1,
      "displayName": "fengchao1993@gmail.com",
      "email": "fengchao1993@gmail.com",
      "creationTime": 1470290028965,
      "inviterDisplayName": "Xifeng",
      "inviterEmail": "xifeng.zheng@nextlabs.com"
    }
  }
}
```


### List Pending Invitations For a Project

 - **URL:** /rms/rs/project/{projectId}/invitation/pending?page=1&size=10&orderBy=creationTime&q=email&searchString=abraham
 - **method:** GET
 - **consumes:** Pass userId and ticket as header parameters. 

**Supported query parameters are listed below.**


> |  *Parameter*                  |  *Value*   |
> |-------------------------|------------|
> |  page	    | Page number (start from 1)| 
> |  size     | Number of records to be returned. Positive number. You can use page and size fields to support pagination on client.|
> | orderBy | A comma-separated list of sort keys. Supported fields ***inviteTime***, ***displayName***. Each key sorts ascending by default, but may be reversed with the '-' modifier. Example usage: orderBy=***-inviteTime,displayName***. ***displayName*** here represents for invitee's email.
>|q|Search field . Currently only support "email"|
>|searchString|String which the user searches for. |
      
 - **produces:** application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1484905488932,
   "results":{
      "pendingList":{
         "totalInvitations":2,
         "invitations":[
            {
               "invitationId":1,
               "inviteeEmail":"fengchao1993@gmail.com",
               "inviterDisplayName":"Xifeng",
               "inviterEmail":"xifeng.zheng@nextlabs.com",
               "inviteTime":1470290028965
            },
            {
               "invitationId":2,
               "inviteeEmail":"rmsuser0@gmail.com",
               "inviterDisplayName":"Xifeng",
               "inviterEmail":"xifeng.zheng@nextlabs.com",
               "inviteTime":1470290028965
            }
         ]
      }
   }
}
```

### List Pending Invitations For a User 

The *invitationId* and *code* in the response can be used to either accept or decline the invitation

 - **URL:** /rms/rs/project/user/invitation/pending
 - **method:** GET
 - **consumes:** Pass userId and ticket as header parameters. 

 - **produces:** application/json

NOTE: Maximum length of invitationMsg is 250 characters. It does not have any regular expression as one can type any characters for invitationMsg. 

```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1484905488932,
   "results":{
      "totalPendingInvitations":2
      "pendingInvitations":[
         {
            "invitationId":1,
            "inviteeEmail":"fengchao1993@gmail.com",
            "inviterDisplayName":"Xifeng",
            "inviterEmail":"xifeng.zheng@nextlabs.com",
            "inviteTime":1470290028965,
            "code":"CCC444E4930C07D2F6C2ADD8B0FB327CD469F412CFCEBF3C2F0DFDC36B8E1758",
            “invitationMsg” : “This is an invite message by ABC project”,
            "project":{
               "id":19,
               "name":"My next project",
               "description":"my next project",
               "displayName":"My next project",
               "creationTime":1485227101163,
               "owner":{
                  "userId":1,
                  "name":"Abraham Lincoln",
                  "email":"rmsuser0@gmail.com"
               }
            }
         },
         {
            "invitationId":2,
            "inviteeEmail":"rmsuser0@gmail.com",
            "inviterDisplayName":"Xifeng",
            "inviterEmail":"xifeng.zheng@nextlabs.com",
            "inviteTime":1470290028965,
            "code":"CCC444E4930C07D2F6C2ADD8B0FB327CD469F412CFCEBF3C2F0DFDC36B8E1758",
            “invitationMsg” : “This is an invite message by XYZ project”,
            "project":{
               "id":29,
               "name":"A confidential project",
               "description":"Undisclosed",
               "displayName":"A confidential project",
               "creationTime":1485227101163,
               "owner":{
                  "userId":1,
                  "name":"Abraham Lincoln",
                  "email":"rmsuser0@gmail.com"
               }
            }
         }
      ]
   }
}
```

### Delete Project

This will delete the project, members, activity logs, invitations and the files.
Currently, tenant and keys are not deleted. 
**This cannot be reversed.** You will be prompted to confirm deletion again. (Yes/No)


For Windows
```
$ cd webapps
$ java -classpath "rms\WEB-INF\classes;rms\WEB-INF\lib\*;..\lib\*" com.nextlabs.rms.Main --cmd=DeleteProject --projectId={id}
```

For Unix
```
$ cd webapps
$ java -classpath "rms/WEB-INF/classes:rms/WEB-INF/lib/*:../lib/*" com.nextlabs.rms.Main --cmd=DeleteProject --projectId={id}
```

For RMTEST
```
$ cd /var/lib/tomcat7/webapps
$ java -classpath "rms/WEB-INF/classes:rms/WEB-INF/lib/*:/usr/share/tomcat7/lib/*" com.nextlabs.rms.Main --cmd=DeleteProject --projectId={id}
```

## Folder MetaData

 - **URL:** /rms/rs/project/{projectId}/folderMetadata?page=1&size=10&orderBy=creationTime&pathId=/{pathId}/ &lastModified=1487829150000  
 - **method:** GET

**Supported values for Query parameters**
> |  *Parameter*                  |  *Value*   |
> |-------------------------|------------|
> |  page	    | Page number (start from 1)| 
> |  size     | Number of records to be returned. Positive number. You can use page and size fields to support pagination on client.|
> | orderBy | A comma-separated list of sort keys. Supported fields ***creationTime***, ***name***. Each key sorts ascending by default, but may be reversed with the '-' modifier. Example usage: orderBy=***-creationTime,name***.
> |pathId| The path where you want to list the file. If you want to go inside a folder name then put it as /folderName/.|
> |lastModified| Last modified time of the folder which is sent in response of the list files API

 - **produces:** application/json
```
#!json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1487902124167,
    "results": {
        "usage": 322560,
        "quota": 26843545600,
        "folderLastUpdatedTime": 1499743566000,
        "detail": {    
            "totalFiles": 2,
            "files": [
                {
                    "id": "a0725c74-cbd6-4c08-889f-2a2f9336f719",
                    "duid": "BEE96295E8AD9AA51459CFD559BAA0BE",
                    "pathDisplay": "/zabbix_mail-2017-02-23-13-52-28.PNG.nxl",
                    "pathId": "/zabbix_mail-2017-02-23-13-52-28.png.nxl",
                    "name": "zabbix_mail-2017-02-23-13-52-28.PNG.nxl",
                    "lastModified": 1487829150000,
                    "creationTime": 1487829150000,
                    "size": 28672,
                    "folder": false,
                    "owner": {
                        "userId": 1,
                        "displayName": "rupali.choudhury@nextlabs.com",
                        "email": "rupali.choudhury@nextlabs.com"
                    }
                },
                {
                    "id": "acadf108-ec7c-42ca-aeaf-7b4ed783cd7c",
                    "duid": "3D9C27F90FE34ED7C4643D98BF1A1A3E",
                    "pathDisplay": "/ZabbixArchitecture-2017-02-23-13-52-35.PNG.nxl",
                    "pathId": "/zabbixarchitecture-2017-02-23-13-52-35.png.nxl",
                    "name": "ZabbixArchitecture-2017-02-23-13-52-35.PNG.nxl",
                    "lastModified": 1487829157000,
                    "creationTime": 1487829157000,
                    "size": 293888,
                    "folder": false,
                    "owner": {
                        "userId": 1,
                        "displayName": "rupali.choudhury@nextlabs.com",
                        "email": "rupali.choudhury@nextlabs.com"
                    }
                }
            ]
        }
    }
}
```

- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  304 | Folder not modified.|

## Reclassify file

 - **URL:** /rms/rs/project/{projectId}/file/classification
 - **method:** PUT
 - **headers** Pass userId, clientId and ticket as header parameters
 - **consumes** application/json
```
#!json
{
    "parameters": {
        "fileName":"test-2019-03-22-10-36-44.txt.nxl",
        "parentPathId":"/folderName/",
        "fileTags": "{\"AdminCategory\":[\"Cat0\"],\"NormalCategory\":[\"NC0\"]}"
    }
}
```

 - **produces:** application/json
```
#!json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1553565911266,
    "results": {
        "entry": {
            "pathId": "/foldername/test-2019-03-22-10-36-44.txt.nxl",
            "pathDisplay": "/folderName/test-2019-03-22-10-36-44.txt.nxl",
            "size": 40960,
            "name": "test-2019-03-22-10-36-44.txt.nxl",
            "folder": false,
            "lastModified": 1553565911248
        }
    }
}
```

- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Missing required parameters.(fileName, parentPathId)|
> |  400 | Missing request.|
> |  400 | Invalid project.|
> |  400 | Malformed request.|
> |  400 | Missing checksum.|
> |  403 | Reclassification is only for company rights protected files.|
> |  4005 | Invalid file name.|
> |  500 | Internal Server Error.|