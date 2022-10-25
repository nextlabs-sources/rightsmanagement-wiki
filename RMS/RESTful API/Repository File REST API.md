# **THIS API HAS BEEN REMOVED. PLEASE USE FAVORITE REST API INSTEAD** #

# ~~Repository File service~~ (REMOVED)

 - [Get Favorite and Offline files in all repositories](#markdown-header-get-favorite-and-offline-files-in-all-repositories)
 - [Get Favorite files in a repository](#markdown-header-get-favorite-files-in-a-repository)
 - [Mark files in a repository as Favorite](#markdown-header-mark-files-in-a-repository-as-favorite)
 - [Unmark files in a repository as Favorite](#markdown-header-unmark-files-in-a-repository-as-favorite)
 - [Get Offline files in a repository](#markdown-header-get-offline-files-in-a-repository)
 - [Mark files in a repository as Offline](#markdown-header-mark-files-in-a-repository-as-offline)
 - [Unmark files in a repository as Offline](#markdown-header-unmark-files-in-a-repository-as-offline)

## Get Favorite and Offline files in all repositories
This API is used to get all the favorite and offline files in all the repositories of the (user+tenant)

- **URL**: /rms/rs/repository/all/files
- **method**: GET
- **consumes**: Pass userId and ticket as header parameters
- **produces**: application/json
```json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1474590650465,
   "results":{
      "repos":[
         {
            "repoId":"07ebed38-f23c-45af-96ea-5a16908266b4",
            "repoName":"myRepo",
            "repoType":"OneDrive",
            "markedOfflineFiles":[
               {
                  "pathId":"id:bDWM0KRPeTAAAA56789ACA",
                  "pathDisplay":"/onedrive-document.docx"
               },
               {
                  "pathId":"id:bDWMOKRzzTAAAA56789ACA",
                  "pathDisplay":"/myengine.ppt"
               }
            ],
            "unmarkedOfflineFiles":[

            ],
            "markedFavoriteFiles":[

            ],
            "unmarkedFavoriteFiles":[

            ],
            "isFullCopy":true
         },
         {
            "repoId":"34774e6a-0e02-43b3-8693-3663e5aaf546",
            "repoName":"myRepo",
            "repoType":"OneDrive",
            "markedOfflineFiles":[
               {
                  "pathId":"id:bDWMOKRPeTACDD56789ACA",
                  "pathDisplay":"/BoltsAndNuts.dwg"
               }
            ],
            "unmarkedOfflineFiles":[

            ],
            "markedFavoriteFiles":[
               {
                  "pathId":"id:bDWMOKRPeTAAAA56789ACA",
                  "pathDisplay":"/ClassifiedRankings.nxl"
               }
            ],
            "unmarkedFavoriteFiles":[

            ],
            "isFullCopy":true
         }
      ]
   }
}
```

## Get Favorite files in a repository or all repositories
This API is used to get favorites files in a repository or all repositories while repository_id specified as all (with an optional `lastModified` parameter).
If `lastModified` parameter is not present or `lastModified` date is more than 30 days before the current date, a complete list of favorite files will be returned, otherwise only the files that are marked/unmarked after lastModified will be returned. `lastModified` should be in unix (epoch) time.

- **URL**: /rms/rs/repository/{repository_id}/files/favorite?lastModified=1477875479013
- **method**: GET
- **consumes**: Pass userId and ticket as header parameters
- **produces**: application/json
```json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1474590650465,
   "results":{
      "isFullCopy":false,
      "markedFavoriteFiles":[
         {
            "pathId":"id:bDWMOKRPeTAAAA56789ACA",
            "pathDisplay":"/MyDocuments/Motors.doc"
         }
      ],
      "unmarkedFavoriteFiles":[
         {
            "pathId":"id:bDWMOKRPeTAAAAAAAAAACA",
            "pathDisplay":"/MyDocuments/Engine.doc"
         }
      ]
   }
}

Response for getting all repositories' favourite files
{
  "statusCode": 200,
  "message": "OK",
  "serverTime": 1493865109612,
  "results": {
    "repos": [
      {
        "repoId": "c2770725-3399-42e9-b098-ea4fd5b28374",
        "repoName": "MyDrive",
        "repoType": "S3",
        "markedOfflineFiles": [],
        "unmarkedOfflineFiles": [],
        "markedFavoriteFiles": [
          {
            "pathId": "/z/Motor-Details.txt",
            "pathDisplay": "/z/Motor-Details.txt"
          },
          {
            "pathId": "/zz/Motor-Details.txt",
            "pathDisplay": "/zz/Motor-Details.txt"
          }
        ],
        "unmarkedFavoriteFiles": [],
        "isFullCopy": true
      }
    ]
  }
}
```

## Mark files in a repository as Favorite
This API is used to mark files in a repository as favorite

- **URL**: /rms/rs/repository/{repository_id}/files/favorite
- **method**: POST
- **consumes**: application/json; Pass userId and ticket as header parameters
```json
{
   "parameters":{
      "files":[
         {
            "pathId":"id:bDWMOKRPeTAAAAAAAAAACA",
            "pathDisplay":"/MyDocuments/Engine.doc"
         },
         {
            "pathId":"id:bDWMOKRPeTAAAA56789ACA",
            "pathDisplay":"/MyDocuments/Motors.doc"
         }
      ]
   }
}
```

- **produces**: application/json
```json
{
   "statusCode":200,
   "message":"Files successfully marked as favorite",
   "serverTime":1474591219931
}
```

## Unmark files in a repository as Favorite
This API is used to unmark files in a repository as favorite

- **URL**: /rms/rs/repository/{repository_id}/files/favorite
- **method**: DELETE
- **consumes**: application/json; Pass userId and ticket as header parameters
```json
{
   "parameters":{
      "files":[
         {
            "pathId":"id:bDWMOKRPeTAAAAAAAAAACA",
            "pathDisplay":"/MyDocuments/Engine.doc"
         },
         {
            "pathId":"id:bDWMOKRPeTAAAA56789ACA",
            "pathDisplay":"/MyDocuments/Motors.doc"
         }
      ]
   }
}
```

- **produces**: application/json
```json
{
   "statusCode":200,
   "message":"Files successfully unmarked as favorite",
   "serverTime":1474591551519
}
```

## Get Offline files in a repository 
This API is used to get favorites files in a repository (with an optional `lastModified` parameter).
If `lastModified` parameter is not present or `lastModified` date is more than 30 days before the current date, a complete list of offline files will be returned, otherwise only the files that are marked/unmarked after lastModified will be returned. `lastModified` should be in unix (epoch) time.

- **URL**: /rms/rs/repository/{repository_id}/files/offline?lastModified=1477875479013
- **method**: GET
- **consumes**: Pass userId and ticket as header parameters
- **produces**: application/json
```json
{
   "statusCode":200,
   "message":"OK",
   "serverTime":1474590650465,
   "results":{
      "isFullCopy":false,
      "markedOfflineFiles":[
         {
            "pathId":"id:bDWMOKRPeTAAAA56789ACA",
            "pathDisplay":"/MyDocuments/Motors.doc"
         }
      ],
      "unmarkedOfflineFiles":[
         {
            "pathId":"id:bDWMOKRPeTAAAAAAAAAACA",
            "pathDisplay":"/MyDocuments/Engine.doc"
         }
      ]
   }
}
```

## Mark files in a repository as Offline
This API is used to mark files in a repository as offline

- **URL**: /rms/rs/repository/{repository_id}/files/offline
- **method**: POST
- **consumes**: application/json; Pass userId and ticket as header parameters

```json
{
   "parameters":{
      "files":[
         {
            "pathId":"id:bDWMOKRPeTAAAAAAAAAACA",
            "pathDisplay":"/MyDocuments/Engine.doc"
         },
         {
            "pathId":"id:bDWMOKRPeTAAAA56789ACA",
            "pathDisplay":"/MyDocuments/Motors.doc"
         }
      ]
   }
}
```

- **produces**: application/json
```json
{
   "statusCode":200,
   "message":"Files successfully marked as offline",
   "serverTime":1474591219931
}
```

## Unmark files in a repository as Offline
This API is used to unmark files in a repository as offline

- **URL**: /rms/rs/repository/{repository_id}/files/offline
- **method**: DELETE
- **consumes**: application/json; Pass userId and ticket as header parameters
```json
{
   "parameters":{
      "files":[
         {
            "pathId":"id:bDWMOKRPeTAAAAAAAAAACA",
            "pathDisplay":"/MyDocuments/Engine.doc"
         },
         {
            "pathId":"id:bDWMOKRPeTAAAA56789ACA",
            "pathDisplay":"/MyDocuments/Motors.doc"
         }
      ]
   }
}
```		

- **produces**: application/json
```json
{
   "statusCode":200,
   "message":"Files successfully unmarked as offline",
   "serverTime":1474591551519
}
```