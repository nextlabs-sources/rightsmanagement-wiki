# Tenant service

 - [Register a new tenant on RMS](#markdown-header-register-a-new-tenant-on-rms)
 - [Update tenant profile](#markdown-header-update-tenant-profile) 
 - [Get tenant preferences](#markdown-header-get-tenant-preferences)
 - [Set tenant preferences](#markdown-header-set-tenant-preferences)
 - [Get Tenant User Attributes](#markdown-header-get-tenant-user-attributes)
 - [Set Tenant User Attributes](#markdown-header-set-tenant-user-attributes)
 - [Get Project Admin](#markdown-header-get-project-admin)
 - [Set Project Admin](#markdown-header-set-project-admin)
 - [Get Idp details](#markdown-header-get-idp-details)

## Register a new tenant on RMS
This API is used to create a new tenant on RMS server. 

- **URL**: /rms/rs/tenant
- **method**: PUT
- **consume**: application/json
```
#!json
{
   "parameters":{
      "name":"skydrm.com",
      "otp":"DDDDF9F2636532519D3E5BF3C6B51C46",
      "displayName":"SkyDRM",
      "admin":"admin@nextlabs.com",
      "dns":"rms.nextlabs.com",
      "preference":"{\"heartbeat\":1000}"
   }
}
```
- **produces**: application/json
```
#!json
{
   "statusCode":200,
   "message":"Tenant registered"
}
```

## Update tenant profile
This API is used to update tenant profile. 

- **URL**: /rms/rs/tenant
- **method**: POST
- **consume**: application/json

```
#!json
{
   "parameters":{
      "userId":"3",
      "ticket":"DDDDF9F2636532519D3E5BF3C6B51C46",
      "tenantId":"86e2b8a3-65de-4af6-a11f-add61a9e90db",
      "displayName":"SkyDRM",
      "admin":"admin@nextlabs.com",
      "dns":"rms.nextlabs.com",
      "preference":"{\"heartbeat\":1000}"
   
   }
}
```
- **produces**: application/json
```
#!json
{
   "statusCode":200,
   "message":"Tenant updated"
}
```

## Get tenant preferences
This API is used to update tenant profile. 

### v2 ###

Pass userId and ticket as header parameters. The {tenant_id} should be the default/master tenant or a sub-tenant ID, not the project tenant ID. 

- **URL**: /rms/rs/tenant/v2/{tenant_id}
- **method**: GET
- **consume**: N/A
- **produces**: application/json
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "extra":{
      "heartbeat":1000,
      "CLIENT_HEARTBEAT_FREQUENCY":"10",
      "ADHOC_ENABLED":true,
      "SYSTEM_DEFAULT_PROJECT_TENANTID": "86e2b8a3-65de-4af6-a11f-add61a9e90db", // will change to SYSTEM_BUCKET_NAME
      "PROJECT_ADMIN":["1@2.com"]
   }
}
```

Note:

- the `SYSTEM_PROJECT_TENANTID` feature is not yet implemented as of build # 183 on 2019-01-24. 
- `CLIENT_HEARTBEAT_FREQUENCY` is in minutes.
### ~~v1~~ (deprecated) ###
- **URL**: /rms/rs/tenant/{user_id}/{ticket}/{tentant_id}
- **method**: GET
- **consume**: N/A
- **produces**: application/json

```
#!json
{
   "statusCode":200,
   "message":"OK",
   "extra":{
      "heartbeat":1000,
      ...
   }
}
```

## Set Tenant Preferences

This API is called by tenant admin. This will enable him to enable/disable adhoc-rights for project

Pass userId and ticket as header parameters

- **URL**: /rms/rs/tenant/preference/{tenant_id}
- **method**: POST
- **consume**: application/json

```
#!json
{
   "parameters":{
      "adhocEnabled": false 
   }
}
```

- **produces**: application/json

```
#!json
{
   "statusCode": 200,
    "message": "OK",
}
```

## Get Tenant User Attributes

Pass userId and ticket as header parameters

- **URL**: /rms/rs/tenant/{tenant_id}/userAttr
- **method**: GET
- **consume**: N/A
- **produces**: application/json
```
#!json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1515394303089,
    "results": {
        "attrList": [
            {
                "name": "Comany",
                "custom": true,
                "selected": false
            },
            {
                "name": "Type",
                "custom": false,
                "selected": true
            },
            {
                "name": "Nationality",
                “custom": true,
                "selected": true
            }
        ]
    }
}

```
## Set Tenant User Attributes
This API is used to update tenant user attributes. 

- **URL**: /rms/rs/tenant/{tenant_id}/userAttr
- **method**: POST
- **consume**: application/json

```
#!json
{
   "parameters":{
      "attributes": [{
                "name": "Comany",
                "custom": true,
                "selected": false
            },
            {
                "name": "Type",
                "custom": false,
                "selected": true
            },
            {
                "name": "Nationality",
                "custom": true,
                "selected": true
            }]
      }
   }
}

```
- **produces**: application/json
```
#!json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1515394846125,
}

```

> |  *Error code*                  |  *Description*   |
> |-------------------------|------------|
> |  400	    | Missing request| 
> |  403	    | Access denied| 
> |  4001	    | User attribute name cannot exceed 50 characters| 
> |  500	    | Internal Server Error| 
> |  400	    | Malformed request| 
> |  4003	    | Attribute name cannot contain special characters other than space, - and _| 

## Get Project Admin
Pass userId, ticket, clientId, platformId as header parameters

- **URL**: /rms/rs/tenant/{tenant_id}/projectAdmin
- **method**: GET
- **consume**: N/A
- **produces**: application/json


**Note that the tenant admins are added first to response**
```
#!json

{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1521096105466,
    "results": {
        "projectAdmin": [
            {
                "email": "skydrm@gmail.com",
                "tenantAdmin": true
            },
            {
                "email": "a@gmail.com"
            },
            {
                "email": "b@gmail.com"
            },
            {
                "email": "c@gmail.com"
            }        
        ]
    }
}
```
  
Note: `tenant_id` refers to the parent tenant ID, not the project tenant ID. For SAAS deployment, this API will need to be secured further based on membership as the check whether caller was tenant admin, was removed in Apr 2019 to support client team's requirement for on-prem.
  

## Set Project Admin
Pass userId, ticket, clientId, platformId as header parameters

- **URL**: /rms/rs/tenant/{tenant_id}/projectAdmin
- **method**: PUT
- **produces**: application/json
- **consume**: json body as below:

```
#!json

{
   "parameters":{
                 "projectAdmin": ["skydrm@gmail.com","a@gmail.com","b@gmail.com","c@gmail.com"]
      }
}
```
Incorrect emails with return error like:

```
#!json

{
    "statusCode": 400,
    "message": "One or more emails have an invalid format.",
    "serverTime": 1521097085603
}
```
In the client supplied list of emails, only the email addresses of users who are not Tenant Admins will be inserted into the database **(tenant admin emails are ignored)**.

On completion the call produces:

```
#!json

{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1521097158762
}
```

## Get Idp details
Pass userId, ticket, clientId, platformId as header parameters

- **URL**: /rms/rs/tenant/{tenant_id}/idp/details
- **method**: GET
- **consume**: N/A
- **produces**: application/json

```
#!json

{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1521096105466,
    "results": {
        "idps": [
           {
                "id": 2794,
                "type": 4,
                "attributes": "{\"ldapType\":\"AD\",\"name\":\"MyAD\",\"hostName\":\"10.23.58.5\",\"domain\":\"qapf1.qalab01.nextlabs.com\",\"searchBase\":\"DC=qapf1,DC=qalab01,DC=nextlabs,DC=com\",\"userSearchQuery\":\"(&(objectClass=user)(sAMAccountName=$USERID$))\",\"rmsGroup\":\"\",\"ldapSSL\":false,\"securityPrincipalUseUserID\":false}"
            },
            {
                "id": 2792,
                "type": 2,
                "attributes": "{\"appId\":\"298938231237-8fuecnud9f922golci5tq52gqpk7sgr6.apps.googleusercontent.com\",\"appSecret\":\"1j8HpiOSvfEJsJoV3ktAZTE5\"}"
            },
            ...       
        ]
    }
}
```