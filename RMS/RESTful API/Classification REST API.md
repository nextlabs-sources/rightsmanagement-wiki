# RMS Classifications REST API

RMS exposes the following web services to access the classification profile for each tenant.

* [Get Classification Profile](#markdown-header-get-classification-profile)
* [Update Classification Profile](#markdown-header-update-classification-profile)


## Get Classification Profile
 - **URL:** /rms/rs/classification/{tokenGroupName}
 - **method:** GET
 - **headers** Pass userId and ticket as header parameters
 - **produces:** application/json;

```
#!json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1484060079827,
    "results": {
        "maxCategoryNum": 5,
        "maxLabelNum": 10,
        "categories":[
            {
                "name": "Sensitivity",
                "multiSelect": true,
                "mandatory": true,
                "labels": [
                    {
                        "name": "Non-Business",
                        "default":true
                    },
                    {
                        "name": "General Business"
                    },
                    {
                        "name": "Confidential"
                    }
                ]
            },
            {
                "name": "Project",
                "multiSelect": false,
                "mandatory": true,
                "labels": [
                    {
                        "name": "Project"
                    }
                ]
            }
        ]
    }
}
```  
  
**Note: ** To retrieve the classification profile for the system bucket, the `tokenGroupName` must be the token group for the parent tenant, not the system bucket. This is because the system bucket uses the classification profile of the tenant and there is no separate classification profile defined for the system bucket. This is aligned with the behaviour of the SkyDRM server in past releases.     
   
   
## Update Classification Profile

 - **URL:** /rms/rs/classification/{tokenGroupName}
 - **method:** POST
 - **consumes:** application/json; Pass userId and ticket as header parameters

```
#!json 
{
   "parameters":{
      "categories":[
            {
                "name": "Sensitivity",
                "multiSelect": true,
                "mandatory": true,
                "labels": [
                    {
                        "name": "Non-Business",
                        "default":true
                    },
                    {
                        "name": "General Business"
                    },
                    {
                        "name": "Confidential"
                    }
                ]
            },
            {
                "name": "Project",
                "multiSelect": false,
                "mandatory": true,
                "labels": [
                    {
                        "name": "Project"
                    }
                ]
            }
        ]
   }
}
```
      
 - **produces:** application/json

```
#!json
{
    "statusCode": 201,
    "message": "Classification profile successfully updated",
    "serverTime": 1484060079827
}
```
  
**Note: ** To update the classification profile for the system bucket, the `tokenGroupName` must be the token group for the parent tenant, not the system bucket. This is aligned with the behaviour of the SkyDRM server in past releases.    
  

We have added naming validation where category/label name must not exceed 50 characters.

Category/label name can contain:

1) 'a' to 'z'

2) 'A' to 'Z'

3) '0' to '9'

4) special characters except percentage(%), single quotes(') and double quotes(")


- **Supported Response Status Code**:
> |  *Status Code*                  |  *Description*   |
> |-------------------------|------------|
> |  201 | Classification profile successfully updated.|
> |  400 | Missing required parameters. (Missing category names or label names)|
> |  401 | Authentication failed.|
> |  403 | Access denied (Only Tenant admin/project owner can modify the classification profile) |
> |  4001 | Category/Label name Too Long.|
> |  4002 | Category/Label name contains illegal characters. |
> |  4003 | Duplicate Category/Label name.|
> |  4004 | A non multi-select category cannot have multiple default labels |
> |  4005 | Category/Label limit exceeded. |
> |  500 | Internal Server Error.|