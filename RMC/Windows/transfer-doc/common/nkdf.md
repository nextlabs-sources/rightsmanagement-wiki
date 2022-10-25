
# **NKDF (NextLabs Kernel-Mode Development Foundation)** #

    By Gavin Ye
    8/8/2017


NKDF (NextLabs Kernel Mode Development Foundation) is a kernel mode library which provides the most common used APIs to reduce developer work and unify common features.


## Table of Content

- [Table of Content](#table-of-content)
- [1. Overview](#1-overview)
    - [1.1 Repository](#11-repository)
    - [1.2 Settings](#12-settings)
- [2. Reference](#2-reference)
    - [2.1 Basic](#21-basic)
        - [2.1.1 Compress](#211-compress)
        - [2.1.2 CSQ](#212-csq)
        - [2.1.3 Device Control](#213-device-control)
        - [2.1.4 Generic Table](#214-generic-table)
        - [2.1.5 List](#215-list)
        - [2.1.6 Registry](#216-registry)
        - [2.1.7 String](#217-string)
        - [2.1.8 Time](#218-time)
    - [2.2 Crypto](#22-crypto)
        - [2.2.1 Common](#221-common)
        - [2.2.2 AES](#222-aes)
        - [2.2.3 RSA](#223-rsa)
        - [2.2.4 CRC](#224-crc)
        - [2.2.5 MD5](#225-md5)
        - [2.2.6 SHA](#226-sha)
    - [2.3 File System](#23-file-system)
        - [2.3.1 Disk](#231-disk)
        - [2.3.2 Volume](#232-volume)
        - [2.3.3 Mini-filter Communication](#233-mini-filter-communication)
        - [2.3.4 Mini-filter CSQ](#234-mini-filter-csq)
        - [2.3.5 Mini-filter Operation](#235-mini-filter-operation)
        - [2.3.6 Fs Operation](#236-fs-operation)
    - [2.4 Log](#24-log)
        - [2.4.1 Definition](#241-definition)
        - [2.4.2 File Log](#242-file-log)
    - [2.5 Process](#25-process)
        - [2.5.1 Monitor](#251-monitor)
        - [2.5.2 Utility](#252-utility)


## 1. Overview

### 1.1 Repository

NKDF is in repository [RightsManagementClient](https://bitbucket.org/nxtlbs-devops/rightsmanagementclient/overview).

User can use following command to clone the repository.

```bash
git clone git@bitbucket.org:nxtlbs-devops/rightsmanagementclient.git
```

### 1.2 Settings

To build NKDF, you need to have WDK for Windows 10 (Version 10.0.10586 or later).

## 2. Reference

### 2.1 Basic

This is some basic APIs.

#### 2.1.1 Compress

APIs to compress/decompress data.

```c
#include <nkdf/basic/compress.h>

_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkCompressBuffer(
    _In_ const VOID* Data,
    _In_ ULONG DataLength,
    _Out_ VOID* CompressedBuffer,
    _In_ ULONG CompressedBufferSize,
    _Out_ PULONG CompressedDataLength
    );

_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkDecompressBuffer(
    _In_ const VOID* CompressedData,
    _In_ ULONG CompressedDataLength,
    _In_ VOID* UncompressedBuffer,
    _In_ ULONG UncompressedBufferSize,
    _Out_ PULONG UncompressedDataLength
    );
```

#### 2.1.2 CSQ

APIs for Cancel Safe Queue

```c
#include <nkdf/basic/csq.h>

_IRQL_requires_max_(APC_LEVEL)
_Check_return_
NTSTATUS
NkCsqCreate(
            _In_ PDEVICE_OBJECT DeviceObject,
            _In_ PDRIVER_DISPATCH IrpDispatch,
            _Out_ PNKCSQ* Csq
            );

_IRQL_requires_max_(APC_LEVEL)
VOID
NkCsqDestroy(
             _In_ PNKCSQ Csq
             );

_IRQL_requires_max_(DISPATCH_LEVEL)
_Check_return_
NTSTATUS
NkCsqInsert(
             _In_ PNKCSQ Csq,
             _Inout_ PIRP Irp
             );
```

#### 2.1.3 Device Control

Device control API.

```c
#include <nkdf/basic/device.h>

_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkDeviceIoControl(
                  _In_ ULONG IoControlCode,
                  _In_ PDEVICE_OBJECT Device,
                  _In_opt_ PVOID InputBuffer,
                  _In_ ULONG InputBufferLength,
                  _Out_opt_ PVOID OutputBuffer,
                  _In_ ULONG OutputBufferLength,
                  _In_ BOOLEAN InternalDeviceIoControl,
                  _In_ BOOLEAN OverrideVerify,
                  _Out_opt_ PIO_STATUS_BLOCK Iosb
                  );
```

#### 2.1.4 Generic Table

Generic table APIs.

```c
#include <nkdf/basic/gtable.h>

typedef
_IRQL_requires_same_
_Function_class_(NKGT_TRAVERSE_ROUTINE)
VOID
NTAPI
NKGT_TRAVERSE_ROUTINE(
                      _In_ struct _RTL_GENERIC_TABLE *Table,
                      _In_ PVOID Element,
                      _Out_ PBOOLEAN Continue,
                      _Inout_opt_ PVOID TraverseContext
                      );
typedef NKGT_TRAVERSE_ROUTINE* PNKGT_TRAVERSE_ROUTINE;

typedef
_IRQL_requires_same_
_Function_class_(NKGT_OPERATE_ROUTINE)
VOID
NTAPI
NKGT_OPERATE_ROUTINE(
                     _In_ struct _RTL_GENERIC_TABLE *Table,
                     _In_ PVOID Element,
                     _Inout_opt_ PVOID OperateContext
                     );
typedef NKGT_OPERATE_ROUTINE* PNKGT_OPERATE_ROUTINE;

_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkCreateSafeGenericTable(
                         _Out_ PHANDLE TableHandle,
                         _In_ PRTL_GENERIC_COMPARE_ROUTINE CompareRoutine,
                         _In_ ULONG ElementSize,
                         _In_ POOL_TYPE PoolType,
                         _In_ ULONG PoolTag
                         );
                         
_IRQL_requires_(PASSIVE_LEVEL)
VOID
NkDestroySafeGenericTable(
                          _In_ HANDLE TableHandle
                          );
                          
_Check_return_
_IRQL_requires_max_(APC_LEVEL)
BOOLEAN
NkIsGenericTableEmpty(
                      _In_ HANDLE TableHandle
                      );
                      
_Check_return_
_IRQL_requires_max_(APC_LEVEL)
ULONG
NkNumberGenericTableElements(
                             _In_ HANDLE TableHandle
                             );
                             
_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkInsertElementGenericTable(
                            _In_ HANDLE TableHandle,
                            _In_ PVOID Buffer,
                            _In_ CLONG BufferSize
                            );
                            
_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkDeleteElementGenericTable(
                            _In_ HANDLE TableHandle,
                            _In_ PVOID Buffer,
                            _In_opt_ PNKGT_OPERATE_ROUTINE OperateRoutine,
                            _Inout_opt_ PVOID OperateContext
                            );
                            
_Check_return_
_IRQL_requires_max_(APC_LEVEL)
BOOLEAN
NkGetElementGenericTable(
                         _In_ HANDLE TableHandle,
                         _In_ ULONG Index,
                         _In_ BOOLEAN Exclusive,
                         _In_ PNKGT_OPERATE_ROUTINE OperateRoutine,
                         _Inout_opt_ PVOID OperateContext
                         );
                         
_Check_return_
_IRQL_requires_max_(APC_LEVEL)
BOOLEAN
NkLookupElementGenericTable(
                            _In_ HANDLE TableHandle,
                            _In_ PVOID Buffer,
                            _In_ BOOLEAN Exclusive,
                            _In_opt_ PNKGT_OPERATE_ROUTINE OperateRoutine,
                            _Inout_opt_ PVOID OperateContext
                            );
                            
_IRQL_requires_max_(APC_LEVEL)
VOID
NkEnumerateGenericTable(
                        _In_ HANDLE TableHandle,
                        _In_ BOOLEAN Exclusive,
                        _In_ PNKGT_TRAVERSE_ROUTINE TraverseRoutine,
                        _Inout_opt_ PVOID TraverseContext
                        );

```

#### 2.1.5 List

Convinient List APIs.

```c
#include <nkdf/basic/list.h>

typedef
BOOLEAN
(*NKLIST_COMPARE_ROUTINE)(
                          _In_ const LIST_ENTRY* Entry1,
                          _In_ const LIST_ENTRY* Entry2
                          );

typedef
VOID
(*NKLIST_FREE_ROUTINE)(
                       _In_ LIST_ENTRY* Entry
                       );

typedef
NTSTATUS
(*NKLIST_TRAVERSE_ROUTINE)(
                           _In_ PLIST_ENTRY Entry,
                           _Out_ PBOOLEAN Continue
                           );
                           
_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkCreateList(
             _In_ NKLIST_COMPARE_ROUTINE CompareRoutine,
             _In_ NKLIST_FREE_ROUTINE FreeRoutine,
             _Out_ PHANDLE ListHandle
             );
             
_IRQL_requires_max_(APC_LEVEL)
VOID
NkDestoryList(
              _In_ HANDLE ListHandle
              );
              
_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkTraverseList(
               _In_ HANDLE ListHandle,
               _In_ BOOLEAN Exclusive,
               _In_ NKLIST_TRAVERSE_ROUTINE TraverseRoutine
               );
               
_Check_return_
_IRQL_requires_max_(APC_LEVEL)
BOOLEAN
NkIsListEmpty(
              _In_ HANDLE ListHandle
              );
              
_IRQL_requires_max_(APC_LEVEL)
VOID
NkClearList(
            _In_ HANDLE ListHandle
            );
            
_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkInsertHeadList(
                 _In_ HANDLE ListHandle,
                 _In_ PLIST_ENTRY Entry,
                 _In_ BOOLEAN Unique
                 );
                 
_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkInsertTailList(
                 _In_ HANDLE ListHandle,
                 _In_ PLIST_ENTRY Entry,
                 _In_ BOOLEAN Unique
                 );
                 
_Check_return_
_IRQL_requires_max_(APC_LEVEL)
PLIST_ENTRY
NkRemoveHeadList(
                 _In_ HANDLE ListHandle
                 );
                 
_Check_return_
_IRQL_requires_max_(APC_LEVEL)
PLIST_ENTRY
NkRemoveTailList(
                 _In_ HANDLE ListHandle
                 );
                 
_Check_return_
_IRQL_requires_max_(APC_LEVEL)
BOOLEAN
NkRemoveEntryList(
                  _In_ HANDLE ListHandle,
                  _In_ PLIST_ENTRY Entry
                  );
```

#### 2.1.6 Registry

APIs to operate registry.

```c
#include <nkdf/basic/registry.h>

_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegOpenKey(
             _Out_ PHANDLE KeyHandle,
             _In_ PUNICODE_STRING KeyPath,
             _In_ ACCESS_MASK DesiredAccess
             );
             
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegCreateKey(
               _In_ PUNICODE_STRING KeyPath,
               _Out_ PHANDLE KeyHandle,
               _Out_opt_ PBOOLEAN CreateNew
               );
               
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegKeyExist(
              _In_ PUNICODE_STRING KeyPath,
              _Out_ PBOOLEAN Exist
              );
              
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegDeleteKey(
               _In_ PUNICODE_STRING KeyPath
               );
               
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegDeleteValue(
                 _In_ PUNICODE_STRING KeyPath,
                 _In_ PUNICODE_STRING ValueName
                 );
                 
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegReadIntValue(
                  _In_ PUNICODE_STRING KeyPath,
                  _In_ PUNICODE_STRING ValueName,
                  _Out_ PULONG ValueData
                  );
                  
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegReadInt64Value(
                    _In_ PUNICODE_STRING KeyPath,
                    _In_ PUNICODE_STRING ValueName,
                    _Out_ PULONGLONG ValueData
                    );
                    
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegReadBooleanValue(
                      _In_ PUNICODE_STRING KeyPath,
                      _In_ PUNICODE_STRING ValueName,
                      _Out_ PBOOLEAN ValueData
                      );
                      
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegReadStringValue(
                     _In_ PUNICODE_STRING KeyPath,
                     _In_ PUNICODE_STRING ValueName,
                     _In_ BOOLEAN PagePool,
                     _In_ ULONG PoolTag,
                     _Out_ PUNICODE_STRING ValueData
                     );
                     
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegReadBinaryValue(
                     _In_ PUNICODE_STRING KeyPath,
                     _In_ PUNICODE_STRING ValueName,
                     _Out_ PULONG ValueType,
                     _Out_writes_opt_(*ValueDataLength) PVOID ValueData,
                     _Inout_ PULONG ValueDataLength
                     );
                     
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegWriteIntValue(
                   _In_ PUNICODE_STRING KeyPath,
                   _In_ PUNICODE_STRING ValueName,
                   _In_ ULONG Data
                   );
                   
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegWriteInt64Value(
                     _In_ PUNICODE_STRING KeyPath,
                     _In_ PUNICODE_STRING ValueName,
                     _In_ ULONGLONG Data
                     );
                     
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegWriteBooleanValue(
                       _In_ PUNICODE_STRING KeyPath,
                       _In_ PUNICODE_STRING ValueName,
                       _In_ BOOLEAN Data
                       );
                       
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegWriteStringValue(
                      _In_ PUNICODE_STRING KeyPath,
                      _In_ PUNICODE_STRING ValueName,
                      _In_opt_ const WCHAR* Data
                      );
                      
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkRegWriteBinaryValue(
                      _In_ PUNICODE_STRING KeyPath,
                      _In_ PUNICODE_STRING ValueName,
                      _In_ PVOID Data,
                      _In_ ULONG DataSize
                      );
```

#### 2.1.7 String

APIs for string manipulation.

```c
#include <nkdf/basic/string.h>

_Check_return_
LONG
NkCompareStringA(
                 _In_ const CHAR* Str1,
                 _In_ const CHAR* Str2,
                 _In_ BOOLEAN CaseInSensitive
                 );
                 
_Check_return_
LONG
NkCompareStringW(
                 _In_ const WCHAR* Str1,
                 _In_ const WCHAR* Str2,
                 _In_ BOOLEAN CaseInSensitive
                 );
                 
_Check_return_
LONG
NkCompareUnicodeString(
                       _In_ PCUNICODE_STRING Str1,
                       _In_ PCUNICODE_STRING Str2,
                       _In_ BOOLEAN CaseInSensitive
                       );
                       
_Check_return_
BOOLEAN
NkEqualStringA(
               _In_ const CHAR* Str1,
               _In_ const CHAR* Str2,
               _In_ BOOLEAN CaseInSensitive
               );
               
_Check_return_
BOOLEAN
NkEqualStringW(
               _In_ const WCHAR* Str1,
               _In_ const WCHAR* Str2,
               _In_ BOOLEAN CaseInSensitive
               );
               
_Check_return_
BOOLEAN
NkEqualUnicodeString(
                     _In_ PCUNICODE_STRING Str1,
                     _In_ PCUNICODE_STRING Str2,
                     _In_ BOOLEAN CaseInSensitive
                      );
                      
_Check_return_
BOOLEAN
NkStartsWithA(
              _In_ const CHAR* Str1,
              _In_ const CHAR* Str2,
              _In_ BOOLEAN CaseInSensitive
              );
              
_Check_return_
BOOLEAN
NkStartsWithW(
              _In_ const WCHAR* Str1,
              _In_ const WCHAR* Str2,
              _In_ BOOLEAN CaseInSensitive
              );
              
_Check_return_
BOOLEAN
NkStartsWithUnicodeString(
                          _In_ PCUNICODE_STRING Str1,
                          _In_ PCUNICODE_STRING Str2,
                          _In_ BOOLEAN CaseInSensitive
                          );
                          
_Check_return_
BOOLEAN
NkEndsWithA(
            _In_ const CHAR* Str1,
            _In_ const CHAR* Str2,
            _In_ BOOLEAN CaseInSensitive
            );
            
_Check_return_
BOOLEAN
NkEndsWithW(
            _In_ const WCHAR* Str1,
            _In_ const WCHAR* Str2,
            _In_ BOOLEAN CaseInSensitive
            );
            
_Check_return_
BOOLEAN
NkEndsWithUnicodeString(
                        _In_ PCUNICODE_STRING Str1,
                        _In_ PCUNICODE_STRING Str2,
                        _In_ BOOLEAN CaseInSensitive
                        );
                        
_Check_return_
CHAR*
NkFindA(
        _In_ const CHAR* Str1,
        _In_ const CHAR* Str2,
        _In_ BOOLEAN CaseInSensitive
        );
        
_Check_return_
WCHAR*
NkFindW(
        _In_ const WCHAR* Str1,
        _In_ const WCHAR* Str2,
        _In_ BOOLEAN CaseInSensitive
        );
        
_Check_return_
UNICODE_STRING
NkFindUnicodeString(
                    _In_ PCUNICODE_STRING Str1,
                    _In_ PCUNICODE_STRING Str2,
                    _In_ BOOLEAN CaseInSensitive
                    );
                    
_Check_return_
BOOLEAN
NkContainsA(
            _In_ const CHAR* Str1,
            _In_ const CHAR* Str2,
            _In_ BOOLEAN CaseInSensitive
            );
            
_Check_return_
BOOLEAN
NkContainsW(
            _In_ const WCHAR* Str1,
            _In_ const WCHAR* Str2,
            _In_ BOOLEAN CaseInSensitive
            );
            
_Check_return_
BOOLEAN
NkContainsUnicodeString(
                        _In_ PCUNICODE_STRING Str1,
                        _In_ PCUNICODE_STRING Str2,
                        _In_ BOOLEAN CaseInSensitive
                        );
                        
_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
BOOLEAN
NkStringMatch(
              _In_ UNICODE_STRING Str,
              _In_ UNICODE_STRING Pattern,
              _In_ BOOLEAN CaseInSensitive
              );
              
_IRQL_requires_max_(DISPATCH_LEVEL)
VOID
NkSplitUnicodeString (
                      _In_ const UNICODE_STRING Str,
                      _In_ WCHAR Token,
                      _Out_ PUNICODE_STRING FirstPart,
                      _Out_ PUNICODE_STRING RemainPart
                      );
                      
_IRQL_requires_max_(DISPATCH_LEVEL)
VOID
NkBackSplitUnicodeString (
                          _In_ const UNICODE_STRING Str,
                          _In_ WCHAR Token,
                          _Out_ PUNICODE_STRING FinalPart,
                          _Out_ PUNICODE_STRING RemainPart
                          );
                          
_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkAllocUnicodeString(
                     _Out_ PUNICODE_STRING String,
                     _In_ PCUNICODE_STRING Source,
                     _In_ BOOLEAN PagePool,
                     _In_ ULONG PoolTag
                     );
                     
_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkAllocEmptyUnicodeString(
                          _Out_ PUNICODE_STRING String,
                          _In_ USHORT MaximumLength,
                          _In_ BOOLEAN PagePool,
                          _In_ ULONG PoolTag
                          );
                          
_IRQL_requires_max_(DISPATCH_LEVEL)
VOID
NkGetFileNameFromPath(
                      _In_ const UNICODE_STRING FilePath,
                      _Out_ PUNICODE_STRING FileName
                      );
                      
__forceinline
_Check_return_
CHAR
NkToUpperA(
           _In_ CHAR C
           );

__forceinline
_Check_return_
WCHAR
NkToUpperW(
           _In_ WCHAR C
           );

__forceinline
_Check_return_
CHAR
NkToLowerA(
           _In_ CHAR C
           );

__forceinline
_Check_return_
WCHAR
NkToLowerW(
           _In_ WCHAR C
           );

__forceinline
_Check_return_
LONG
NkCompareA(
           _In_ CHAR c1,
           _In_ CHAR c2,
           _In_ BOOLEAN CaseInSensitive
           );

__forceinline
_Check_return_
BOOLEAN
NkEqualA(
         _In_ CHAR c1,
         _In_ CHAR c2,
         _In_ BOOLEAN CaseInSensitive
         );

__forceinline
_Check_return_
LONG
NkCompareW(
           _In_ WCHAR c1,
           _In_ WCHAR c2,
           _In_ BOOLEAN CaseInSensitive
           );

__forceinline
_Check_return_
BOOLEAN
NkEqualW(
         _In_ WCHAR c1,
         _In_ WCHAR c2,
         _In_ BOOLEAN CaseInSensitive
         );

__forceinline
_Check_return_
BOOLEAN
NkIsAlphabetA(
              _In_ CHAR C
              );

__forceinline
_Check_return_
BOOLEAN
NkIsAlphabetW(
              _In_ WCHAR C
              );

__forceinline
_Check_return_
BOOLEAN
NkIsNumberA(
            _In_ CHAR C
            );

__forceinline
_Check_return_
BOOLEAN
NkIsNumberW(
            _In_ WCHAR C
            );

__forceinline
_Check_return_
BOOLEAN
NkIsHexA(
         _In_ CHAR C
         );

__forceinline
_Check_return_
BOOLEAN
NkIsHexW(
         _In_ WCHAR C
         );

__forceinline
_Check_return_
BOOLEAN
NkIsDosPathA(
             _In_ const CHAR* Path
             );

__forceinline
_Check_return_
BOOLEAN
NkIsDosPathW(
             _In_ const WCHAR* Path
             );

__forceinline
_Check_return_
BOOLEAN
NkIsUnicodeDosPath(
                   _In_ PCUNICODE_STRING Path
                   );

__forceinline
_Check_return_
BOOLEAN
NkIsGlobalDosPathA(
                   _In_ const CHAR* Path
                   );

__forceinline
_Check_return_
BOOLEAN
NkIsGlobalDosPathW(
                   _In_ const WCHAR* Path
                   );

__forceinline
_Check_return_
BOOLEAN
NkIsUnicodeGlobalDosPath(
                         _In_ PCUNICODE_STRING Path
                         );

__forceinline
VOID
NkParseFilePath(
                _In_ UNICODE_STRING FilePath,
                _Out_ PUNICODE_STRING ParentDir,
                _Out_ PUNICODE_STRING FileName
                );

__forceinline
VOID
NkParseFileName(
                _In_ UNICODE_STRING FileName,
                _Out_ PUNICODE_STRING NamePart,
                _Out_ PUNICODE_STRING Extension
                );

__forceinline
_Check_return_
BOOLEAN
NkIsEmptyUnicodeString(
                       _In_ PCUNICODE_STRING Str
                       );

__forceinline
VOID
NkFreeUnicodeString(
                    _In_ PUNICODE_STRING Str
                    );
```

#### 2.1.8 Time

APIs to get/convert/serialize time.

```c
#include <nkdf/basic/time.h>

_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkTimeToStringA(
                _In_ LONGLONG Time,
                _Out_writes_bytes_(TIME_FORMATA_SIZE) CHAR* TimeString
                );
                
_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
LONGLONG
NkTimeGetSystemTime(
                    );

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
LONGLONG
NkTimeGetLocalTime(
                   );

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
LONGLONG
NkTimeAdjustTime(
                 _In_ LONGLONG SystemTime,
                 _In_ LONG N
                 );

NkTimeSecondToPicoSecond;
NkTimeSecondToNanoSecond;
NkTimeSecondToMicroSecond;
NkTimeSecondToMilliSecond;
NkTimePicoSecondToSecond;
NkTimeNanoSecondToSecond;
NkTimeMicroSecondToSecond;
NkTimeMilliSecondToSecond;
NkTimeMilliSecondToNanoSecond;
NkTimeMicroSecondToNanoSecond;
NkTimeNanoSecondToMilliSecond;
NkTimeNanoSecondToMicroSecond;
NkTimeSystemTimeToNanoSecond;
NkTimeSystemTimeToMicroSecond;
NkTimeSystemTimeToMilliSecond;
NkTimeNanoSecondToSystemTime;
NkTimeMicroSecondToSystemTime;
NkTimeMilliSecondToSystemTime;

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
LONGLONG
NkTimeGetSystemLiveTime(
                        );

_IRQL_requires_max_(APC_LEVEL)
VOID
NkTimeSleep(
            _In_ LONG MilliSeconds
            );
```

### 2.2 Crypto

NKDF provide wrapped APIs for CNG.

#### 2.2.1 Common

Initialize/Cleanup Crypto Library.

```c
#include <nkdf/crypto/provider.h>

_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkCryptoInit(
             );

_IRQL_requires_(PASSIVE_LEVEL)
VOID
NkCryptoCleanup(
                );

_Check_return_
BOOLEAN
NkCryptoInitialized();

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
const NXALGOBJECT*
NkCryptoGetProvider(
                    _In_ NKCRYPTO_PROV_ID Id
                    );
```

#### 2.2.2 AES

AES encrypt/decrypt APIs.

```c
#include <nkdf/crypto/aes.h>

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkAesGenerateKey(
    _Out_writes_bytes_(KeySize) UCHAR* Key,
    _In_ ULONG KeySize
    );

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkAesImportKey(
    _In_reads_bytes_(KeySize) const UCHAR* Key,
    _In_ ULONG KeySize,
    _Out_ PHANDLE KeyHandle
    );

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkAesExportKey(
    _In_ HANDLE KeyHandle,
    _Out_writes_bytes_(*KeySize) UCHAR* Key,
    _Inout_ ULONG* KeySize
    );

VOID
NkAesDestroyKey(
    _In_ HANDLE KeyHandle
    );

NTSTATUS
NkAesGetKeySize(
    _In_ HANDLE KeyHandle,
    _Out_ PULONG KeySize
    );

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkAesEncrypt(
    _In_ HANDLE KeyHandle,
    _In_reads_bytes_opt_(16) const UCHAR* IvecSeed,
    _In_ ULONGLONG BlockOffset,
    _In_ ULONG BlockSize,
    _Inout_updates_(Size) PVOID Data,
    _In_ ULONG Size
    );

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkAesDecrypt(
    _In_ HANDLE KeyHandle,
    _In_reads_bytes_opt_(16) const UCHAR* IvecSeed,
    _In_ ULONGLONG BlockOffset,
    _In_ ULONG BlockSize,
    _Inout_updates_(Size) PVOID Data,
    _In_ ULONG Size
    );

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkAesEncrypt2(
    _In_reads_bytes_(KeySize) const UCHAR* Key,
    _In_ ULONG KeySize,
    _In_reads_bytes_opt_(16) const UCHAR* IvecSeed,
    _In_ ULONGLONG BlockOffset,
    _In_ ULONG BlockSize,
    _Inout_updates_(Size) PVOID Data,
    _In_ ULONG Size
    );

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkAesDecrypt2(
    _In_reads_bytes_(KeySize) const UCHAR* Key,
    _In_ ULONG KeySize,
    _In_reads_bytes_opt_(16) const UCHAR* IvecSeed,
    _In_ ULONGLONG BlockOffset,
    _In_ ULONG BlockSize,
    _Inout_updates_(Size) PVOID Data,
    _In_ ULONG Size
    );
```
#### 2.2.3 RSA

RSA key management, encrypt/decrypt/sign/verify APIs.

```c
#include <nkdf/crypto/rsa.h>

NTSTATUS
NRsaCreateKey(
    _In_ ULONG BitsLength,
    _Out_ BCRYPT_KEY_HANDLE* ph
    );

NTSTATUS
NRsaLoadKey(
    _In_reads_bytes_(Size) const VOID* Key,
    _In_ ULONG Size,
    _Out_ BCRYPT_KEY_HANDLE* ph,
    _Out_opt_ PBOOLEAN IsPrivateKey
    );

NTSTATUS
NRsaExportPublicKey(
    _In_ BCRYPT_KEY_HANDLE h,
    _Out_writes_bytes_opt_(*Size) VOID* Key,
    _Inout_ PULONG Size
    );

NTSTATUS
NRsaExportPrivateKey(
    _In_ BCRYPT_KEY_HANDLE h,
    _Out_writes_bytes_opt_(*Size) VOID* Key,
    _Inout_ PULONG Size
    );

NTSTATUS
NRsaCloseKey(
    _In_ BCRYPT_KEY_HANDLE h
    );

NTSTATUS
NRsaGetOutputSize(
    _In_ BCRYPT_KEY_HANDLE h,
    _In_ RSAOPERATOR Operator,
    _In_reads_bytes_(DataSize) const VOID* Data,
    _In_ ULONG DataSize,
    _Out_ PULONG OutputSize
    );

NTSTATUS
NRsaEncrypt(
    _In_ BCRYPT_KEY_HANDLE h,
    _In_reads_bytes_(DataSize) const VOID* Data,
    _In_ ULONG DataSize,
    _Out_writes_bytes_(OutSize) VOID* CipherData,
    _Inout_ PULONG OutSize
    );

NTSTATUS
NRsaDecrypt(
    _In_ BCRYPT_KEY_HANDLE h,
    _In_reads_bytes_(DataSize) const VOID* CipherData,
    _In_ ULONG DataSize,
    _Out_writes_bytes_(OutSize) VOID* Data,
    _Inout_ PULONG OutSize
    );


NTSTATUS
NRsaSign(
    _In_ BCRYPT_KEY_HANDLE h,
    _In_reads_bytes_(DataSize) const VOID* Data,
    _In_ ULONG DataSize,
    _Out_writes_bytes_(OutSize) VOID* Signature,
    _Inout_ PULONG OutSize
    );

NTSTATUS
NxRsaVerify(
    _In_ BCRYPT_KEY_HANDLE h,
    _In_reads_bytes_(DataSize) const VOID* Data,
    _In_ ULONG DataSize,
    _In_reads_bytes_(SignatureSize) const VOID* Signature,
    _In_ ULONG SignatureSize
    );
```

#### 2.2.4 CRC

APIs to generate CRC checksum.

```c
#include <nkdf/crypto/crc.h>

_Check_return_
ULONG
NkCrc32(
        _In_ ULONG InitialCrc,
        _In_reads_bytes_(Size) const VOID* Data,
        _In_ ULONG Size
        );

_Check_return_
ULONGLONG
NkCrc64(
        _In_ ULONGLONG InitialCrc,
        _In_reads_bytes_(Size) const VOID* Data,
        _In_ ULONG Size
        );
```

#### 2.2.5 MD5

APIs to generate MD5 hash.

```c
#include <nkdf/crypto/crc.h>


_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkMd5Hash(
          _In_reads_bytes_(Size) const VOID* Data,
          _In_ ULONG Size,
          _Out_writes_bytes_(16) PVOID Hash
          );

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkHmacMd5Hash(
              _In_reads_bytes_(Size) const VOID* Data,
              _In_ ULONG Size,
              _In_reads_bytes_(Size) const VOID* Key,
              _In_ ULONG KeySize,
              _Out_writes_bytes_(16) PVOID Hash
              );
```

#### 2.2.6 SHA

APIs to generate SHA1/SHA256 hash.

```c
#include <nkdf/crypto/crc.h>

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkSha1Hash(
           _In_reads_bytes_(Size) const VOID* Data,
           _In_ ULONG Size,
           _Out_writes_bytes_(20) PVOID Hash
           );

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkHmacSha1Hash(
               _In_reads_bytes_(Size) const VOID* Data,
               _In_ ULONG Size,
               _In_reads_bytes_(KeySize) const VOID* Key,
               _In_ ULONG KeySize,
               _Out_writes_bytes_(20) PVOID Hash
               );

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkSha256Hash(
             _In_reads_bytes_(Size) const VOID* Data,
             _In_ ULONG Size,
             _Out_writes_bytes_(32) PVOID Hash
             );

_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkHmacSha256Hash(
                 _In_reads_bytes_(Size) const VOID* Data,
                 _In_ ULONG Size,
                 _In_reads_bytes_(KeySize) const VOID* Key,
                 _In_ ULONG KeySize,
                 _Out_writes_bytes_(32) PVOID Hash
                 );
```

### 2.3 File System

File system related APIs.

#### 2.3.1 Disk

APIs to get disk information

```c
#include <nkdf/fs/disk.h>

_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkGetDiskGeometry (
                   _In_ PDEVICE_OBJECT DiskDeviceObject,
                   _Out_writes_bytes_(sizeof(DISK_GEOMETRY)) PDISK_GEOMETRY DiskGeometry
                   );
                   
_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkGetDriveLetter(
                 _In_ PDEVICE_OBJECT DiskDeviceObject,
                 _Out_writes_bytes_(sizeof(WCHAR)) PWCHAR DriveLetter
                 );
```

#### 2.3.2 Volume

APIs to get volume information

```c
#include <nkdf/fs/vol.h>

_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkFltGetVolumeProperties(
                         _In_ PFLT_VOLUME Volume,
                         _Out_ PFLT_VOLUME_PROPERTIES* PropPtr
                         );
                         
_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkFltGetVolumePropertiesEx(
                           _In_ PFLT_FILTER Filter,
                           _In_ PCUNICODE_STRING VolumeName,
                           _Out_ PFLT_VOLUME_PROPERTIES* PropPtr
                           );
                           
_Check_return_
_IRQL_requires_max_(PASSIVE_LEVEL)
NTSTATUS
NkFltGetVolumeDiskInfo(
                       _In_ PFLT_VOLUME Volume,
                       _Out_ PWCHAR DriveLetter,
                       _Out_writes_bytes_(sizeof(DISK_GEOMETRY)) PDISK_GEOMETRY DiskGeometry
                       );
                       
_Check_return_
_IRQL_requires_max_(PASSIVE_LEVEL)
NTSTATUS
NkFltGetVolumeDiskInfoEx(
                         _In_ PFLT_FILTER Filter,
                         _In_ PCUNICODE_STRING VolumeName,
                         _Out_ PWCHAR DriveLetter,
                         _Out_writes_bytes_(sizeof(DISK_GEOMETRY)) PDISK_GEOMETRY DiskGeometry
                         );
                         
_Check_return_
_IRQL_requires_max_(PASSIVE_LEVEL)
NTSTATUS
NkFltGetVolumeDriveLetter(
                          _In_ PFLT_VOLUME Volume,
                          _Out_ PWCHAR DriveLetter
                          );
                          
_Check_return_
_IRQL_requires_max_(PASSIVE_LEVEL)
NTSTATUS
NkFltVolumeNameToDriveLetter(
                             _In_ PFLT_FILTER Filter,
                             _In_ PCUNICODE_STRING VolumeName,
                             _Out_ PWCHAR DriveLetter
                             );
```

#### 2.3.3 Mini-filter Communication

APIs to implement mini-filter communication chanel.

```c
#include <nkdf/fs/fltconn.h>

typedef NTSTATUS
(*NKFLT_CONN_MSGDISPATCH_ROUTINE)(
                                  _In_opt_ PVOID InputBuffer,
                                  _In_ ULONG InputBufferLength,
                                  _Out_opt_ PVOID OutputBuffer,
                                  _In_ ULONG OutputBufferLength,
                                  _Out_ PULONG ReturnOutputBufferLength
                                  );

_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkFltConnStart(
               _In_ PFLT_FILTER Filter,
               _In_ PCUNICODE_STRING Name,
               _In_ ULONG ServerConn,
               _In_ ULONG ClientConn,
               _In_opt_ NKFLT_CONN_MSGDISPATCH_ROUTINE MsgDispatchRoutine
               );
               
_IRQL_requires_(PASSIVE_LEVEL)
VOID
NkFltConnShutdown(
                  );
                  
_Check_return_
NTSTATUS
NkFltConnQuery(
               _In_ PVOID SenderBuffer,
               _In_ ULONG SenderBufferLength,
               _Out_opt_ PVOID ReplyBuffer,
               _Inout_ PULONG ReplyLength,
               _In_opt_ PLARGE_INTEGER Timeout
               );
```

#### 2.3.4 Mini-filter CSQ

APIs to implement mini-filter safe-cancel-queue.

```c
#include <nkdf/fs/fltcsq.h>

_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkFltCsqCreate(
               _In_ PFLT_FILTER Filter,
               _In_ PFLT_INSTANCE Instance,
               _In_ PNKFLTCSQ_PRE_CALLBACK CsqPreCallback,
               _In_ PNKFLTCSQ_POST_CALLBACK CsqPostCallback,
               _Out_ PNKFLTCSQ* Csq
               );
               
_IRQL_requires_max_(APC_LEVEL)
VOID
NkFltCsqDestroy(
                _In_ PNKFLTCSQ Csq
                );
                
_Check_return_
_IRQL_requires_max_(DISPATCH_LEVEL)
NTSTATUS
NkFltCsqInsert(
               _Inout_ PFLT_CALLBACK_DATA Data,
               _In_ PNKFLTCSQ Csq,
               _In_ BOOLEAN IsPreCb,
               _In_opt_ PVOID UserContext,
               _In_ ULONG UserContextSize
               );               
```

#### 2.3.5 Mini-filter Operation

APIs to for mini-filter file operation.

```c
#include <nkdf/fs/fltop.h>
```

#### 2.3.6 Fs Operation

APIs to for legacy file operation.

```c
#include <nkdf/fs/fsop.h>
```

### 2.4 Log

Kernel-mode log utility.

#### 2.4.1 Definition

Define log level and other const values.

```c
#include <nkdf/log/logdef.h>

enum _LOGLEVEL {
    LogCritical = 0,
    LogError,
    LogWarning,
    LogInfo,
    LogDebug,
    LogDump,  
    LogMax
};
```

#### 2.4.2 File Log

APIs implement file log.

```c
#include <nkdf/log/filelog.h>

_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkLogInitialize(
                _In_ LOGLEVEL AcceptedLevel,
                _In_ ULONG RotateCount,
                _In_ ULONG QueueLimit,
                _In_ ULONG FileSizeLimit,
                _In_ PUNICODE_STRING Directory,
                _In_ PUNICODE_STRING Name
                );
                
_IRQL_requires_(PASSIVE_LEVEL)
VOID
NkLogCleanup(
             );

_IRQL_requires_max_(DISPATCH_LEVEL)
VOID
NkLog(
      _In_ LOGLEVEL Level,
      _In_ const CHAR* MsgFmt,
      ...
      );
```

### 2.5 Process

Process related kernel-mode APIs.

#### 2.5.1 Monitor

APIs to start/stop a process monitor.

```c
#include <nkdf/process/monitor.h>


_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkProcessMonitorStart(
                      _In_ ULONG Flags,
                      _In_ ULONG ContextSize,
                      _In_ ULONG ContextTag,
                      _In_opt_ PROCESS_CREATE_CALLBACK CreateCallback,
                      _In_opt_ PROCESS_DESTROY_CALLBACK DestroyCallback
                      );

_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkProcessMonitorStop(
                     );

_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkFindProcessInformation(
                         _In_ HANDLE ProcessId,
                         _Out_writes_bytes_opt_(sizeof(PROCESS_INFOW)) PPROCESS_INFOW Info
                         );

_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkGetProcessFlags(
                  _In_ HANDLE ProcessId,
                  _Out_ PULONG Flags
                  );

_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkSetProcessFlags(
                  _In_ HANDLE ProcessId,
                  _In_ ULONG NewFlags
                  );


_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkRemoveProcessFlags(
                     _In_ HANDLE ProcessId,
                     _In_ ULONG Flags
                     );

_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkAcquireProcessContext(
                        _In_ HANDLE ProcessId,
                        _In_ BOOLEAN Exclusive,
                        _Out_ PNKPROCESS_CONTEXT* PtrContext
                        );

_IRQL_requires_max_(DISPATCH_LEVEL)
VOID
NkReleaseProcessContext(
                        _In_ PNKPROCESS_CONTEXT ProcContext
                        );
```

#### 2.5.2 Utility

Utility APIs related with process/thread.

```c
#include <nkdf/process/utility.h>

_Check_return_
PEPROCESS
NkGetThreadProcess(
                   _In_ PETHREAD Thread
                   );

_Check_return_
HANDLE
NkGetThreadId(
              _In_ PETHREAD Thread
              );

_Check_return_
HANDLE
NkGetThreadProcessId(
                     _In_ PETHREAD Thread
                     );

_Check_return_
NTSTATUS
NkProcessHandleToId(
                    _In_ HANDLE ProcessHandle,
                    _Out_ PHANDLE ProcessId
                    );

_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkGetProcessImageName(
                      _In_ PEPROCESS Process,
                      _Out_writes_z_(MAX_IMAGE_NAME) PWCH ImageName
                      );

_Check_return_
_IRQL_requires_max_(APC_LEVEL)
NTSTATUS
NkGetProcessImageNameById(
                          _In_ HANDLE ProcessId,
                          _Out_writes_z_(MAX_IMAGE_NAME) PWCH ImageName
                          );

_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkGetProcessImageNameByHandle(
                              _In_ HANDLE Handle,
                              _Out_writes_z_(MAX_IMAGE_NAME) PWCH ImageName
                              );

_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkGetProcessInfo(
                 _In_ HANDLE ProcessId,
                 _Out_ PULONG SessionId,
                 _Out_writes_z_(MAX_USER_NAME) PWCH UserName,
                 _Out_writes_z_(MAX_SID_NAME) PWCH UserSid,
                 _Out_writes_z_(MAX_IMAGE_NAME) PWCH ImageName
                 );

_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkOpenProcess(
              _In_ HANDLE ProcessId,
              _In_ ACCESS_MASK DesiredAccess,
              _Out_ PHANDLE ProcessHandle
              );

_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkTerminateProcess(
                   _In_ HANDLE ProcessId,
                   _In_ NTSTATUS ExitStatus
                   );

_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkIsNativeProcess(
                  _In_ HANDLE ProcessHandle,
                  _Out_ PBOOLEAN Result
                  );

_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
BOOLEAN
NkIs64bitProcess(
                 _In_ HANDLE ProcessHandle
                 );

_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkIsSystemProcess(
                  _In_ HANDLE ProcessHandle,
                  _Out_ PBOOLEAN Result
                  );


_Check_return_
_IRQL_requires_(PASSIVE_LEVEL)
NTSTATUS
NkGetProcessEntryPoint(
                       _In_ HANDLE ProcessHandle,
                       _Out_ PVOID* EntryPoint
                       );
```