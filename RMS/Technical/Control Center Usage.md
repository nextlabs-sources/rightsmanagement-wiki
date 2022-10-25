# Overview  
  
This page is to record any investigation into the usage of control center, its features, gaps, requirements for SkyDRM and proposals.  
  
## Policy Model and Subject/User Components  
  
This is an investigation into how subject/user components can be created and used. One of the goals is to determine whether the control center can be used to manage user attributes. This is related to a requirement from PLM to be able to use TeamCenter IDP attributes for policies on SkyDRM.  
  
The "Policy Model" section in the CC contains a pre-defined "User" resource type that cannot be deleted. This is shown below.   
  
![01.png](https://bitbucket.org/repo/dBgzdj/images/757832945-01.png)  
   
This is a single resource available across the entire CC deployment. In the case of SkyDRM, this policy model applies across the domain across all tenants. You can edit the "User" entry as follows.  
  
![02.png](https://bitbucket.org/repo/dBgzdj/images/1893736763-02.png)  
  
I have added three attributes here: title, department and employee_id. The first two are strings and the third one is a number.  
  
![03.png](https://bitbucket.org/repo/dBgzdj/images/3169742883-03.png)  
  
I can now go to the Subject component section and add a subject component.  
  
![05.png](https://bitbucket.org/repo/dBgzdj/images/84573398-05.png)  
  
![06.png](https://bitbucket.org/repo/dBgzdj/images/4266709407-06.png)  
  
I can add a condition here.  
  
![07.png](https://bitbucket.org/repo/dBgzdj/images/3709692193-07.png)  
  
I can choose from one of the three attributes I had defined in the policy model.  
  
![08.png](https://bitbucket.org/repo/dBgzdj/images/2945864265-08.png)  
  
Here I have chosen employee_id which is a number.  
  
![09.png](https://bitbucket.org/repo/dBgzdj/images/3861089489-09.png)  
  
I can change it to something else. Here I have chosen the title attribute and entered "Director of Operations" as free form text.  
  
![10.png](https://bitbucket.org/repo/dBgzdj/images/987271361-10.png)  
  
This subject/user component can now be saved and deployed.  
  
![11.png](https://bitbucket.org/repo/dBgzdj/images/397980647-11.png)  
  
It is now visible in the components section.  
  
![12.png](https://bitbucket.org/repo/dBgzdj/images/530923355-12.png)
  
Note that I have tagged this component as "UserComponent-01" which is visible in the listing.   
  
## Limitations  
  
* This mechanism allows us to create one User policy model. However it applies across the entire SkyDRM deployment, i.e., the whole domain, across all the tenants. This does not allow for differences in attribute types or attribute mapping across different tenants. We can consider this approach to managing user attributes only if all tenants will use the same attributes. This may not work for SaaS for business accounts where different business accounts may need different attributes that are unique to that business. 
  
  
# Discussion Notes  
  
2019-05-03 Call with Keng.  
  
* We can use the single ("User") subject resource type in the policy model section to define attributes used by all tenants. 
* Create Subject/User components per tenant using the attributes defined in the policy model.  

# Reference

This page [here](https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/Technical/SkyDRM%20architecture%20migration) contains details of an investigation into how the SkyDRM policies are created and deployed on CC 8.6 as of build 212, on 2019-05-03.