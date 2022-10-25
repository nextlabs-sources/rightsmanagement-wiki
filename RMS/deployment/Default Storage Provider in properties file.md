# Please make changes similar to the following, in your admin.properties file: 
(note that there is a back slash ‘\’ at the end of each line if there are multiple lines for a single entry)
 
## Remove these as they are deprecated:

```
#!python

web.S3_ACCESSKEYID=<S3AccessKey>
web.S3_SECRETACCESSKEY=<S3AccessId>
web.S3_BUCKETNAME=<bucketname>
web.S3_PROJECT_BUCKETNAME=<bucketname>
web.inbuilt_service_provider=S3

```

 
## Add the following for configuring S3 as a default storage provider: 

```
#!python

web.inbuilt_storage_provider=S3
web.inbuilt_storage_provider.attributes={ \
                "APP_ID": "<S3AccessId>",       \
                "APP_SECRET": "<S3AccessKey>", \
                "MYSPACE_BUCKET_NAME": "<bucketname>", \
                "PROJECT_BUCKET_NAME": "<bucketname>" \
}

```