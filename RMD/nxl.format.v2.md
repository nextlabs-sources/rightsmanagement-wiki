# **NXL File Format**

# History

| *Version* |  *Author*     |   *Date*      |   *Comments*              |
|-----------|---------------|---------------|---------------------------|
|   2.1     |   Sriram      |   01/31/2019  |   Updated .FileInfo       |
|   2.0     |   Gavin Ye    |   05/27/2016  |   Draft                   |
|   1.0     |   Gavin Ye    |   01/23/2014  |   1.0 Finalized           |
|   1.0     |   Gavin Ye    |   01/06/2014  |   1.0 Draft               |
|           |               |               |   *Initial*               |


# Version 2.0

## Overview

|  *Fields*         |  *Offset*   |   *Size*    |   *Comments*              |
|-------------------|-------------|-------------|---------------------------|
| Signature         |   0x0000    |   0x0100    |   NXL file signature      |
| File Header       |   0x0100    |   0x0128    |   File information header |
| Key Header        |   0x0228    |   0x029C    |   Keys header             |
| Sections Header   |   0x04C4    |   0x0804    |   Section header (list all the section records)           |
| Extended Data     |   0x0CC8    |   0x0310    |   Extended data (Reserved)           |
| Dynamic Header    |   0x0FD8    |   0x0028    |   Encrypted document content. Offset is decided by `Content Offset` field in `File` header |
| Section Data      |   0x1000    |   0x????    |   Section data (Default size is 0x3000 bytes, includes 3 default sections) |
| Content           |   0x????    |   0x????    |   Encrypted content (The offset is "`HeaderSize + SectionDataSize`", default offset is 0x4000)           |
|                   |             |             |    *NXL Format Overview*  |


## Signature

|  *Fields*     |  *Offset*   |   *Size*    |   *Comments*              |
|---------------|-------------|-------------|---------------------------|
|   Magic Code  |   0x0000    |   0x0008    |   A magic code to indentify NXL header `"NXLFMT@"` |
|   Version     |   0x0008    |   0x0004    |   NXL Format version. High WORD is major version, low WORD is minor version |
|   Message     |   0x000C    |   0x00F4    |   UTF-8 message which can be read by user (Up to 244 characters, including last '\0') |
|               |             |             |   *(Total Size: 256 Bytes)*     |


## File Header

|  *Fields*             |  *Offset*   |   *Size*    |   *Comments*              |
|-----------------------|-------------|-------------|---------------------------|
|  DUID                 |   0x0100    |   0x0010    |   A GUID uniquely indentify this document |
|  Flags                |   0x0110    |   0x0004    |   `File flags` of this NXL document |
|  Alignment            |   0x0114    |   0x0004    |   Data alignment of this NXL document |
|  Algorithm            |   0x0118    |   0x0004    |   The `algorithm` used to encrypt content |
|  Cipher Block Size    |   0x011C    |   0x0004    |   The cipher block size, default is 512 bytes |
|  Content Offset       |   0x0120    |   0x0004    |   The offset to content |
|  Owner's Id           |   0x0124    |   0x0100    |   Owner's member id (UTF-8 string, up to 255 bytes) |
|  Extended Data Offset |   0x0224    |   0x0004    |   The offset of extended data |
|                       |             |             |   *(Total Size: 296 Bytes)*    |

> ** *File Flags* **
>
> - NXL_FLAG_NONE (0x00000000)
>
> ** *Algorithm* **
>
> - AES 128 (0x01)
> - AES 256 (0x02)
>

## Key Header

|  *Fields*             |  *Offset*   |   *Size*    |   *Comments*              |
|-----------------------|-------------|-------------|---------------------------|
|  Flags                |   0x0228    |   0x0004    |   Key header flags (See comments) |
|  IV Seed              |   0x022C    |   0x0010    |   Random Ivec Seed (used to generate IV for AES algorithm) |
|  TP CEK               |   0x023C    |   0x0040    |   Token protected CEK. 0 – 31 bytes: CEK encrypted by token. 32 – 63 bytes: HMAC_SHA256(Token, CEK) |
|  RPP CEK              |   0x027C    |   0x0040    |   Recovery password protected CEK. 0 – 31 bytes: CEK encrypted by recovery password. 32 – 63 bytes: HMAC_SHA256(Recovery Password, CEK) |
|  Public Key #1        |   0x02BC    |   0x0100    |   The public key between member and Root CA |
|  Public Key #2        |   0x03BC    |   0x0100    |   The public key between member and iCA |
|  Token Level          |   0x04BC    |   0x0004    |   Token's maintenance level |
|  Extended Data Offset |   0x04C0    |   0x0004    |   The offset of extended data |
|                       |             |             |   *(Total Size: 668 Bytes)*    |

