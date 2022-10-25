# PCV Builds for non-master branches  
    
## Token Group: Architecture Update  
  
There is a significant change in the SkyDRM server design in April-May 2019. The changes are in a separate branch in BitBucket:   
[https://bitbucket.org/nxtlbs-devops/rmsng/branch/architecture_update_token_group](https://bitbucket.org/nxtlbs-devops/rmsng/branch/architecture_update_token_group)  
   
Code from this branch is built through a new Jenkins job created for PCV builds:  
[Project PCV_RMSNG-master-nightly](http://nxt-build16.nextlabs.com:9031/job/PCV_RMSNG-master-nightly/)  
  
The build output goes to the PCV directory on the build server at [/pcv/SecureCollaboration/10.7](http://nxt-build18-apache.nextlabs.com:9052/pcv/SecureCollaboration/10.7/) instead of the [release candidate directory](http://nxt-build18-apache.nextlabs.com:9052/release_candidate/SecureCollaboration/10.7/).  
  
Code from the [master branch](https://bitbucket.org/nxtlbs-devops/rmsng/branch/master) will continue to be built using the old Jenkins build job at [RMSNG-master-nightly](http://nxt-build16.nextlabs.com:9031/job/RMSNG-master-nightly/) whose artifacts are output to the [/release_candidate/SecureCollaboration/10.7](http://nxt-build18-apache.nextlabs.com:9052/release_candidate/SecureCollaboration/10.7/) directory on the build18 server.  
  
## Generic PCV builds  
  
In general, it is agreed that the builds for prototyping and concept verification should produce outputs in the `/pcv/...` directory on the build18 server.  
  
Any future new branch will require a new Jenkins build job to ensure that the prototype code does not touch the artifacts from the master branch.