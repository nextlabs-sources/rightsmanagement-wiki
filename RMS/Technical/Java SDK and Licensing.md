[Jira story SC-2298](https://nextlabs.atlassian.net/browse/SC-2298)  
  

## Java SDK Licensing Issues  
  
Oracle does not allow free-of-charge commercial use of Java. Link [here](https://www.oracle.com/technetwork/java/javase/terms/license/javase-license.html).  
According to the license, 
> 
> Oracle grants You a nonexclusive, nontransferable, limited license to internally use the Programs, subject to the restrictions stated in this Agreement and Program Documentation, only for the purpose of developing, testing, prototyping and demonstrating Your Application and not for any other purpose.
>  

Also,

>
> You may not:
> -    use the Programs for any data processing or any commercial, production, or internal business purposes other than developing, testing, prototyping, and demonstrating your Application;
>  
  
Oracle clearly advises customers looking for the free JDK to use the GPL Licensed OpenJDK [here](https://jdk.java.net/11/).


![jdk-free.png](https://bitbucket.org/repo/dBgzdj/images/3998635830-jdk-free.png)
  
  

Oracle clearly states [here](https://java.com/en/download/release_notice.jsp)  
![jdk-free-2.png](https://bitbucket.org/repo/dBgzdj/images/2346839838-jdk-free-2.png)  
  
If we choose to license the SDK from Oracle, the [official pricing is](http://www.oracle.com/us/corporate/pricing/technology-price-list-070617.pdf)  
  
![jdk-commercial-pricing.png](https://bitbucket.org/repo/dBgzdj/images/4070185206-jdk-commercial-pricing.png)  
  
More information [here](https://www.oracle.com/assets/java-se-subscription-pricelist-5028356.pdf)  
  
![jdk-commercial-pricing-2.png](https://bitbucket.org/repo/dBgzdj/images/2211607257-jdk-commercial-pricing-2.png)  
  
## Risk Management Options
  
[OpenJDK](https://openjdk.java.net/)  

* Only JDK 11 and JDK 10 are available as GA releases.
* JDK 8u202 is available as an early access release. No JDK 8 GA releases are available.
  

[Amazon Corretto](https://aws.amazon.com/blogs/opensource/amazon-corretto-no-cost-distribution-openjdk-long-term-support/). This is Amazon's LTS supported version of the JDK, free of charge.  
  
* Only Java 1.8.0_192 is available, as a preview release. 
* No other versions are available.
* Supported platforms are Amazon Linux 2, Windows 7 or later, MacOS 10.10 or later, and Docker 
* Runs on the OpenJDK JVM by default, but can be configured to work with the OpenJ9 VM


[Azul Zulu](https://www.azul.com/products/zulu-enterprise/) Open source, no license fee.  

* Java 6 through 11 are available for Linux, Windows and Mac. 
* Azul builds their own Zulu JVM


[Adopt Open JDK](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)  

* Supported platforms are : Linux x64, Linux x64 large heap, Windows x32 and x64, Linux s390, and AIX.
* OpenJDK versions are 8 LTS, 9, 10 and 11 LTS. 
* HotSpot (OpenJDK) and IBM / Eclipse OpenJ9 JVMs are supported.

[RHEL OpenJDK](https://access.redhat.com/articles/1299013)  

* Supports the OpenJDK JVM.
* Can be configured to work with other JVMs.
* JDK 8 and 11 are available. JDK 9 and 10 are skipped. 

[SAP Machine](https://sap.github.io/SapMachine/) an OpenJDK version supported and maintained by SAP  
  
## Recommentations

Build our own OpenJDK (8.X) from source (Thanks Kent for the suggestion). Instructions [here](http://cr.openjdk.java.net/~ihse/demo-new-build-readme/common/doc/building.html)
  
We can also consider the Adopt Open JDK or the Azul Zulu JDK.  
These are the more mature and widely supported distributions with JDK 1.8.  
  
Evaluate them for compatibility, integration with our build & validation process, performance, security, support on enterprise and SaaS.  

## References  
[Do I need to pay for Java now?](https://dev.karakun.com/java/2018/06/25/java-releases.html)  
[RHEL OpenJDK versions](https://access.redhat.com/articles/1299013)  
[Time to look beyond Oracle's JDK](https://blog.joda.org/2018/09/time-to-look-beyond-oracles-jdk.html?m=1)  
[Java is still free](https://medium.com/@javachampions/java-is-still-free-c02aef8c9e04)  
[Eliminating the Java update confusion](https://www.azul.com/eliminating-java-update-confusion/)



# Minutes of Discussion on 18/01/2019 #

There are 2 different aspects here:

1. All source stays at 7 but we deploy on openJDK 8/9/10/11 - this is supposed to work by design as Java is backward compatible, meaning that code compiled by 7 should work on JVMs 8 (we do this now already while compiling+deploying using 8), 9, 10, or 11 with very few exceptions (listed on Oracle site). This will primarily solve the license issues and is part of https://nextlabs.atlassian.net/browse/SC-2374
2. All or parts of source move to at least Java 8 (if not further) for the benefit of unlocking JLS 8 features for developers - the dependency is implicitly consumers of the RMSDK which is/can be potentially used by SmartClassifier, Teamcenter, SAP, any other EDRM partner who might need to run on JRE 7. This was tested via https://bitbucket.org/nxtlbs-devops/rmsng/pull-requests/2481

Its possible to upgrade the bulk of the source to JLS 8 while leaving some specific parts (like rmjavasdk-ng, shared.jar) at srcCompatibility=7. Another possibility is to check with potential consumers/PS and ask if any potential customers would only deploy on 8+ (this exercise was done last year and TC asked for sdk to be at 7 - refer mail with Chenwei/Sarita).

Also discussed whether can build with JDK 11 with gradle srcCompatibility = 7 and targetCompatibility=11 (so we add no new JLS features in code - so no benefit to dev), and then check if it can be deployed in JRE 11) - under investigation, but no use as no new features unlocked.

# OpenJDK Comparison study between different vendors #

**Oracle is currently not planning to support GPL License versioned JDK 11(OpenJDK) once the next version of JDK is released which means "No backporting of security patches and bug fixes for JDK 11". **

After carefully evaluating different vendors, we have taken the decision to go for [AdoptOpenJDK](https://adoptopenjdk.net/). The rationale behind choosing AdoptOpen JDK is "Its free and provides Long term support."

![javavendors.PNG](https://bitbucket.org/repo/dBgzdj/images/2602735241-javavendors.PNG)

![javacomparison.png](https://bitbucket.org/repo/dBgzdj/images/3845574113-javacomparison.png)

Note: We are using HotSpot JVM from AdoptOpen JDK.
  
  
The Oracle / Java support roadmap is available at [this link](https://www.oracle.com/technetwork/java/java-se-support-roadmap.html)
  
![support.png](https://bitbucket.org/repo/dBgzdj/images/3390769478-support.png)