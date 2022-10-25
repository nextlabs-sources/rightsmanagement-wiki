# OpenLDAP for Windows #
*
This is an example of OpenLDAP, need to verify some proper vendor for production support.
*

To verify OpenLDAP in SkyDRM application, please download from [https://userbooster.de/download/openldap-for-windows.aspx](OpenLDAP).

Follow the instruction specified in the given link [http://www.userbooster.de/en/support/feature-articles/openldap-for-windows-installation.aspx](OpenLDAP instructions).

Also download Softerra LDAP Administrator [https://www.ldapadministrator.com/download.htm](Softerra).

# Verify LDAP Connection #
Using Softerra, you can verify the connection. 
## Step 1: ##
default user is "cn=Manager,dc=maxcrc,dc=com". Enter this value.

![openldapconfig.PNG](https://bitbucket.org/repo/dBgzdj/images/2835849754-openldapconfig.PNG)
## Step 2: ##
![openldapconfig.PNG](https://bitbucket.org/repo/dBgzdj/images/3408450540-openldapconfig.PNG)

LDAP Configuration Details:


```
#!python
web.idp.ldap.1.attributes={\
                "ldapType":"openLDAP",\
                "hostName":"localhost",\
                "domain":"nextlabs.com",\
                "searchBase":"dc=maxcrc,dc=com",\
                "userSearchQuery":"(&(objectClass=*))",\
                "rmsGroup":"",\
                "ldapSSL":false,\
                "securityPrincipalUseUserID":false\
}

```
1. Change localhost to your server name.
2. Make sure change the search base to your rootdn. You can find this detail in slapd.conf file in OpenLDAP installation folder.
3. Change user search query based on objectClass. If you are not sure, keep this one (&(objectClass=*)).
4. Make sure you have mail attribute as this is required by SkyDRM application.