# Policy Evaluation

 - [Policy Evaluation API](#markdown-perform-policy-evaluation)

## Perform Policy Evaluation
This API is used to perform policy evaluation  

- **URL**: /rms/rs/policyEval
- **method**: POST
- **consume**: application/json
```
#!json
{
   "parameters":{
      "evalRequest":{
         "adhocPolicy":"Policy body",
         "membershipId":"member1@skydrm.com",
         "resources":[
            {
               "dimensionName":"from",
               "resourceType":"fso",
               "resourceName":"Example1.pdf",
               "duid":"12345",
               "classification":{
                  "itar":[
                     "confidential"
                  ],
                  "Applicable Licenses":[
                     "Lic1",
                     "Lic2"
                  ]
               },
               "attributes":{
                  "file creation date":[
                     "10-jan-2018"
                  ]
               }
            }
         ],
         "rights":255,
         "user":{
            "id":9,
            "attributes":{
                  "Job Function":[
                     "engineer"
                  ],
                  "Age": [
                     "14"
                  ]
            }
         },
         "application":{
            "name":"RMS",
            "path":"/path/to/application",
            "pid":"12345",
            "attributes":{
               "publisher":[
                  "nextlabs",
                  "v1"
               ],
               "licensed":[
                  "yes"
               ]
            }
         },
         "host":{
            "ipAddress":"118.189.77.114"
         },
         "environments":[
            {
               "name":"environment",
               "attributes":{
                  "connection_type":[
                     "console"
                  ]
               }
            }
         ],
         "evalType":0
      }
   }
}
```
#### Notes:
	 1. Compulsory fields: membershipId, resources, user, evalType, rights
	 2. evalType is an enum which has 4 possible values: 0(Central policy only); 1(Adhoc policy only); 2(Evaluate both but take central policy in precedence); 3(Evaluate both but take adhoc policy in precedence). Currently the only supported type is 0. In response we are currently returning protectionType (0 adHoc, 1 Central policy eval) depending on how it was evaluated (in policyEval rest API it'll be 1).
	 3. Membership is required to filter policies defined.
	 4. If user attributes are specified, RMS will combine them with attributes stored in RMS cache for policy evaluation.
	 5. environments is an array.
	 6. host and application are optional, you can leave the entire field empty. But if host is present then you need to specify ipAddress or hostname or both of them, if application is present then name is must to have.
	 7. adhocPolicy is reserved to be used in the future.
	 8. rights definition: 
	    VIEW(1),
	    EDIT(1 << 1),
	    PRINT(1 << 2),
	    CLIPBOARD(1 << 3),
	    SAVEAS(1 << 4),
	    DECRYPT(1 << 5),
	    SCREENCAP(1 << 6),
	    SEND(1 << 7),
	    CLASSIFY(1 << 8),
	    SHARE(1 << 9),
	    DOWNLOAD(1 << 10),
	    WATERMARK(1 << 30);


- **produces**: application/json
```
#!json
{
    "statusCode": 200,
    "message": "Policy Evaluated",
    "serverTime": 1520841284693,
    "results": {
        "adhocObligations": [],
        "rights": 5,
        "obligations": [],
        "protectionType": 1
    }
}

```

Sample Policy:
```
ID 82 STATUS APPROVED POLICY "ROOT_82/Test Eval API"
    ATTRIBUTE TRUE_ALLOW
    TAG tokenGroupName="16036ea5-2409-4b63-9e50-5650d32add48"
    FOR resource.fso.itar = "confidential"
    ON (RIGHT_PRINT OR RIGHT_VIEW)
    BY user.mail = "rmsuser39@gmail.com"
    DO allow
    ON deny DO log
```