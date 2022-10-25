# **Heart Beat Request/Response**


- **URL**: 
- **method**: POST
- **consume**: application/json
```json
{
    "parameters" : {
        "userId": "3",
        "tenant": "jt2go.nextlabs.com",
        "platformId": 0,
        "ticket": "DDDDF9F2636532519D3E5BF3C6B51C46",
        "objects": [
            {
                "name": "policyBundle",
                "serialNumber": "current serial number"
            },
            {
                "name": "clientConfig",
                "serialNumber": "current serial number"
            },
            {
                "name": "classifyConfig",
                "serialNumber": "current serial number"
            },
            {
                "name": "watermarkConfig",
                "serialNumber": "current serial number"
            }
        ]
    }
}
```
- **produces**: application/json
```json
{
    "statusCode": 200,
    "message": "OK",
    "results": {
        "policyBundle": {
        	"serialNumber": "new serial number",
        	"content": "new content"
        },
        "clientConfig": {
        	"serialNumber": "new serial number",
        	"content": "new content"
        },
        "classifyConfig": {
        	"serialNumber": "new serial number",
        	"content": "new content"
        },
        "watermarkConfig": {
        	"serialNumber": "new serial number",
        	"content": "new content"
        }
    }
}
```

        In request, the "serialNumber" could be empty (when client doesn't have any previous data)
    
        In response, only new content will be returned.


# Platform Id

See [HERE](platform.md)

# Policy Bundle

See [Policy Format](policy.format.md)

# Client Config

```json
{
    "heartbeatInterval": 86400,
    "logInterval": 86400,
    "bypassFilter": "...",
    "protection": {
        "enabled": false,
        "filetypeFilter": "..."
    }
}
```

## Heartbeat Interval

How often to send heartbeat request, in seconds.


## Log Interval

How often to send log request, in seconds.

## allowProtection

Is document protection allowed on client?


## bypassFilter

A regular expression filter that define the directories/files to be ignored.


## protection

Client side file protection configuration.

- `enabled`: TRUE if protection is enabled, otherwise it is FALSE
- `filetypeFilter` (optional): A regular expression that decides what type of files can be protected. Only valid when protection is enabled.


# Classify Config

See [Classify Config](classify.config.md)

# Watermark Config

```json
{
    "text": "TEXT TO SHOW",
    "transparentRatio": 30,
    "fontName": "Arial",
    "fontSize": 65,
    "fontColor": "#FF00FF",
    "rotation": "Clockwise/Anticlockwise"
}
```

- **text**: The text to be shown. Support escape characters.
- **transparentRatio**: The transparent ratio of the watermark, integer between 0 - 100.
- **fontName**: The name of the font
- **fontSize**: The size of the font
- **fontColor**: The color of the font in "#RRGGBB" format.
- **rotation**: Text rotation. two available values are "Clockwise" and "Anticlockwise".


# [Default policies](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/browse/tenants)

## jt2go
- [Policy Bundle](../tenants/jt2go/policyBundle.json)
- [Client Config](../tenants/jt2go/clientConfig.json)
- [Classify Config](../tenants/jt2go/classifyConfig.xml)
- [Watermark Config](../tenants/jt2go/watermarkConfig.json)

## skydrm.com
- [Policy Bundle](../tenants/skydrm.com/policyBundle.json)
- [Client Config](../tenants/skydrm.com/clientConfig.json)
- [Classify Config](../tenants/skydrm.com/classifyConfig.xml)
- [Watermark Config](../tenants/skydrm.com/watermarkConfig.json)

