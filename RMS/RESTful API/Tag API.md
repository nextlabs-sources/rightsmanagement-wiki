# RMS Tag REST API

RMS exposes the following web services to create and access tags on tenant and project level.

* [Update Tenant Tags](#markdown-header-update-tenant-tags)
* [Get Tenant Tags](#markdown-header-get-tenant-tags)
* [Update Project Tags](#markdown-header-update-project-tags)
* [Get Project Tags](#markdown-header-get-project-tags)

## Update Tenant Tags
 - **URL:** rms/rs/tags/tenant/{tenantId}
 - **method:** POST
 - **headers:** Pass userId, clientId and ticket as header parameters
 - **consumes:** description is an optional parameter
~~~
#!json
{
    "parameters":{
      "tagType":0,
       "tags":[
         {"name":"HR"},
         {"name":"IT"},         	
         {"name":"PUBLIC"}
      ]
   }
}
~~~
 - ** produces:** application/json;
~~~
#!json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1543821441663
}
~~~

## Get Tenant Tags
 - **URL:** rms/rs/tags/tenant/{tanantId}?type=0
 - **Method:** GET
 - **headers:** Pass userId, clientId and ticket as header parameters
 - **produces:** application/json;

~~~
#!json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1543827900087,
    "results": {
        "tags": [
            {
                "id": 3,
                "name": "HR",
                "description": "for HR",
                "orderId": 3,
                "type": 0
            },
            {
                "id": 4,
                "name": "IT",
                "description": "for IT",
                "orderId": 4,
                "type": 0
            },
            {
                "id": 5,
                "name": "PUBLIC",
                "description": "for all",
                "orderId": 5,
                "type": 0
            }
        ]
    }
}
~~~

## Update Project Tags
 - **URL:** rms/rs/tags/project/{projectId}
 - **method:** POST
 - **headers:** Pass usetId, clientId and ticket as header parameters
 - **consumes:**
~~~
#!json
{
    "parameters":{
           "projectTags":[3,4]
   }
}
~~~
 - ** produces:** application/json;
~~~
#!json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1543828006812
}
~~~

## Get Project Tags
 - **URL:** rms/rs/tags/project/{projectId}
 - **Method:** GET
 - **headers:** Pass userId, clientId and ticket as header parameters
 - **produces:** application/json;
~~~
#!json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1543821404548,
    "results": {
        "tags": [
            {
                "id": 3,
                "name": "HR",
                "description": "for HR",
                "orderId": 3,
                "type": 0
            },
            {
                "id": 4,
                "name": "IT",
                "description": "for IT",
                "orderId": 4,
                "type": 0
            }
        ]
    }
}
~~~