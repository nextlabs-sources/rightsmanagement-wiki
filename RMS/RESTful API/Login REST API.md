# Login service

 - [Login flow for RMC/RMC mobile/Web client](#markdown-header-login-flow-for-rmcrmc-mobileweb-client)
 - [Display tenant specific login page](#markdown-header-display-tenant-specific-login-page)
 - [Basic Login](#markdown-header-basic-login)
 - [Facebook Login](#markdown-header-facebook-login)
 - [Google Login](#markdown-header-google-login)
 - [Logout](#markdown-header-logout)

## Login flow for RMC/RMC mobile/Web client
1. Client need configure following information:
	- Router url: default should be: `https://r.skydrm.com/router`
	- Client tenant name, default tenant name should be "**skydrm.com**".
Client need query where is your rms server: router_url + tenant_name, for example: `https://r.skydrm.com/router/rs/q/tenant/skydrm.com`
return url will be like: `https://www.skydrm.com/rms'

2. If the client does not know their tenant name then they can make the query to their router url (which they will know, or the default https://r.skydrm.com/router) as follows:
`https://r.skydrm.com/router/rs/q/defaultTenant`
This will return the rms url for the default tenant (and if router was the default value https://r.skydrm.com/router then rms url be returned as https://skydrm.com/rms)

Example: https://sudong.nextlabs.com/router/rs/q/defaultTenant will return
https://sudong.nextlabs.com/rms 




3. Once client get rms server url, client should cache it in client side to avoid repeating query.
4. Client need to set clienId and platformId in the cookie, and open browser and display login page: `https://skydrm.com/rms/rs/tenant?tenant=skydrm.com`
	- Web client will inherit cookie and be redirect to web client URL.
	- RMC/RMC mobile should intercept json reponse payload (specifically with message="Authorized") and close the browser, and store user information in client side.


## Display tenant specific login page
This API is used to display co-branded tenant login page in browser.

- URL: https://www.skydrm.com/rms/rs/tenant?tenant=skydrm

    if tenant parameter is not specified, server will pick a default tenant for that RMS server. 

This URL will be redirect to proper tenant login page. After user successfully login, Browser will receive a AJAX json payload like:
```
#!json
{
   "statusCode":200,
   "message":"Authorized",
   "serverTime":1471296772918,
   "idpType":0,
   "extra":{
      "userId":1,
      "ticket":"7BF546A96F307E2C0C211DCFD52AE1ED",
      "ttl":1467743717902,
      "name":"user_0001",
      "email":"client_0001@skydrm.com",
      "memberships":[
         {
            "id":"m1@skydrm",
            "type":0,
            "tenantId":"3783caa4-744f-4c69-a750-0cf7cd6f7540"
            // will add a new parameter "tokenGroupName"
         }
      ]
   }
}
```
javascript in the browser will redirect to main page after receive this json. RMC/RMC mobile, should intercept this json payload and close browser.


- **Supported IDP Type**:
> |  *Type*                  |  *Value*   |
> |-------------------------|------------|
> |  DB| 0|
> |  SAML|1|
> |  GOOGLE| 2|
> |  FACEBOOK| 3|
> |  AD| 4|

## Basic Login
This API is used to login to RMS native database user account. 

- **URL**: /rms/rs/usr
- **method**: POST
- **consume**: application/x-www-form-urlencoded

> | Parameter   |  Description            |
> |-------------|-------------------------|
> | email       | Email address           |
> | password	| MD5 hash value          |
> | rememberMe  | true / false (optional) |


- **produces**: application/json

```
#!json
{
   "statusCode":200,
   "message":"Authorized",
   "serverTime":1498190244565,
   "extra":{
      "userId":13,
      "ticket":"B122849DC1E2661CE5CF693817DA7F29",
      "tenantId":"21b06c79-baab-419d-8197-bad3ce3f4476",
      "ttl":1498276644563,
      // will add a new parameter "tokenGroupName" for the login tenant
      "name":"Kelly Huang",
      "email":"kelly.huang@nextlabs.com",
      "idpType":0,
      "memberships":[
         {
            "id":"m17@skydrm.com",
            "type":1,
            "tenantId":"21b06c79-baab-419d-8197-bad3ce3f4476", // will be changed to "tokenGroupName"
            "projectId":1
         },
         {
            "id":"m28@t-5948bf18ba1749e8a6a99b66edba429d",
            "type":1,
            "tenantId":"8360d053-9f7d-4bcc-a265-dab4a31a92fd",
            "projectId":9
         }
      ],
      "defaultTenant":"skydrm.com",
      "defaultTenantUrl":"https://rmtest.nextlabs.solutions/rms"
   }
}
```
## Facebook Login
This API is used to login to RMS with facebook account. 

- **URL**: /rms/rs/login/fb
- **method**: POST
- **consume**: application/x-www-form-urlencoded


		url encoded form data post to RMS:

		token=C9808CFE7A98ACF1088E791B41BDAEED
				
- **produces**: application/json
```
#!json
{
   "statusCode":200,
   "message":"Authorized",
   "serverTime":1498190244565,
   "extra":{
      "userId":13,
      "ticket":"B122849DC1E2661CE5CF693817DA7F29",
      "tenantId":"21b06c79-baab-419d-8197-bad3ce3f4476",
      // will add a new parameter "tokenGroupName" for the login tenant
      "ttl":1498276644563,
      "name":"Kelly Huang",
      "email":"kelly.huang@nextlabs.com",
      "idpType":3,
      "memberships":[
         {
            "id":"m17@skydrm.com",
            "type":0,
            "tenantId":"21b06c79-baab-419d-8197-bad3ce3f4476",
            "projectId":1// will be changed to "tokenGroupName"
         },
         {
            "id":"m28@t-5948bf18ba1749e8a6a99b66edba429d",
            "type":1,
            "tenantId":"8360d053-9f7d-4bcc-a265-dab4a31a92fd",// will be changed to "tokenGroupName"
            "projectId":9
         }
      ],
      "defaultTenant":"skydrm.com",
      "defaultTenantUrl":"https://rmtest.nextlabs.solutions/rms"
   }
}
```

## Google Login
This API is used to login to RMS with google account. 

- **URL**: /rms/rs/login/google
- **method**: POST
- **consume**: application/x-www-form-urlencoded


		url encoded form data post to RMS:

		token=C9808CFE7A98ACF1088E791B41BDAEED
				
- **produces**: application/json
```
#!json
{
   "statusCode":200,
   "message":"Authorized",
   "serverTime":1498190244565,
   "extra":{
      "userId":13,
      "ticket":"B122849DC1E2661CE5CF693817DA7F29",
      "tenantId":"21b06c79-baab-419d-8197-bad3ce3f4476",
      // will add a new parameter "tokenGroupName" for the login tenant
      "ttl":1498276644563,
      "name":"Kelly Huang",
      "email":"kelly.huang@nextlabs.com",
      "idpType":2,
      "memberships":[
         {
            "id":"m17@skydrm.com",
            "type":0,
            "tenantId":"21b06c79-baab-419d-8197-bad3ce3f4476",
            "projectId":1// will be changed to "tokenGroupName"
         },
         {
            "id":"m28@t-5948bf18ba1749e8a6a99b66edba429d",
            "type":1,
            "tenantId":"8360d053-9f7d-4bcc-a265-dab4a31a92fd",// will be changed to "tokenGroupName"
            "projectId":9
         }
      ],
      "defaultTenant":"skydrm.com",
      "defaultTenantUrl":"https://rmtest.nextlabs.solutions/rms"
   }
}

```

### Logout
This is the entry point to clean the login session.

- **URL**: /rms/rs/usr/logout
- **method**: GET
- **consume**: N/A
- **produces**: application/json
- **request header**: userid, ticket and all related parameters in https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Common%20Parameters%20for%20REST%20API
```
#!json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1471296772918
}

```
- **Supported Query Parameter**:
> |  *Name*                  |  *Value*   | Description |
> |--------------------------|------------|-------------|
> |  forceLogout             | true/false | This parameter is optional. It will logout all session for current user if true.|


- **Ticket lifetime**:

> |             |  *Basic Login (Remember Me)* | *Basic Login (w/o Remember Me)* | *Google* | *Facebook* |
> |-------------|------------------------------|---------------------------------|----------|------------|
> | RMS         |                       14 days|                           1 day |     1 day|       1 day|
> | RM Client	|                          N/A |                         30 days |   30 days|     30 days|