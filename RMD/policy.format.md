# **Rights Management Policy**

# History

| *Version* |  *Author*     |   *Date*      |   *Comments*              |
|-----------|---------------|---------------|---------------------------|
|   0.1     |   Gavin Ye    |   07/11/2016  |   0.1 Draft               |
|           |               |               |   *Initial*               |


# Bundle

Rights Management Policy Bundle is in UTF-8 JSON format.

```json
{
    "version": "1.0",
    "issuer": "skydrm.com",
    "issueTime": "2016-07-11T13:09:45Z",
    "policies": [
        {
            "id": 0,
            "name": "TEST/policy0",
            "action": 1,
            "rights": [
                "VIEW", "EDIT"
            ],
            "conditions": {
                "subject": {
                    EXPRESSION
                },
                "resource": {
                    EXPRESSION
                },
                "environment": {
                    EXPRESSION
                }
            },
            "obligations": [
                ...
            ]
        },
        {
            ...
        }
    ]
}
```

## Version

The policy bundle format version, in "major.minor" format. Current it is "1.0".

## Issuer

*Who* issue this policy bundle.

For central policy, the issuer is tenant id.

For ad-hoc policy, the issuer is NXL document's owner id.

## IssueTime

A UTC date-time string in ISO-8601 format.

## Policies

This is an array of policy objects.

# Policy

A policy element is a JSON object. It has following elements:

- id
- name
- action
- rights
- conditions

## 1. id (mandatary)

An integer that can uniquely identify this policy.

## 2. name (optional)

Name of this policy.

## 3. action (mandatary)

How this policy deal with rights.

- "REVOKE" (0)
- "GRANT" (1)

## 4. rights (mandatary)

The rights to be granted or revoked. It could be the name of right, or just "*" which indicates all the rights.


## 5. conditions (mandatary)

A conditions object contains 3 condition expressions.
It decides whether caller match this policy.

### **(I) Subject condition expression** (Optional)

Subject conditions decides **WHO** match this policy. There are 3 subject types:

- User
- Application
- Host

*User*

All the user properties name start with "user.". For example, "user.name", "user.email", etc.

Here is predefined user property names:
 
- user.tenant_id (`Mandatary`): tenant id of user.
- user.id (`Mandatary`): a 64 bits integer that uniquely identify this user.
- user.name (`Mandatary`): the full name of user. For example, "John Tyler".
- user.email (`Mandatary`): the user's email address. For example, "John.Tyler@nextlabs.com".
- user.principle_name (`Optional`): the principle name of a domain user. If the user is not in domain, leave it empty.
- user.phone (`Optional`): the phone number related with user (in E.164 format with '+' prefix, `+16503339999`, `+8613998184952`, etc.).

*Application*

All the application properties name start with "application.". For example, "application.path", "application.signature.publisher", etc.

Here is predefined application property names:
 
- application.path (`Mandatary`): the full path of application's executable. For example, "C:\Program Files (x86)\Microsoft Office\Office15\WINWORD.EXE"
- application.is_associated_app (`Mandatary`): is this app associated with resource type. (boolean)
- application.signature.publisher (`Optional`): the name in application signature. If the binary doesn't have a valid signature, leave it empty.
- application.signature.issuer (`Optional`): the issuer in application signature. If the binary doesn't have a valid signature, leave it empty.
- application.name (`Optional`): the "OriginalFilename" field in file version.
- application.version (`Optional`): the "FileVersion" field in file version.
- application.company (`Optional`): the "CompanyName" field in file version.
- application.product (`Optional`): the "ProductName" field in file version.
- application.product.version (`Optional`): the "ProductVersion" field in file version.


*Host*

All the host properties name start with "host.". For example, "host.network_address", "host.name", etc.

Here is predefined host property names:

- host.name (`Mandatary`): the host's FQDN
- host.network_address (`Optional`): host's IPv4 address (32 bits big endian unsigned integer).


### **(II) Resource condition expression** (Optional)

Resource conditions decide **WHAT** is being accessed.

All the resource properties name start with "resource.". For example, "reource.path", "resource.owner", etc.

Here is predefined resource property names:

- resource.path (`Mandatary`): the host's FQDN
- resource.id (`Mandatary`): resource's NXL unique document id
- resource.owner (`Mandatary`): resource's NXL owner id
- resource.info.XXX (`Optional`): resource's NXL file info valueo value
- resource.tag.XXX (`Optional`): resource's NXL tag value


### **(III) Environment condition expression** (Optional)

Environment conditions try to address caller runtime environment.

All the environment properties name start with "environment.". For example, "environment.seconds_since_last_heartbeat", "environment.connection", etc.

Here is predefined resource property names:

- environment.seconds_since_last_heartbeat (`Mandatary`): an integer indicates how many seconds past since last heartbeat
- environment.connection (`Optional`): how user connect to this machine (`remote` or `console`)
- environment.date (`Optional`): current epoch time in milliseconds (used to determine the validity period of the digital rights)

