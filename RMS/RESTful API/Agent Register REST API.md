# Agent Register REST API

**URL:** 

	/agent/regsiter

**method:** 
	
	POST

**Headers:**
	
- **content-type:** 

		application/json

- **X-NXL-S-CERT:**

		MIIDmzCCAoOgAwIBAgIEI8+FCTANBgkqhkiG9w0BAQsFADB+MQswCQYDVQQGEwJVUzELMAkGA1UECBMCQ0ExEjAQBgNVBAcTCVNhbiBNYXRlbzERMA8GA1UEChMITmV4dExhYnMxGjAYBgNVBAsTEVJpZ2h0cyBNYW5hZ2VtZW50MR8wHQYDVQQDExZSaWdodHMgTWFuYWdlbWVudCBUZW1wMB4XDTE1MDUyNzE0MDI0MVoXDTI1MDUyNDE0MDI0MVowfjELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAkNBMRIwEAYDVQQHEwlTYW4gTWF0ZW8xETAPBgNVBAoTCE5leHRMYWJzMRowGAYDVQQLExFSaWdodHMgTWFuYWdlbWVudDEfMB0GA1UEAxMWUmlnaHRzIE1hbmFnZW1lbnQgVGVtcDCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAPUFAKjEmwL/oWQpafK5vOz8/dOEf9mmcjKawhxNhJJ5R+4olKHF9ZaKMve542ehSKngA53buaPsvP96ix7j8O8E2DHnYqbR5I9jNfZarIWjAwfO94TDvQovuIFBCWMnJXsRUtVm36cF6WpQdqctIbgvSbjGbgYacqGZ6QaaWrySGxupjyB8lfD6dYBG5lXFRcFA7QQVbNAGM7Xis2S3sPZOch4VJK7faX2xRyW6sIKL0FU8W9HCbm2PjG+XBr+dmsP3lk6HOqlSEy55HRYldMI/KCSlTGIcUHjH0qpiBxceSHILgY+YOqJ3l6/d8k9ui3MK2XGUhNFgwwLYFruk1l8CAwEAAaMhMB8wHQYDVR0OBBYEFGKcZJB9ZVJ6q/T2DNJIUoQEqx6sMA0GCSqGSIb3DQEBCwUAA4IBAQA9oFoR9GYVvba1WTdq2sl7kqTxqTPkUtD5LGi5A7q1yxMkAwsR2kW00L5dbRmADT7PjE3x42V2ZHHuYhDjGg/zm+2xHVrUWl2ZxHodmHz6+qDbdAZ3+9U4Zz7nt2oxDFghp/eE1adXa2kfAIZzn8VVamD6TS9O0R/KyXToYgpjLmz6QD9GFsz5wGbVsnJGWTxfiNjX3LnFIkqJU8rHn1DcMyB3/xd3ytUJzKrAnD8f46JpfR1amJOQAxiDy5+kW1OnclGBImS9iisvCmwU3+UNixbFAAxymBA9VvAO90sw0tHcLN7M1NSpenVlAnJTHhGuLSepk8gv4jAEsa9+DPKR

**Body Request:**

	{
		"parameters": {
		      "host": "101",
		      "version": {
		        "major": "7",
		        "minor": "7",
		        "maintenance": "7",
		        "patch": "7",
		        "build": "7"
		      }
		}
	}
	
**Response:**

produces: application/json

	{
		"statusCode": 200,
		"message": "OK",
		"serverTime": 1471296031677,
		"results": {
			"id": <id>,
			"heartBeatFrequency": {
				"time": <>,
				"unit": <>
			},
			"logLimit": <limit>,
			"logFrequency": {
				"time": <>,
				"unit": <>
			}
		}
	}