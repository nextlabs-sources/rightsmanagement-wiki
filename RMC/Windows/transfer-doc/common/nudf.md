
# **NUDF (NextLabs User-Mode Development Foundation)** #

    By Gavin Ye
    8/8/2017


NUDF (NextLabs User Mode Development Foundation) is a library which provides the most common used classes and APIs to reduce developer work and unify common features.


## Table of Content

- [Table of Content](#table-of-content)
- [1. Basic](#1-basic)
    - [1.1 Static Link to CRT](#11-static-link-to-crt)
    - [1.2 Dynamically Link to CRT](#12-dynamically-link-to-crt)
    - [1.3 Namespace](#13-namespace)
    - [1.4 Repository](#14-repository)
- [2. Reference](#2-reference)
    - [2.1 Error Handling](#21-error-handling)
        - [2.1.1 SEH](#211-seh)
        - [2.1.2 Exception](#212-exception)
    - [2.2 String](#22-string)
        - [2.2.1 String Buffer](#221-string-buffer)
        - [2.2.2 String Formatter](#222-string-formatter)
        - [2.2.3 String Utilities](#223-string-utilities)
    - [2.3 Conversion](#23-conversion)
        - [2.3.1 String Conversion](#231-string-conversion)
        - [2.3.2 String and Buffer](#232-string-and-buffer)
        - [2.3.3 Endian](#233-endian)
        - [2.3.4 URL Encoding](#234-url-encoding)
        - [2.3.5 Base64 Encoding](#235-base64-encoding)
    - [2.4 Crypto](#24-crypto)
        - [2.4.1 Common](#241-common)
        - [2.4.2 AES](#242-aes)
        - [2.4.3 RSA](#243-rsa)
        - [2.4.4 Diffie Hellman](#244-diffie-hellman)
        - [2.4.5 MD5](#245-md5)
        - [2.4.6 SHA](#246-sha)
        - [2.4.7 Certificate](#247-certificate)
    - [2.5 Bits Map](#25-bits-map)
    - [2.6 Bitmap](#26-bitmap)
    - [2.7 Debug](#27-debug)
        - [2.7.1 APIs and Macros](#271-apis-amd-macros)
        - [2.7.2 Log](#272-log)
    - [2.8 File System](#28-file-system)
        - [2.8.1 Drive](#281-drive)
        - [2.8.2 File Name](#282-file-name)
        - [2.8.3 File Path](#283-file-path)
        - [2.8.4 File Object](#284-file-object)
        - [2.8.5 File Checksum](#285-file-checksum)
    - [2.9 Service](#29-service)
        - [2.9.1 Service Information](#291-service-information)
        - [2.9.2 Service Status](#292-service-status)
        - [2.9.3 Service Instance](#293-service-instance)
        - [2.9.4 Service Control](#294-service-control)
    - [2.10 Security](#210-security)
    - [2.11 Time](#211-time)
    - [2.12 Windows Utilities](#212-windows-utilities)
        - [2.12.1 Windows Info](#2121-windows-info)
        - [2.12.2 Language](#2122-language)
        - [2.12.3 Installation](#2123-installation)
        - [2.12.4 Hardware](#2124-hardware)
        - [2.12.5 File Version](#2125-file-version)
        - [2.12.6 File Association](#2126-file-association)
        - [2.12.7 PE File](#2127-pe-file)
        - [2.12.8 Registry](#2128-registry)
        - [2.12.9 Security](#2129-security)
        - [2.12.10 Host and User](#2129-host-and-user)
    - [2.13 Http](#213-http)
    - [2.14 Json](#214-json)
    - [2.15 Compress](#215-compress)
        - [2.15.1 File Zipping](#2151-file-zipping)
        - [2.15.2 Compress and Decompress](#2152-compress-and-decompress)


## 1. Basic

### 1.1 Static Link to CRT

Output libraries are built using "/MT" or "/MTd" to static link to CRT.
nxrmcore.dll link to these libraries.

### 1.2 Dynamically Link to CRT

Output libraries are built using "/MD" or "/MDd" to dynamically link to CRT.
This is default setting and most of RMD application link to these libraries.

### 1.3 Namespace

All the code is under namespace `"NX"`.
Callers may want to use following code in their source file:

```cpp
using namespace NX;
```

### 1.4 Repository

NUDF is in repository [RightsManagementClient](https://bitbucket.org/nxtlbs-devops/rightsmanagementclient/overview).

User can use following command to clone the repository.

```bash
git clone git@bitbucket.org:nxtlbs-devops/rightsmanagementclient.git
```


## 2. Reference

### 2.1 Error Handling

NUDF supports both C++ exception and SEH.

#### 2.1.1 SEH

Following class allow to setup exception translater which translate SEH to C++ exception, raise an exception or retrieve exception information.

```cpp
class structured_exception;
```

#### 2.1.2 Exception

NUDF defines its own C++ exception class which provides more information than std::execption.

```cpp
class exception : public std::exception;
```

### 2.2 String

Provide most common string APIs

```cpp
#include <nudf/string.hpp>
```

#### 2.2.1 String Buffer

Convert std::basic_string<T> to C style T* pointer.

```cpp
template <typename T>
class string_buffer;
```

#### 2.2.2 String Formatter

```cpp
std::string string_formater(const char* format, ...);
std::wstring string_formater(const wchar_t* format, ...);
std::string safe_buffer_to_string(const unsigned char* buf, size_t size);
```

#### 2.2.3 String Utilities

```cpp
template<typename CharType> bool is_digit(CharType ch);
template<typename CharType> bool is_hex(CharType ch);
template<typename CharType> unsigned char hex_to_uchar(CharType c);
template<typename CharType> int hex_to_int(CharType c);
template<typename CharType> bool is_alphabet(CharType ch);
inline bool is_space(int c);
template<typename CharType> bool iequal(CharType c1, CharType c2);
template<typename CharType> bool iequal(const CharType* s1, const CharType* s2);
template<typename CharType> bool iequal(const std::basic_string<CharType>& s1,
                                        const std::basic_string<CharType>& s2);
template<typename CharType> int icompare(CharType c1, CharType c2);
template<typename CharType> int icompare(const CharType* s1, const CharType* s2);
template<typename CharType> int icompare(const std::basic_string<CharType>& s1,
                                         const std::basic_string<CharType>& s2);
template<typename CharType>
std::vector<unsigned char> hex_string_to_buffer(const std::basic_string<CharType>& s);
template<class T>
void Split(const std::basic_string<T>& s,
           T c,
           std::vector<std::basic_string<T>>& list);
template<class T>
void Split(const std::basic_string<T>& s,
           const std::basic_string<T>& c,
           std::vector<std::basic_string<T>>& list);
template<class T>
void Split(const std::basic_string<T>& s, T c, std::vector<int>& list);
template<class T>
void Split(const std::basic_string<T>& s, T c, std::vector<__int64>& list);
template<class T>
void Split(const std::basic_string<T>& s, T c, std::vector<unsigned int>& list);
template<class T>
void Split(const std::basic_string<T>& s, T c, std::vector<unsigned __int64>& list);
template <typename CharType>
int Replaceall_string(_Inout_ std::basic_string<CharType> & sourcestr, _In_ const std::basic_string<CharType> replacestr, _In_ const std::basic_string<CharType> newstr);
```

### 2.3 Conversion

Provide convert/encoding methods.

```cpp
#include <nudf/conversion.hpp>
```

#### 2.3.1 String Conversion

Provide method to convert string to/from other format. For example,

- UTF8, UTF16 and ANSI
- Number to/from string
- Lower or upper
- To fixed width

#### 2.3.2 String and Buffer

Convert strings to/from double zero ended buffer.

```cpp
template <typename CharType>
std::vector<std::basic_string<CharType>>
buffer_to_strings(const std::vector<CharType>& buf);

template <typename CharType>
std::vector<CharType>
strings_to_buffer(const std::vector<std::basic_string<CharType>>& strings);

template <typename CharType>
std::vector<CharType>
pair_to_buffer(_In_ const std::vector<std::pair<std::basic_string<CharType>,
               std::basic_string<CharType>>> &pairs);

template <typename CharType>
std::vector<std::pair<std::basic_string<CharType>, std::basic_string<CharType>>>
buffer_to_pair(_In_ const std::vector<CharType> buf);

template <typename CharType>
std::vector<std::pair<std::basic_string<CharType>, std::basic_string<CharType>>>
buffer_to_pair(_In_ const CharType * pbuf);
```

#### 2.3.3 Endian

Convert between big endian and little endian.

```cpp
unsigned short convert_endian16(unsigned short u);
unsigned long convert_endian32(unsigned long u);
unsigned __int64 convert_endian64(unsigned __int64 u);
```

#### 2.3.4 URL Encoding

URL encoding/decoding.

```cpp
std::wstring x_www_form_urlencode(const std::wstring& raw_content);
std::wstring x_www_form_urldecode(const std::wstring& encoded_content);
```

#### 2.3.5 Base64 Encoding

Base64 encoding/decoding.

```cpp
std::wstring to_base64(const std::vector<unsigned char>& data);
std::wstring to_base64(const unsigned char* data, unsigned long size);
std::vector<unsigned char> from_base64(const std::string& base64);
std::vector<unsigned char> from_base64(const std::wstring& base64);
```

### 2.4 Crypto

#### 2.4.1 Common

Crypto library support following algorithms:

- AES
- RSA
- Diffie-Hellman
- MD5
- SHA

```cpp
#include <nudf/crypto.hpp>
using namespace NX::crypto;
```

#### 2.4.2 AES

```cpp
class aes_key : public basic_key;

bool aes_encrypt(_In_ const aes_key& key,
                 _In_reads_bytes_(in_size) const unsigned char* in_buf,
                 _In_ unsigned long in_size,
                 _Out_writes_bytes_opt_(*out_size) unsigned char* out_buf,
                 _Inout_ unsigned long* out_size,
                 _In_reads_bytes_opt_(16) const unsigned char* ivec_seed,
                 _In_ const unsigned __int64 block_offset = 0,
                 _In_ unsigned long cipher_block_size = 0);

bool aes_encrypt(_In_ const aes_key& key,
                 _Inout_updates_bytes_(size) unsigned char* buf,
                 _In_ unsigned long size,
                 _In_reads_bytes_opt_(16) const unsigned char* ivec_seed,
                 _In_ const unsigned __int64 block_offset = 0,
                 _In_ unsigned long cipher_block_size = 0);

bool aes_decrypt(_In_ const aes_key& key,
                 _In_reads_bytes_(in_size) const unsigned char* in_buf,
                 _In_ unsigned long in_size,
                 _Out_writes_bytes_opt_(*out_size) unsigned char* out_buf,
                 _Inout_ unsigned long* out_size,
                 _In_reads_bytes_opt_(16) const unsigned char* ivec_seed,
                 _In_ const unsigned __int64 block_offset = 0,
                 _In_ unsigned long cipher_block_size = 0);

bool aes_decrypt(_In_ const aes_key& key,
                 _Inout_updates_bytes_(size) unsigned char* buf,
                 _Inout_ unsigned long size,
                 _In_reads_bytes_opt_(16) const unsigned char* ivec_seed,
                 _In_ const unsigned __int64 block_offset = 0,
                 _In_ unsigned long cipher_block_size = 0);
```

#### 2.4.3 RSA


```cpp
class rsa_key : public basic_key;

bool rsa_encrypt(const rsa_key& key,
    _In_reads_bytes_(in_size) const unsigned char* in_buf,
    _In_ unsigned long in_size,
    _Out_writes_bytes_opt_(*out_size) unsigned char* out_buf,
    _Inout_ unsigned long* out_size);

bool rsa_decrypt(const rsa_key& key,
    _In_reads_bytes_(in_size) const unsigned char* in_buf,
    _In_ unsigned long in_size,
    _Out_writes_bytes_opt_(*out_size) unsigned char* out_buf,
    _Inout_ unsigned long* out_size);


bool rsa_sign(const rsa_key& key,
    _In_reads_bytes_(data_size) const unsigned char* data,
    _In_ unsigned long data_size,
    _Out_writes_bytes_opt_(*signature_size) unsigned char* signature,
    _Inout_ unsigned long* signature_size);

bool rsa_verify(const rsa_key& key,
    _In_reads_bytes_(data_size) const unsigned char* data,
    _In_ unsigned long data_size,
    _In_reads_bytes_(signature_size) const unsigned char* signature,
    _In_ unsigned long signature_size);
```

#### 2.4.4 Diffie Hellman

Read and get factors of DH key.

```cpp
class diffie_hellman_key_blob;
class diffie_hellman_key : public basic_key;
```

#### 2.4.5 MD5

```cpp
bool md5(_In_reads_bytes_(data_size) const unsigned char* data,
    _In_ unsigned long data_size,
    _Out_writes_bytes_(16) unsigned char* out_buf);

bool hmac_md5(_In_reads_bytes_(data_size) const unsigned char* data,
    _In_ unsigned long data_size,
    _In_reads_bytes_(key_size) const unsigned char* key,
    _In_ unsigned long key_size,
    _Out_writes_bytes_(16) unsigned char* out_buf);
```

#### 2.4.6 SHA

```cpp
bool sha1(_In_reads_bytes_(data_size) const unsigned char* data,
    _In_ unsigned long data_size,
    _Out_writes_bytes_(20) unsigned char* out_buf);

bool hmac_sha1(_In_reads_bytes_(data_size) const unsigned char* data,
    _In_ unsigned long data_size,
    _In_reads_bytes_(key_size) const unsigned char* key,
    _In_ unsigned long key_size,
    _Out_writes_bytes_(20) unsigned char* out_buf);

bool sha256(_In_reads_bytes_(data_size) const unsigned char* data,
    _In_ unsigned long data_size,
    _Out_writes_bytes_(32) unsigned char* out_buf);

bool hmac_sha256(_In_reads_bytes_(data_size) const unsigned char* data,
    _In_ unsigned long data_size,
    _In_reads_bytes_(key_size) const unsigned char* key,
    _In_ unsigned long key_size,
    _Out_writes_bytes_(32) unsigned char* out_buf);
```

#### 2.4.7 Certificate

- Get certificate information
- Find/Create/Delete certificates in store

```cpp
class cert_info;
class cert_context;
class store;
class system_store : public store;
class system_personal_store : public system_store;
class system_trust_root_store : public system_store;
class system_intermediate_store : public system_store;
class system_trustpublisher_store : public system_store;
class system_trustpeople_store : public system_store;
class system_otherpeople_store : public system_store;
class system_thirdparty_root_store : public system_store;
class system_revoked_store : public system_store;
class memory_store : public store;
class pcks12_file_store : public store;
```

### 2.5 Bits Map

A bits map class.

### 2.6 Bitmap

Class/methdos to manipulate bitmap.

### 2.7 Debug

```cpp
#include <nudf/debug.hpp>
#include <nudf/dbglog.hpp>
using namespace NX::dbg;
```

#### 2.7.1 APIs and Macros

- APIs

```cpp
build_error_msg;
build_error_msg2;
translate_win32_error;
translate_winhttp_error;
dump;
```

- MACROs

```cpp
ERROR_MSG(c, m, ...)
NT_STATUS_MSG(status, msg)
NT_STATUS_MSG2(status)

HRESULT_MSG(hr, msg)
HRESULT_MSG2(hr)

WIN32_ERROR_MSG(err, msg)
WIN32_ERROR_MSG2(err)
```

#### 2.7.2 Log

Generate debug log.

```cpp
enum LOGLEVEL;
class log_item;
class file_log;
```

### 2.8 File System

```cpp
#include <nudf/filesys.hpp>
using namespace NX::fs;
```

#### 2.8.1 Drive

```cpp
class drive;
```

#### 2.8.2 File Name

```cpp
class filename;
```

#### 2.8.3 File Path

```cpp
class filepath;
class nt_filepath : public filepath;
class dos_filepath : public filepath;
class module_path : public dos_filepath;
```

#### 2.8.4 File Object

```cpp
class file_object;
```

#### 2.8.5 File Checksum

```cpp
class file_checksum;
class file_checksum_crc32 : public file_checksum;
class file_checksum_sha1 : public file_checksum;
```


### 2.9 Service

NUDF provides several classes which allow user to manage system service, query system service information or create their own service.

```cpp
#include <nudf/service.hpp>
using namespace NX::win;
```

#### 2.9.1 Service Information

```cpp
class service_info;
```

#### 2.9.2 Service Status

```cpp
class service_status : public SERVICE_STATUS;
```

#### 2.9.3 Service Instance

```cpp
class service_instance;
```

#### 2.9.4 Service Control

```cpp
class service_control;
```

### 2.10 Security

NUDF provides several classes to get/set securely.

```cpp
#include <nudf/security.hpp>
using namespace NX::secure;

class smemory;
template<typename CharType>
class basic_sstring : public smemory;
```

### 2.11 Time

NUDF provides several classes to get/set/convert time.

```cpp
#include <nudf/time.hpp>
using namespace NX::time;

class span;
class timezone;
class datetime;
class datetime_rfc1123 : public datetime;
class datetime_special_1 : public datetime;
```

### 2.12 Windows Utilities

NUDF provides several utility classes for Windows.

```cpp
#include <nudf/winutil.hpp>
using namespace NX::win;
```

#### 2.12.1 Windows Info

```cpp
class windows_info;
```

#### 2.12.2 Language

```cpp
class language;
class system_default_language : public language;
class user_default_language : public language;
```

#### 2.12.3 Installation

```cpp
using namespace NX::win::installation;
class software;
std::vector<software> get_installed_software(unsigned long flags = INSTALLED_SOFTWARE_ALL);
std::vector<std::wstring> get_installed_kbs();
```

#### 2.12.4 Hardware

```cpp
using namespace NX::win::hardware;
class processor_information;
class memory_information;
class network_adapter_information;
```

#### 2.12.5 File Version

```cpp
class file_version;
```

#### 2.12.6 File Association

```cpp
class file_association;
```

#### 2.12.7 PE File

```cpp
class pe_file;
```

#### 2.12.8 Registry

```cpp
class reg_key;
template <HKEY root>
class reg_root_key : public reg_key;
```

#### 2.12.9 Security

```cpp
class sid;
class explicit_access : public EXPLICIT_ACCESS;
class security_attribute : public SECURITY_ATTRIBUTES;
bool grant_access(const std::wstring& file, const explicit_access& ea);
bool grant_access(const std::wstring& file, const explicit_access* eas, unsigned long ea_count);
```

#### 2.12.10 Host and User

```cpp
class host;
class user_or_group;
class token;
class process_token : public token;
class session_token : public token;
class guid : public GUID;
class impersonate_object;
class user_dirs;
```

### 2.13 Http

NUDF provides class to for HTTP Client to connect to server, send request and get response.

```cpp
#include <nudf/http_client.hpp>
using namespace NX::http;

class client;
```


### 2.14 Json

A simple JSON parser.

```cpp
#include <nudf/json.hpp>
class json_value;
class json_null;
class json_boolean;
class json_number;
class json_string;
class json_array;
class json_object;
```

### 2.15 Compress

NUDF provides APIs to zip/unzip files, as well as compress/decrompress data.

```cpp
#include <nudf/zip.hpp>
using namespace NX::ZIP;
```

#### 2.15.1 File Zipping

```cpp
bool zip(_In_ const std::wstring& source,
         _In_ const std::wstring& zipfile,
         _In_opt_ LPSECURITY_ATTRIBUTES sa);
bool unzip(_In_ const std::wstring& zipfile,
           _In_ const std::wstring& targetdir);
```

#### 2.15.2 Compress and Decompress

```cpp
std::vector<unsigned char> compress_buffer(const void* data,
                                           unsigned long length);
std::vector<unsigned char> decompress_buffer(const void* compressed_data,
                                             unsigned long length,
                                             unsigned long estimated_decompress_size);

bool compress_to_file(const void* data,
                      unsigned long length,
                      const std::wstring& file,
                      bool replace_existing);
bool decompress_to_file(const void* compressed_data,
                        unsigned long length,
                        unsigned long estimated_decompress_size,
                        const std::wstring& file,
                        bool replace_existing);
```
