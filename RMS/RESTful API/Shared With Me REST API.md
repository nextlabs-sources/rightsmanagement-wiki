# Shared With Me REST API

The shared with me provides the following services:

* [List files](#markdown-header-list-files)

* [Reshare file](#markdown-header-reshare-file)

* [Download file](#markdown-header-download-file)

* [Get Metadata](#markdown-header-get-metadata)

## List files

List files will **list** all the files that are shared with the particular user. One can also **sort** based on file name, file size, shared by, shared date and **search** files based on file names

- **URL:** /rms/rs/sharedWithMe/list?page=1&size=10&orderBy=sharedBy,-sharedDate&q=name&searchString=co
- **Method:** GET
- **Consumes:** Pass userId, ticket, clientId and platformId as header parameters


**Supported values for Query parameters**
> |  *Parameter*                  |  *Value*   |
> |-------------------------|------------|
> |  page	    | Page number (start from 1)| 
> |  size     | Number of records to be returned. Positive number. You can use page and size fields to support pagination on client.|
> | orderBy | A comma-separated list of sort keys. Supported fields ***name***, ***size***, ***sharedBy***, ***sharedDate***. Each key sorts ascending by default, but may be reversed with the '-' modifier. Example usage: orderBy=***sharedBy,-sharedDate***. This will sort sharedBy in ascending order. If the sharedBy fields are same then sorting will be done based on shared date in descending order.
> |q| The value will always hold "name" as searching is done based on the file name|
> |searchString| Enter any string here. Search will be done based on file name |

- **Produces:** application/json

```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1494212623109,
   "results":{
      "detail":{
         "totalFiles":2,
         "files":[
            {
               "duid":"9F89C6627EF4A22DFA63062E31EB9110",
               "name":"Configurations-2017-03-10-11-24-59.PNG.nxl",
               "fileType":"png",
               "size":79360,
               "sharedDate":1489116302102,
               "sharedBy":"rupali.choudhury@nextlabs.com",
               "transactionId":"79279c20-e257-4170-9fce-b37aec3852ae",
               "transactionCode":"9C18D6D9C7204965447D4958512F432134739BE64282036EB6D838A37067CDCB",
               "sharedLink":"https://western.nextlabs.com/rms/main#/personal/viewSharedFile?d=77d625a0-3305-4f1e-b102-700f73cc6516&c=AA8E5BE363E9FDD3E5091F65C84705E96717080EB4553DC3A6F1DDFA81513678",
               "rights":[
                  "VIEW",
                  "SHARE",
                  "DOWNLOAD"
               ],
               "comment": "comments from the owner",
               "isOwner": true
            },
            {
               "duid":"40D8AB0B9FE19D2D9B96F9AD89D8E979",
               "name":"Configurations-2017-02-09-16-29-37.PNG.nxl",
               "fileType":"png",
               "size":79360,
               "sharedDate":1486628996478,
               "sharedBy":"rupali.choudhury@nextlabs.com",
               "transactionId":"69d32fcd-4ba8-45d6-89ca-45f9ede6c9d6",
               "transactionCode":"AB937F174CAFA447C95B2C5E692E19E39B1383FC26EECA66EB2E8E7FC56B3051",
               "sharedLink":"https://western.nextlabs.com/rms/main#/personal/viewSharedFile?d=ab73b1fd-9d91-4ad7-8529-c71a7bb3c142&c=370FF8BBABD1AFBB6A4CB303713F01CBAD8A0FB7B9E4FB127D6C6DA62DC1E49D",
               "rights":[
                  "VIEW",
                  "SHARE"
               ],
               "comment": "comments from the owner",
               "isOwner": false
            }
         ]
      }
   }
}
```

## Reshare file
When someone else share a file which contains share right with me, this reshare file API can be used to share the file with others.

- **URL:** /rms/rs/sharedWithMe/reshare
- **Method:** POST
- **Header:** userId, ticket, clientId, deviceId and platformId
- **Consumes:** application/json
- **Body:**
```
#!json
{
   "parameters":{
      "transactionId":"4f56f2ec-3d2b-4371-8625-82d7586352eb",
      "transactionCode":"6D264B8612361AA5C35D4984829B213E661441FF81C3B019995A0A8EADEC525E",
      "shareWith":"aabb@ccc.com,ccdd@eee.com"
   }
}
```
- **Produces:** application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1496641517750,
   "results":{
      "newTransactionId":"36d81004-4773-44c9-a06a-d5fc92f1c321",
      "sharedLink":"https://rmtest.nextlabs.solutions/rms/main#/personal/viewSharedFile?d=36d81004-4773-44c9-a06a-d5fc92f1c321&c=174E1973861237F6FB1941600EC8E966169A6A90C01A36CABDE210842A906F7A",
      "alreadySharedList":[
         "ccdd@eee.com"
      ],
      "newSharedList":[
         "aabb@ccc.com"
      ]
   }
}
```
**Note**
When there is no new recipients added, newTransactionId and sharedLink will not be present in response;
When there is no recipients who are already in the shared list, alreadySharedList will be absent.

- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  400 | Missing required parameters.|
> |  400 | Missing request.|
> |  400 | Unknown platform.|
> |  400 | Invalid transaction.|
> |  400 | Invalid emails.|
> |  400 | Transaction ID and code mismatch.|
> |  401 | Missing login parameters.|
> |  401 | Authentication failed.|
> |  403 | File is not allowed to share.|
> |  4001 | File has been revoked. |
> |  500 | Internal Server Error.|

## Download file

The person can download the file, if the person is granted DOWNLOAD rights

- **URL:** /rms/rs/sharedWithMe/download

- **Method:** POST

- **Header:** userId, ticket, clientId, deviceId, platformId

- **Consumes:** application/json

- **Body:**

```
#!json
{
	"parameters": {
		"transactionCode":"07A8D85154920D18437C9D0DC488A7A0E300D917B8EA21787F4443C73ACF3225",
		"transactionId":"9e239ccb-65f1-4786-bf45-5084ae24a14e",
        "forViewer": "true", (NOTE: this value will get "true" value when the view icon or the file is clicked. "true" is for ignoring the check for download rights. This value will get "false" when download icon is clicked from the 3 dots or from the viewer)
        "start": -1, (optional)
        "length": -1 (optional)
	}
}
```
- **Produces:** 

    * **application/octet-stream** (if success, HTTP STATUS = 200)

        * **HTTP Response Header**:
   > |  *Key*               | Example              |  *Description*   |
   > |----------------------|----------------------|------------------|
   > |  x-rms-last-modified | `1477637785817`  | File last modified time in Unix format.|
   > |  Content-Disposition | attachment; filename*=UTF-8''user-guide.2016-12-25-14-23-14.xlsx.nxl | Indicating the file content is expected to be displayed as attachment.| 
   > |  x-rms-file-size | 1000 | File size.|
   > |  x-rms-file-duid | 40D8AB0B9FE19D2D9B96F9AD89D8E979 | DUID
   > |  x-rms-file-membership | m1@skydrm.com | Owner membership

        * **Response body**:
	
             File content as stream

    * **application/json** (if failure, HTTP STATUS != 200)

        * **Response body**
```
#!json
{
    "statusCode": 4001,
    "message": "File has been revoked",
    "serverTime": 1500467544633
}
```

- **Supported Response Status Code**:
> | *HTTP Status Code* |  *Json Status Code* |  *Description*   |
> |-------------------------|------------|------------|
> |  400 | 400 | Missing required parameters.|
> |  400 | 400 | Missing request.|
> |  400 | 400 | Malformed request.|
> |  400 | 400 | Invalid transaction code.|
> |  400 | 400 | Transaction ID|
> |  401 | 401 | Missing login parameters.|
> |  401 | 401 | Authentication failed.|
> |  403 | 403 | You are not allowed to download the file.|
> |  403 | 4001 | File has been revoked. |
> |  404 | 404 | Missing file.|
> |  500 | 500 | Internal Server Error.|

## Get Metadata

- **URL:** /rms/rs/sharedWithMe/metadata/{transactionId}/{transactionCode}
- **Method:** GET
- **Consumes:** Pass userId, ticket, clientId, deviceId and platformId as header parameters
- **Produces:** application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1496641517750,
   "results":{
      "comment":"this is a comment from the owner",
      "duid":"40D8AB0B9FE19D2D9B96F9AD89D8E979",
      "fileType": "png",
      "isOwner": false,
      "name": "Configurations-2017-02-09-16-29-37.PNG.nxl",
      "protectionType": 0,
      "rights": ["VIEW"],
      "sharedBy": "rupali.choudhury@nextlabs.com",
      "sharedDate": 1489116302102,
      "sharedLink": "https://rmtest.nextlabs.solutions/rms/main#/personal/viewSharedFile?d=36d81004-4773-44c9-a06a-d5fc92f1c321&c=174E1973861237F6FB1941600EC8E966169A6A90C01A36CABDE210842A906F7A",
      "size": 79360,
      "transactionCode": "07A8D85154920D18437C9D0DC488A7A0E300D917B8EA21787F4443C73ACF3225",
      "transactionId": "9e239ccb-65f1-4786-bf45-5084ae24a14e",
      "validity": {
          "option":0
      }
   }
}
```