## 6. obligations (optional)

The obligations object is a JSON array which contains a list of obligations required by this policy.


# Policy Condition Expression

## Expression Type

There are 2 expression types:

- LogicExpression (0)
- PropertyExpression (1)

`LogicExpression` indicates tat the expression is a logic tree which contains sub expressions.

`PropertyExpression` indicates tat the expression is a single expression to evaluate a propert.


## Logic Expression

```json
{
    "type": 0,
    "operator": "&&" | "||",
    "expressions": [
        ...
    ]
}
```

`type`

The type must be 0 (*LogicExpression*)

`operator`

There are 4 logic operators:

- **&&** (AND): To match this expression, caller must match all its sub-expressions. 
- **||** (OR): To match this expression, caller only need to match any of its sub-expressions.

`expressions`

This is an array of sub-expressions.


## Property Expression

```json
{
    "type": 1,
    "operator": "=, !=, >, >=, <, <=",
    "name": "user.email",
    "value": "*@nextlabs.com"
}
```

`type`

The type must be 1 (*PropertyExpression*)

`operator`

There are 4 logic operators:

- "=": Caller's property value match (equal to or match the regular expression) given value
- "!=": Caller's property value NOT match given value
- ">": Caller's property value is greater than given value 
- ">=": Caller's property value is greater than or equal to given value 
- "<": Caller's property value is less than given value 
- "<=": Caller's property value is less than or equal to given value 

`name`

This is property name, string, case insensitive.

`value`

This is given property value.

> Value Types
> - Number (integer or float)
> - Boolean
> - String (Regular expression, case insensitive)


# Policy Obligations

## Basic Format

Each obligation is an JSON object.

```json
{
    "name": "ObligationName",
    "parameters": {
        "parameterName1": "parameterValue",
        "parameterName2": "parameterValue"
    }
}
```

**name** (mandatary)

The name of this obligation.

**parameters** (optional)

the parameters required by this obligation.

## Predefined Obligations

### 1. *Watermark*

Client should show watermark (overlay) if this obligation present.

```json
{
    "name": "WATERMARK",
    "value": {
        "text": $(User)$(Break)$(Date)$(Time)
     }
}
```
* $(User) => Email ID of the logged in user
* $(Date) => Date (yyyy-mm-dd)
* $(Time) => Time(HH:mm:ss)
* $(Break) => Line Break

# Example

## Ad-hoc Policy

```json
{
    "version": "1.0",
    "issuer": "m15.nextlabs.com",   // owner's id
    "issueTime": "2016-07-11T13:09:45Z",
    "policies": [
        {
            "id": 0,
            "name": "Ad-hoc",
            "action": 1,    // "GRANT"
            "rights": [
                "VIEW"
            ],
            "conditions": {
                "subject":  {
                	"type": 1,
                   	"operator": "=",
                  	"name": "application.is_associated_app",
                   	"value": true
              	}
            },
            "obligations": [
                {
                    "name": "WATERMARK",
                    "value": {
                        "text": $(User)$(Break)$(Date)$(Time)
                    }
                }
            ]
        }
    ]
}
```

## Central Policy

```json
{
    "version": "1.0",
    "issuer": "skydrm.com",
    "issueTime": "2016-07-11T13:09:45Z",
    "policies": [
        {
            "id": 0,
            "name": "Grant VIEW, EDIT and PRINT rights to all NextLabs users",
            "action": 1, // "GRANT"
            "rights": [
                "VIEW", "EDIT", "PRINT"
            ],
            "conditions": {
                "subject": {
                    "type": 1,
                    "operator": "=",
                    "name": "user.email",
                    "value": ".*@nextlabs\.com"
                },
                "resource": {},
                "environment": {
                    "type": 0,
                    "operator": "&&",
                    "expressions": [
                        {
                            "type": 1,
                            "operator": "=",
                            "name": "environment.connection_type",
                            "value": "console"
                        },
                        {
                            "type": 1,
                            "operator": ">",
                            "name": "user.id",
                            "value": 500
                        }
                    ]
                }
            }
        },
        {
            "id": 1,
            "name": "Revoke all the rights except VIEW when user login with RDP connection",
            "action": 0, // "REVOKE"
            "rights": [
                "EDIT", "PRINT", "SAVEAS", ...
            ],
            "conditions": {
                "environment": {
                    "type": 1,
                    "operator": "=",
                    "name": "environment.connection",
                    "value": "remote"
                }
            }
        },
        {
            "id": 2,
            "name": "Revoke all the rights when there lost heartbeat for more than 3 days",
            "action": 0, // "REVOKE"
            "rights": [
                "*"
            ],
            "conditions": {
                "environment": {
                    "type": 1,
                    "operator": ">",
                    "name": "environment.seconds_since_last_heartbeat",
                    "value": 259200
                }
            }
        }
    ]
}
```