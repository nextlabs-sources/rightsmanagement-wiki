# SkyDRM Server: August 2018 Release
  
# Features

* Enterprise application integration
    + Token management API for enterprise application integration
    + Trusted application registration with AppID and AppKey. DCE uses the AppID and AppKey with the RMJavaSDK for authentication. 
`RightsManager manager = new RightsManager(routerURL, appId, appKey);`
    + Enterprise applications mapped to specific "partner enterprise applications" projects with the flexibility to add more projects if required
        - Separation of duties (master tenant admin vs project admin)
        - Fine-grained control of access
* Projects and MySpace
    + Ad-hoc policies -> per file, created by users, available for both MySpace and Projects
    + Central policies for projects -> created by the project admin or master tenant admin
* User management
    + SkyDRM IDP
    + SAML ID providers (Ping, Okta)
    + Google
    + Facebook
    + LDAP/AD
* Storage providers supported
    + Amazon S3
    + OneDrive for business
* SMTP for email notifications
* Databases supported
    + PostgreSQL internal database
    + PostgreSQL external database. MS-SQL 2012 not yet fully supported. 
* Control center 8.6 integration
  
  
# Schedule
* Token retrieval client API: 06 August [completed]
* Bug fixes and code complete: 10 August [completed]
* Release to QA: 13 Aug onwards. Build 141 released 17 Aug, Build 142 with bugfixes 21 Aug.
* Documentation freeze: week 33
* Validation complete and code freeze: week 34
* GA release: week 34 - 35