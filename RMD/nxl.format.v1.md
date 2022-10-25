# **NXL File Format**

# History

| *Version* |  *Author*     |   *Date*      |   *Comments*              |
|-----------|---------------|---------------|---------------------------|
|   1.0     |   Gavin Ye    |   01/23/2014  |   1.0 Finalized           |
|   1.0     |   Gavin Ye    |   01/06/2014  |   1.0 Draft               |
|           |               |               |   *Initial*               |

# Version 1.0

## Overview

|  *Fields*     |  *Offset*   |   *Size*    |   *Comments*              |
|---------------|-------------|-------------|---------------------------|
|   Magic       |   0x0000    |   0x0090    |   NXL magic header        |
|   Basic       |   0x0090    |   0x0020    |   Basic information header|
|   Crypto      |   0x00B0    |   0x02B8    |   Crypto header           |
| Section Table |   0x0368    |   0x0498    |   Section table           |
| Sections Data |   0x0800    |   0x????    |   Section data (Size is decided by section table)           |
|   Content     |   0x????    |   0x????    |   Encrypted document content. Offset is decided by PointerOfContent field in `Basic` header |
|               |             |             |    *NXL Format Overview*  |


## Magic Header

|  *Fields*     |  *Offset*   |   *Size*    |   *Comments*              |
|---------------|-------------|-------------|---------------------------|
|   Signature   |   0x0000    |   0x0008    |   A magic code to indentify NXL header `"NXLFMT!"` |
|   Message     |   0x0008    |   0x0088    |   UTF-16 readable message |
|               |             |             |   *(Total Size: 144 Bytes)*     |


## Basic Header

|  *Fields*         |  *Offset*   |   *Size*    |   *Comments*              |
|-------------------|-------------|-------------|---------------------------|
|  Thumbprint       |   0x0000    |   0x0010    |   A GUID uniquely indentify this document |
|  Version          |   0x0010    |   0x0004    |   NXL Format version. High WORD is major version, low WORD is minor version |
|  Flags            |   0x0014    |   0x0004    |   `File flags` of this NXL document |
|  Alignment        |   0x0018    |   0x0004    |   Data alignment of this NXL document |
|  PointerOfContent |   0x001C    |   0x0004    |   The offset to content |
|                   |             |             |   *(Total Size: 32 Bytes)*    |

> ** *File Flags* **
> 
> - NXL_FLAG_NONE (0x00000000)
>


## Crypto Header

|  *Fields*         |  *Offset*   |   *Size*    |   *Comments*              |
|-------------------|-------------|-------------|---------------------------|
|  Algorithm        |   0x0000    |   0x0004    |   The `algorithm` used to encrypt content |
|  CipherBlockSize  |   0x0004    |   0x0004    |   The cipher block size, default is 512 bytes |
|  Primary Key Blob |   0x0008    |   0x0140    |   Mandatory. The primary key BLOB. (NXL_KEY_BLOB) |
| Recovery Key Blob |   0x0148    |   0x0140    |   Optional. The BLOB of recovery key. (NXL_KEY_BLOB) |
|  Content Length   |   0x0288    |   0x0008    |   The real size of content |
| Allocation Length |   0x0290    |   0x0008    |   Unused. Must be zero. |
|  Content Padding  |   0x0298    |   0x0020    |   Unused. Must be zero. |
|                   |             |             |   *(Total Size: 696 Bytes)*    |

*NXL_KEK_BLOB*

|  *Fields*     |  *Offset*   |   *Size*    |   *Comments*              |
|---------------|-------------|-------------|---------------------------|
| KEK Algorithm |   0x0000    |   0x0002    |   KEK `algorithm`  |
| KEK Id Size   |   0x0002    |   0x0002    |   Size of KEK id |
| KEK Id        |   0x0004    |   0x003C    |   KEK id |
| Encrypted CEK |   0x0040    |   0x0140    |   CEK encrypted by KEK |
|               |             |             |   *(Total Size: 384 Bytes)*    |

> ** *Algorithm* **
> 
> - AES 128 (0x01)
> - AES 256 (0x02)
> - RSA 1024 (0x03)
> - RSA 2048 (0x04)
>

```
NOTE:
    KEK is not stored in NXL file. To decrypt CEK, application must use KEK id to get KEK from server or service.
```

## Section Header

|  *Fields*         |  *Offset*   |   *Size*    |   *Comments*              |
|-------------------|-------------|-------------|---------------------------|
|  Checksum         |   0x0000    |   0x0010    |   Checksum of whole table. Encrypted with first 16 bytes CEK (AES 128) |
|  Count            |   0x0010    |   0x0004    |   Number of sections |
|  Reserved         |   0x0014    |   0x0004    |   Reserved. Must be zero |
|  Section 0        |   0x0018    |   0x0010    |   Section 0: ".Attrs" (SECTION_RECORD) |
|  Section 1        |   0x0028    |   0x0010    |   Section 1: ".Rights" (SECTION_RECORD) |
|  Section 2        |   0x0038    |   0x0010    |   Section 2: ".Tags" (SECTION_RECORD) |
|  ...              |   ...       |   ...       |   Other sections  |
|  Section 71       |   0x0488    |   0x0010    |   Last section. Support up to 72 sections. |
|                   |             |             |   *(Total Size: 1176 Bytes)*    |

> - SECTION_RECORD
> 
> |  *Fields*     |  *Offset*   |   *Size*    |   *Comments*              |
> |---------------|-------------|-------------|---------------------------|
> |  Name         |   0x0000    |   0x0008    |   Name of section (UTF-8, up to 7 characters)  |
> |  Size         |   0x0008    |   0x0004    |   Size of section data area |
> |  Checksum     |   0x000C    |   0x0004    |   CRC32 checksum of section data |
> |               |             |             |   *(Total Size: 16 Bytes)*    |
> 
> 
> ** *Default Sections* **
>
> There are 3 default sections: ".FileInfo", ".Policy" and ".FileTag".
>    
> - Section ".FileInfo"
> 
> This section stores all the internal attributes of this NXL document.
> 
>> *Available Attributes*
>> 
>> |  *Attribute*  |  *Type*     |   *Comments*              |
>> |---------------|-------------|---------------------------|
>> |  .FileExt     |   String    |   Original file extension  |
>> |               |             |   *UTF-16, Multi-strings end with '\0\0'*    |
> 
> 
> - Section ".Policy"
> 
> This section stores descitionary policies in UTF-16 multi-strings (end with '\0\0').
> 
> - Section ".FileTag"
> 
> This section stores document tags ("name=Value") in UTF-16 multi-strings (end with '\0\0').