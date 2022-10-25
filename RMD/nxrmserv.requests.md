# **NextLabs Rights Management Service Requests**



# Basic Request & Response Format

Both request and response data are utf-8 json strings.

- **request** : utf-8 json string
```json
{
	"code": 1,
	"parameters": {
        ...
	}
}
```
    `code` (mandatory) is a integer request id, see following list.
    
    `parameters` (optional) is a json object contains all the parameters used by request.


- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
        ...
	}
}
```
    `code` (mandatory) is a integer error code, zero stands for success.

    `message` (optional) is the information of response.

    `data` (optional) is the result of request returned from service.


# Query service status

This request is sent to service to query overall service status, which includes:

- **request** : utf-8 json string
```json
{
	"code": 1,
	"parameters": {
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
    "data": {
        "os": {
            "name": "Windows 10 Enterprise",
            "version": "10.0",
            "build": 10240,
            "type": "x64/x86",
            "language": "en-US"
        },
        "product": {
            "name": "NextLabs Rights Management Client",
            "version": "9.1.0.1652",
            "releasetype": "release/debug",
            "cpuarch": "x64/x86",
            "directory": "C:\\Program Files\\NextLabs\\Rights Management"
        },
        "rmsSettings": {
            "serverUrl": "https://jt2go.skydrm.com",
            "tenantId": "jt2go.skydrm.com",
            "connected": true
        },
        "logSettings": {
            "logLevel": 4,
            "logRotation": 10,
            "logSize": 5
        },
        "logonStatus": {
            "status": true,
            "expireTime": "2016-06-24T13:24:59",
            "logonUser": {
                "id": 5,
                "name": "John Tyler",
                "email": "John.Tyler@nextlabs.com",
                "defaultMember": "m1.jt2go.skydrm.com",
                "defaultMenant": "jt2go.skydrm.com",
                "securityMode": 1
            }
        },
        "policyStatus": {
            "policyId": "2016-07-13T13:24:59",
            "lastUpdateTime": "2016-07-14T13:24:59"
	}
}
```
- **comment** :

## *product information* (mandatory)

Product information contains generic endpoint product information, includes `name`, `version`, `product type`, and `install directory`

## *rms settings* (mandatory)

RMS server, tenant information and connection status

## *rmc settings* (mandatory)

RMC global settings (log, etc)

## *logon status* (optional)

If user has logged on, it set `status` to true and return logon user's profile & session expire time, otherwise it set `status` to false

## *policy status* (optional)

if user has logged on, it returns policy status, otherwise, there is no policy status.

# Query product information

This request is to get NextLabs Rights Management product information from service.

- **request** : utf-8 json string
```json
{
	"code": 2,
	"parameters": {
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
        "name": "NextLabs Rights Management Client",
        "version": "9.1.0.1652",
        "releasetype": "release/debug",
        "cpuarch": "x64/x86",
        "directory": "C:\\Program Files\\NextLabs\\Rights Management"
	}
}
```
- **comment** :


# Query RMS settings

This request is to get NextLabs Rights Management product information from service.

- **request** : utf-8 json string
```json
{
	"code": 3,
	"parameters": {
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
        "tenantId": "jt2go.skydrm.com",
        "serverUrl": "https://jt2go.skydrm.com"
	}
}
```
- **comment** :

# Query RMS connection status

This request is to get NextLabs Rights Management product information from service.

- **request** : utf-8 json string
```json
{
	"code": 4,
	"parameters": {
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
        "connected": true
	}
}
```
- **comment** :



# Query RMC settings

This request is to get Rights Management Client settings.

- **request** : utf-8 json string
```json
{
	"code": 5,
	"parameters": {
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
        "protectionEnabled": true,
        "defaultSecurityMode": 2,
        "fileTypeBlacklist": "REGULAR EXPRESSION",
        "fileTypeWhitelist": "REGULAR EXPRESSION"
	}
}
```
- **comment** :


# Query log settings

This request is to get NextLabs Rights Management Client log settings.

- **request** : utf-8 json string
```json
{
	"code": 6,
	"parameters": {
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
        "logLevel": 4,
        "logRotation": 10,
        "logSize": 5
	}
}
```
- **comment** :


# Query logon urls

This request is to get logon urls.

- **request** : utf-8 json string
```json
{
	"code": 7,
	"parameters": {
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
        "logonUrl": "https://jt2go.skydrm.com/login"
	}
}
```
- **comment** :


# Finalize Login

This request is to let service finalize user login when user finish login via UI.

- **request** : utf-8 json string
```json
{
	"code": 8,
	"parameters": {
        "userId": 3,
        "ticket": "DDDDF9F2636532519D3E5BF3C6B51C46",
        "ttl": 1465583460683,
        "name": "user_0001",
        "email": "client_0001@nextlabs.com",
        "preferences": {
            "defaultTenant": "DDDDF9F2636532519D3E5BF3C6B51C46",
            "defaultMember": "m3@nextlabs.com",
            "securityMode": 1,
            ...
        },
        "memberships": [
            {
                "id": "m3@nextlabs.com",
                "type": 0,
                "name": "IT",
                "tenantId": "DDDDF9F2636532519D3E5BF3C6B51C46"
            },
            ...
        ]
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
	}
}
```
- **comment** :

The `parameters` element is the data returned from RMS.

# Query logon status

This request is to get logon status and logon user information if there is one.

- **request** : utf-8 json string
```json
{
	"code": 9,
	"parameters": {
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
        "status": true,
        "expireTime": "2016-06-24T13:24:59Z",
        "logonUser": {
            "id": 5,
            "name": "John Tyler",
            "email": "John.Tyler@nextlabs.com",
            "preferences": {
                "defaultTenant": "DDDDF9F2636532519D3E5BF3C6B51C46",
                "defaultMember": "m3@nextlabs.com",
                "securityMode": 1,
                ...
            },
            "memberships": [
                {
                    "id": "m1.jt2go.skydrm.com",
                    "type": 0,
                    "name": "IT",
                    "tenantId": "DDDDF9F2636532519D3E5BF3C6B51C46"
                },
                {
                    "id": "m5.jt2go.skydrm.com",
                    "type": 0,
                    "name": "Engineer",
                    "tenantId": "DDDDF9F2636532519D3E5BF3C6B51C46"
                },
                ...
            ]
        }
	}
}
```
- **comment** :



# Check user profile update

This request is to let service send a update request to get latest logon user's profile.

- **request** : utf-8 json string
```json
{
	"code": 10,
	"parameters": {
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
        "profile_changed": true,
        "policy_changed": false,
	}
}
```
- **comment** :



# Check software update update

This request is to let service send a update request to get latest software.

- **request** : utf-8 json string
```json
{
	"code": 11,
	"parameters": {
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
        "hasUpdate": true,
        "newVersion": "9.1.145",
        "checksum": "DDDDF9F2636532519D3E5BF3C6B51C46"
	}
}
```
- **comment** :

If there is a update, set `hasUpdate` to `true` and fill `newVersion` and `checksum` with correct value. Otherwise, set `hasUpdate` to `false`

# Set log settings

This request is to let service send a update request to get latest software.

- **request** : utf-8 json string
```json
{
	"code": 12,
	"parameters": {
        "permanentChange": true,
        "logLevel": 3,
        "logSize": 10,
        "logRotation": 0
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
	}
}
```
- **comment** :


# Set default membership

This request is to let service set default membership of current logon user.

- **request** : utf-8 json string
```json
{
	"code": 13,
	"parameters": {
        "userId": 5,
        "defaultMember": "m1.nextlabs.com"
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
	}
}
```
- **comment** :


# Set default secure mode

This request is to let service set default secure mode of current logon user.

- **request** : utf-8 json string
```json
{
	"code": 14,
	"parameters": {
        "securityMode": 1
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
	}
}
```
- **comment** :


# Register Notification Handler

This request register a window as a service notifition handler

- **request** : utf-8 json string
```json
{
	"code": 15,
	"parameters": {
        "WindowHandle": 839
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
	}
}
```
- **comment** :


# Log sharing transaction

This request ask service to log one sharing transaction which may include one or more files

- **request** : utf-8 json string
```json
{
	"code":	16,
	"parameters":	{
		"sharedDocuments": [
			{
				"shareType": 0,		// 0 - Protect source file and share, 1 - Share protected file
				"duid": "DDDDF9F2636532519D3E5BF3C6B51C46",
				"membershipId": "m14.nextlabs.com",
				"fileName": "C:\test\test.docx",
				"filePath": "C:\Users\Admin\AppData\Local\NextLabs\cache\NXL89B0.tmp\test.docx.nxl",
				"permissions": 1892372582,
				"watermark": true,
				"expireTime": "2016-06-24T13:24:59",
				"recipients": [
				 	{
				 		"email": "aaa@bbb.com"
				 	},
				 	{				 	
				 		"email": "ccc@ddd.com"
				 	},
				 	{
				 		"phone": "+1-111-222333"
					},
					{
						"group": "my_share_group_name"
					}		 		
				 ]
			},
			{
				"shareType": 1,		// 0 - Protect source file and share, 1 - Share protected file
				"duid": "DDDDF9F2636532519D3E5BF3C6B51C4a",
				"membershipId": "m14.nextlabs.com",
				"fileName": "C:\test\aaa.pptx",
				"filePath": "C:\Users\Admin\AppData\Local\NextLabs\cache\NXL4147.tmp\aaa.pptx.nxl",
				"permissions": 1892372582,
				"watermark": false,
				"expireTime": "2016-06-24T13:24:59",
				"recipients": [
					{
				 		"email": "ddd@eee.com"
				 	},
				 	{				 	
				 		"email": "ggg@mmm.com"
				 	}
				 ]
			},
			...
		]
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
	}
}
```
- **comment** :



# User Logout

User logout

- **request** : utf-8 json string
```json
{
	"code": 17,
	"parameters": {
	}
}
```
- **response** : utf-8 json string
```json
// Succeed
{
	"code": 0,
    "message": "succeed",
	"data": {
	}
}
// When there is NXL file opened by any process
{
	"code": 170,
    "message": "Fail to log out due to process(es) with opened NXL file",
	"data": {
		"7024": "Acrobat.exe",
		"7044": "notepad.exe"
	}
}
```
- **comment** :



# Collect Debug Log

This command request service to generate a zipped debug log file on user's desktop.

- **request** : utf-8 json string
```json
{
	"code": 18,
	"parameters": {
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
	}
}
```
- **comment** :



# Activity Notification

This command notify service that a user activity has happened.

- **request** : utf-8 json string
```json
{
	"code": 19,
	"parameters": {
		"operation": 4,
		"result": 0,
		"fileName": "test.docx",
		"filePath": "C:\test\test.docx",
		"notifyUser": true,
		"languageId": 1033
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
	}
}
```
- **comment** :

`Operation` is defined [HERE](../RMS/RESTful API/Log REST API.md)

`Result` is defined [HERE](../RMS/RESTful API/Log REST API.md)



# Enable/Disable DWM

This command notify service that DWM status has been chanegd

- **request** : utf-8 json string
```json
{
	"code": 20,
	"parameters": {
		"enabled": true
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
	}
}
```
- **comment** :


# Query Activity Records

This command notify service that Tray-App wants to get log information

- **request** : utf-8 json string
```json
{
	"code": 21,
	"parameters": {
		"count": 20,
		"criteria": {
			"start_time": 89292929292,
			"end_time": 28828893,
			"file": ".*\.txt",
			"duid": "022D8BF2E0A500F22787EFF222F8BF33",
			"operation": 1,
			"result": 0
		}
	}
}
```
- **response** : utf-8 json string
```json
{
	"code": 0,
    "message": "succeed",
	"data": {
		"record_file": "C:\Program Files\NextLabs\Rights Management\profiles\S-1-5-21-2018228179-1005617703-974104760-187941\skydrm.com\99\query_result.json"
	}
}
```
- **comment** :

	*Parameters*
	
	`count`: (Optional) How many records client want to query. It is optional. If it is zero or doesn't exist, service try to return all the records match the criteria.
	
	`start_time`: (Optional) Windows UTC time. Only return records whose time is later than or equal to start_time.
	
	`end_time`: (Optional) Windows UTC time. Only return records whose time is earlier than or equal to end_time.
	
	`file`: (Optional) Regular expression string. Only return records whose file path match input regular expression.
	
	`duid`: (Optional) String. Only return records whose file duid match input.
	
	`operation`: (Optional) Integer. Operation id.
	
	`result`: (Optional) Integer. 1 (Allowed) or 0 (Denied)

	*Response Data*

	All the found records will be put into a temp JSON file specified in response. The format is like:

```json
[
	{
		"application": "DllHost.exe",
		"duid": "0FC5405880785896794713C6A690C1E1",
		"file": "C:\\Test\\Shared\\philip\\test.tif",
		"operation": "View",
		"owner": "m130@jt2go",
		"result": "Allowed",
		"timestamp": "2016-09-29T16:21:37.842-07:00"
	},
	{
		"application": "DllHost.exe",
		"duid": "03CCB1E6F4EA1ED375700278E2391480",
		"file": "C:\\Test\\Shared\\philip\\test.gif",
		"operation": "View",
		"owner": "m130@jt2go",
		"result": "Allowed",
		"timestamp": "2016-09-29T16:21:37.842-07:00"
	},
	{
		"application": "DllHost.exe",
		"duid": "0AE0D28DFD72BC861ADAACD1F7ECB812",
		"file": "C:\\Test\\Shared\\philip\\test.png",
		"operation": "View",
		"owner": "m130@jt2go",
		"result": "Allowed",
		"timestamp": "2016-09-29T16:21:37.826-07:00"
	}
]
```