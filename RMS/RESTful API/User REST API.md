# User service

RMS exposes the following web services to access Projects for the following operations:

* [Get CAPTCHA](#markdown-header-get-captcha)
* [Register new user](#markdown-header-register-a-new-user)
* [Unregister a pending account](#markdown-header-unregister-a-pending-account)
* [Activate pending account](#markdown-header-activate-pending-account)
* [Request link to reset user password](#markdown-header-request-link-to-reset-user-password)
* [Change user password](#markdown-header-change-user-password)
* [Reset user password](#markdown-header-reset-user-password)
* [Retrieve user's basic profile](#markdown-header-retrieve-basic-profile)
* [Retrieve user's full profile](#markdown-header-retrieve-full-profile)
* [Update user's profile](#markdown-header-update-user-profile)
* [List Memberships](#markdown-header-list-memberships)
* [Retrieve user preferences](#markdown-header-retrieve-user-preference)
* [Update user preferences](#markdown-header-update-user-preference)
* [List Users](#markdown-header-list-users)
* [Assign API User](#markdown-header-assign-api-user)

## Get CAPTCHA
This API is used to display a CAPTCHA. 

- **URL**: /rms/rs/usr/captcha
- **method**: GET
- **consume**: N/A
- **produces**: application/json


		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "OK",
			"results": {
				"captcha": "iVBORw0KGgo...",
				"nonce": "B303E6DFE546A889498956A973895A3E57688BC7"
			}
		}

## Register a new user
This API is used to create a new user account. 

- **URL**: /rms/rs/usr/register
- **method**: POST
- **consume**: application/x-www-form-urlencoded


		url encoded form data looks like:

		email=client_0001%40nextlabs.com&password=C9808CFE7A98ACF1088E791B41BDAEED&displayName=user_0001&nonce=B303E6DFE546A889498956A973895A3E57688BC7&captcha=12345

- **produces**: application/json


		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "OK",
			"extra": {
				"userId": 3,
				"ticket": "DDDDF9F2636532519D3E5BF3C6B51C46",
				"ttl": 1465583460683,
				"name": "user_0001",
				"email": "client_0001@nextlabs.com",
				"preferences": {
					"defaultTenant": "86e2b8a3-65de-4af6-a11f-add61a9e90db",
					"defaultMember": "m3@nextlabs.com",
					"securityMode": 1
				}
				"memberships": [
				{
					"id": "m3@nextlabs.com",
					"type": 0,
					"tenantId": "86e2b8a3-65de-4af6-a11f-add61a9e90db"
                                        // will add a new parameter "tokenGroupName" for the login tenant
				},
				...
				]
			}
		}


- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  304 | Your account is not activated. |
> |  304 | User already exists. |
> |  400 | Missing required parameters.|
> |  403 | Auto provision is not allowed.|
> |  500 | Internal Server Error.|
> |  503 | Service is not initialized yet.|
> |  5001 | Display name is too long.|
> |  5002 | Display name contains illegal special characters|
> |  5003 | Invalid Email|
> |  5004 | Email is too long.|


## Unregister a pending account
This API is used to create a pending account. 

- **URL**: /rms/rs/usr/unregister/{account_id}/{otp}
- **method**: GET
- **consume**: N/A
- **produces**: application/json


		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "OK",
		}

## Activate pending account
This API is used to activate a pending account. 

- **URL**: /rms/rs/usr/activate/{account_id}/{otp}
- **method**: GET
- **consume**: N/A
- **produces**: application/json


		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "OK",
		}

## Request link to reset user password
This API is used to request a link to reset user's password. The link will be emailed to the user.

- **URL**: /rms/rs/usr/forgotPassword
- **method**: POST
- **consume**: application/x-www-form-urlencoded


		url encoded form data looks like:

		email=client_0001%40nextlabs.com&nonce=B303E6DFE546A889498956A973895A3E57688BC7&captcha=12345

- **produces**: application/json


		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "OK",
		}

## Change user password
This API is used to change user's password. Passwords have to be MD5-hashed.
Clients need to validate the regular expression before they are MD5-hashed.
The Regex for password is
```
/^(?=.*[A-Za-z])(?=.*\d)(?=.*[\W])[A-Za-z\d\W]{8,}$/
```

Pass userId and ticket as header parameters.

- **URL**: /rms/rs/usr/changePassword
- **method**: POST
- **consume**: application/json

		{
			"parameters":{
				"oldPassword":"C9808CFE7A98ACF1088E791B41BDAEED",
				"newPassword":"F383D5C7587A58E5D31A7C602C605764"
			}
		}

- **produces**: application/json
- This API will also set the new ticket value in the cookie "ticket".

		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "OK",
			"extra": {
				"ticket": "81F2DCE07A93423C04A96BF6BF5EC5F9",
				"ttl": "1494816965712"
			}
		}

## Reset user password
This API is used to reset user's password. Passwords have to be MD5-hashed.
Clients need to validate the regular expression before they are MD5-hashed.
The Regex for password is
```
/^(?=.*[A-Za-z])(?=.*\d)(?=.*[\W])[A-Za-z\d\W]{8,}$/
```

- **URL**: /rms/rs/usr/resetPassword
- **method**: POST
- **consume**: application/x-www-form-urlencoded


		url encoded form data looks like:

		userId=1&otp=B303E6DFE546A889498956A973895A3E57688BC7&newPassword=F383D5C7587A58E5D31A7C602C605764

- **produces**: application/json
- After calling this API, all tickets previously issued will be invalidated (across different platforms)

		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "OK"
		}

## Retrieve basic profile
This API is used to retrieve user's basic profile information. 

### v2 ###
Pass userId and ticket as header parameters

- **URL**: /rms/rs/usr/v2/basic
- **method**: GET
- **consume**: N/A
- **produces**: application/json


		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "OK",
			"extra": {
				"email": "client_0001@nextlabs.com",
				"displayName": "user_0001",
			}
		}


### ~~v1~~ (deprecated) ###
- **URL**: /rms/rs/usr/basic/{user_id}/{ticket}
- **method**: GET
- **consume**: N/A
- **produces**: application/json


		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "OK",
			"extra": {
				"email": "client_0001@nextlabs.com",
				"displayName": "user_0001",
			}
		}


## Retrieve full profile
This API is used to retrieve user detailed profile and preferences. 

### v2 ###
Pass userId and ticket as header params

- **URL**: /rms/rs/usr/v2/profile
- **method**: GET
- **consume**: N/A
- **produces**: application/json


Jsonresponse object looks like (adpass has/will be removed from returned json):


```
#!json

{
    "statusCode": 200,
    "message": "Authorized",
    "serverTime": 1526278702322,
    "extra": {
        "userId": 21,
        "ticket": "55F23B75BA953CAAB8B9754F54CBE8F1",
        "tenantId": "48726a20-493e-4258-8d3d-25ae75793138",
        // will add a new parameter "tokenGroupName" for the login tenant
        "ttl": 1526365087606,
        "name": "Abraham Lincoln",
        "email": "abraham.lincoln@qapf1.qalab01.nextlabs.com",
        "memberships": [
            {
                "id": "m24@skydrm.com",
                "type": 0,
                "tenantId": "48726a20-493e-4258-8d3d-25ae75793138",
                "projectId": 3 // will be changed to "tokenGroupName"
            },
            {
                "id": "m65@t-d0fb1eabcfc94b20984c5816071bc004",
                "type": 0,
                "tenantId": "5994bdab-880c-4704-8a84-2f90d884d572", // will be changed to "tokenGroupName"
                "projectId": 5
            },
            {
                "id": "m67@t-fc00458990ec4975833bc4f8e2157e44",
                "type": 0,
                "tenantId": "ae54a444-cab5-481c-9d03-198e9cbee267", // will be changed to "tokenGroupName"
                "projectId": 39
            },
            {  // system bucket example
                "id":"user22@ed8b24a9-40ba-482f-8d0a-4ff1781317e7_system",
                "type":0,
                // tenantId is system bucket name, it will be a temporary fix in build 200. Will be removed after refractor
                "tenantId":"ed8b24a9-40ba-482f-8d0a-4ff1781317e7_system"// will be changed to "tokenGroupName"
            }
        ],
        "defaultTenant": "skydrm.com",
        "defaultTenantUrl": "https://sudong.nextlabs.com/rms",
        "attributes": {
            "aduser": [
                "abraham.lincoln"
            ],
            "Company": [
                "biflex"
            ],
            "Department": [
                "HR"
            ],
            "addomain": [
                "qapf1.qalab01.nextlabs.com"
            ],
            "displayName": [
                "Abraham Lincoln"
            ],
            "objectSid": [
                "S-1-5-21-2018228179-1005617703-974104760-1200"
            ],
            "memberOf": [
                "CN=Prop Class 1 - Source Code,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=SAP EM Admin,OU=bae-group,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=Tes,OU=License Groups,OU=bae-group,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=ReportingGroup {6d07c1f1-3416-4077-b5b8-08029cf31da1},DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=Embraer Admin,OU=Embraer,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=RIGHTSWATCH_SECURITY_ROLE_2,OU=RIGHTSWATCH,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=RIGHTSWATCH_SECURITY_ROLE_1,OU=RIGHTSWATCH,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=RightsWATCH_AUDITING,OU=RIGHTSWATCH,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=RightsWATCH_MONIT,OU=RIGHTSWATCH,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=RightsWATCH_USERS,OU=RIGHTSWATCH,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=RightsWATCH_CLASSIFICATIONS,OU=RIGHTSWATCH,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=RightsWATCH_CONFIGURATION,OU=RIGHTSWATCH,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=RightsWATCH_LICENSING,OU=RIGHTSWATCH,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=RightsWATCH_ROLES,OU=RIGHTSWATCH,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=RightsWATCH_RULES,OU=RIGHTSWATCH,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=RightsWATCH_BLACKLIST,OU=RIGHTSWATCH,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=UK GSC 2014 O,OU=License Groups,OU=bae-group,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=Ametek_DSP 050056205,OU=License Groups,OU=bae-group,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=LMT Administrators,OU=bae-group,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=RMT Administrators,OU=bae-group,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=PrivReportingGroup {773f1cc1-7b9a-4963-bd9a-f5db42ca1731},DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=PrivUserGroup {773f1cc1-7b9a-4963-bd9a-f5db42ca1731},DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=Export Control Access Rights,OU=bae-group,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=BAE Systems Intellectual Property Access Rights,OU=bae-group,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=Government Security Access Rights,OU=bae-group,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=Board Members,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=Presidential Soccer Team,OU=Groups,OU=Presidents,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=Administrators,CN=Builtin,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=Domain Admins,CN=Users,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=Schema Admins,CN=Users,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",
                "CN=Enterprise Admins,CN=Users,DC=qapf1,DC=qalab01,DC=nextlabs,DC=com"
            ],
            "email": [
                "abraham.lincoln@qapf1.qalab01.nextlabs.com"
            ]
        }
    }
}
```

		


### ~~v1~~ (deprecated) ###

- **URL**: /rms/rs/usr/profile/{user_id}/{ticket}
- **method**: GET
- **consume**: N/A
- **produces**: application/json


		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "OK",
			"extra": {
				"userId": 3,
				"ticket": "DDDDF9F2636532519D3E5BF3C6B51C46",
				"ttl": 1465583460683,
				"name": "user_0001",
				"email": "client_0001@nextlabs.com",
				"preferences": {
					"defaultTenant": "86e2b8a3-65de-4af6-a11f-add61a9e90db",
					"defaultMember": "m3@nextlabs.com",
					"securityMode": 1
				}
				"memberships": [
				{
					"id": "m3@nextlabs.com",
					"type": 0,
					"tenantId": "86e2b8a3-65de-4af6-a11f-add61a9e90db",
                                        "projectId": 1 // will be changed to "tokenGroupName"
				},
				...
				]
			}
		}

## Update user profile
This API is used to update user's display and preferences. 

Regex for Email
```
^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$
```

Regex for Display Name
```
^((?![\~\!\#\$\%\^\&\*\(\)\+\=\[\]\{\}\;\:\"\\\/\<\>\?]).)+$
```

### v1 ###

- **URL**: /rms/rs/usr/profile
- **method**: POST
- **consume**: application/json

		{
			"parameters": {
				"userId": "3",
				"ticket": "DDDDF9F2636532519D3E5BF3C6B51C46",
				"displayName": "Test User",
				"preferences": { "securityMode":"100" }
			}
		}

- **produces**: application/json


		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "User updated",
		}

## List Memberships
This API is used to retrieve memberships (both static and dynamic) for a user. 

Pass userId and ticket as header params

- **URL**: /rms/rs/usr/memberships?q=489
- **method**: GET
- **consume**: N/A
- **produces**: application/json


Jsonresponse object looks like:

```
#!java

{  
   "statusCode":200,
   "message":"OK",
   "serverTime":1490189844715,
   "results":{  
      "memberships":[  
         {  
            "id":"m836@t-5abb7501ad794b0cb1da10710e997859",
            "type":0,
            "tenantId":"abd298ff-f3ab-4f1a-a856-e0cd7d4166f7",// will be changed to "tokenGroupName"
            "projectId":489
         },
         {  
            "id":"user22@t-c10c9c579a834125b07a2d659a002fc8",
            "type":0, // will change to 1 for projects
            "tenantId":"ed8b24a9-40ba-482f-8d0a-4ff1781317e7",// will be changed to "tokenGroupName"
            "projectId":88
         },
         {  // system bucket example
            "id":"user22@ed8b24a9-40ba-482f-8d0a-4ff1781317e7_system",
            "type":0,  // will change to 2 for system bucket
            // tenantId is system bucket name, it will be a temporary fix in build 200. Will be removed after refractor
            "tenantId":"ed8b24a9-40ba-482f-8d0a-4ff1781317e7_system"// will be changed to "tokenGroupName"
         }
      ]
   }
}

```

**Supported query parameters are listed below.**


> |  *Parameter*                  |  *Value*   |
> |-------------------------|------------|
>|q|Comma Seperated projectId .If the parameter is not passed or if it is empty it will return all the memberships for the user.

## Retrieve user preference
This API is used to retrieve user's preference information about the expiry option and the watermark template. 

Pass userId, ticket, clientId and platformId as header parameters

- **URL**: /rms/rs/usr/preference
- **method**: GET
- **produces**: application/json


		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "OK",
			"results": {
				"watermark": "$(User)$(Break)$(Date)$(Time)",
				"expiry": {
					"option": 2,
					"endDate": 1514735999999
				}
			}
		}

## Update user preference
This API is used to update user's preference information about the expiry option and the watermark template. 

Pass userId, ticket, clientId and platformId as header parameters

- **URL**: /rms/rs/usr/preference
- **method**: PUT
- **consume**: application/json

		// Option 0: this is for never expired option
		{
		    "parameters":{
		        "expiry": {
		             "option":0
		         },
		        "watermark": "$(User)\n$(Date) $(Time)"
		    }
		}

		// Option 1: this is for relative expiry date
		{
		    "parameters":{
		        "expiry": {
		             "option":1,
		             "relativeDay" : {
		                   "year": 1,
		                   "month": 2,
		                   "week": 3,
		                   "day": 6
		              }
		         },
		        "watermark": "$(User)\n$(Date) $(Time)"
		    }
		}

		// Option 2: this is for absolute expiry date
		{
		    "parameters":{
		        "expiry": {
		             "option":2,
		             "endDate" : 1469091455000
		         },
		        "watermark": "$(User)\n$(Date) $(Time)"
		    }
		}
		
		// Option 3: this is for range  expiry date
		{
		    "parameters":{
		        "expiry": {
		             "option":3,
		             "startDate" : 1469091455000,
					 "endDate" : 1469091455000
		         },
		        "watermark": "$(User)\n$(Date) $(Time)"
		    }
		}

		// If you just want to update watermark not expiry, can set watermark tag only like
		{
		    "parameters":{
		        "watermark": "$(User)\n$(Date) $(Time)"
		    }
		}
		// same to the expiry
		{
		    "parameters":{
		        "expiry": {
		             "option":0
		         }
		    }
		}




- **Supported Response Status Code**:

> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  200 | Success.|
> |  304 | Folder not modified.|
> |  400 | Missing request.|
> |  401 | Authentication failed/Missing login parameters.|
> |  4001 | Watermark too long. (255)|
> |  4002 | Invalid parameters.|
> |  500 | Internal Server Error.|

## List Users
This API is used to retrieve user list

- **URL**: /rms/rs/usr/list?searchString=user
- **method**: GET
- **consume**: N/A
- **produces**: application/json


		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "OK",
			"serverTime": 1490189844715,
                        "results": {
                            "totalUsers": 4,
                            "users": [
                                {
                                    "userId": "1",
                                    "ticket": "8ED53FDF6C5C35185CCF37664F15073F"
                                    "name": "test",
                                    "creationTime": "1528185046979",
                                    "status": 0,
                                    "isSuperAdmin": true
                               }
                            ]
                        }
		}

## Assign API User
This api is used to assign the api user

- **URL**: /rms/rs/usr/apiUser
- **method**: PUT
- **consume**: application/json

		{
			"parameters": {
				"apiUserId": "1"
			}
		}

- **produces**: application/json


		Jsonresponse object looks like:
		{
			"statusCode": 200,
                        "message": "OK",
                        "serverTime": 1544173822640,
                        "results": {
                            "ticket": "8ED53FDF6C5C35185CCF37664F15073F",
                            "appId": 1
                        }
		}