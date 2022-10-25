# Project Download Refactoring #
Decision date: July/Aug 2018

Approval: Approved

Approver: RMS team (Aug), Sudhi/RMS/Keng/Michelle (July)


## Flow as Follows: ## 
Only for Project download flow - not touching MySpace downloads here.

In a project, a member clicks file download icon for an nxl file:
Get the file from repo/s3/...
Check if file tags indicate an adhoc *or centrally protected file (to be implemented)*


```
#!java

if (adhoc file) [
   apply adhoc evaluation
   if have download right
       reEncrypt with downloader's MySpace key and return this file (new owner, new duid)
       upload this file above to downloader's MyVault (who is document steward now)
   update logs
} else if (centrally protected) { // this part is missing and is about to be implemented
   apply central policy evaluation
   if have download right
       reEncrypt with downloader's membership but with original owner but new duid
       Do not upload to myvault since owner's the same.
   update logs
}
```



### Code changes proposed: ###
From email on 19/7/2018 "FYI: proposed project download refactoring"

### Current Download flow: ###

```
#!python

                new duid
                new owner         
                same rights

get nxl filemetadata from db
get duid from db
get adhoc file rights from db
check file rights (is user owner) and log download if denied
download nxl file
reEncryptDocument
                do adhoc evaluation and get watermark
                decrypt nxl file
                create new nxl (which has new duid) using logged in user's membership with same rights
                upload to myvault
                update db all_nxl table with new nxl file
                log that new file protected
write back new file to client
log download

```


### To be refactored into: ###

```
#!python

                new duid
                same owner       
                same rights
                
get nxl filemetadata from db
get duid from db
decide if adhoc or centralPolicyEvaluation and do evaluation
check file rights (if adhoc then check if owner, if centralPolicyEvaluation then as per policy) and log download if denied
download nxl file
reEncryptDocumentCopy
                get watermark
                decrypt nxl file
                create new nxl (which has new duid) using existing membership so owner remains the same and same rights
                (no more upload to myvault)
                update db all_nxl table with new nxl file
                log that new file protected
write back new file to client
log download

```
                

In the UI there will be cosmetic changes only – will edit the msg that is displayed telling the user what will happen on download, but the download button will always be visible (irrespective of whether Central Policy will allow download or not – it’s just clicking it will say “unauthorized”)

![d.png](https://bitbucket.org/repo/dBgzdj/images/3469517431-d.png)

![Download.jpg](https://bitbucket.org/repo/dBgzdj/images/3163859309-Download.jpg)