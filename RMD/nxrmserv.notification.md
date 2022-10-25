
# **NextLabs Rights Management Service Notification**

# Method

Service notify rmc application by sending a JSON code via pipe.

# Global Notification

Global notifications will be sent to all the active sessions.


## Policy Changed

This notification is to notify rmc application that service's status has been changed

- **notification id** : unsigned long (1)

- **content** : utf-8 json string
```json
{
	"code": 1,
	"policyId": "xxxxxxx"
}
```


# Session Notification

Session notifications will only be sent to specific session.

## Message Popup

This notification is to notify rmc application to pop up a message

- **notification id** : unsigned long (100)

- **content** : utf-8 json string
```json
{
	"code": 100,
	"message": "File operation is blocked"
}
```


## Logon Required

This notification is to notify rmc application to pop up logon dialog (normally it hapeens when user try to open NXL file without login)

- **notification id** : unsigned long (101)

- **content** : utf-8 json string
```json
{
	"code": 101
}
```


## Quit

This notification is to notify rmc application to quit

- **notification id** : unsigned long (102)

- **content** : utf-8 json string
```json
{
	"code": 102
}
```



## Status Changed

This notification is to notify rmc application that service status has been finished

- **notification id** : unsigned long (103)

- **content** : utf-8 json string
```json
{
	"code": 103
}
```

