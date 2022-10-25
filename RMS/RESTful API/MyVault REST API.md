# RMS MyVault REST API

RMS exposes the following web services to access MyVault for the following operations: 

 - [get ticket](#markdown-header-get-ticket)
 - [upload file](#markdown-header-upload-file)
 - [upload large file](#markdown-header-upload-large-file)
 - [file listing](#markdown-header-list-file)
 - [delete file](#markdown-header-delete-file)
 - [download file](#markdown-header-download-file)
 - [file metadata](#markdown-header-file-metadata)

This API is intended to be consumed by RMC/RMD.  Currently, MyVault is backed by Amazon's S3 storage.

##Get Ticket

Pass userId and ticket as header parameters

 - __URL:__ /rms/rs/myVault/uploadTicket
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

## Upload File

Client should upload the protected file to MyVault as soon as a user protects a file.
Pass userId and ticket as header parameters

 - **URL:** /rms/rs/myVault/upload
 - **method:** POST
 - **consumes**: multipart/form-data
      * parameter file (only NXL file can be uploaded) should contain the file content
      * parameter API-input(only NXL file can be uploaded) should contain information about the file's origin.

```
#!json
{
   "parameters":{
      "srcPathId":"/test/abc.txt",
      "srcPathDisplay":"/test/ABC.txt",
      "srcRepoId":"37672637863763232",
      "srcRepoName":"Xinxin Drive",
      "srcRepoType":"DROPBOX"
   }
}
```

 - **produces:** application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1477623263276,
   "results":{
      "name":"ABC.txt.nxl",
      "lastModified":1477637785817,
      "size":7212,
	  "duid": "30C7EE51ABF57D59E77A5DEDFDA77A9A",
	  "pathId": "/nxl_myvault_nxl/abc.txt.nxl",
	  "pathDisplay": "/ABC.txt.nxl"
   }
}
```

- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Missing required parameters (userId, ticket).|
> |  400 | Missing JSON request in body.|
> |  400 | Invalid tenant.|
> |  401 | Authentication failed.|
> |  403 | Access denied (This error occurred when trying to upload NXL file and current user is not document steward). |
> |  500 | Internal Server Error.|
> |  5001 | Invalid NXL format.|
> |  5002 | Invalid repository metadata.|
> |  5003 | Invalid filename.|
> |  5004 | Invalid file extension.|
> |  5005 | Invalid DUID.|

**Notes:** When uploading local files to MyVault through this API endpoint, use input similar to the following:

```
#!json
{
   "parameters":{
      "srcPathDisplay":"/test/ABC.txt",
      "srcRepoName":"Local",
      "srcRepoType":"Local"
   }
}
```

Note that the following two parameters were omitted:

 - srcPathId
 - srcRepoId

These two parameters do not have a context when uploading a local file, because the path ID is handled internally within MyVault and cannot be directly specified by the consumer of the API.  Similarly, the repo ID also does not have a context, as the implementation of MyVault's repo is beyond control of the consumer.

Please use this style of input to ensure consistent behavior with RMS' handling of local files.

## Upload Large File

Client should upload the protected file to MyVault as soon as a user protects a file.
Pass userId and ticket as header parameters

 - **URL:** /rms/rs/myVault/uploadLargeFile/{routing_key}
 - **method:** POST
 - **consumes**: multipart/form-data
      * parameter file (only NXL file can be uploaded) should contain the file content
      * parameter API-input(only NXL file can be uploaded) should contain information about the file's origin.

```
#!json
{
   "parameters":{
      "srcPathId":"/test/abc.txt",
      "srcPathDisplay":"/test/ABC.txt",
      "srcRepoId":"37672637863763232",
      "srcRepoName":"Xinxin Drive",
      "srcRepoType":"DROPBOX"
   }
}
```

 - **produces:** application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1477623263276,
   "results":{
      "name":"ABC.txt.nxl",
      "lastModified":1477637785817,
      "size":7212,
	  "duid": "30C7EE51ABF57D59E77A5DEDFDA77A9A",
	  "pathId": "/nxl_myvault_nxl/abc.txt.nxl",
	  "pathDisplay": "/ABC.txt.nxl"
   }
}
```

- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Missing required parameters (userId, ticket).|
> |  400 | Missing JSON request in body.|
> |  400 | Missing routing key.|
> |  400 | Invalid tenant.|
> |  401 | Authentication failed.|
> |  403 | Access denied (This error occurred when trying to upload NXL file and current user is not document steward). |
> |  500 | Internal Server Error.|
> |  5001 | Invalid NXL format.|
> |  5002 | Invalid repository metadata.|
> |  5003 | Invalid filename.|
> |  5004 | Invalid file extension.|
> |  5005 | Invalid DUID.|

**Notes:** When uploading local files to MyVault through this API endpoint, use input similar to the following:

```
#!json
{
   "parameters":{
      "srcPathDisplay":"/test/ABC.txt",
      "srcRepoName":"Local",
      "srcRepoType":"Local"
   }
}
```

Note that the following two parameters were omitted:

 - srcPathId
 - srcRepoId

These two parameters do not have a context when uploading a local file, because the path ID is handled internally within MyVault and cannot be directly specified by the consumer of the API.  Similarly, the repo ID also does not have a context, as the implementation of MyVault's repo is beyond control of the consumer.

Please use this style of input to ensure consistent behavior with RMS' handling of local files.

## Delete File

Pass userId and ticket as header parameters. Pass duid as path parameter.
File will be **revoked** before deletion and the activity will be logged by the server.

 - **URL:** /rms/rs/myVault/{duid}/delete
 - **method:** POST
 - **consumes**: application/json

```
#!json
{
   "parameters":{
      "pathId":"/nxl_myvault_nxl/deletefile.txt"
   }
}
```
    
 - **produces:** application/json
```
#!json
{
   "statusCode":200,
   "message":"OK"
}
```

- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  304 | File has been revoked.|
> |  400 | Missing required parameters (userId, ticket).|
> |  400 | Unknown platform.|
> |  400 | Invalid tenant.|
> |  400 | Unable to revoke document.|
> |  401 | Authentication failed.|
> |  403 | Access Denied.(the duid provided does not match with duid in nxl file)|
> |  403 | Forbidden Operation.(deleting root folder is not allowed)|
> |  404 | File not found.|
> |  500 | Internal Server Error.|
> |  5001 | File is deleted.|

**Note**: Client doesn't need to create activity log for this operation.

## List File

Pass userId and ticket as header parameters

 - **URL:** /rms/rs/myVault?page=1&size=10&orderBy=creationTime&filter=activeTransaction&q.fileName=document
 - **method:** GET
 - **consumes**:

**Supported values for query parameters**
> |  *Parameter*            |  *Description*   |
> |-------------------------|------------------|
> | page	  | Page number. Positive number.| 
> | size      | Maximum number of data to return. You can use page and size fields to support pagination on client.|
> | orderBy   | A comma-separated list of sort keys. Valid keys are ***fileName***, ***creationTime***, ***size***. Each key sorts ascending by default, but may be reversed with the '-' modifier. Example usage: ***orderBy=-creationTime, fileName***.|
> |filter     | Filter transaction type. Valid keys are ***activeTransaction***, ***allShared***, ***allFiles***, ***protected***,***deleted***,***revoked***.|
> |q.fileName | Search by filename.|
    
 - **produces:** application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1484526776414,
   "results":{
      "detail":{
         "totalFiles":2,
         "files":[
            {
               "pathId":"/nxl_myvault_nxl/get started with dropbox-2017-01-13-14-30-28.pdf.nxl",
               "pathDisplay":"/Get Started with Dropbox-2017-01-13-14-30-28.pdf.nxl",
               "repoId":"2c5ee284-8156-483e-abc6-f030c9f037e1",
               "sharedOn":1484289028934,
               "sharedWith": [
                       "abraham.lincoln@qapf1.qalab01.nextlabs.com",
                       "prateek.kumar@nextlabs.com",
                       "john.tyler@qapf1.qalab01.nextlabs.com",
                       "barack.obama@qapf1.qalab01.nextlabs.com"
               ],
               "name":"Get Started with Dropbox-2017-01-13-14-30-28.pdf.nxl",
               "duid":"F8DC0C3FB945B3671CE994DD7D82761A",
               "revoked":false,
               "deleted":false,
               "shared":false,
               "size":708608,
               "customMetadata":{
                  "SourceRepoType":"DROPBOX",
                  "SourceFilePathDisplay":"/beacon/Get Started with Dropbox.pdf",
                  "SourceFilePathId":"id:GiWQfAKOyAAAAAAAAAAAAg",
                  "SourceRepoName":"DB",
                  "SourceRepoId":"a081a600-2a8a-4181-99d6-b3b7d57f3707"
               }
            },
            {
               "pathId":"/nxl_myvault_nxl/helloworld-2017-01-13-14-29-43.pptx.nxl",
               "pathDisplay":"/HelloWorld-2017-01-13-14-29-43.pptx.nxl",
               "repoId":"2c5ee284-8156-483e-abc6-f030c9f037e1",
               "sharedOn":1484288983946,
               "name":"HelloWorld-2017-01-13-14-29-43.pptx.nxl",
               "duid":"467BC12F710CB27D028BE0FD9989903F",
               "revoked":false,
               "deleted":false,
               "shared":true,
               "size":55808,
               "customMetadata":{
                  "SourceRepoType":"DROPBOX",
                  "SourceFilePathDisplay":"/HelloWorld.pptx",
                  "SourceFilePathId":"id:GiWQfAKOyAAAAAAAAAAALg",
                  "SourceRepoName":"DB",
                  "SourceRepoId":"a081a600-2a8a-4181-99d6-b3b7d57f3707"
               }
            }
         ]
      }
   }
}
```

## File Metadata
Pass userId and ticket as header parameters

 - **URL:** /rms/rs/myVault/{duid}/metadata
 - **method:** POST
 - **consumes**: application/json

```
#!json
{
   "parameters":{
      "pathId":"/nxl_myvault_nxl/get started with dropbox-2017-01-13-14-30-28.pdf.nxl"
   }
}

```
 
 - **produces**: application/json

```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1484527953665,
   "results":{
      "detail":{
         "name":"Get Started with Dropbox-2017-01-13-14-30-28.pdf.nxl",
         "recipients":[
            "user1@nextlabs.com",
            "user2@nextlabs.com"
         ],
         "fileLink":"https://rmtest.nextlabs.solutions/rms/main.jsp#/personal/viewSharedFile?d=b8785cd6-632d-405e-973c-27eca6b905a7&c=EEF638B42F19D674D87FC8AE3ADF0C598818FECA6C49A9F34646A8E409F45359",
         "protectedOn":1484289028934,
         "sharedOn":1484527940420,
         "rights":[
            "VIEW"
         ],
         "shared":true,
         "deleted":false,
         "revoked":false,
         "validity":{
            "startDate":1509638400000,
            "endDate":1512316799999
         },
         "protectionType":0
      }
   }
}
```
- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.| 
> |  400 | Missing JSON request in body.| 
> |  400 | Missing required parameters (userId, ticket, filePathId).|
> |  400 | Malformed JSON request.|
> |  401 | Authentication failed.|
> |  404 | Unable to find file metadata (either DUID is not found, or wrong filePathId).|
> |  500 | Internal Server Error.|

## Download File

### v2 ###
Pass userId and ticket as header parameters

 - **URL:** /rms/rs/myVault/v2/download
 - **method:** POST
 - **consumes**: application/json
 - **type**: *0* for normal download, *1* for download for viewer(same with forViewer:true in v1) and *2* for download for offline
```
#!json
{
   "parameters":{
      "start":0,
      "length":1000,
      "pathId":"/nxl_myvault_nxl/user-guide.2016-12-25-14-23-14.xlsx.nxl",
      "type":0
   }
}
```
    
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

 - **URL:** /rms/rs/myVault/download
 - **method:** POST
 - **consumes**: application/json
 - set *forViewer* property to true if client is downloading this file for rendering in viewer (user is not downloading the file)
```
#!json
{
   "parameters":{
      "start":0,
      "length":1000,
      "pathId":"/nxl_myvault_nxl/user-guide.2016-12-25-14-23-14.xlsx.nxl",
      "forViewer":true
   }
}
```
    
 - **produces:** application/octet-stream

     **HTTP Response Header**:
   > |  *Key*               | Example              |  *Description*   |
   > |----------------------|----------------------|------------------|
   > |  x-rms-last-modified | `1477637785817`  | File last modified time in Unix format.|
   > |  Content-Disposition | attachment; filename*=UTF-8''user-guide.2016-12-25-14-23-14.xlsx.nxl | Indicating the file content is expected to be displayed as attachment.| 
   > |  x-rms-file-size | 1000 | File size.|

     **Response body**:
	
     File content as stream