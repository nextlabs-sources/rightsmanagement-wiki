# Sharing service

 - [Share a document with recipients](#markdown-header-share-a-document-with-recipients-deprecated)
 - [Sharing repository files](#markdown-header-sharing-repository-files)
 - [Sharing local files](#markdown-header-sharing-local-files)
 - [Update recipients from a document](#markdown-header-update-recipients-from-a-document)
 - [Revoking a document](#markdown-header-revoking-a-document)

## ~~Share a document with recipients~~ (deprecated)
This API is used to Share a document with recipients. 

- **URL**: /rms/rs/share
- **method**: POST
- **consume**: application/json

```
#!json
{
   "parameters":{
      "userId":"1",
      "deviceId":"1012313431",
      "deviceType":"client_001",
      "ticket":"52409E58D89C34CC94C142F9D2DA2D12",
      "checksum":"AF3FD14BC37DBBAA3D5F3370944C02FCC7A87823625605CAEB2EBADB296AB589",
      "sharedDocument":"{\"duid\":\"D92A5F729B8437792D2221CC431E9AC1\",\"membershipId\":\"m1@nextlabs.com\",\"permissions\":275,\"metadata\":\"{}\",\"expireTime\":1469091455000,\"fileName\":\"user.json.nxl\",\"recipients\":[{\"email\":\"client_0002@nextlabs.com\"},{\"email\":\"client_0003@nextlabs.com\"},{\"email\":\"client_0004@nextlabs.com\"},{\"email\":\"client_0005@nextlabs.com\"}]}"
   }
}
```
        
- **produces**: application/json

```
#!json
{
   "statusCode":200,
   "message":"OK"
}
```

## Sharing repository files
This API is used to share a file in the repository; this is a single service for protecting file (if file is native in the repository), uploading protected file to MyVault and sending email notifications to recipients.

- **URL**: /rms/rs/share/repository
- **method**: POST
- **consume**: application/json
- **header**: userId=1 ticket=14802018F8DDAFD97AF10E0E6CD993BB
- **body**

```
#!json
{
   "parameters":{
      "asAttachment":"false",
      "sharedDocument":{
         "membershipId":"m1@skydrm.com",
         "permissions":275,
         "tags": {
           "Classification":["ITAR"],
           "Clearance": ["Confidiential","Top Secret"]
         },
         "metadata":"{}",
         "fileName":"user.json.nxl",
         "repositoryId":"b83a41e6-9f88-4522-aba5-391f0131598c",
         "filePathId":"/0Bx4--SpDCDU9TVBDV3d3Zms2LWs/0Bx4--SpDCDU9ZTdKdWtFLU04Z2s",
         "filePath":"/NextlabsTest/image.jpeg",
         "recipients":[
            {
               "email":"rmsuser38@gmail.com"
            },
            {
               "email":"rmsuser39@gmail.com"
            }
         ],
         "comment": "comments for sharing",
         "expiry": {
            "option": 0
         },
         "watermark": "$(User)$(Break)$(Date) $(Time)"
      }
   }
}

Available Expiry Options:
// Option 0: this is for never expired option
        "expiry": {
             "option":0
         }

// Option 1: this is for relative expiry date
        "expiry": {
             "option":1,
             "endDate" : 1469091455000
         }

// Option 2: this is for absolute expiry date
        "expiry": {
             "option":2,
             "endDate" : 1469091455000
         }

// Option 3: this is for range  expiry date

        "expiry": {
             "option":3,
             "startDate" : 1469091455000,
			 "endDate" : 1469091455000
         }
```

- **produces**: application/json

```
#!json		
{
   "statusCode":200,
   "message":"OK",
   "results":{
      "duid":"820255E9C6691C55C56D58AC399CE988",
      "filePathId":"/nxl_myvault_nxl/image2017-03-01-12-01-40.jpeg.nxl",
      "transactionId":"48c3fb8b-0534-4bf1-9944-b08a8b278044",
      "alreadySharedList":[
         "rmsuser38@gmail.com"
      ],
      "newSharedList":[
         "rmsuser39@gmail.com"
      ],
      "fileName":"image2017-03-01-12-01-40.jpeg.nxl"
   }
}
```
	
	if server cannot download file from repository or facing some other connection issues, the response would be like:

```
#!json    
{
   "statusCode":502,
   "message":"Bad Gateway"
}
```

	receiving this message, client can download file from repository and use sharing local file service api.

**Note**: Client doesn't need to create activity log for this operation.

## Sharing local files

This API is used to share a local file; this is a single service for protecting file (if file is native), uploading protected file to MyVault and sending email notifications to recipients.

The clients need to set `filePathId` and `filePath` in the JSON request in order to get the correct source path when getting back the metadata from MyVault. Same value can be used for both. Please provide the absolute path when possible and filename if not. For Windows, the path separator needs to be escaped. For example:

Unix: `/Users/SkyDRM/UM001.txt`

Windows: `D:\\Users\\SkyDRM\\UM001.txt`


- **URL**: /rms/rs/share/local
- **method**: POST
- **consume**: multipart/form-data
- **header**: userId=1 ticket=14802018F8DDAFD97AF10E0E6CD993BB
- **body**

```
#!json 
Content-Disposition: form-data; name=" API-input" 
{
   "parameters":{
      "asAttachment":"false",
      "sharedDocument":{
         "membershipId":"m1@skydrm.com",
         "permissions":275,
         "tags": {
           "Classification":["ITAR"],
           "Clearance": ["Confidiential","Top Secret"]
         },
         "metadata":"{}",
         "filePathId":"C:\\NXL\\image.jpeg",
         "filePath":"C:\\NXL\\image.jpeg",
         "recipients":[
            {
               "email":"user1@dummy.com"
            },
            {
               "email":"user2@dummy.com"
            }
         ],
         "comment": "comments for sharing",
         "expiry": {
            "option": 0
         },
         "watermark": "$(User)$(Break)$(Date) $(Time)"
      }
   }
}

Content-Disposition: form-data; name="file"; filename="****";
File contents

Available Expiry Options:
// Option 0: this is for never expired option
        "expiry": {
             "option":0
         }

// Option 1: this is for relative expiry date
        "expiry": {
             "option":1,
             "endDate" : 1469091455000
         }

// Option 2: this is for absolute expiry date
        "expiry": {
             "option":2,
             "endDate" : 1469091455000
         }

// Option 3: this is for range  expiry date

        "expiry": {
             "option":3,
             "startDate" : 1469091455000,
			 "endDate" : 1469091455000
         }
```

- **produces**: application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1505267953762,
   "results":{
      "fileName":"image-2017-09-13-09-59-08.jpeg.nxl",
      "duid":"820255E9C6691C55C56D58AC399CE988",
      "filePathId":"/nxl_myvault_nxl/image-2017-09-13-09-59-08.jpeg.nxl",
      "newSharedList":[
         "user1@dummy.com"
      ],
      "alreadySharedList":[
         "user2@dummy.com"
      ],
      "transactionId":"48c3fb8b-0534-4bf1-9944-b08a8b278044"
   }
}
```

**Note**: Client doesn't need to create activity log for this operation.

## Update recipients from a document
This API is used to remove recipients from a shared document. 

- **URL**: /rms/rs/share/{duid}/update
- **method**: POST
- **consume**: application/json
- **header**: userId=1 ticket=14802018F8DDAFD97AF10E0E6CD993BB

```
#!json
{
   "parameters":{
      "newRecipients":[
         {
            "email":"user3@nextlabs.com"
         },
         {
            "email":"user4@nextlabs.com"
         },
         {
            "email":"user5@nextlabs.com"
         }
      ],
      "removedRecipients":[
         {
            "email":"user1@nextlabs.com"
         }
      ],
      "comment": "comments for sharing"
   }
}
```

- **produces**: application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1484210436761,
   "results":{
      "newRecipients":[
         "user3@nextlabs.com",
         "user4@nextlabs.com",
         "user5@nextlabs.com"
      ],
      "removedRecipients":[
         "user1@nextlabs.com"
      ]
   }
} 
```

- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  304 | No changes on the recipients.|
> |  400 | Missing JSON request in the request body.| 
> |  400 | Missing required parameters (userId, ticket).|
> |  400 | Unknown platform ID.|
> |  400 | Malformed JSON request.|
> |  401 | Authentication failed.|
> |  403 | Access denied (either DUID is not found or user doesn't allow to perform this operation).|
> |  500 | Internal Server Error.|
> |  4001| File has been revoked.|
> |  4002| No transaction has been performed. You need to share the document in order to update the recipients.|
> |  4007| Comment too long. (Maximum length for the comment is 250)|

**Note**: Client doesn't need to create activity log for this operation.


## Revoking a document
This API is used to revoke a shared document. Only the document owner/steward can revoke the document.

- **URL**: /rms/rs/share/{duid}/revoke
- **method**: DELETE
- **header**: userId=1 ticket=14802018F8DDAFD97AF10E0E6CD993BB
- **produces**: application/json
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
> |  400 | Unknown platform.|
> |  401 | Authentication failed.|
> |  403 | Access denied.|
> |  404 | Unable to find file metadata for given DUID.|
> |  500 | Internal Server Error.|

**Note**: Client doesn't need to create activity log for this operation.