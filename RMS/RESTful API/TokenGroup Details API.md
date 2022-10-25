# TokenGroup Details  

 - [Get TokenGroup Details](#markdown-get-tokengroup-details)

## Get TokenGroup Details  
This API is used to retrieve token group details and is only called by rmjavasdk-ng.

Pass userId, ticket, clientId, platformId as header parameters. The {tenant_id} should be the default/master tenant or a sub-tenant ID, not the project tenant ID.

URL: /rms/rs/tokenGroup/details?tokenGroupName={tokenGroupName}
method: GET
consumes: N/A
produces: application/json

Sample responses:

API request and responses:

### System bucket:  
GET : /rs/tokenGroup/details?tokenGroupName=a7ef38ba-8839-45bd-ab78-f5046d0221fd_system


```
#!json

response:

{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1556246026434,
    "results": {
        "tokenGroupName": "a7ef38ba-8839-45bd-ab78-f5046d0221fd_system",
        "parentTenantName": "a7ef38ba-8839-45bd-ab78-f5046d0221fd",
        "tokenGroupType": 2,
        "parentTenantId": "ee43ed26-c628-48ff-8b65-f9cc3d0ad3c4",
        "membershipName": "user1@a7ef38ba-8839-45bd-ab78-f5046d0221fd"
    }
}
```

### Project :    

GET /rs/tokenGroup/details?tokenGroupName=a7ef38ba-8839-45bd-ab78-f5046d0221fd_TestProject_1


```
#!json

response:

{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1556246159675,
    "results": {
        "tokenGroupName": "a7ef38ba-8839-45bd-ab78-f5046d0221fd_TestProject_1",
        "parentTenantName": "a7ef38ba-8839-45bd-ab78-f5046d0221fd",
        "tokenGroupType": 1,
        "parentTenantId": "ee43ed26-c628-48ff-8b65-f9cc3d0ad3c4",
        "projectId": 1,
        "membershipName": "m2@a7ef38ba-8839-45bd-ab78-f5046d0221fd_TestProject_1"
    }
}
```

### tenant:    
GET /rs/tokenGroup/details?tokenGroupName=a7ef38ba-8839-45bd-ab78-f5046d0221fd


```
#!json

response:

{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1556246185292,
    "results": {
        "tokenGroupName": "a7ef38ba-8839-45bd-ab78-f5046d0221fd",
        "tenantName": "a7ef38ba-8839-45bd-ab78-f5046d0221fd",
        "tokenGroupType": 0,
        "tenantId": "ee43ed26-c628-48ff-8b65-f9cc3d0ad3c4",
        "membershipName": "m1@a7ef38ba-8839-45bd-ab78-f5046d0221fd"
    }
}
```