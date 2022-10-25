# Token service

 - [Generate new encryption tokens](#markdown-header-generate-new-encryption-tokens)
 - [Retrieve decryption token](#markdown-header-retrieve-decryption-token) 


## Generate new encryption tokens
This API is used to create a new tokens to encrypt documents. 

- **URL**: /rms/rs/token
- **method**: PUT

 **1. Prefetch tokens**: clients can call this API to prefetch a batch of tokens for future use, but the token is in INACTIVE status unless client call updateNxlMetadata API to activate it.
- **consume**: application/json
```json
{
   "parameters":{
      "userId":"3",
      "ticket":"DDDDF9F2636532519D3E5BF3C6B51C46",
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
      "userId":"3",
      "ticket":"DDDDF9F2636532519D3E5BF3C6B51C46",
      "membership":"m3@nextlabs.com",
      "agreement":"2490190aab76f9415d75...",
      "count":"1",
      "prefetch":false,
      "protectionType": 1
      "filePolicy":"",
      "fileTags":"{itar=[classified]}"
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
> 3. filePolicy and fileTags are only present for non-prefetch case. These two values are **utf-8 Json Strings** stored in nxl file header. For details please refer to [NXL 2.0 format specification](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMD/nxl.format.v2.md). **There should be no leading/trailing white spaces or blanks outside double-quoted string literals**.
> 4. otp is only returned for prefetch case, clients will use this otp to update nxl metadata later.



## Retrieve decryption token
This API is used to create a new tokens to decrypt documents. 

- **URL**: /rms/rs/token
- **method**: POST
- **consume**: application/json
```json
{
   "parameters":{
      "userId":"3",
      "ticket":"DDDDF9F2636532519D3E5BF3C6B51C46",
      "tenant":"skydrm",
      "owner":"m3@jt2go",
      "agreement":"2490190aab76f9415d75...",
      "duid":"AD3D7A52634AFC99CCD35D0413CEEC5D",
      "ml":"0"
   }
}
```

> **NOTES**:
>
> 1. Client should send request to owner's tenant server, not user's login server.
> 2. **tenant**: this tenant is referring to user's login tenant, RMS based on this tenant to locate user. This is Not the same as "tenant" parameter defined in [Common Parameters for REST API](Common Parameters for REST API).
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