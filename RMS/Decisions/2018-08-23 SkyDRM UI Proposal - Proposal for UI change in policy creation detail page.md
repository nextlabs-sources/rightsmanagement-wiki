# SkyDRM UI Proposal - Proposal for UI change in policy creation detail page

## Goal

Tag libraries (Angular directives like ngTagsInput), which we explored, do not support plain texts. That means supporting combiners between the tags will be a challenge. That was the reason we were required to either change the UI for policy creation page in SkyDRM or use another Angular directive which can support plain texts as well.

## Content

- Control Center's Policy UI
- Difference between Control Center and SkyDRM policy creation UI
- Proposal option 1
- Proposal option 2

![slide 1.PNG](https://bitbucket.org/repo/dBgzdj/images/3315706259-slide%201.PNG)
![slide 2.PNG](https://bitbucket.org/repo/dBgzdj/images/3411215196-slide%202.PNG)
![slide 3.PNG](https://bitbucket.org/repo/dBgzdj/images/1463972917-slide%203.PNG)
![slide 4 modified.PNG](https://bitbucket.org/repo/dBgzdj/images/2597588593-slide%204%20modified.PNG)
![slide 5.PNG](https://bitbucket.org/repo/dBgzdj/images/312496251-slide%205.PNG)

**Decision date:** 23rd August 2018

**Decision:** 

We go with "Proposal option 1" :

- No change in the SkyDRM UI design for policy creation. Keng had specifically mentioned using combiners between the tags as it will be more UI friendly 
- Use Control Center's custom Angular directive "go-as-you-type" as their code involves combiners between the tags. Handling of combiners is taken care by their custom directive 

**Approved by:** RMS team and Neelkant