# **Check for Update Request/Response**  
  
**Note: ** This API is not in use.  
  


- **URL**: rms/rs/upgrade?tenant=skydrm
- **method**: POST
- **consume**: application/json

## RMC Windows

For RMC Windows, please pass the following parameters,

```json
{
    "parameters" : {
        "platformId": 0,
        "processorArch": "x64",
        "currentVersion": "9.1.1680"
    }
}
```
- **produces**: application/json
```json
{
    "statusCode": 200,
    "message": "OK",
    "results": {
        "newVersion": "9.1.1681",
        "downloadURL": "https://skydrm.com/download/rmc/windows/x64/rmc_9_1_1681_x64.cab",
        "sha1Checksum": "da39a3ee5e6b4b0d3255bfef95601890afd80709"
    }
}
```

> NOTE
>
> - If request version doesn't match version on server side, response contains the latest version/url/checksum on server side. Otherwise results is empty.
>

### Processor Architecture

There are three available values:

|   *Name*	|  *Value*	|
|-----------|-----------|
|    x86    |   "x86"   |
|    x64    |   "x64" 	|
|    IA64   |   "IA64"  |

## RMC Mac

For RMC Mac, please pass the following parameters
```json
{
	"parameters":{
		"platformId":300,
		"currentVersion":"1.0.0"
	}
}
```

- **produces**: application/json

```json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1503471269037,
    "results": {
        "downloadURL": "https://s3.amazonaws.com/nextlabs-rc/SkyDRM/Latest+Version/SkyDRM.dmg",
        "newVersion": "1.0.1"
    }
}
```