## Requirements:

* The tenant admins/project admins number should not be limited.

* We need to have a centralized authorization filter to secure RMS REST APIs.

* We need to design a role management system to support more upcoming roles and give end-user the flexiblity to define more roles by themselves
## Proposed Design   
Implement roles management and API authorization with JAX-RS security standard and Jersey framework.

Propose to:
  
* Change db schema to support roles management
* Follow JAX-RS standard to authorize REST API requests
* Update existing REST APIs where has roles checking
* Review all REST APIs and add dynamic permissions

  
## DB schema design:
* need to add two new tables 'roles' and 'user_roles'
* roles table:
```
| id| name | description | permissions |
```
permissions field is designed as an json object, which contains permissions for different modules.

For example:
```
{
	"projectMgmt": 7, //0111
	"userMgmt": 8, //1000
	"tenantMgmt": 5, //0101
	......
}
// the permissions value is a bitmap consisting of 4 bits - from high to low, each bit mean CRUD(CREATE, READ, UPDATE, DELETE)
```

* user_roles table:
```
| id| email| role_id| tenant_id |
```

## JAX-RS security standard and Jersey framework:

* [Secure JAX-RS with security annotations](https://www.ibm.com/support/knowledgecenter/ro/SSAW57_8.5.5/com.ibm.websphere.nd.multiplatform.doc/ae/twbs_jaxrs_impl_securejaxrs_annotations.html)

* [Use Jersey framework to secure REST APIs](https://howtodoinjava.com/jersey/jersey-rest-security/)

 
## Update existing REST APIs

* Three roles (superAdmin,tenantAdmin,projectAdmin) will be pre-defined and inserted to DB once server startup, update the existing code where we are doing roles checking, update with @RolesAllowed annotation, ensure code backward compatibility

## Review all REST APIs and assign dynamic permissions

* Based on the 4 permissions for each module, review all existing REST APIs and declare required permissions to access. Note that not all the APIs need to do access control. For example 'accept project invitation' API, it's safe for all the users to access, in this case we don't need to declare required permisssions, or we can declare it with @PermitAll annotation.