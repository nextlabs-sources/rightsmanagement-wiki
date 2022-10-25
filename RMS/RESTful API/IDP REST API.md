# IDP service

 - [List IDPs of a tenant with User Attributes Map](#markdown-header-list-idps-of-a-tenant-with-user-attributes-map)
 - [Set IDP User Attributes](#markdown-header-set-idp-user-attributes) 
 - [Add an IDP to a tenant](#markdown-header-add-an-idp-to-a-tenant)
 - [Delete an IDP from a tenant](#markdown-header-delete-an-idp-from-a-tenant)

## List IDPs of a tenant with User Attributes Map
This API is used to list IDPs of a tenant on RMS server. Supported IDP types can be found in 
[Login REST API](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Login%20REST%20API)

- **URL**: /rms/rs/idp/{tenantId}/userAttrMap
- **method**: GET
- **produces**: application/json
```
#!json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1515394303089,
    "results": {
        "idps": [
            {
                "id": 1,
                "type": 2
            },
            {
                "id": 2,
                "type": 3
            },
            {
                "id": 3,
                "type": 1,
                "attributes": "{\"buttonText\":\"Log In with Okta\"}",
                "userAttrMap": {\"Company\":\"Comp.\", \"Nationality\":\"Country\", \"Type\":\"Type\"}
            },
            {
                "id": 4,
                "type": 0
            },
            {
                "id": 5,
                "type": 4,
                "attributes": "{\"domain\":\"qapf1.qalab01.nextlabs.com\"}",
                "userAttrMap": {\"Company\":\"Comp.\", \"Nationality\":\"Country\", \"Type\":\"Type\"}
            }
        ]
    }
}

```

## Set IDP User Attributes
This API is used to add a IDP to a tenant. The userId/ticket must belong to the administrator of {tenantId}. Supported IDP types can be found in 
[Login REST API](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Login%20REST%20API)

- **URL**: /rms/rs/idp/{tenantId}/userAttrMap
- **method**: POST
- **consume**: application/json
```
#!json
{
   "parameters":{
      "attrMap": [
			{
                "id": 1,
                "userAttrMap": {\"Company\":\"Comp.\", \"Nationality\":\"Country\", \"Type\":\"Type\"}
            },
            {
                "id": 2,
                "userAttrMap": {\"Company\":\"Comp.\", \"Nationality\":\"Country\", \"Type\":\"Type\"}
            },
            {
                "id": 3,
                "userAttrMap": {\"Company\":\"Comp.\", \"Nationality\":\"Country\", \"Type\":\"Type\"}
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
    "serverTime": 1515394846125
}
```

> |  *Error code*                  |  *Description*   |
> |-------------------------|------------|
> |  400	    | Missing request| 
> |  404	    | Tenant not found| 
> |  403	    | Access denied| 
> |  5002	    | Attribute list cannot contain more than the specified number of attributes| 
> |  5003	    | User attribute does not exist or not selected| 
> |  5001	    | UserAttrMap exceeds length limit| 
> |  4003	    | Attribute name cannot contain special characters other than space, - and _| 



## Add an IDP to a tenant
This API is used to add a IDP to a tenant. The userId/ticket must belong to the administrator of {tenantId}. Supported IDP types can be found in 
[Login REST API](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Login%20REST%20API)

- **URL**: /rms/rs/idp/{tenantId}
- **method**: PUT
- **consume**: application/json
```
#!json
{
   "parameters":{
      "idp": {
      	"type": 1,
      	"attributes": "{appId: \"1746174985599401\", appSecret: \"fe7b0fe0d9915aceba6ffbd0090e1cf7\"}"
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
    "results": {
        "id": "a273f0e0-b7ad-4877-a330-949a54dc4251"
    }
}
```

## Delete an IDP from a tenant
This API is used to delete a IDP from a tenant. The userId/ticket must belong to the administrator of {tenantId}. Supported IDP types can be found in 
[Login REST API](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Login%20REST%20API)

- **URL**: /rms/rs/idp/{tenantId}/{idp_id}
- **method**: DELETE
- **produces**: application/json
```
#!json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1515393753366
}
```