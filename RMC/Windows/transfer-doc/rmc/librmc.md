
# **Library RMC** #

    By Gavin Ye
    8/8/2017



## Table of Content

- [Table of Content](#table-of-content)
- [1. Basic](#1-basic)
    - [1.1 Link to CRT](#11-link-to-crt)
    - [1.2 Namespace](#12-namespace)
    - [1.3 Repository](#13-repository)
- [2. Reference](#2-reference)
    - [2.1 Create and Initialize](#21-create-and-initialize)
    - [2.2 Account](#22-account)
        - [2.2.1 Tenant](#221-tenant)
        - [2.2.2 User](#222-user)
        - [2.2.3 Membership](#222-membership)
        - [2.2.3 Login](#223-login)
        - [2.2.4 Logout](#224-logout)
    - [2.3 Directories](#23-tenant)
        - [2.3.1 Install Directory](#231-install-directory)
        - [2.3.2 Application Data](#232-application-data)
        - [2.3.3 Cache Directory](#233-cache-directory)
        - [2.3.4 Temp Directory](#234-temp-directory)
    - [2.4 Repository](#24-repository)
        - [2.4.1 MyDrive](#241-mydrive)
        - [2.4.2 Google Drive](#242-google-drive)
        - [2.4.3 One Drive](#243-one-drive)
        - [2.4.4 Dropbox](#244-dropbox)
        - [2.4.5 SharePoint Online](#245-sharepoint-online)
    - [2.4 MyVault](#24-myvault)
    - [2.5 Project](#25-project)
    - [2.6 Local Files](#26-local-files)
    - [2.7 Key Management](#27-key-management)
    - [2.8 RESTful APIs](#28-restful-apis)
    - [2.9 Activity Log](#29-activity-log)
    - [2.10 Viewer Manager](#210-viewer-manager)



## 1. Basic

### 1.1 Link to CRT

All libraries are built using "/MT" or "/MTd" to static link to CRT.

### 1.2 Namespace

All the code is under namespace `"NX"`.
Callers may want to use following code in their source file:

```cpp
using namespace NX;
```

### 1.3 Repository

All the common libraries are in repository [RMC-Windows](https://bitbucket.org/nxtlbs-devops/rmc-windows/overview).

User can use following command to clone the repository.

```bash
git clone git@bitbucket.org:nxtlbs-devops/rmc-windows.git
```


## 2. Reference

**Source Code**

`/RMC-Windows/source/libraries/librmc`

**Class**

```cpp
class RmSession;
```

### 2.1 Create and Initialize

Caller need to use following two static functions to create and initialize an `RmSession` object.

```cpp
static RmSession* Create(Result& error);
static RmSession* Create(const RmRouter& router, Result& error);
```

### 2.2 Account

User must login with a valid SkyDRM account before he or she can use this software.

#### 2.2.1 Tenant

A user could belong to a tenant. By default, it belongs to tenant `skydrm.com`.

```cpp
class RmTenant;
```

#### 2.2.2 User

See

```cpp
class RmUser;
```

#### 2.2.3 Membership

A user can have multiple memberships. All the memberships will be returned to client when user login.

See

```cpp
class RmUserMembership;
```

#### 2.2.3 Login

- Login with SkyDRM Credential

    If user has already registered a user in `SkyDRM.com`, he or she can login with this credential.

```cpp
Result RmSession::Login(const std::wstring& userName, const std::wstring& password);
```

- SSO

    `SkyDRM.com` also support SSO. User can login with their Google or facebook credential. RMS will return JSON result after SSO finish.

```cpp
Result RmSession::LoginFromResult(const std::string& jsonResult);
```

- Auto Login

    The login result can be cached so that user doesn't need to login again before the ticket expire. The login data will be encrypted and cached in user's application data root.

#### 2.2.4 Logout

When suer logout, cached login data will be deleted and UI will jump to login page.

### 2.3 Directories

#### 2.3.1 Install Directory

If this software is installed for all users, the install directory is under `"C:\Program Files\"`.

If it is installed for current user only, it will be put in common program data.

#### 2.3.2 Application Data

`C:\Users\<Current User>\AppData\Local\NextLabs\SkyDRM`

#### 2.3.3 Cache Directory

`C:\Users\<Current User>\AppData\Local\NextLabs\SkyDRM\<tenant>\<userId>\Cache`

#### 2.3.4 Temp Directory

`C:\Users\<Current User>\AppData\Local\NextLabs\SkyDRM\<tenant>\<userId>\Temp`

### 2.4 Repository

#### 2.4.1 MyDrive

See `"session_mydrive.cpp"` in `"librmc"`.

#### 2.4.2 Google Drive

See `"session_google.cpp"` in `"librmc"`.

#### 2.4.3 One Drive

See `"session_onedrive.cpp"` in `"librmc"`.

#### 2.4.4 Dropbox

See `"session_dropbox.cpp"` in `"librmc"`.

#### 2.4.5 SharePoint Online

See `"session_sharepointonline.cpp"` in `"librmc"`.

### 2.4 MyVault

See `"session_myvault.cpp"` in `"librmc"`.

### 2.5 Project

See `"session_project.cpp"` in `"librmc"`.

### 2.6 Local Files

See `"session_localfile.cpp"` in `"librmc"`.

### 2.7 Key Management

See `"session_keym.cpp"` in `"librmc"`.

### 2.8 RESTful APIs

See `"rest.h" and "rest_xxxxx.cpp"` in `"librmc"`.

### 2.9 Activity Log

See `"activity.h" and "activity.cpp"` in `"librmc"`.

### 2.10 Viewer Manager

See `"viewermgr.h" and "viewermgr.cpp"` in `"librmc"`.