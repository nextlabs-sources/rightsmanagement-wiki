# Common Parameters for REST API

This wiki page defines common parameters which apply to all RMS RESTful API.

## clientId

In order identify each RMC client installation, a 32 bytes UUID should be assigned to each client. This ID will be used for analytics purpose. Each request should include this header.

**clientId** will be send as HTTP header:

```
clientId: 3783caa4744f4c69a7500cf7cd6f7540
```

## platformId
[`platform id`](../../RMD/platform.md) should be sent as HTTP header. This value will be used for logging when the server has to log the activity on behalf of the client (e.g. sharing/downloading files).

```
platformId: 600
```

| Device Type     | Platform ID |
|-----------------|-------------|
| Windows Desktop | 0           |
| Windows Server  | 100         |
| Linux/Unix      | 200         |
| Mac OS          | 300         |
| Windows Phone   | 400         |
| Windows Tablet  | 500         |
| iPhone          | 600         |
| iPad            | 700         |
| Android Phone   | 800         |
| Android Tablet  | 900         |
| WebApp          | 1000        |

##deviceId
This should be sent as HTTP header. This value will be used for logging when the server has to log the activity on behalf of the client (e.g. sharing/downloading files).
For desktops, this should be the hostname. For mobile, this should be the device name. For web, this should be ip address.

```
deviceId: "Kevin's iPhone"
```

## tenant

Some of the API allow client to specific a tenant name. If client didn't specify tenant name, RMS will pick up default tenant. For SkyDRM deployment, default tenant will be: "**skydrm.com**".

There are two ways for client to specify tenant:

1. Use tenant's dns name if tenant is assigned a dedicate domain name. for example:

		https://jt2go.skydrm.com/rms

2. User query string in url, for example:

		https://www.skydrm.com/rms/rs/login?tenant=jt2go