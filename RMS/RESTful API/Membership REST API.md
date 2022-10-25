# Membership service
## Request membership certificate
This API is used to handle membership certificate signing request. RMS will sign membership's certificate with iCA certificate. 

- **URL**: /rms/rs/membership
- **method**: PUT
- **consume**: application/json


		{
			"parameters": {
				"userId": 3,
				"ticket": "DDDDF9F2636532519D3E5BF3C6B51C46",
				"membership": "m3@nextlabs.com",
				"publicKey": "MIIBpjCCARsGCSqGSIb3DQEDATCCAQwCgYEA6Or95vngFUSpeoxBJixk5oeQjnj39aWJ3JW1agFcw0L6hpX3XwBdhuhrsl6CI4HRG+P/Q93gJ0cAI0IyZYte1dX/4abrrrUmfgTDZwll39nhac4Y2cL17pwPK9b0053G/oZWCVdorOsFI22Gx3zZwj2LKRw4p4FqntgZeoJX5EcCgYEAxbonqnTL2InLjbGdfcVpxfFV6mZ14vEqFUv/qA4fqthCFHYqohD9LeWdTYkImy5XKdMswlok4HxKn6h2Ra6HDZKxPn8x6CZVdbmAesGbb8ySFySuwvuKh25yg1s7I7ViTOHK5zkDys+VSGI+X1/yeir1sV3OVrMI2ODcPBnCAgcCAgIAA4GEAAKBgBE2844vF8jW8oVa6NjNRoZhZkhxPfF5GT9s3/0PH+GMV/2KHdXSJUAN1GZ3mpDXsy+TEIeZq4xsajJHJN/eRQITX6EbcgnBCN2CQVfh7ZRv2beBM/zT/sgVGZRdBMSJqcis3m/RuP8aHNaL3QU/ns3I7Qn0NsACM2XGV+lAX0fz"
			}
		}

- **produces**: application/json


		Jsonresponse object looks like:
		{
			"statusCode": 200,
			"message": "OK",
			"results": {
				"certficates": "-----BEGIN CERTIFICATE-----\nMIIEFzCCA...\n-----END CERTIFICATE-----\n"
			}
		}