# **Console Test Application (rmctest.exe)**


# Purpose

This console application is used to test all the backend APIs provided by librmc.

It is also used as examples for developers who are using librmc.


# Usage

## *Before Start*

Before start this program, you need to do following things:

1. Run "clientreg.exe" as Administrator to register this client

    This tool will generate a unique certificate in system certificate store. And RMC uses this certificate to generate unique client id and client key.
In official product, this task should be done by installer.

## *Start*

At begining, this application ask user to input router URL.

This decides which server will be used.

It supports two alias now:

- Empty/Default: Use official Sky DRM server ("https://r.skydrm.com")
- Debug: Use staging server ("https://rmtest.nextlabs.solutions")

## *Login*

The console application supports native RNS login - user will be asked for name and password.

After login successfully, the application will enter main command loop. Otherwise it will stay in the login mode.

## *Main Command Namespace*

After login successfully, application enter a loop waiting for user's command.

In this loop, it supports following commands:

|      Command                  |       Description                                                     |
|-------------------------------|-----------------------------------------------------------------------|
| **help/commands**             |       Show commands information                                       |
| **exit/quit**                 |       Quit this application                                           |
| **logout**                    |       Logout current user, enter login mode again                     |
| **profile**                   |       Show current user's profile                                     |
| **status**                    |       Show general application status                                 |
| **protect file**              |       Protect a local file (convert it to NXL file)                   |
| **property file**             |       Show NXL file properties                                        |
| **decrypt source [target]**   |       Decrypt an NXL file to target file (if not specified, it will use the same file name without NXL extension) |
| **mydrive**                   |       Enter MyDrive command namespace                                 |
| **myvault**                   |       Enter MyVault command namespace                                 |
| **repository**                |       Enter Repository command namespace                              |
| **project**                   |       Enter Project command namespace                                 |
|                               |                                                                       |


## *MyDrive Command Namespace*

In 'MyDrive' command namespace, user can run commands related with MyDrive.

|      Command                  |       Description                                                     |
|-------------------------------|-----------------------------------------------------------------------|
| **help/commands**             |       Show commands information                                       |
| **exit/quit**                 |       Exit from current command loop, and back to main command loop   |
| **usage**                     |       Show MyDrive usage                                              |
| **pwd**                       |       Show current path                                               |
| **cd path**                   |       Go to target path                                               |
| **mkdir name**                |       Create a folder                                                 |
| **dir**                       |       List all files (or files match specified keywords)              |
| **find keyword**              |       Find all files match specified keywords                         |
| **del file**                  |       Delete a file                                                   |
| **download source [target]**  |       Download a file to local                                        |
| **upload source [target]**    |       Upload a file to MyDrive                                        |
|                               |                                                                       |


## *MyVault Command Namespace*

In 'MyVault' command namespace, user can run commands related with MyVault.

|      Command                  |       Description                                                     |
|-------------------------------|-----------------------------------------------------------------------|
| **help/commands**             |       Show commands information                                       |
| **exit/quit**                 |       Exit from current command loop, and back to main command loop   |
| **usage**                     |       Show MyVault usage                                              |
| **dir**                       |       List all files (or files match specified keywords)              |
| **find keyword**              |       Find all files match specified keywords                         |
| **del duid file**             |       Delete a file                                                   |
| **property file**             |       Show target file's Metadata                                     |
| **download source [target]**  |       Download a file to local                                        |
| **upload source**             |       Upload a file to MyVault                                        |
|                               |                                                                       |


## *Repository Command Namespace*

### General

In 'Repository' command namespace, user can run commands related with Repository.

|      Command                  |       Description                                                     |
|-------------------------------|-----------------------------------------------------------------------|
| **help/commands**             |       Show commands information                                       |
| **exit/quit**                 |       Exit from current command loop, and back to main command loop   |
| **list**                      |       List all repositories information                               |
| **select repoId**             |       Select a repository and enter its command namespace             |
| **property repoId**           |       Show a repository's information                                 |
| **del repoId**                |       Delete a repository                                             |
|                               |                                                                       |

### Google Drive

TBD

### Dropbox

TBD

### OneDrive

TBD

### Sharepoint Online

TBD

## *Project Command Namespace*

### General

|      Command                  |       Description                                                     |
|-------------------------------|-----------------------------------------------------------------------|
| **help/commands**             |       Show all the commands information                               |
| **exit/quit**                 |       Exit from current command loop, and back to main command loop   |
| **list**                      |       List all projects information                                   |
| **find keywords**             |       Find a project by given keywords                                |
| **create name description**   |       Create a new project                                            |
| **select projectId**          |       Select a project and enter its command namespace                |
| **property repoId**           |       Show a repository's information                                 |
| **invitation**                |       Enter Invitation command loop                                   |
|                               |                                                                       |


### Invitation Command Namespace


|      Command                  |       Description                                                     |
|-------------------------------|-----------------------------------------------------------------------|
| **list**                      |       List all pending invitations                                    |
| **accept id code**            |       Accept an invitation                                            |
| **decline id code [reason]**  |       Decline an invitation                                           |
|                               |                                                                       |

### Specific Project Command Namespace

|      Command                  |       Description                                                     |
|-------------------------------|-----------------------------------------------------------------------|
| **profile**                   |       Show current project profile                                    |
| **list file**                 |       List all files                                                  |
| **list member**               |       List all members                                                |
| **list invitation**           |       List all pending invitations of this project                    |
| **invite email1 email2 ...**  |       Invite people to this project                                   |
| **delmember memberId**        |       Delete a project member                                         |
| **del file**                  |       Delete a project file                                           |
| **property file**             |       Show a file's Metadata                                          |
| **create folder**             |       Create a folder                                                 |
| **download source [target]**  |       Download a file to local                                        |
| **upload source**             |       Upload a file to MyVault                                        |
|                               |                                                                       |
