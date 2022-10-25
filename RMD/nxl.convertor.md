# **NXL Format Convertor**

# Description

This tool convert old NXL format to latest version.
Currently it converts format 1.0 to format 2.0.

# Limitation

- Original RMS must be 8.x
- Current RMS must be 9.x or later
- This tool doesn't support 3rd-party login (Google, Facebook, etc.)

# Usage

## Command

`nxlfmtconv.exe <Options ...> <file/dir 1> <file/dir 2> ...`

### Options

- **`/os <URL>`**:      Mandatory. Original RMS URL (support old NXL format).
- **`/ot <Tenant>`**:   Optional. Tenant name required by original RMS server. Default value is `"N/A"`.
- **`/ou <User>`**:     Optional. User name required by original RMS server. If it is not set, use current windows user.
- **`/op <Password>`**: Optional. Password required by current RMS server. This must be set if `/ou` is set.
- **`/cs <URL>`**:      Mandatory. Current RMS URL (support current NXL format).
- **`/ct <Tenant>`**:   Optional. Tenant name required by current RMS server. If it is not set, this tool will ask for user input.
- **`/cu <User>`**:     Optional. User name required by current RMS server. If it is not set, this tool will ask for user input.
- **`/cp <Password>`**: Optional. Password required by current RMS server. If it is not set, this tool will ask for user input.
- **`/v`**:             Optional. Verbose, print a message for each converted file.
- **`/q`**:             Optional. Quiet mode - If `/ct` is not set, use default tenant "skydrm.com". If `/cu` or `/cp` is not set, this tool will fail immediately instead of asking user.
- **`/r`**:             Optional. Recursively convert all the files in sub folders. Otherwise this tool only converts files under specified directory.
- **`/l <file name>`**: Optional. Generate a log file.
- **`/s <file name>`**: Optional. Generate a summary XML file.

## Comments

#### *Original RMS*

Original RMS must be RMS version 8.x which support internal authentication and external authentication. 
If `/ou` is not set, this tool will use internal authentication to login to RMS (use current Windows login 
user's credential). And `/op` is ignored.
If `/ou` is set, `/op` must be set at the same time. And this tool use external authentication to login to RMS.

#### *Current RMS*

Current RMS must be version 9.x which use new login procedure. So user must provide tenant (`/ct`), user name (`/cu`) 
and password (`/cp`) information.
If any of those information is not passed as parameter, this tool fails immediately if `/q` is set, or ask for user's 
input when `/q` is not set. 

#### *Quiet Mode*

In quiet mode, this tool won't ask for any user input, and won't print any message to console (`/v` is ignored).


## Examples

#### Convert file

Following command convert a NXL 1.0 file to to current version.

`nxlfmtconv.exe /os https://devtestrms.nextlabs.com:8443/RMS/service /cs https://skydrm.com/rms/rs "D:\private\design.docx.nxl"`

Since `/t`, `/u` and `/p` is not set, this tool will ask user to input corresponding information.

        > Enter tenant name (empty for default tenant 'skydrm.com'):
        > Enter user name: john.tyler
        > Enter password:

#### Convert Folders silently

Following command convert all the NXL 1.0 files under two folders to current version.

`nxlfmtconv.exe /os https://devtestrms.nextlabs.com/RMS /cs https://skydrm.com/rms/rs /ct skydrm.com /cu john.tyler /cp 123456 /r /l convert.log /s summary.xml "C:\Users\john.tyler\Documents" "D:\Test Data\Old"`

