# Heartbeat

## v2
This API is used to get policy bundles when client contacts RMS. 

- **URL**: /rms/rs/v2/heartbeat
- **method**: POST
- **headers**: userId, ticket, clientId, platformId (1000), Content-Type (application/json) -- key value pairs
- **consume**: application/json; Pass userId and ticket as header parameters
- **body**:
```json
{
   "parameters":{
      "platformId":0,
      "clientData": [
      	{
	      	"tokenGroupName": "skydrm.com",
	      	"policyBundleTimeStamp": 123,
	      	"configurationModifiedTimeStamp": 12
    	},
    	{
	      	"tokenGroupName": "skydrm.com_a_1",
	      	"policyBundleTimeStamp": 123,
	      	"configurationModifiedTimeStamp": 12
    	}]
   }
}
```

- **produces**: application/json
```json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1520401503023,
    "results": {
        "heartbeatFrequency": 1000,
        "policyConfigData": [
            {
                "tokenGroupName": "skydrm.com",
                "policyBundleTimeStamp": 1520401254866,
                "policyBundle": "ID 73 STATUS APPROVED POLICY \"ROOT_73/PKPOLICY\"\r    DESCRIPTION \"\"\r\n    ATTRIBUTE TRUE_ALLOW\r\n    TAG tenant=\"skydrm.com\"\r\n    TAG pktag=\"PKTAG\"\r\n    FOR TRUE\r\n    ON RIGHT_VIEW\r\n    BY TRUE\r\n    DO allow\r\n    ON deny DO log\r\n\nID 75 STATUS APPROVED POLICY \"ROOT_75/SkyDRM policy\"\r    ATTRIBUTE TRUE_ALLOW\r\n    TAG tenant=\"skydrm.com\"\r\n    FOR TRUE\r\n    ON TRUE\r\n    BY TRUE\r\n    DO allow\r\n    ON deny DO log\r\n",
                "configurationModifiedTimeStamp": 1517998870135,
                "watermarkConfig": {},
                "classificationCategories": []
            },
            {
                "tokenGroupName": "skydrm.com_a_1",
                "policyBundleTimeStamp": 1520401255229,
                "policyBundle": "ID 76 STATUS APPROVED POLICY \"ROOT_76/TenantPolicy\"\r    DESCRIPTION \"t-d0fb1eabcfc94b20984c5816071bc004\"\r\n    ATTRIBUTE TRUE_ALLOW\r\n    TAG tenant=\"t-d0fb1eabcfc94b20984c5816071bc004\"\r\n    FOR TRUE\r\n    ON TRUE\r\n    BY TRUE\r\n    DO allow\r\n    ON deny DO log\r\n",
                "configurationModifiedTimeStamp": 1519891154798,
                "watermarkConfig": {},
                "classificationCategories": [
                    {
                        "name": "Sensitivity",
                        "multiSelect": false,
                        "mandatory": false,
                        "labels": [
                            {
                                "name": "Sensitivity",
                                "default": false
                            }
                        ]
                    }
                ]
            }
        ],
        "watermarkConfig": {
            "serialNumber": "f8cf145d",
            "content": "{\r\n\"text\": \"$(User)\\n $(Date) $(Time)\",\r\n\"transparentRatio\": 70,\r\n\"fontName\": \"Arial Unicode MS\",\r\n\"fontSize\": 26,\r\n\"fontColor\": \"#008000\",\r\n\"rotation\": \"Anticlockwise\",\r\n\"repeat\": true,\r\n\"density\": \"Dense\"\r\n}"
        }
    }
}
```
Note:

