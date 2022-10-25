# Log service

 - [Log operations from RMC](#markdown-header-log-operations-from-rmc)
 - [List activity log information](#markdown-header-list-activity-log-information)

## Log operations from RMC
This API is used log operations (ACTIVITY_LOG) performed through RMC client 

### log ACTIVITY_LOG operation v2

Pass userId and ticket as header parameters
		
- **URL**: /rms/rs/log/v2/activity
- **method**: PUT
- **consume**: text/csv
- **Content-Encoding**: support: gzip, deflate or none

### ~~log ACTIVITY_LOG operation v1~~ (deprecated)
		
- **URL**: /rms/rs/log/activity/{user_id}/{ticket}
- **method**: PUT
- **consume**: text/csv
- **Content-Encoding**: support: gzip, deflate or none

> A simple comma delimited csv format that doesn't support quoted field. No comma allowed in each field. Fields is defined as:
>
> 
> ## CSV Data
> 
> |*Id*|  *Column*       |   *Type*   |  *Description* |
> |----|-----------------|------------|----------------|
> | 0  |  DUID	         |   String   | Document unique Id |
> | 1  |  Owner Id       |   String   | Document owner's Id |
> | 2  |  User Id        |   Int 64   | User's Id |
> | 3  |  Operation      |   Int 32   | Operation's Id |
> | 4  |  Device Id      |   String   | Device's Id |
> | 5  |  Device Type    |   Int 32   | Use [`platform id`](../../RMD/platform.md) |
> | 6  |  Repository Id  |   String   | Optional, for RMS/RMC mobile, should be empty string unless client has a valid repositoryId that exists on server |
> | 7  |  PathId         |   String   | Optional, for RMS, the path ID used in backend to download files, eg. /xxxxx/yyyy/zzzz |
> | 8  |  File Name      |   String   | Optional, if empty, use the name part in file path |
> | 9  |  PathDisplay    |   String   | File path Display|
> | 10 |  App Name       |   String   | Optional, if empty, use file name in app path |
> | 11 |  App Path       |   String   | Application path |
> | 12 |  App Publisher  |   String   | Application publisher (get from signature) |
> | 13 |  Access Result  |   Int 32   | Allow/Deny |
> | 14 |  Access Time    |   Int 64   | Milliseconds since 1970-1-1T00:00:00 |
> | 15 |  Activity Data  |   String   | Extra activity data. Need to be sent as Json String. See Activity data for data that can be passed|
> | 16 |  Account Type   |   Int 32   | Account Type (starting from v2) where activity is performed, if not present, default value '0' will be used. |
>
>## Activity Data
> |  *key*                  |  *Value*   |
> |-------------------------|------------|
> |  sharing recipients	    |"{""recipients"":""althaf@nextlabs.com,althaf@gmail.com""}" | 
> 
> 
> ## Activity Operation
> 
> |  *Operation*    |  *Value*   |
> |-----------------|------------|
> |  Protect	    |     1	 |
> |  Share          |     2      |
> |  Remove User    |     3      |
> |  View           |     4      |
> |  Print          |     5      |
> |  Download  	    |     6      |
> |  Edit/Save	    |     7      |
> |  Revoke   	    |     8      |
> |  Decrypt   	    |     9      |
> |  Copy Content   |    10      |
> |  Capture Screen |    11      |
> |  Classify       |    12      |
> |  Reshare        |    13      |
> |  Delete         |    14      |
> 
> 
> ## Activity Result
> 
> |  *Result*	|  *Value*  |
> |-------------|-----------|
> |  Denied	|     0     |
> |  Allowed	|     1	    |

> ## Account Type
> 
> |  *Result*	|  *Value*  |
> |-------------|-----------|
> |  PERSONAL   |     0     |
> |  PROJECT	|     1	    |

- **produces**: application/json


		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "OK",
		}


Fields containing line breaks (CRLF), double quotes, and commas should be enclosed in double-quotes.
Please refer to https://tools.ietf.org/html/rfc4180



### List activity log information

Pass userId and ticket as header parameters
		
- **URL**: /rms/rs/log/v2/activity/{DUID}?start=N1&count=N2&searchField=field1&searchText=filter&orderBy=field2&orderByReverse=false
- **method**: GET
- **produces**: application/json


```
#!json

{
	"statusCode": 200,
	"message": "Success",
	"serverTime": 1484193294457,
	"results": {
		"data": {
			"name": "AdhocSharing2-2016-12-30-03-47-02.pptx.nxl",
			"duid": "63369AB6FBCAC06EDD007E65DC178F9F",
			"logRecords": [{
				"email": "naresh.nallagatla@nextlabs.com",
				"operation": "Share",
				"deviceType": "WebApp",
				"deviceId": "118.189.77.114",
				"accessTime": 1484115333911,
				"accessResult": "Allow"
			}, {
				"email": "naresh.nallagatla@nextlabs.com",
				"operation": "View",
				"deviceType": "WebApp",
				"deviceId": "118.189.77.114",
				"accessTime": 1484115080584,
				"accessResult": "Allow"
			}, {
				"email": "naresh.nallagatla@nextlabs.com",
				"operation": "Protect",
				"deviceType": "WebApp",
				"deviceId": "118.189.77.114",
				"accessTime": 1483069622214,
				"accessResult": "Allow"
			}]
		},
		"totalCount": 3
	}
}

```

**Supported values for Query parameters**
> |  *Parameter*                  |  *Value*   |
> |-------------------------|------------|
> |  start	    | the number of records to skip. Positive number| 
> |  count     | number of records to be returned. Positive number. You can use start and count fields to support pagination on client|
> |  searchField | the field on which search is being done. can be empty. Supported values are ***email***, ***operation***, ***deviceId***|
> |  searchText | this text will be used to filter on searchField|
> | orderBy | results will be ordered by this field. Supported Fields ***accessTime***, ***accessResult***. Default is ***accessTime***|
> |orderByReverse| ***true*** for descending Order, ***false*** for ascending Order. Default is ***true***|