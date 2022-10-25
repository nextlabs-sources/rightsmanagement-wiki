# **LibRMC Reference - Application Directories and Files**


# Directories

## LibRMC define following directories:

- **Application Root**

    Application install directory. Default is:
        
        "C:\Program Files\NextLabs\Rights Management Client"

- **Public Application Data Root**

    RMC Public Application Data directory is used to store common data such as installer.

        "%PublicAppDataRoot%" --- "C:\Users\Default\AppData\Local\NextLabs\Rights Management Client"

- **User Application Data Root**

    Each Windows User has its own RMC Application data directory:

        "%AppDataRoot%" --- "C:\Users\<Current User>\AppData\Local\NextLabs\Rights Management Client"
    
- **Server Home Directory**

    Every server has its own data space:

        "%RmsHome%" --- "C:\Users\<Current User>\AppData\Local\NextLabs\Rights Management\<RMS Router Host>"
    
- **Tenant Home Directory**

    Each tenant has its own data space too:

        "%TenantHome%" --- "C:\Users\<Current User>\AppData\Local\NextLabs\Rights Management\<RMS Router Host>\<Tenant Name>"
    
- **RM User Home Directory**

    Each Rights Management user has its own home directory, under its tenant's home directory:

        "%UserHome%" --- "C:\Users\<Current User>\AppData\Local\NextLabs\Rights Management\<RMS Router Host>\<Tenant Name>\<User Id>"
    
- **RM User Config Directory**

    Config directory contains all the configuration files owned by current user:

        "%UserConfigDir%" --- "C:\Users\<Current User>\AppData\Local\NextLabs\Rights Management\<RMS Router Host>\<Tenant Name>\<User Id>\Config"
    
- **RM User Temp Directory**

    Temp directory contains all the temp files used by application at runtime:

        "%UserTempDir%" --- "C:\Users\<Current User>\AppData\Local\NextLabs\Rights Management\<RMS Router Host>\<Tenant Name>\<User Id>\Temp"
    
- **RM User Memberships Directory**

    Memberships directory contains all the membership related files owned by current user:

        "%UserMembershipsDir%" --- "C:\Users\<Current User>\AppData\Local\NextLabs\Rights Management\<RMS Router Host>\<Tenant Name>\<User Id>\Memberships"
    
- **RM User Repositories Directory**

    Repositories directory contains all the repository directories owned by current user:

        "%ReposRoot%" --- "C:\Users\<Current User>\AppData\Local\NextLabs\Rights Management\<RMS Router Host>\<Tenant Name>\<User Id>\Repos"
    
- **RM User Repository Home Directory**

    Repository directory contains all the files owned by repository:

        "%RepoHome%" --- "C:\Users\<Current User>\AppData\Local\NextLabs\Rights Management\<RMS Router Host>\<Tenant Name>\<User Id>\Repos\<Repo Id>"
    
- **RM User Projects Directory**

    Prokects directory contains all the project directories related with current user:

        "%ProjectsRoot%" --- "C:\Users\<Current User>\AppData\Local\NextLabs\Rights Management\<RMS Router Host>\<Tenant Name>\<User Id>\Projects"
    
- **RM User Project Home Directory**

    Repository directory contains all the files owned by project:

        "%ProjectHome%" --- "C:\Users\<Current User>\AppData\Local\NextLabs\Rights Management\<RMS Router Host>\<Tenant Name>\<User Id>\Projects\<Project Id>"
    

# Registry

## Global Application key:

### Key Path

        "HKEY_LOCAL_MACHINE\Software\NextLabs\Rights Management Client"

### Values

        "Company": String

        "Version": String

        "DebugLogLevel": DWORD (Optional)

        "DebugLogSize": DWORD (Optional)

## User Application key:

### Key Path

        "HKEY_CURRENT_USER\Software\NextLabs\Rights Management Client"

### Values

        "Check Update": DWORD (Optional)

        "Auto Update": DWORD (Optional)

        "DebugLogLevel": DWORD (Optional)

        "DebugLogSize": DWORD (Optional)



# Config File

## RMS List

        "%AppDataRoot%\rms.list"

# Cache File

## Cached Login History

        "%RmsHome%\login.history"

## Cached User Credential

- **User Profile**

        "%UserHome%\profile.dat"

- **User Ticket**

        "%UserHome%\ticket.dat"

## Cached Config Files

- **Policy Bundle**

        "%UserConfig%\policy.bin"

- **Water Mark**

        "%UserConfig%\watermark.png"

## Cached User Membership Files

- **Membership Profile**

        "%UserMembershipsDir%\<MemberIdHash>.profile"

- **Membership Agreement 0**
    
        "%UserMembershipsDir%\<MemberIdHash>.agreement0"

- **Membership Agreement 1**
    
        "%UserMembershipsDir%\<MemberIdHash>.agreement1"

- **Membership Available File Tokens**
    
        "%UserMembershipsDir%\<MemberIdHash>.tokens"

## Cached Repository Files

- **Repository Profile**

        "%RepoHome%\repo.profile"

- **Repository Files List**
    
        "%RepoHome%\files.list"

- **Offline Files List**
    
        "%RepoHome%\offline.list"

- **Favorite Files List**
    
        "%RepoHome%\favorite.list"

## Cached Project Files

- **Pending Invitations for Me**
    
        "%ProjectsRoot%\invitations.list"

- **Project Profile**

        "%ProjectHome%\repo.profile"

- **Project Files List**
    
        "%ProjectHome%\files.list"

- **Project Member List**
    
        "%ProjectHome%\members.list"

- **Pending Project Invitations List**
    
        "%ProjectHome%\invitations.list"

# Debug Data

## Log File

        "%AppDataRoot%\DebugDump.txt"

## Dump File

        "%PublicAppDataRoot%\Dump\*.dmp"

# Update/Installer

##  Skipped Versions

        "%PublicAppDataRoot%\Installer\skip.list

##  Downloaded Version

        "%PublicAppDataRoot%\Installer\available.list

##  Downloaded Installer

        "%PublicAppDataRoot%\Installer\*.msi (or *.cab)