> ** *Key Flags* **
>
> *Highest Byte: Protect Mode*
>
> - Client Token Mode (0x01)
> - Server Token Mode (0x02)
> - Split Token Mode (0x03)
>
> *Lower 3 Bytes: Flags*
> 
> - KF_RECOVERY_KEY_ENABLED (0x000001)
>


## Section Header

|  *Fields*             |  *Offset*   |   *Size*    |   *Comments*              |
|-----------------------|-------------|-------------|---------------------------|
|  Map                  |   0x04C4    |   0x0004    |   A bits map indicating which sections are valid (Header supports up to 32 sections |
|  Record 0             |   0x04C8    |   0x0040    |   SECTION_RECORD: ".FileInfo" (Built-in, UTF-8 JSON string) |
|  Record 1             |   0x0508    |   0x0040    |   SECTION_RECORD: ".FilePolicy" (Built-in, UTF-8 JSON string) |
|  Record 2             |   0x0548    |   0x0040    |   SECTION_RECORD: ".FileTag" (Built-in, UTF-8 JSON string) |
|  Record 3             |   0x0588    |   0x0040    |   SECTION_RECORD: Reserved |
|     ...               |    ...      |   0x0040    |   ... |
|  Record 31            |   0x0C88    |   0x0040    |   SECTION_RECORD: Reserved |
|                       |             |             |   *(Total Size: 2052 Bytes)*    |


> ** *SECTION_RECORD* **
>
> |  *Fields*             |  *Offset*   |   *Size*    |   *Comments*              |
> |-----------------------|-------------|-------------|---------------------------|
> |  Name                 |   0x0000    |   0x0010    |   Section name, null terminated ASCII string, up to 15 characters |
> |  Flags                |   0x0010    |   0x0004    |   Section flags |
> |  Start offset         |   0x0014    |   0x0004    |   Section start offset |
> |  Section size         |   0x0018    |   0x0004    |   Section size |
> |  Original Data size   |   0x001C    |   0x0002    |   Original data size |
> |  Compressed Data size |   0x001E    |   0x0002    |   Compressed data size, only valid if data is compressed (if data is not compressed, it is zero) |
> |  Checksum             |   0x0020    |   0x0020    |   Section data checksum: HMAC_SHA256(Token, Data) |
> |                       |             |             |   *(Total Size: 64 Bytes)*    |
>
> *Section Flags*
> 
> - SECTION_FLAG_ENCRYPTED (0x00000001)     - Data is encrypted by token
> - SECTION_FLAG_COMPRESSED (0x00000002)    - Data is compressed
>

## Extended Data

|  *Fields*             |  *Offset*   |   *Size*    |   *Comments*              |
|-----------------------|-------------|-------------|---------------------------|
|  Reserved             |   0x0CC8    |   0x0310    |   Reserved |
|                       |             |             |   *(Total Size: 784 Bytes)*    |



## Dynamic Header

|  *Fields*             |  *Offset*   |   *Size*    |   *Comments*              |
|-----------------------|-------------|-------------|---------------------------|
|  Header Hash          |   0x0FD8    |   0x0020    |   Hash of fixed header (Signature/FileInfo/Key/Section/Extended) HMAC_SHA256(Token, Header) |
|  Content Length       |   0x0FF8    |   0x0008    |   Length of content |
|                       |             |             |   *(Total Size: 40 Bytes)*    |



## Section Data
`(Default Section Data)`

|  *Sections*   |  *Offset*   |   *Size*    |   *Comments*              |
|---------------|-------------|-------------|---------------------------|
|  .FileInfo    |   0x1000    |   0x1000    |   File information, UTF-8 JSON string |
|  .FilePolicy  |   0x2000    |   0x1000    |   Descrationary policy, UTF-8 string |
|  .FileTag     |   0x3000    |   0x1000    |   File tags, UTF-8 JSON string |
|               |             |             |   *(Total Size: 0x3000 Bytes)*    |


### FileInfo Json Format

All the information are stored in a JSON object. For example:

```json
{
   "fileExtension":".docx",
   "fileName":"MyDocument.docx",
   "modifiedBy":"m1a9dee2d-51e5-493c-b1fe-0b8c22c43961@t-19eb9e32d0574bbcbe4cf65a3f7bda8a",
   "dateModified":1543823813000,
   "createdBy":"m1a9dee2d-51e5-493c-b1fe-0b8c22c43961@t-19eb9e32d0574bbcbe4cf65a3f7bda8a",
   "dateCreated":1543816404000
}
```

`NOTE`

- This section can be compressed, but should not be encrypted
- Element name is all lower-case

### FilePolicy Format

Policy is strored as a utf-8 JSON string.

See [**Policy Bundle Format**](policy.format.md)

*Example*

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
                    "name": "WATERMARK"
                }
            ]
        }
    ]
}
```

`NOTE`

- This section can be compressed, and must be encrypted by token



### FileTag JSON Format

All the tags are stored in a JSON object, every JSON object Element is a tag object: `key` is tag name, and the `value` is a JSON array which contains one or more tag values. For example:

```json
{
    "tag_name_1": [
        "Value1",
        "Value2"
    ],
    "tag_name_2": [
        0,
        3,
        4
    ]
}
```

`NOTE`

- This section can be compressed, but should not be encrypted
- Tag name is always in lower-case

## Encryption & Decryption

### IV

To enhance security, NXL Format 2.0 use a random IV seed and Block Offset to generate real IV.

> Generate Rule:
> 
> IvecSeed xor BlockOffset

** Examples **
```c
void gen_ivec(_In_reads_bytes_opt_(16) const unsigned char* seed, unsigned __int64 offset, _Out_writes_bytes_(16) unsigned char* ivec)
{
    if (NULL == seed) {
        memset(ivec, 0, 16);
        memcpy(ivec, &offset, 8);
    }
    else {
        memcpy(ivec, seed, 16);
        if (offset) {
            offset = (offset - 1) * 31;
        }
        ((unsigned __int64*)ivec)[0] ^= offset;
        ((unsigned __int64*)ivec)[1] ^= offset;
    }
}
```


### Content Encryption & Decryption

We use AES 256 algorithm to encrypt file content.
The content is encryptedin blocks, and in each block, we use AES CBC mode.

- The IV of a block is generated by a random IV seed and the block's offset (see previous section "IV")
- To satisfy some File System requirements, we set block size to 512
- Encrypted content size is always aligned with Block Size, and the content_length in file header record the real content length.


## Checksum Calculation

We use HMAC_SHA256 algorithm to calculate checksum.

    HMAC_SHA256(Token, CombinedData)

- Key: we use file token as key
- Message: the message is a combined data  --- input data and its length (4 bytes). See following code:

    For example, if input data buffer is `data`, length is `size`, the combined data is:

        **Combined Data Buffer**

        |-------------------|-------------------------------------|
        |   size (4 bytes)  |                data                 |
        |___________________|_____________________________________|


See following pseudocode

```cpp
void calculate_checksum(_In_reads_bytes_(size) const unsigned char* data,
                        _In_ unsigned long size,
                        _Out_writes_bytes_(32) unsigned char checksum)
{
    unsigned char combined_data[4 + size] = {0};

    memcpy(combined_data, &size, 4);
    memcpy(combined_data + 4, data, size);
    
    checksum = HMAC_SHA256(token, combined_data);
}
```

### CEK Checksum

See following pseudocode

```cpp
void calculate_cek_checksum(_In_reads_bytes_(32) const unsigned char* cek, _Out_writes_bytes_(32) unsigned char checksum)
{
    unsigned char combined_data[4 + 32] = {0};
    unsigned long data_length = 32;

    memcpy(combined_data, &data_length, 4);
    memcpy(combined_data + 4, cek, 32);
    
    checksum = HMAC_SHA256(token, combined_data);
}
```

### Section Checksum

See following pseudocode

```cpp
void calculate_section_checksum(_In_ unsigned long section_flags,
                                _In_ unsigned short original_data_size,
                                _In_ unsigned short compressed_data_size,
                                _In_ const unsigned char* section_data,
                                _Out_writes_bytes_(32) unsigned char* checksum)
{
    unsigned long raw_data_size = BooleanFlagOn(section_flags, COMPRESS_FlAG) ? compressed_data_size : original_data_size;
    
    if(BooleanFlagOn(section_flags, ENCRYPT_FlAG)) {
        raw_data_size = ROUND_TO_SIZE(raw_data_size, 32);
    }

    unsigned char combined_data[4 + raw_data_size] = {0};
    memcpy(combined_data, &raw_data_size, 4);
    memcpy(combined_data + 4, section_data, raw_data_size);
    checksum = HMAC_SHA256(token, combined_data);
}
```


### Header Checksum

The header checksum is for fixed header (Signature, FileInfo, Keys Header, Section Header and Extended Header).

```cpp
typedef struct _NXL_FIXED_HEADER {
    Signature;
    FileInfo;
    Keys;
    Sections;
    Extended;
} NXL_FIXED_HEADER;

void calculate_header_checksum(_In_ const NXL_FIXED_HEADER* fixed_header, _Out_writes_bytes_(32) unsigned char checksum)
{
    unsigned long fixed_header_size = sizeof(NXL_FIXED_HEADER);
    unsigned char combined_data[4 + fixed_header_size] = {0};

    memcpy(combined_data, &fixed_header_size, 4);
    memcpy(combined_data + 4, fixed_header, fixed_header_size);
    
    checksum = HMAC_SHA256(token, combined_data);
}
```