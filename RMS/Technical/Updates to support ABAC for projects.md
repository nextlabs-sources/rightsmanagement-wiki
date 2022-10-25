[Related Jira story: SC-2263](https://nextlabs.atlassian.net/browse/SC-2263)  
  

## Proposed REST API Updates   

  
[Tenant API](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Tenant%20REST%20API)  
Propose to :  
  
* Create a new API to return tenant tags (or)  
* Create a new API to return all tenant metadata (do we need this?)  
* Update [Register new tenant API](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Tenant%20REST%20API#markdown-header-register-a-new-tenant-on-rms) to include tags
* Update [Update tenant profile](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Tenant%20REST%20API#markdown-header-update-tenant-profile) to include tags  

  
[Project APIs](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API)  
Propose to update the following APIs to include project tags :  

* [Get project metadata](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API#markdown-header-get-project-metadata) should return tags
* [Create project](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API#markdown-header-create-project) should accept tags
* [Update project](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Projects%20REST%20API#markdown-header-update-project) should accept tags  

Policy APIs (not recorded in wiki)

* [Get policy model] to return obligations
* [Create policy] change to accept detailed obligations for ABAC policy
* [Update policy] change to accept detailed obligations for ABAC policy
	
	Sample payload: Note that the obligation name and param name are corresponding to the shortName of obligation and shortName of obligation parameter defined in policy model.
```
{
	"allowObligations": [{
		"policyModelId": "75",
		"name": "projects",
		"params": {
			"projects": "IT, HR" //format to be defined
		}
	}],
	"status": "APPROVED",
	"parentId": null,
	"deployed": true,
	"skipValidate": false
}
```
* May also want to update [policyEvaluation REST API](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/RESTful%20API/Policy%20Evaluation%20API), if clients want to do ABAC policy evaluation.

## UI updates

* Tenant and project creation UI update
* Policy UI update
* API user rights assignment

TBD

  

## Java backend updates

* Update /rms-server/src/com/nextlabs/rms/ps/service/PolicyStudioModelService.java updateResourcePolicyModel function to set obligation in policy model. Sample obligation payload:
```
    "obligations": [{
		"id": null,
		"name": "accessible_projects",
		"shortName": "projects",
		"runAt": "PEP",
		"parameters": [{
			"id": null,
			"name": "accesible_projects",
			"type": "TEXT_SINGLE_ROW",
			"defaultValue": "IT, HR, ENGINEERING",
			"value": "",
			"listValues": null,
			"hidden": false,
			"editable": true,
			"mandatory": false,
			"sortOrder": 0,
			"shortName": "projects"
		}],
		"sortOrder": 0
	}],
```
* May need to create new policy model for master tenant ABAC policy. (As you may not want to use the obligation defined for ABAC to be shown in file policy)
* Create a new ACTION type "ACCESS" as ABAC evaluation result?
* Create new Tag for ABAC policy(for policy filtering) TAG name should be SKYDRM_ABAC?
* Update policy related REST APIs (as documented in REST API updates section)
* Create ABACObligation handler to define/format/evaluate ABAC obligations
* May need to store obligation in database for Non-CC policy? (optional)