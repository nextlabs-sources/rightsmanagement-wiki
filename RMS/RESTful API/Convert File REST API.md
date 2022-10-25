# Convert File REST API
## Convert CAD file
This API is an internal API purely used by RMC iOS app. 

### v2 ###

userId and ticket should be passed as header parameters

- **URL**: /rms/rs/convert/v2/file?fileName=XXX&toFormat=hsf
    - Supported *toFormat*
        - hsf
        - hwf

- **method**: POST
- **consume**: application/octet-stream

        File content will be treated as binary data.

- **produces**: application/octet-stream

        Out will be converted binary file.

### ~~v1~~ (deprecated) ###

- **URL**: /rms/rs/convert/file/{user_id}/{ticket}?fileName=XXX&toFormat=hsf
    - Supported *toFormat*
        - hsf
        - hwf

- **method**: POST
- **consume**: application/octet-stream

        File content will be treated as binary data.

- **produces**: application/octet-stream

        Out will be converted binary file.