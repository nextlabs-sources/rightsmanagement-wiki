# AWS Configuration Changes #

# After Feb 2018 #


```
#!python

The following changes are required before deploying build 10.7.93
Please remove the following lines from your admin.properties.

web.S3_ACCESSKEYID=<S3AccessKey>
web.S3_SECRETACCESSKEY=<S3AccessId>
web.S3_BUCKETNAME=<bucketname>
web.S3_PROJECT_BUCKETNAME=<bucketname>
web.inbuilt_service_provider=S3

The new values are 

web.inbuilt_storage_provider=S3
web.inbuilt_storage_provider.attributes={ \
                "APP_ID": "=<S3AccessId>",       \
                "APP_SECRET": "<S3AccessKey>", \
                "MYSPACE_BUCKET_NAME": "<bucketname> ", \
                "PROJECT_BUCKET_NAME": "<bucketname>" \
}
```