- "heartbeatFrequency" is in minutes.
### Screenshot from Postman: ###
![hb.png](https://bitbucket.org/repo/dBgzdj/images/33471371-hb.png)

## FAQs/Notes ##

* ### Timestamps and Input Fields ###
Timestamps are passed in and returned as long number type (no quotes required, unlike tokenGroupName) and if their fields (policyBundleTimeStamp, configurationModifiedTimeStamp) are missing or misspelled, they are treated as a default value of 0 i.e server presumes the request is for all policies from time 0 in epoch time ms (0 ms from 01/01/1970). If "tokenGroupName" string or its value is misspelled RMS will still find the correct token group in the memberships (its possible there are new memberships since client last checked via CHB) and their data will be returned after evaluating against timestamps = default value of 0.

* ### Cache and heartbeat ###
The sequence diagrams below depicts the RMS's internal server cache and is unrelated to the client caching scheme (if/when its implemented). Similarly heartbeat refers to the one between CC and RMS server, and client heartbeat (RMC to RMS) is referred as CHB.

* ### Initial Heartbeat ###
Clients can make an initial heartbeat without any clientData field in request body (omit field entirely like below or make it "clientData": [] i.e empty array). Both will check and return the data for all token groups in the user's membership and evaluate the timestamp conditions with client's timestamps = 0 default value. 

Request - 
```
#!json

{
   "parameters":{
      "platformId":0
   }
}
```

or alternatively:
```
#!json

{
   "parameters":{
      "platformId":0,
      "clientData": []
   }
}
```
Response is same as postman screenshot.

* ### Response contents if no policy or configuration change ###

If RMS has no policies newer than client's policyBundleTimeStamp then it will not insert the field policyBundleTimeStamp nor the policyBundle string field for that tokenGroupName in the membership. 

If RMS has no config change newer than client's configurationModifiedTimeStamp then it will not insert the field configurationModifiedTimeStamp nor the watermarkConfig, classificationCategories fields for that tokenGroupName in the membership. 

If RMS has no new policies and no new config change, then that tokenGroupName will simply be excluded from the policyConfigData array of objects in the response, as follows:

Timestamp = 9999999999999 ms is 11/21/2286, 1:46:39 AM
Request - 
```
#!json

{
   "parameters":{
      "platformId":0,
      "clientData": [
      	{
	      	"tokenGroupName": "skydrm.com",
	      	"policyBundleTimeStamp": 123,
	      	"configurationModifiedTimeStamp": 9999999999999
    	},
    	{
	      	"tokenGroupName": "skydrm.com_a_1",
	      	"policyBundleTimeStamp": 9999999999999,
	      	"configurationModifiedTimeStamp": 9999999999999
    	}]
   }
}
```
Returns Response json - 

```
#!json

{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1520402998169,
    "results": {
        "heartbeatFrequency": 1000,
        "policyConfigData": [
            {
                "tokenGroupName": "skydrm.com",
                "policyBundleTimeStamp": 1520401254866,
                "policyBundle": "ID 73 STATUS APPROVED POLICY \"ROOT_73/PKPOLICY\"\r    DESCRIPTION \"\"\r\n    ATTRIBUTE TRUE_ALLOW\r\n    TAG tenant=\"skydrm.com\"\r\n    TAG pktag=\"PKTAG\"\r\n    FOR TRUE\r\n    ON RIGHT_VIEW\r\n    BY TRUE\r\n    DO allow\r\n    ON deny DO log\r\n\nID 75 STATUS APPROVED POLICY \"ROOT_75/SkyDRM policy\"\r    ATTRIBUTE TRUE_ALLOW\r\n    TAG tenant=\"skydrm.com\"\r\n    FOR TRUE\r\n    ON TRUE\r\n    BY TRUE\r\n    DO allow\r\n    ON deny DO log\r\n"
            }
        ],
        "watermarkConfig": {
            "serialNumber": "f8cf145d",
            "content": "{\r\n\"text\": \"$(User)\\n $(Date) $(Time)\",\r\n\"transparentRatio\": 70,\r\n\"fontName\": \"Arial Unicode MS\",\r\n\"fontSize\": 26,\r\n\"fontColor\": \"#008000\",\r\n\"rotation\": \"Anticlockwise\",\r\n\"repeat\": true,\r\n\"density\": \"Dense\"\r\n}"
        }
    }
}
```
Note that tokenGroupName "skydrm.com_a_1" for which client requested updates was excluded from returned policyConfigData. For tenantName "skydrm.com" there was no new config changes so only policyBundleTimeStamp and policyBundle was returned for that tenant.

* ### Empty json arrays ###
By design certain fields can be returned as empty arrays or strings (depending on their type. For e.g policyBundle can be empty string "" if policies were withdrawn for that token group and its policyBundleTimeStamp is present. classificationCategories can be [] if there is no classification as long as its configurationModifiedTimeStamp is present. policyConfigData can be [] implying that for the token groups in the clientData request sent by client, RMS found nothing new at all for all the token groups in the membership.
Request - 

```
#!json

{
   "parameters":{
      "platformId":0,
      "clientData": [
      	{
	      	"tokenGroupName": "skydrm.com",
	      	"policyBundleTimeStamp": 9999999999999,
	      	"configurationModifiedTimeStamp": 9999999999999
    	},
    	{
	      	"tokenGroupName": "skydrm.com_a_1",
	      	"policyBundleTimeStamp": 9999999999999,
	      	"configurationModifiedTimeStamp": 9999999999999
    	}]
   }
}
```
Response json - 

```
#!json

{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1520403479029,
    "results": {
        "heartbeatFrequency": 1000,
        "policyConfigData": [],
        "watermarkConfig": {
            "serialNumber": "f8cf145d",
            "content": "{\r\n\"text\": \"$(User)\\n $(Date) $(Time)\",\r\n\"transparentRatio\": 70,\r\n\"fontName\": \"Arial Unicode MS\",\r\n\"fontSize\": 26,\r\n\"fontColor\": \"#008000\",\r\n\"rotation\": \"Anticlockwise\",\r\n\"repeat\": true,\r\n\"density\": \"Dense\"\r\n}"
        }
    }
}
```

* ### Mobile Clients ###
At present the platformId field is not used but in the future we can choose to conditionalize excluding the policyBundle string from response if platformId indicates mobile client (if/when their decision to use policy controller is finalized).


## Websequence diagrams of updated heartbeat flow with logic: ##

### CC-RMS: ###
![CC_RMS.png](https://bitbucket.org/repo/dBgzdj/images/105115274-CC_RMS.png)

```
#!python

note left of CC
PB: Policy Bundle string per token group\n
client.PB_TS: Timestamp supplied by client when it \n*last checked policies* for that tokenGroupName\n
PB_TS: Server timestamp when policies for that\ntoken group were *most recently stored in RMS cache*\n
CHB: RMC Client Heartbeat with RMS\n(say for e.g every 4 hours)\n
current_time: Now(), unrelated to client.PB_TS\nbut logically its always true that\ncurrent_time >= client.PB_TS\n
end note

participant CC
participant RMS
participant RMS Cache as Cache

note left of RMS
Heartbeat \nbetween \nCC & RMS
end note

CC<->RMS:Heartbeat frequency\nfor e.g every 1 hour\nstarting @12PM
activate RMS
RMS->Cache:Update cache with\n[PB + PB_TS]\n for e.g here @12PM
activate Cache
Cache->Cache:Insert/Update\ntokenGroupName={PB, PB_TS}\niff it has changed from\npreviously stored PB,\nor if does not exist
deactivate Cache
deactivate RMS

note right of Cache
At 4PM cache can contain, for e.g.\nskydrm.com={PB, 4PM}\nassuming policies changed every hour\nor\nskydrm.com={PB, 12PM}\nif NO policy/config changes since\nthe first heartbeat at 12 PM.
end note
```

### RMS-RMC clients: ###
![RMS_RMD.png](https://bitbucket.org/repo/dBgzdj/images/3450336091-RMS_RMD.png)


```
#!python

participant RMS Cache as Cache
participant RMS
participant "RMC\nClient" as Client

note left of RMS
PB: Policy Bundle string per token group\n
client.PB_TS: Timestamp supplied by client when it \n*last checked policies* for that tokenGroupName\n
PB_TS: Server timestamp when policies for that\ntenant were *most recently stored in RMS cache*\n
CHB: RMC Client Heartbeat with RMS\n(say for e.g every 4 hours)\n
clientData: Client-supplied data which they would\nnormally have cached for each token group like:\n{tokenGroupName, client.PB_TS, client.config_TS}\nwhere config_TS is last when they last checked\nproject configuration (see wiki for format)\n
config_TS: Server timestamp when project\nconfiguration or classification last changed\n
policyConfigData: Json response that can contain\nfields like tokenGroupName, PB, PB_TS, config_TS,\nwatermarkConfig, classificationCategories (see wiki)\n
current_time: Now(), unrelated to client.PB_TS\nbut logically its always true that\ncurrent_time >= client.PB_TS\n
end note

note right of Client
First Client Heartbeat
end note

Client->RMS:CHB with (for e.g.) no clientData parameters\nhttps://skydrm.com/rms/rs/v2/heartbeat
activate RMS
RMS<->Cache:Fetch PB\nfor ALL token groups\nof which caller is a member
RMS->Client:Return response policyConfigData:\n{tokenGroupName, PB_TS, new PB, config_TS, watermarkConfig, classificationCategories}\nfor ALL token groups in membership list\nalong with\nlogin tenant's heartBeatFrequency & default watermarkConfig\n***Clients should cache new PB_TS and config_TS***\n
deactivate RMS

note right of Client
Client Heartbeat with\n frequency for e.g.\n4 hours starting@4:01PM
end note

Client->RMS:CHB with clientData parameter(s)\nfor e.g client.PB_TS 12:01PM in Epoch time ms\nhttps://skydrm.com/rms/rs/v2/heartbeat (has request body)
activate RMS
note over Client
current_time\nmay be\nsay 4:01PM
end note
RMS<->Cache:for each(tokenGroupName in membership list)\nreturn newly changed policyConfigData iff\nPB.get(PB_TS) > client.PB_TS || PB.get(config_TS) > client.config_TS\nelse return {} for that token group
RMS->Client:Return response policyConfigData: with only new/changed fields\n{tokenGroupName, PB_TS, new PB, config_TS, watermarkConfig, classificationCategories}\nfor ONLY those token groups in membership list which\nhave either new policies or changed project config or new membership\nalong with\nlogin tenant's heartBeatFrequency & default watermarkConfig\n***Clients should cache new PB_TS and config_TS***\n
deactivate RMS

note right of Client
Here\nPB.get(PB_TS) > client.PB_TS\ni.e. 4PM > 12:01 PM in this e.g.\nso will return updated\npolicies cached at 4PM\nby heartbeat from CC to RMS\nClients will save/cache\nnew timestamps ***for next CHB***.
end note

```

--------------------------------------------------------------------------------------------------------------------


## ~~v1~~ (deprecated)
This API is used to update client when client contact RMS. 

- **URL**: /rms/rs/heartbeat?tenant=skydrm
- **method**: POST
- **consume**: application/json
```json
{
   "parameters":{
      "ticket":"65FBDFB7B6EA6F72C2A293616C80B25D",
      "objects":[
         {
            "name":"policyBundle",
            "serialNumber":""
         },
         {
            "name":"clientConfig",
            "serialNumber":""
         },
         {
            "name":"classifyConfig",
            "serialNumber":""
         },
         {
            "name":"watermarkConfig",
            "serialNumber":""
         }
      ],
      "platformId":0,
      "userId":"1"
   }
}
```
	

- **produces**: application/json
```json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1471296031677,
   "results":{
      "classifyConfig":{
         "serialNumber":"4d2ade5",
         "content":"<Classify>\r\n\t<Profiles>\r\n\t\t<Default>0</Default>\r\n\t</Profiles>\r\n\t<LabelList>\r\n\t\t<Label default-value=\"0\" multi-select=\"false\" mandatory=\"true\" display-name=\"Owner's Tenant Id\" name=\"owner_tenant_id\" id=\"0\">\r\n\t\t\t<VALUE value=\"nextlabs.com\" priority=\"0\"/>\r\n\t\t</Label>\r\n\t</LabelList>\r\n</Classify>"
      },
      "watermarkConfig":{
         "serialNumber":"b7fd7dd",
         "content":"{\r\n    \"text\": \"$(User)\\\\nSkyDRM.com protected\",\r\n    \"transparentRatio\": 70,\r\n    \"fontName\": \"Sitka Text\",\r\n    \"fontSize\": 26,\r\n    \"fontColor\": \"#008000\",\r\n    \"rotation\": \"Anticlockwise\"\r\n}"
      },
      "clientConfig":{
         "serialNumber":"dac7c5ea",
         "content":"{\r\n    \"platformId\": 0,\r\n    \"heartbeatInterval\": 86400,\r\n    \"logInterval\": 86400,\r\n    \"bypassFilter\": \"^[c-zC-Z]{1}:\\\\\\\\windows\\\\\\\\.*|.*\\\\.exe$|.*\\\\.dll$|.*\\\\.ttf$|.*\\\\.zip$|.*\\\\.rar\",\r\n    \"protection\": {\r\n        \"enabled\": false\r\n    }\r\n}"
      },
      "policyBundle":{
         "serialNumber":"435b09af",
         "content":"{\n    \"version\": \"1.0\",\n    \"issuer\": \"skydrm.com\",\n    \"issueTime\": \"2016-08-12T13:09:45Z\",\n    \"policies\": [\n        {\n            \"id\": 0,\n            \"name\": \"Grant VIEW rights to all JT2GO users\",\n            \"action\": 1,\n            \"rights\": [\n                \"VIEW\"\n            ],\n            \"conditions\": {\n                \"subject\": {\n                    \"type\": 0,\n                    \"operator\": \"&&\",\n                    \"expressions\": [\n                        {\n                            \"type\": 1,\n                            \"operator\": \"=\",\n                            \"name\": \"application.name\",\n                            \"value\": \"visview_ng.exe\"\n                        },\n                        {\n                            \"type\": 1,\n                            \"operator\": \"=\",\n                            \"name\": \"user.tenant_id\",\n                            \"value\": \"jt2go\"\n                        }\n                    ]\n                }\n            }\n        }\n    ]\n}"
      }
   }
}
```