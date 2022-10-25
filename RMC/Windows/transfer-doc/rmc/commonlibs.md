
# **Common Libraries** #

    By Gavin Ye
    8/8/2017



## Table of Content

- [Table of Content](#table-of-content)
- [1. Basic](#1-basic)
    - [1.1 Link to CRT](#11-link-to-crt)
    - [1.2 Namespace](#12-namespace)
    - [1.3 Repository](#13-repository)
- [2. Libraries](#2-libraries)
    - [2.1 Library Common](#21-library-common)
        - [2.1.1 CONST and Macros](#211-const-and-macros)
        - [2.1.2 Return Value](#212-return-value)
        - [2.1.3 String](#213-string)
        - [2.1.4 Time](#214-time)
        - [2.1.5 Bitsmap](#215-bitsmap)
        - [2.1.6 Json](#216-json)
    - [2.2 Library Crypto](#22-library-crypto)
        - [2.2.1 Common](#221-common)
        - [2.2.2 AES](#222-aes)
        - [2.2.3 RSA](#223-rsa)
        - [2.2.4 Diffie Hellman](#224-diffie-hellman)
        - [2.2.5 MD5](#225-md5)
        - [2.2.6 SHA](#226-sha)
        - [2.2.7 CRC](#227-crc)
        - [2.2.8 Certificate](#228-certificate)
    - [2.3 Library NXL](#23-library-nxl)
        - [2.3.1 NXL Format](#231-nxl-format)
        - [2.3.2 NXL Rights](#232-nxl-rights)
        - [2.3.3 NXL File](#233-nxl-file)
        - [2.3.4 Utilities](#234-utilities)
    - [2.4 Library REST](#24-library-rest)
        - [2.4.1 CONST](#241-common)
        - [2.4.2 Uri](#242-uri)
        - [2.4.3 Http Client](#243-http-client)
    - [2.5 Library Windows Utilities](#25-library-windows-utilities)
        - [2.5.1 OS Version](#251-os-version)
        - [2.5.2 Hardware Info](#252-hardware-info)
        - [2.5.3 Credential](#253-credential)
        - [2.5.4 Language](#254-language)
        - [2.5.5 File Info](#255-file-info)
        - [2.5.6 Path](#256-path)
        - [2.5.7 Process](#257-process)
        - [2.5.8 Registry](#258-registry)
        - [2.5.9 Zip](#259-zip)
    - [2.6 Library RMC](#26-library-rmc)



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

## 2. Libraries

### 2.1 Library Common

This library (`libcommon.lib`) provides common functionalities.

#### 2.1.1 CONST and Macros

Defines const value and handy macros.

```cpp
#include <nx/common/constant.h>
#include <nx/common/macros.h>
```

#### 2.1.2 Return Value

Defines return value object and related macros used by all RMC libraries.

```cpp
#include <nx/common/result.h>
```

#### 2.1.3 String

Declare string related classes and APIs, which include:

- String buffer
- Lower/Upper
- String conversion
- Encoding conversion
- String split
- String check
- Escape and unescape

```cpp
#include <nx/common/string.h>
#include <nx/common/stringex.h>
```

#### 2.1.4 Time

Declare time related classes and APIs, which include:

- Time span
- Time zone
- Date time
- Time serialize
- Time conversion

```cpp
#include <nx/common/time.h>
```

#### 2.1.5 Bitsmap

Declare bitsmap classe.

```cpp
#include <nx/common/bitsmap.h>
```

#### 2.1.6 Json

Declare JSON object class and JSON parser classe.

```cpp
#include <nx/common/rapidjson.h>
```


### 2.2 Library Crypto

This library (`libcrypt.lib`) provides crypto related classes and APIs.

#### 2.2.1 Common

Declare CNG providers object.

```cpp
#include <nx/crypt/provider.h>
```

#### 2.2.2 AES

Declare AES key object and encrypt/decrypt APIs.

```cpp
#include <nx/crypt/aes.h>
```

#### 2.2.3 RSA

Declare RSA key object and encrypt/decrypt/sign/verify APIs.

```cpp
#include <nx/crypt/rsa.h>
```

#### 2.2.4 Diffie Hellman

Declare Diffie Hellman key object.

```cpp
#include <nx/crypt/dh.h>
```

#### 2.2.5 MD5

Declare MD5 hash APIs.

```cpp
#include <nx/crypt/md5.h>
```

#### 2.2.6 SHA

Declare SHA1/SHA256 hash APIs.

```cpp
#include <nx/crypt/md5.h>
```

#### 2.2.7 CRC

Declare CRC32/CRC64 checksum APIs.

```cpp
#include <nx/crypt/crc.h>
```

#### 2.2.8 Certificate

Declare certificate info/context/store classes.

```cpp
#include <nx/crypt/cert.h>
```

### 2.3 Library NXL

This library (`libnxl.lib`) provides NXL file related classes and APIs.

#### 2.3.1 NXL Format

Define NXL File Format.

```cpp
#include <nx/nxl/nxlfmt.h>
```

#### 2.3.2 NXL Rights

Define rights supported by NXL file, and related Macros/APIs as well.

```cpp
#include <nx/nxl/nxlrights.h>
```

#### 2.3.3 NXL File

Declare NXL file related classes and APIs which allow user to create/open/manipulate NXL files.

```cpp
#include <nx/nxl/nxlfile.h>
```

#### 2.3.4 Utilities

Declare NXL file related utility APIs.

```cpp
#include <nx/nxl/nxlutil.h>
```

### 2.4 Library REST

This library (`librest.lib`) provides basic classes and APIs used to build RESTful client.

#### 2.4.1 CONST

Define constant values.

```cpp
#include <nx/rest/http_const.h.h>
```

#### 2.4.2 Uri

Declare Uri class to create/parse Uri.

```cpp
#include <nx/rest/uri.h.h>
```

#### 2.4.3 Http Client

Declare http client classes.

```cpp
#include <nx/rest/http_client.h>
```

### 2.5 Library Windows Utilities

This library (`libwinutil.lib`) provides handy classes and APIs specific for Windows.

#### 2.5.1 OS Version

Declare class and APIs to get Windows OS version.

```cpp
#include <nx/winutil/osver.h>
```

#### 2.5.2 Hardware Info

Declare class and APIs to get hardware information:

- CPU
- Disk
- Host
- Memory
- Netwrok Adapter

```cpp
#include <nx/winutil/cpu.h>
#include <nx/winutil/disk.h>
#include <nx/winutil/host.h>
#include <nx/winutil/memory.h>
#include <nx/winutil/network.h>
```


#### 2.5.3 Credential

Declare classes and APIs to get security objects.

```cpp
#include <nx/winutil/cred.h>
```

#### 2.5.4 Language

Declare classes and APIs to get language information.

```cpp
#include <nx/winutil/lang.h>
```

#### 2.5.5 File Info

Declare classes and APIs to get file information.

```cpp
#include <nx/winutil/file.h>
```

#### 2.5.6 Path

Declare classes and APIs to parse/generate/convert file path.

```cpp
#include <nx/winutil/path.h>
```

#### 2.5.7 Process

Declare classes and APIs to get process information.

```cpp
#include <nx/winutil/process.h>
```

#### 2.5.8 Registry

Declare classes and APIs to manipulate registry.

```cpp
#include <nx/winutil/registry.h>
```

#### 2.5.9 Zip

Declare APIs to create/add/unpack zip file.

```cpp
#include <nx/winutil/zip.h>
```


### 2.6 Library RMC

Please refer to document ["LIBRMC"](librmc.md)