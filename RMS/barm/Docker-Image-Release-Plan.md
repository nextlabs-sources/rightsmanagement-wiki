**SkyDRM 8.X Release Process**

![SkyDRM 8.X Release Process](https://bitbucket.org/repo/dBgzdj/images/929410245-RMS-8.X-release-process.png)

* Jenkins generates installers
* After QA validation, release management process is executed
    * Installers published to release area (\\nextlabs.com\share\data\releases)
* Support team uploads installers from release area to customer download portal
* Customers download installers from download portal

--------------------------------------------------------------------------------------------------------------------

**SkyDRM SaaS Release Process**

![SkyDRM SaaS Release Process](https://bitbucket.org/repo/dBgzdj/images/2392129639-RMS-SkyDRM-SaaS-AWS-release-process.png)

* Jenkins generates installers
* QA validation is performed
* Release management process is executed
    * Installers published to release area (\\nextlabs.com\share\data\releases)
* DevOps team performs refresh of SkyDRM on AWS
* Customer accesses SkyDRM from supported clients

--------------------------------------------------------------------------------------------------------------------

**SkyDRM Container Release Process - Proposal**

![SkyDRM Container Release Process - Proposal](https://bitbucket.org/repo/dBgzdj/images/3087999072-RMS-SkyDRM-Docker-ECR-release-process.png)

* Jenkins generates SkyDRM Docker images
    * Images exported as tar archives
    * Placed in Nextlabs share (\\nextlabs.com\share\data\release_candidate)
* For NextLabs internal consumption only
* QA validates Docker images
* After QA validation, release management process is executed
    * Docker images pushed to Amazon ECR
* Registered customers can pull SkyDRM Docker images from Amazon ECR

--------------------------------------------------------------------------------------------------------------------

**SkyDRM ECR Docker Images – Access Control**

* Support team raises DevOps ticket to create AWS account for customer
* DevOps team creates AWS account for customer
    * Programmatic access only (no console access)
    * AWS Access token generated (to be shared with customer)
    * ECR only access policy attached to AWS account 
        * Customers will not have access to any other AWS services in NextLabs’ production account
* No version specific access control for customers
    * once they get access to SkyDRM, they’ll have access to future releases as well, unless we revoke their AWS credentials
* Customer downloads SkyDRM-setup-xxxxxx.zip from customer download portal
* Customer executes the setup.sh to generate the required SkyDRM configuration file
* Customer configures the AWS ECR credentials in their Docker environment
* Customer uses the out-of-the-box docker-compose.yml file to deploy the SkyDRM stack
    * Customers can modify the docker-compose file based on their infrastructure needs
    * Docker downloads the required images from AWS ECR and brings up the SkyDRM stack

--------------------------------------------------------------------------------------------------------------------

# Decision Taken

After discussion with Keng on Mar 6th 2018, it is decided that all licensed customers will have complete access to all supported versions (N & N-1) of SkyDRM Docker images