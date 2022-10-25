# Please make changes similar to the following, in your admin.properties file: 
(note that there is a back slash ‘\’ at the end of each line if there are multiple lines for a single entry)
 
## Remove these as they are deprecated:

```
#!python

web.idp.google.appId=395137018315-0lt77svf3bq2okcv3lt1f2tpje77nfaq.apps.googleusercontent.com
web.idp.google.appSecret=3FwS4YiNrG5ARH1Z1nEcvEJf
web.idp.fb.appId=259146497824373
web.idp.fb.appSecret=b11c96e2f4922b27190d6ca615fc7afc
```

 
## Add like these for Google and Facebook: 

```
#!python

web.idp.google.attributes={\
                "appId":"395137018315-0lt77svf3bq2okcv3lt1f2tpje77nfaq.apps.googleusercontent.com",\
                "appSecret":"3FwS4YiNrG5ARH1Z1nEcvEJf"\
}
web.idp.fb.attributes={\
                "appId":"259146497824373",\
                "appSecret":"b11c96e2f4922b27190d6ca615fc7afc"\
}
web.idp.rms.attributes={}

```

## LDAP (add fields like these if/for AD users) ##
Last 3 fields (rmsGroup, ldapSSL, securityPrincipalUseUserID) are optional.
To test using ldapSSL:true, the AD certificate must be imported into your deployment otherwise it will throw exception. rmsGroup can be empty (it's set to test group membership access).


```
#!python

web.idp.ldap.count=2
web.idp.ldap.1.attributes={\
                "ldapType":"AD",\
                "hostName":"10.23.58.5",\
                "domain":"qapf1.qalab01.nextlabs.com",\
                "searchBase":"DC=qapf1,DC=qalab01,DC=nextlabs,DC=com",\
                "userSearchQuery":"(&(objectClass=user)(sAMAccountName=$USERID$))",\
                "rmsGroup":"Board Members",\
                "ldapSSL":true,\
                "securityPrincipalUseUserID":false\
}

web.idp.ldap.2.attributes={\
                "ldapType":"AD",\
                "hostName":"10.23.57.52",\
                "domain":"qapf2.nextlabs.com",\
                "searchBase":"DC=qapf2,DC=nextlabs,DC=com",\
                "userSearchQuery":"(&(objectClass=user)(sAMAccountName=$USERID$))",\
                "rmsGroup":"ODRMUsers",\
                "ldapSSL":false,\
                "securityPrincipalUseUserID":false\
}
```

## SAML (add like these for Okta IDP) ##


```
#!python

web.idp.saml.count=1
web.idp.saml.1.attributes={\
                "spEntityId": "https://amami.nextlabs.com/rms/IdpManager/SamlAuth/SamlMetadata",\
                "spAcsUrl": "https://amami.nextlabs.com/rms/IdpManager/SamlAuth/SamlAuthFinish", \
                "idpEntityId": "http://www.okta.com/exkdly7chpyx4p9RP0h7",   \
                "idpSsoUrl": "https://dev-398860.oktapreview.com/app/nextlabsdev398860_rmsngamami_1/exkdly7chpyx4p9RP0h7/sso/saml", \
                "idpX509Cert":"MIIDpDCCAoygAwIBAgIGAV7hAs4/MA0GCSqGSIb3DQEBCwUAMIGSMQswCQYDVQQGEwJVUzETMBEGA1UECAwKQ2FsaWZvcm5pYTEWMBQGA1UEBwwNU2FuIEZyYW5jaXNjbzENMAsGA1UECgwET2t0YTEUMBIGA1UECwwLU1NPUHJvdmlkZXIxEzARBgNVBAMMCmRldi0zOTg4NjAxHDAaBgkqhkiG9w0BCQEWDWluZm9Ab2t0YS5jb20wHhcNMTcxMDAzMDY1MjMxWhcNMjcxMDAzMDY1MzI5WjCBkjELMAkGA1UEBhMCVVMxEzARBgNVBAgMCkNhbGlmb3JuaWExFjAUBgNVBAcMDVNhbiBGcmFuY2lzY28xDTALBgNVBAoMBE9rdGExFDASBgNVBAsMC1NTT1Byb3ZpZGVyMRMwEQYDVQQDDApkZXYtMzk4ODYwMRwwGgYJKoZIhvcNAQkBFg1pbmZvQG9rdGEuY29tMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA21IG4fOUPGfJznVRJtN3QoSEJyIPqWMTbrxgSVSfAjYTkqs5Y03CvzOQF2HXmNX15uAN//SKZ5JetgYnu5tAL3f7R1hD7xfTYxj2EP0kb42oHYRdiaRvgOFoLv3thiKtoKtYyoeV9MBFF+xlcVSUmwKAfJB5+mLbFAoZ8gB6QhK1VnEfP45uCt6Ph0IRLUyIduYsDbQ6vUZaU2N0L1Gk4ssacEqFFQ0W0DUJ9DJZ4bYTIq6g+ikx8MZrWP/InMCJ7F9aUbPFakkwaXxatN9tl9v8Mosba+Khm3/zGiuYR7+feC4m6IfUUe8g1fTeNojY3+O+AWzdteCy3A3Ra3368QIDAQABMA0GCSqGSIb3DQEBCwUAA4IBAQCn4P/Q9fRO3GVkLLtg60/SPKwhEf7Gvcv+121gkfmBZvZ8aGwGwYmop5X8qMNTPTNayIPr9X026xo8MrLFD2Wm0bn3UIvDYrOlRQMARfO2QA5FiN0Eshz+CtDKb6s7y3LFlLabsLc2P83h96b6tkLRhjQmXuogp0OEvX7slaNGM3uFIYzoB99dneq+LFjF8zp000KTtexTaZxv4MjmupTT2+G/A3v7PKb10tYZNQzkeefdTaZNqe5Cm8Qab7tjGA/5epg2Hup8iiYDCwefxYLFlbuEdb4wq11t+FFp4IerEUiP+MM/unwLlAT3FakFodC+JmNJPhY5IwZ2sqHOZGMG"\
} 
```