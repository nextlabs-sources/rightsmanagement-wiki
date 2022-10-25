# RMS RESTful API

RMS RESTful API is based on simple HTTPS. Most of the request/response are using JSON payload.

## Listing all APIs
We have an API that can list all available APIs. In browser open URL: https://www.skydrm.com/rms/rs, you will see a list of APIs in JSON format:
```
{
    "statusCode": 0,
    "serverTime": 1471296772918,
    "results": {
        "/usr": [
            {
                "method": "POST",
                "consumes": "application/x-www-form-urlencoded",
                "produces": "application/json",
                "function": "login",
                "params": []
            }
        ],
	...

    }
}
```

In above example, you can see following information about the API:

- **method**: HTTP method
- **consume**: Accepted content type if API expects request body (POST or PUT method)
- **produces**: return data type
- **function**: server side function name
- **params**: parameters of server side function 

If the API produces a json content type response, it will always return com.nextlabs.rms.rs.JsonResponse object:
```
{
    statusCode : A int statusCode with matches HTTP StatusCode.
    serverTime: RMS server time in milliseconds.
    message : A message that describe the error or status. This is used for developer only.
    results : A Map<String, Object> that store API specific return data.
    extra : A extra arbitrary object for API
}
```

## Show more information about each URL
You can install "Advance Rest Client" in Chrome extension, and use ARC to send OPTIONS method to each URL, a XML with more detail will be return:
```
<?xml version="1.0" encoding="UTF-8" standalone="yes" ?>
<application>
	<doc jersey:generatedBy="Jersey: 2.22 2015-09-25 01:55:01" />
	<grammars />
	<resources base="https://rmtest.nextlabs.solutions/rms/rs/">
		<resource path="tenant">
			<method id="getSsoPath" name="GET">
				<request>
					<param name="tenant" style="query" type="xs:string" />
				</request>
				<response>
					<representation mediaType="*/*" />
				</response>
			</method>
		 </resource>
	</resources>
</application>
```

Since we heavily using json as input and output, we describe them in detail in each API below.

## [Common Parameters for REST API](Common Parameters for REST API)
## [Login REST API](Login REST API)
## [Membership REST API](Membership REST API)
## [Tenant REST API](Tenant REST API)
## [Token REST API](Token REST API)
## [User REST API](User REST API)
## [Sharing REST API](Sharing REST API)
## [Heartbeat REST API](Heartbeat REST API)
## [Log REST API](Log REST API)
## [Check Upgrade REST API](Check Upgrade REST API)
## [Convert File REST API](Convert File REST API)
## [Remote Viewer REST API](Remote Viewer REST API)
## [IDP REST API](IDP REST API)