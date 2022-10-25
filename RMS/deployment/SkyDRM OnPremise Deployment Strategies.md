**SkyDRM onPremise deployment strategies**

Below are the various options discussed for SkyDRM onPremise deployment. The final decision taken can be found at the bottom of the page.

*Option 1-RMS Exposing non default ports*

![option1.png](https://bitbucket.org/repo/dBgzdj/images/1001261515-option1.png)

*Pros*

* Simple Deployment

*Cons*

* Need to expose non default ports to end user
* More suitable for poc and one instance deployments
* Cannot scale

--------------------------------------------------------------------------------------------------------------------



*Option 2- Customer Use WebServer*

![option2.png](https://bitbucket.org/repo/dBgzdj/images/635764323-option2.png)

*Pros*

* Only web server running on port 443 is exposed to the outside world.
* Similar to any existing deployments
* Customers already used to configuring webservers

*cons*

* Cannot scale without a load balancer (Discussed as next option)



--------------------------------------------------------------------------------------------------------------------



*Option 3- Customer Use WebServer and LoadBalancing in MultiNode*

![option3.png](https://bitbucket.org/repo/dBgzdj/images/3723974926-option3.png)

*Pros*

* Only web server running on port 443 is exposed to the outside world.
* Can scale by adding one more host machine and installing the RMS stack and adding the new node to Load Balancer
* Similar to any existing deployments
* Customers already used to configuring webservers and load balancers

*Cons*

* Need to make sure that viewer is sticky (Similar to existing installation environments)

--------------------------------------------------------------------------------------------------------------------


*Option 4- Customer Use WebServer and LoadBalancing in SingleNode*

![option4.png](https://bitbucket.org/repo/dBgzdj/images/4229326056-option4.png)

*Pros*

* Only web server running on port 443 is exposed to the outside world.
* Suitable when they use one big host server
* Easy scaling for the customers
* Extra node not required

*Cons*

* Customers need to monitor container ip and configure their load balancer server.

--------------------------------------------------------------------------------------------------------------------



*Option 5- RMS ships WebServer as a container*

![option5.png](https://bitbucket.org/repo/dBgzdj/images/3254202058-option5.png)

*Pros*

* Only web server running on port 443 is exposed to the outside world.
* Suitable for POC and Single stack deployments
* Customers need not spend effort on web server

*Cons*

* Cannot scale without the webserver doing the load balancing also


--------------------------------------------------------------------------------------------------------------------



*Option 6-RMS provides WebServer and LoadBalancer for Multiple
Instances of containers*

![option6.png](https://bitbucket.org/repo/dBgzdj/images/1695246525-option6.png)

*Pros*

* Only web server running on port 443 is exposed to the outside world.
* Customers need not setup loadbalancer
* Can scale up individual instance by modifying the number of replicas


*Cons*

* Since docker currently not supporting sticky sessions, needs extra development effort to automate the load balancer as and when new containers spins up.

--------------------------------------------------------------------------------------------------------------------


# Decision Taken

After the discussion on Feb 9th 2018 with Keng, QA and the client teams, it is decided that we will go with option option 4 in the first release:

**_Customer manages WebServer and LoadBalancing and deploy multiple instances of containers on a single Host Node._**

In future, we do want to ship our own Load Balancer + Web Server(Option 6) for customers who are looking for an out of the box solution from NextLabs.