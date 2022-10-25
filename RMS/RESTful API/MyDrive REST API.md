# RMS MyDrive REST API

RMS exposes the following web services to its in-built repository:

 - [list files](#markdown-header-list-files)
 - [search](#markdown-header-search)
 - [download file](#markdown-header-download-file)
 - [create folder](#markdown-header-create-folder)
 - [upload file](#markdown-header-upload-file)
 - [get ticket](#markdown-header-get-ticket)
 - [upload large file](#markdown-header-upload-large-file)
 - [delete item](#markdown-header-delete-item)
 - [create public share URL](#markdown-header-create-public-share-url)
 - [copy](#markdown-header-copy)
 - [move](#markdown-header-move)
 - [get storage used](#markdown-header-get-storage-used)
 - [folder metadata ](#markdown-header-folder-metadata)

This API is intended to be consumed by RMC/RMD.  Currently, the repository is backed by Amazon's S3 storage, though we may expose other storage providers at a later date.

##List Files

 - **URL:** /rms/rs/myDrive/list
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters
```
#!json
{
   "parameters":{
      "pathId":"/one/"
   }
}
```
 - **produces:** application/json
```
#!json
{
  "statusCode": 200,
  "message": "OK",
  "serverTime": 1489645368373,
  "results": {
    "entries": [
      {
        "pathId": "/one/new/",
        "pathDisplay": "/one/new",
        "name": "new",
        "folder": true,
        "lastModified": 1488180295000
      },
      {
        "pathId": "/one/two/",
        "pathDisplay": "/one/two",
        "name": "two",
        "folder": true,
        "lastModified": 1488180295000
      },
      {
        "pathId": "/one/hellorunning.txt",
        "pathDisplay": "/one/hellorunning.txt",
        "size": 16682,
        "name": "hellorunning.txt",
        "folder": false,
        "lastModified": 1488180295000
      },
      {
        "pathId": "/one/yajsw.jnlp",
        "pathDisplay": "/one/yajsw.JNLP",
        "size": 995,
        "name": "yajsw.JNLP",
        "folder": false,
        "lastModified": 1488178664000
      }
    ]
  }
}
```


- **Supported Response Status Code**:

> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Malformed request.|
> |  401 | Authentication failed.|
> |  500 | Internal Server Error.|

##Search

 - **URL:** /rms/rs/myDrive/search
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters

```
#!json
{
   "parameters":{
      "query":"doc"   
   }
}
```

 - **produces:** application/json
```
#!json
{
  "statusCode": 200,
  "message": "OK",
  "serverTime": 1489645912319,
  "results": {
    "matches": [
      {
        "pathId": "/onetwofolder/test/helloo.doc",
        "pathDisplay": "/OneTwoFolder/teSt/hEllOO.doc",
        "size": 331264,
        "name": "hEllOO.doc",
        "folder": false,
        "lastModified": 1489038187000
      },
      {
        "pathId": "/onetwofolder/test/hello99o.doc",
        "pathDisplay": "/OneTwoFolder/teSt/hEllO99O.doc",
        "size": 331264,
        "name": "hEllO99O.doc",
        "folder": false,
        "lastModified": 1489115873000
      }
    ]
  }
}
```

- **Supported Response Status Code**:

> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Malformed request.|
> |  401 | Authentication failed.|
> |  500 | Internal Server Error.|

##Download File

 - **URL:** /rms/rs/myDrive/download
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters

```
#!json
{
   "parameters":{
      "pathId":"/finance/expense-report.xls",
      "start":0,
      "length":1000
   }
}
```

 - **produces:** application/octet-stream
       * HTTP Header
       
       > |  *Header Name*          | *Example*        |  *Description*   |
       > |-------------------------|----------------|------------|
       > |  x-rms-last-modified	   | 1477637785817 | Last modified time in Unix format.| 
       > |  Content-Disposition    | attachment; filename*=UTF-8''expense-report.xls | Indicate the content with filename.|
       > |  x-rms-file-size | 1000 | The size of the file.|
  
  
       * Response Body

         File content as stream

**Notes:**

 - The `start` and `length` parameters specify the byte offset and length in bytes to be read from the file.
 - If _either_ the `start` or `length` parameters be missing, then the entire file will be downloaded
 - The `size` returned is the actual number of bytes read, which may be less than the requested range if the file were to terminate before the `end` could be reached.


##Create Folder

 - **URL:** /rms/rs/myDrive/createFolder
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters
```
#!json
{
    "parameters": {
     "parentPathId": "/onetwofolder/test/",
     "name":"AndroidRMC"
    }
}
```

 - **produces:** application/json
```
#!json
{
  "statusCode": 200,
  "message": "OK",
  "serverTime": 1489646045418,
  "results": {
    "entry": {
      "pathId": "/onetwofolder/test/androidrmc/",
      "pathDisplay": "/OneTwoFolder/teSt/AndroidRMC/",
      "size": 0,
      "name": "AndroidRMC",
      "folder": true,
      "lastModified": 1489646045389
    }
  }
}
```

- **Supported Response Status Code**:

> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Malformed request.|
> |  401 | Authentication failed.|
> |  404 | Parent Folder missing.|
> |  4001 | Invalid folder name. |
> |  4002 | Folder already exists.|
> |  4003 | Folder name limit of 127 characters exceeded.|
> |  500 | Internal Server Error.|


**Notes:**

- The folder name will be the string after the last slash. So please do not put a slash at the end.
- Folder name can contain a-z, A-Z, hyphens, underscores, spaces and character sets like Chinese character, etc. Folder name cannot exceed 127 characters. Folder name cannot contain special characters like ```, . $ ! @ $ % ^ & * ( ) + = / > <``` etc.
- Invalid folder name will be rejected with status code **4001**
- FYI, the regex for verifying folder name ```/^[\\u00C0-\\u1FFF\\u2C00-\\uD7FF\\w- ]*$/```

##Get Ticket

Pass userId and ticket as header parameters

 - __URL:__ /rms/rs/myDrive/uploadTicket
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

##Upload Large File

Pass userId and ticket as header parameters

 - **URL:** /rms/rs/myDrive/uploadLargeFile/{routring_key}
 - **method:** POST
 - **consumes**: multipart/form-data
      * parameter file should contain the file content
      * parameter API-input should have the following json


```
#!json
{     
	"parameters": 
	{      
		"parentPathId": "/onetwofolder/test/", 
		"name":"hEllO99O.doc"   
	} 
}
```

 - **produces:** application/json
```
#!json
{
  "statusCode": 200,
  "message": "OK",
  "serverTime": 1489646330645,
  "results": {
    "entry": {
      "pathId": "/onetwofolder/test/prd.doc",
      "pathDisplay": "/OneTwoFolder/teSt/PRD.doc",
      "size": 58990,
      "name": "PRD.doc",
      "folder": false,
      "lastModified": 1489646329000
    },
    "usage": 32991613,
    "quota": 1073741824,
    "myVaultUsage": 18327552
  }
}
```

- **Supported Response Status Code**:

> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Malformed request or Missing required parameters or Missing routing key.|
> |  401 | Authentication failed.|
> |  404 | Parent Folder missing.|
> |  4001 | Invalid filename.|
> |  4002 | File already exists.|
> |  6001 | Drive Storage Exceeded.|
> |  500 | Internal Server Error.|

##Upload File

Pass userId and ticket as header parameters

 - **URL:** /rms/rs/myDrive/uploadFile
 - **method:** POST
 - **consumes**: multipart/form-data
      * parameter file should contain the file content
      * parameter API-input should have the following json


```
#!json
{     
	"parameters": 
	{      
		"parentPathId": "/onetwofolder/test/", 
		"name":"hEllO99O.doc"   
	} 
}
```

 - **produces:** application/json
```
#!json
{
  "statusCode": 200,
  "message": "OK",
  "serverTime": 1489646330645,
  "results": {
    "entry": {
      "pathId": "/onetwofolder/test/prd.doc",
      "pathDisplay": "/OneTwoFolder/teSt/PRD.doc",
      "size": 58990,
      "name": "PRD.doc",
      "folder": false,
      "lastModified": 1489646329000
    },
    "usage": 32991613,
    "quota": 1073741824,
    "myVaultUsage": 18327552
  }
}
```

- **Supported Response Status Code**:

> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Malformed request or Missing required parameters.|
> |  401 | Authentication failed.|
> |  404 | Parent Folder missing.|
> |  4001 | Invalid filename.|
> |  4002 | File already exists.|
> |  6001 | Drive Storage Exceeded.|
> |  500 | Internal Server Error.|

##Delete Item

 - **URL:** /rms/rs/myDrive/delete
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters
```
#!json
{
   "parameters":{
      "pathId":"/b.pptx"
   }
}
```

- **produces**: application/json
```
#!json
{
  "statusCode": 200,
  "message": "OK",
  "serverTime": 1489646387732,
  "results": {
    "usage": 32660349,
    "quota": 1073741824,
    "name": "hEllO9009O.doc",
    "pathId": "/onetwofolder/test/hello9009o.doc",
    "myVaultUsage": 18327552
  }
}
```

- **Supported Response Status Code**:

> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Malformed request.|
> |  401 | Authentication failed.|
> |  403 | Forbidden Operation.(deleting root folder is not allowed)|
> |  404 | File not found.|
> |  500 | Internal Server Error.|

##Create Public Share URL

- **URL**: /rms/rs/myDrive/PublicUrl
- **method**: POST
- **consume**: application/json; Pass userId and ticket as header parameters
```
#!json
{
   "parameters":{
      "pathId":"/finance/expense-report.xls"
   }
}
```

- **produces**: application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1477623263276,
   "results":{
      "url":"https://abce.com/file=1254154?...........<tokens>.........."
   }
}
```

- **Supported Response Status Code**:

> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Malformed request.|
> |  401 | Authentication failed.|
> |  500 | Internal Server Error.|

**Notes:**

- The output should not only have the file but also the token to access it
- An incorrect path (e.g. filename misspelled) will usually still produce an URL.  This edge case has been handled in the implementation.

##Copy

- **URL**: /rms/rs/myDrive/copy
- **method**: POST
- **consumes**: application/json; Pass userId and ticket as header parameters
```
#!json
{
   "parameters":{
      "srcPathId":"/src/a.pptx",
      "destPathId":"/dest/a.pptx"
   }
}
```

- **produces**: application/json
```
#!json
{
   "statusCode":"200",
   "message":"Accepted",
   "serverTime":"1477623263276",
   "results":{
      "details":[
         {
            "pathId":"/dest/a.pptx",
            "size":12345,
            "isDirectory":"false",
            "success":"true"
         }
      ],
      "usage":23454332
   }
}
```

Caller will have to check the success field of each object in details array to see what objects have been copied

- **Supported Response Status Code**:

> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Malformed request.|
> |  401 | Authentication failed.|
> |  6001| Drive Storage Exceeded.|
> |  500 | Internal Server Error.|

##Move

- **URL**: /rms/rs/myDrive/move
- **method**: POST
- **consumes**: application/json; Pass userId and ticket as header parameters
```
#!json
{
   "parameters":{
      "srcPathId":"/src/a.pptx",
      "destPathId":"/dest/a.pptx"
   }
}
```

- **produces**: application/json
```
#!json
{
   "statusCode":"200",
   "message":"OK",
   "serverTime":"1477623263276",
   "results":{
      "details":[
         {
            "pathId":"/dest/a.pptx",
            "size":12345,
            "isDirectory":"false",
            "success":"true"
         }
      ]
   }
}
```

Caller will have to check the success field of each object in details array to see what objects have been moved

- **Supported Response Status Code**:

> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Malformed request.|
> |  401 | Authentication failed.|
> |  403 | Forbidden Operation.(moving root folder is not allowed)|
> |  500 | Internal Server Error.|

##Get storage used

- **URL**: /rms/rs/myDrive/getUsage
- **method**: POST
- **consumes**: application/json
```
#!json
{
   "parameters":{
      "userId":1,
      "ticket":"5B6ED835E293925D1D9ED38C8C9489B8"
   }
}
```

- **produces**: application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1479283719376,
   "results":{
      "usage":2502740,
      "quota":1000000000
   }
}
```

- **Supported Response Status Code**:

> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Malformed request.|
> |  401 | Authentication failed.|
> |  500 | Internal Server Error.|

##Folder Metadata

 - **URL:** /rms/rs/myDrive/folderMetadata
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters
```
#!json
{
   "parameters":{
      "pathId":"/one/",
      "lastModifiedTime":1489645368373
   }
}
```
 - **produces:** application/json
```
#!json
{
  "statusCode": 200,
  "message": "OK",
  "serverTime": 1489645368373,
  "results": {
    "entries": [
      {
        "pathId": "/one/new/",
        "pathDisplay": "/one/new",
        "name": "new",
        "folder": true,
        "lastModified": 1488180295000
      },
      {
        "pathId": "/one/two/",
        "pathDisplay": "/one/two",
        "name": "two",
        "folder": true,
        "lastModified": 1488180295000
      },
      {
        "pathId": "/one/hellorunning.txt",
        "pathDisplay": "/one/hellorunning.txt",
        "size": 16682,
        "name": "hellorunning.txt",
        "folder": false,
        "lastModified": 1488180295000
      },
      {
        "pathId": "/one/yajsw.jnlp",
        "pathDisplay": "/one/yajsw.JNLP",
        "size": 995,
        "name": "yajsw.JNLP",
        "folder": false,
        "lastModified": 1488178664000
      }
    ]
  }
}
```


- **Supported Response Status Code**:

> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  304 | Folder not modified.|
> |  400 | Malformed request.|
> |  401 | Authentication failed.|
> |  500 | Internal Server Error.|