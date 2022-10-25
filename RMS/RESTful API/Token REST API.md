# Token service

 - [Generate new encryption tokens](#markdown-header-generate-new-encryption-tokens)
 - [Retrieve decryption token](#markdown-header-retrieve-decryption-token) 
 - [Update NXL Metadata for Central Policy Files and Prefetched Adhoc files](#markdown-header-update-nxl-metadata-for-central-policy-files-and-prefetched-adhoc-files) 


## **Generate new encryption tokens** #
This API is used to create a new tokens to encrypt documents. 

- **URL**: /rms/rs/token
- **method**: PUT

 **1. Prefetch tokens**: clients can call this API to prefetch a batch of tokens for future use, but the token is in INACTIVE status unless client call updateNxlMetadata API to activate it.

- **consume**: application/json
```json
{
   "parameters":{
      "membership":"m3@nextlabs.com",
      "agreement":"2490190aab76f9415d75...",
      "count":"50",
      "prefetch":true
   }
}
```

- **produces**: application/json
```json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1552290524371,
    "results": {
        "tokens": {
            "6840F701C5A19215CC5892B7CD82803F": {
                "otp": "914E4953059B5B9162F2B16D87DE20A2",
                "token": "086D797D9E9D319304EF10AD9B11B26F1E23DCB7ACAB8DADA02173D3236B8672"
            },
            "744F3702CC161E08E779D04E495D5164": {
                "otp": "C4BDEED41E90B06E2F412DFF08A84F7E",
                "token": "66CC0D02E6F63B34B6E543533397E2ADC313378D033C428D3CEFC3CA9FF1A9B5"
            },
            ......
        },
        "ml": "0"
    }
}
```
 **2. Generate token to encrypt file**: clients can call this API to directly use the generated token to encrypt file.

- **consume**: application/json
```json
{
   "parameters":{
      "membership":"m3@nextlabs.com",
      "agreement":"2490190aab76f9415d75...",
      "count":"1",
      "prefetch":false,
      "protectionType": "1"
      "fileTags":"{\"publisher\":[\"nextlabs\",\"v1\"],\"licensed\":[\"yes\"]}"
   }
}
```

- **produces**: application/json
```json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1552292133080,
    "results": {
        "tokens": {
            "AA8A1B386AACE08CAFB3B6C56CBA009A": {
                "token": "5C0F1DF5B7812B0D71778A64275D81ABCD5305D7894020ED097CBA93AB64E29E"
            }
        },
        "ml": "0"
    }
}
```

> **NOTES**:
>
> 1. If prefetch is false (meaning we are passing the file details in the current call and API accepts those only for 1 file per call), then count is implicitly 1 – if prefetch is true then count can be between 1-100.
> 2. protectionType can be 0 (adhoc file) or 1 (central policy file). This field is only meaningful for non-prefetch case (prefetch is false or undefined)
> 3. filePolicy and fileTags are only present for non-prefetch case. These two values are **utf-8 Json Strings** stored in nxl file header. For details please refer to [NXL 2.0 format specification](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMD/nxl.format.v2.md). **There should be no leading/trailing white spaces or blanks outside double-quoted string literals**. filePolicy and fileTags will be passed as string (not explicit json). Please note that in samples on this page, the json has been escaped with \ so Postman/rest client can submit it, but the actual content that clients send **do not need to be escaped**.
> 4. otp is only returned for prefetch case, clients will use this otp to update nxl metadata later.
> 5. Typically one would pass protectionType (0 or 1) and based on it's value only one of either filePolicy or fileTags, whichever applies. You can pass both but one can be empty "".




## **Retrieve decryption token** #
This API is used to retrieve tokens to decrypt documents. It compares checksums and owner membership against the server's database before doing policy evaluation (adhoc/central) for VIEW right.

### Central Policy File ###
- **URL**: /rms/rs/token
- **method**: POST
- **consume**: application/json
```json
{  
   "parameters":{  
      "tenant":"f217b3bf-95a3-43be-a687-44649fb60f32", //yes new arch will retain tenant for certain reasons, and token group will be checked using the "owner" field below after @
      "owner":"m35@skydrm.com_My Project_1",
      "agreement":"ac7564e16a83bb0691356003a9dcb367edae96e68c9c99116c98c99462899814ca1a840bdd502a3aae04813b9d624941e43047f27eecae441c7f0028eb49102893fe67fd804f7ce04f882e19c9649e886d8a99629a873585d64623eb6fd66bcb1542f85a18951e0e16059e9396f6fc275c8c2d5cdb122abda4e2813d3300abc6d4f8b25285369e00f2218322e3c86690f446db4cccfec150b59a294a660c0aba06afebb432421196ef5d8d73777a751b2464b60bb6425a167ee0f30a437325009d258c2b85b88c6e96e7b823e4d8471ad3bf9067b92cce9f65882910250e1b6bdf34712a4f27a0df651618e632837c7baac82e027c6504b8cd63ed93de4157e1",
      "duid":"307A72F788157226084A24E39C078C57",
      "protectionType":"1",
      "filePolicy":"",
      "fileTags":"{\"itar\":[\"classified\"]}",
      "ml":"0",
      "dynamicEvalRequest":{  
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
         ]
      }
   }
}
```

> **NOTES**:
>
> 1. Client should send request to owner's tenant server, not user's login server.
> 1. **tenant**: this tenant is referring to user's login tenant, RMS based on this tenant to locate user. This is Not the same as "tenant" parameter defined in [Common Parameters for REST API](Common Parameters for REST API).
>

- **produces**: application/json
```json
{
   "statusCode":200,
   "message":"OK",
   "results":{
      "token":"78D270A56750AE5FAF7261B73A20F5ADA8F786036D402DC50EAF6558584A61BB"
   }
}
```

If no adhoc/central policy matches then
```
#!json

{
    "statusCode": 403,
    "message": "Unauthorized",
    "serverTime": 1552030953274
}

```

### Adhoc File ###
Similarly, here's the sample for request decryption token for adhoc files which also checks for validity:

- **consume**: application/json
```
#!json

{  
   "parameters":{  
      "tenant":"f217b3bf-95a3-43be-a687-44649fb60f32", //new arch will retain tenant as token group is checked from owner field below
      "owner":"m35@skydrm.com_My Project_1",
      "agreement":"ac7564e16a83bb0691356003a9dcb367edae96e68c9c99116c98c99462899814ca1a840bdd502a3aae04813b9d624941e43047f27eecae441c7f0028eb49102893fe67fd804f7ce04f882e19c9649e886d8a99629a873585d64623eb6fd66bcb1542f85a18951e0e16059e9396f6fc275c8c2d5cdb122abda4e2813d3300abc6d4f8b25285369e00f2218322e3c86690f446db4cccfec150b59a294a660c0aba06afebb432421196ef5d8d73777a751b2464b60bb6425a167ee0f30a437325009d258c2b85b88c6e96e7b823e4d8471ad3bf9067b92cce9f65882910250e1b6bdf34712a4f27a0df651618e632837c7baac82e027c6504b8cd63ed93de4157e1",
      "duid":"4B5534C265F7349BCE9B4F6EDED810CD",
      "protectionType":"0",
      "filePolicy":"{\"version\":\"1.0\",\"issuer\":\"m35@t-8462001b114b4939a83eee54ff6d6c90\",\"issueTime\":\"2019-03-06T12:00:06+08\",\"policies\":[{\"id\":0,\"name\":\"Ad-hoc\",\"action\":1,\"rights\":[\"VIEW\",\"PRINT\"],\"conditions\":{\"subject\":{\"type\":1,\"operator\":\"=\",\"name\":\"application.is_associated_app\",\"value\":true}},\"obligations\":[{\"name\":\"WATERMARK\",\"value\":{\"text\":\"$(User)$(Break)$(Date) $(Time)\"}}]}]}",
      "ml":"0"
   }
}
```

- **produces**: application/json
```
#!json

{  
   "statusCode":200,
   "message":"OK",
   "serverTime":1552032187550,
   "results":{  
      "token":"F26D12A99E81CA1C03B530E1F165065FC6DE50280A1EF395714062EA2CA10C10"
   }
}
```

## **Update NXL Metadata for Central Policy Files and Prefetched Adhoc files** ##

This API is used to update the metadata and applies to both: 

* Central Policy classification tags. 

* Adhoc files whose tokens were prefetched (so their status in server db was INACTIVE).

**Active Adhoc files *cannot* update their filePolicy as its stored within the NXL file header** - which means that once an nxl file is released beyond the server's control (like for locally encrypted adhoc nxl files), the server should NOT allow updates to its metadata in its database, unless it receives the uploaded nxl file and returns the changed file on its own.

If the token was prefetched (it's duid would be marked inactive in server's db) then caller will need to supply the otp which will be verified by server. If it has already been updated once, then subsequent updates will succeed only if caller is either owner, project admin or API user (i.e has "admin rights". Even so, the server only stores the CHECKSUMS and never the tags themselves. If calling for a prefetched duid, it will insert the caller's membership record as the owner (as that can be different from when server stored owner when it was generated as "prefetched" token). 

Sample request body for Prefetched Adhoc files (INACTIVE status in server db):


```
#!json

{  
   "parameters":{  
      "otp":"0D12D1744A4B35D1CB3C048ECC577528",
      "protectionType":"0",
      "filePolicy":"{\"version\":\"1.0\",\"issuer\":\"m35@t-8462001b114b4939a83eee54ff6d6c90\",\"issueTime\":\"2019-03-06T12:00:06+08\",\"policies\":[{\"id\":0,\"name\":\"Ad-hoc\",\"action\":1,\"rights\":[\"VIEW\",\"PRINT\"],\"conditions\":{\"subject\":{\"type\":1,\"operator\":\"=\",\"name\":\"application.is_associated_app\",\"value\":true}},\"obligations\":[{\"name\":\"WATERMARK\",\"value\":{\"text\":\"$(User)$(Break)$(Date) $(Time)\"}}]}]}",
      "ml":"0"
   }
}

```

For non-prefetch case, as updating nxl metadata is controlled by role (not file rights), it's possible that the person updating nxl metadata is not allowed to get the token to decrypt the file. So the caller needs to pass additional parameter "fileHeader" when updating tags in file, server will run updateTags process and return updated tag section checksum and header checksum to the caller. The caller must use the returned two checksums to update the local copy of nxl file.

- **URL**: /rms/rs/token/{duid}
- **method**: PUT

PUT https://blumine.nextlabs.com/rms/rs/token/7F918190805DAFE3A8D48077CD8AA48C
Request body has: only valid for protectionType=1 but cannot use for adhoc files (0) unless they were prefetched.


```
#!json

{  
   "parameters":{  
      "otp":"D83A3C104B41E386C41F212CFC0A8683",
      "protectionType":"1",
      "fileTags":"{\"itar\":[\"classified\"]}",
      "ml":"0"
   }
}

```
Produces: Note that if not updating for a prefetched token then response will also return 2 checksums which clients can write in their nxl file for locally encrypted files which are not uploaded (in projects/system bucket).

```
#!json

{  
   "statusCode":200,
   "message":"OK",
   "serverTime":1552379894272
}
```

If status is ACTIVE and classification is allowed (**user is either owner/project admin/API user only**), then need to pass existing fields in request, along with new fileTags (server will check for valid syntax) and complete file heade r(16kB) encoded with Base64:


```
#!json

{  
   "parameters":{  
      "protectionType":"1",
      "fileTags":"{\"itar\":[\"changed\"]}",
      "existingFileTags":"{\"itar\":[\"classified\"]}",
      "ml":"0",
      "fileHeader":"TlhMRk1UQAAAAAMAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAKsahsKcOc6zO7B3KazNkzQAAAAAABAAAAIAAAAAAgAAAEAAAG0zNzFAdC0wZTYzZm......"
   }
}
```


If cannot classify then server produces response:

```
#!json

{  
   "statusCode":403,
   "message":"Unauthorized to classify",
   "serverTime":1552380021344
}
```
If duid in PathParam is wrong (i.e missing in database), then produces response:

```
#!json

{  
   "statusCode":4000,
   "message":"Unverified metadata for duid",
   "serverTime":1552380284396
}
```

Successful response:

```
#!json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1553153546259,
    "results": {
        "headerCheckSum": "T9/ZrO1ozfvXQs9UVT2nwtgE70yqnXgLqI0BLAUeJHw=",
        "tagCheckSum": "XaSqA2NeazYeZvfTMTL5Rimt2mIvCAFcDApkC19easM="
    }
}


```  
  
Note: headerCheckSum and tagCheckSum are Base64 encoded, the caller needs to decode it and save it back to local nxl file.  
  
## Note: This call is only meant for system bucket files ##. For files stored in projects, use the [reclassify](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API#markdown-header-reclassify-file) API.