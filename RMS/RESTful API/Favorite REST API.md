# Favorite File service

 - [Get Favorite files in all repositories AGGREGATED fashion](#markdown-header-get-favorite-files-in-all-repositories-aggregated)
 - [Get Favorite files in all repositories LIST fashion](#markdown-header-get-favorite-files-in-all-repositories-list)
 - [Get Favorite files in a repository](#markdown-header-get-favorite-files-in-a-repository)
 - [Mark files in a repository as Favorite](#markdown-header-mark-files-in-a-repository-as-favorite)
 - [Unmark files in a repository as Favorite](#markdown-header-unmark-files-in-a-repository-as-favorite)

## Get Favorite files in all repositories aggregated
This API is used to get all the favorite files in all the repositories of the (user+tenant) in an **aggregated** way.

MyDrive, Dropbox, GoogleDrive, OneDrive are considered as normal reposiotories while MyVault has to be taken care differently. In the metadata for each file, there will be a flag called ```fromMyVault```. 

- **URL**: /rms/rs/favorite
- **method**: GET
- **consumes**: Pass userId and ticket as header parameters
- **produces**: application/json
```json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1499140174566,
    "results": {
        "repos": [
            {
                "repoId": "e8fbefe2-b07b-48c9-a107-296c5515729f",
                "repoName": "FC",
                "repoType": "GOOGLE_DRIVE",
                "markedFavoriteFiles": [
                    {
                        "pathId": "0Bzs9C4QJRYJFd3Z0WE1nN3VqVjA",
                        "pathDisplay": "/test/RMS DeepDrive.pptx",
                        "fromMyVault": false
                    },
                    {
                        "pathId": "0Bzs9C4QJRYJFaW9ONGNPV2ttUE0",
                        "pathDisplay": "/Presentation Skills.ppt",
                        "fromMyVault": false
                    }
                ],
                "unmarkedFavoriteFiles": [],
                "isFullCopy": true
            },
            {
                "repoId": "4260cc96-7b1d-4a52-bb46-d141dc162ed4",
                "repoName": "One",
                "repoType": "ONE_DRIVE",
                "markedFavoriteFiles": [
                    {
                        "pathId": "833E7E803116890A!1791",
                        "pathDisplay": "/ff.txt",
                        "fromMyVault": false
                    }
                ],
                "unmarkedFavoriteFiles": [],
                "isFullCopy": true
            },
            {
                "repoId": "5ddd68a3-699b-4202-bd31-8a1db07f3120",
                "repoName": "DB",
                "repoType": "DROPBOX",
                "markedFavoriteFiles": [
                    {
                        "pathId": "id:udwdhPU-3x8AAAAAAAAR7g",
                        "pathDisplay": "/Test/shot.png",
                        "fromMyVault": false
                    },
                    {
                        "pathId": "id:udwdhPU-3x8AAAAAAAASFQ",
                        "pathDisplay": "/Report-2017-04-05-17-41-00.pdf.nxl",
                        "fromMyVault": false
                    }
                ],
                "unmarkedFavoriteFiles": [],
                "isFullCopy": true
            },
            {
                "repoId": "c2770725-3399-42e9-b098-ea4fd5b28374",
                "repoName": "MyDrive",
                "repoType": "S3",
                "markedFavoriteFiles": [
                    {
                        "pathId": "/nxl_myvault_nxl/capture-2017-06-23-17-05-13.png.nxl",
                        "pathDisplay": "/nxl_Myvault_nxl/Capture-2017-06-23-17-05-13.PNG.nxl",
                        "fromMyVault": true
                    },
                    {
                        "pathId": "/a/motor-details.txt",
                        "pathDisplay": "/a/Motor-Details.txt",
                        "fromMyVault": false
                    }
                ],
                "unmarkedFavoriteFiles": [],
                "isFullCopy": true
            }
        ]
    }
}
```

## Get Favorite files in all repositories list
This API is used to get all the favorite files in all the repositories of the (user+tenant) in a **list** fashion, which means the results are a list of repoContents instead of aggregated jsonRepoFiles by different repositories. Besides, it supports pagination, sorting and search. 

MyDrive, Dropbox, GoogleDrive, OneDrive are considered as normal reposiotories while MyVault has to be taken care differently. In the metadata for each file, there will be a flag called ```fromMyVault```. 

- **URL**: /rms/rs/favorite/list?page=1&size=10&orderBy=creationTime&q.fileName=document
- **method**: GET
- **consumes**: Pass userId and ticket as header parameters
- **produces**: application/json

**Supported values for query parameters**
> |  *Parameter*            |  *Description*   |
> |-------------------------|------------------|
> | page	  | Page number. Positive number.| 
> | size      | Maximum number of data to return. You can use page and size fields to support pagination on client.|
> | orderBy   | A comma-separated list of sort keys. Valid keys are ***name***, ***lastModifiedTime***, ***fileSize***. Each key sorts ascending by default, but may be reversed with the '-' modifier. Example usage: ***orderBy=-lastModifiedTime, fileName***.|
> |q.fileName | Search by file name.|


```json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1499139945235,
    "results": {
        "loadMore": true,
        "results": [
            {
                "path": "/nxl_Myvault_nxl/Capture-2017-06-23-17-05-13.PNG.nxl",
                "name": "Capture-2017-06-23-17-05-13.PNG.nxl",
                "pathId": "/nxl_myvault_nxl/capture-2017-06-23-17-05-13.png.nxl",
                "fileId": "/nxl_myvault_nxl/capture-2017-06-23-17-05-13.png.nxl",
                "isFolder": false,
                "isRepo": false,
                "favorited": true,
                "fromMyVault": true,
                "lastModifiedTime": 1498208717142,
                "fileSize": 26112,
                "repoId": "c2770725-3399-42e9-b098-ea4fd5b28374",
                "repoName": "MyDrive",
                "repoType": "S3",
                "fileType": "png",
                "protectedFile": true,
                "usePathId": false,
                "children": [],
                "deleted": false,
                "protectionType": 0
            },
            {
                "path": "/Test/shot.png",
                "name": "shot.png",
                "pathId": "id:udwdhPU-3x8AAAAAAAAR7g",
                "fileId": "id:udwdhPU-3x8AAAAAAAAR7g",
                "isFolder": false,
                "isRepo": false,
                "favorited": true,
                "fromMyVault": false,
                "lastModifiedTime": 1495098949000,
                "fileSize": 144036,
                "repoId": "5ddd68a3-699b-4202-bd31-8a1db07f3120",
                "repoName": "DB",
                "repoType": "DROPBOX",
                "fileType": "png",
                "protectedFile": false,
                "usePathId": false,
                "children": [],
                "deleted": false,
                "protectionType": -1
            },
            {
                "path": "/a/Motor-Details.txt",
                "name": "Motor-Details.txt",
                "pathId": "/a/motor-details.txt",
                "fileId": "/a/motor-details.txt",
                "isFolder": false,
                "isRepo": false,
                "favorited": true,
                "fromMyVault": false,
                "lastModifiedTime": 1494834525000,
                "fileSize": 11874,
                "repoId": "c2770725-3399-42e9-b098-ea4fd5b28374",
                "repoName": "MyDrive",
                "repoType": "S3",
                "fileType": "txt",
                "protectedFile": false,
                "usePathId": false,
                "children": [],
                "deleted": false,
                "protectionType": -1
            },
            {
                "path": "/test/RMS DeepDrive.pptx",
                "name": "RMS DeepDrive.pptx",
                "pathId": "0Bzs9C4QJRYJFd3Z0WE1nN3VqVjA",
                "fileId": "0Bzs9C4QJRYJFd3Z0WE1nN3VqVjA",
                "isFolder": false,
                "isRepo": false,
                "favorited": true,
                "fromMyVault": false,
                "lastModifiedTime": 1490177756169,
                "fileSize": 1988528,
                "repoId": "e8fbefe2-b07b-48c9-a107-296c5515729f",
                "repoName": "FC",
                "repoType": "GOOGLE_DRIVE",
                "fileType": "pptx",
                "protectedFile": false,
                "usePathId": false,
                "children": [],
                "deleted": false,
                "protectionType": -1
            },
            {
                "path": "/Presentation Skills.ppt",
                "name": "Presentation Skills.ppt",
                "pathId": "0Bzs9C4QJRYJFaW9ONGNPV2ttUE0",
                "fileId": "0Bzs9C4QJRYJFaW9ONGNPV2ttUE0",
                "isFolder": false,
                "isRepo": false,
                "favorited": true,
                "fromMyVault": false,
                "lastModifiedTime": 1496023633548,
                "fileSize": 471040,
                "repoId": "e8fbefe2-b07b-48c9-a107-296c5515729f",
                "repoName": "FC",
                "repoType": "GOOGLE_DRIVE",
                "fileType": "ppt",
                "protectedFile": false,
                "usePathId": false,
                "children": [],
                "deleted": false,
                "protectionType": -1
            }
        ]
    }
}
```

## Get Favorite files in a repository
This API is used to get favorites files in a repository or all repositories while repository_id specified as all (with an optional `lastModified` parameter).

If `lastModified` parameter is not present or `lastModified` date is more than 30 days before the current date, a complete list of favorite files will be returned, otherwise only the files that are marked/unmarked after lastModified will be returned. `lastModified` should be in unix (epoch) time.

If you want to get favorite files in MyVault only, you need to get all favorite files from MyDrive(since MyVault is within MyDrive) and then filter by the flag ```fromMyVault```. 

- **URL**: /rms/rs/favorite/{repository_id}?lastModified=1477875479013
- **method**: GET
- **consumes**: Pass userId and ticket as header parameters
- **produces**: application/json
```json
{
    "statusCode": 200,
    "message": "OK",
    "serverTime": 1499140274297,
    "results": {
        "unmarkedFavoriteFiles": [],
        "isFullCopy": true,
        "markedFavoriteFiles": [
            {
                "pathId": "/nxl_myvault_nxl/capture-2017-06-23-17-05-13.png.nxl",
                "pathDisplay": "/nxl_Myvault_nxl/Capture-2017-06-23-17-05-13.PNG.nxl",
                "fromMyVault": true
            },
            {
                "pathId": "/a/motor-details.txt",
                "pathDisplay": "/a/Motor-Details.txt",
                "fromMyVault": false
            }
        ]
    }
}
```

## Mark files in a repository as Favorite
This API is used to mark files in a repository as favorite

- **URL**: /rms/rs/favorite/{repository_id}
- **method**: POST
- **consumes**: application/json; Pass userId and ticket as header parameters


**parentFileId** : this attribute should be the immediate parent folder's fileId. If the immediate parent folder, please pass ```"/"```. For files in MyVault, please always pass ```"/nxl_myvault_nxl/"```.

- For Dropbox, OneDrive, SPOL, it's a single ID. 
- For Google drive, it should also be a single ID instead of a hierarchical one. (Mentioning this separately is because the pathId metadata for a Google Drive folder is hierarchical. Should take the part after the last slash or take the value of **fileId**. 
- For MyDrive, it should be the lowercase of the folder's pathDisplay (e.g.  pathId: /a/b/ , pathDisplay: /A/B )


```json
{
   "parameters":{
      "files":[
         {
            "pathId":"id:bDWMOKRPeTAAAAAAAAAACA",
            "pathDisplay":"/MyDocuments/Engine.doc",
            "parentFileId": "id:Nf5wTXNQiIAAAAAAAAAA7g",
            "fileSize": 332,
            "fileLastModified": 1474590650465
         },
         {
            "pathId":"id:bDWMOKRPeTAAAA56789ACA",
            "pathDisplay":"/MyDocuments/Motors.doc",
            "parentFileId": "id:Nf5wTXNQiIAAAAAAAAAA7g"
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

- **URL**: /rms/rs/favorite/{repository_id}
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