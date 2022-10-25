# Servers and tenants

## Production servers

 |                         | *Router 1*   | *viewer 1* | *viewer 2*  | *RMS 1*     | *RMS 2*    | Database |
 |-------------------------|--------------|------------|-------------|-------------|------------|----------|
 | IP                      |    Contact althaf.ashraf@nextlabs.com or chao.feng@nextlabs.com to get the IP|
 | DNS                     | r.skydrm.com | viewer.skydrm.com |      | www.skydrm.com |         |          |
 | CPU                     | 8            | 8          | 8           | 8           | 8          | 4        |
 | Memory                  | 15G          | 15G        | 15G         | 15G         | 15G        | 15G      |
 | Disk                    | 100G         | 100G       | 100G        | 100G        | 100G       | 100G     |
 | File handler limit      |              |            |             |             |            |          |
 | tomcat connection limit | 300          | 300        | 300         | 300         | 300        |          |
 | jdbc connection pool    | 500          |            |             | 500         | 500        |          |
 | JVM heap size           |              |            |             |             |            |          |
 | Postgres max connection |              |            |             |             |            |1633      |

## Staging servers

 |                         | *Router 1*    | *viewer 1* | *viewer 2*  | *RMS 1*     | *RMS 2*    | Database |
 |-------------------------|---------------|------------|-------------|-------------|------------|----------|
 | IP                      | 54.86.246.211 |            |             |             |            |          |
 | DNS                     | r.testdrm.com | viewer.testdrm.com |     | www.testdrm.com |        |          |
 | CPU                     | 8             | 8          | 8           | 8           | 8          | 4        |
 | Memory                  | 15G           | 15G        | 15G         | 15G         | 15G        | 15G      |
 | Disk                    | 100G          | 100G       | 100G        | 100G        | 100G       | 100G     |
 | File handler limit      |               |            |             |             |            |          |
 | tomcat connection limit | 300           | 300        | 300         | 300         | 300        |          |
 | jdbc connection pool    | 500           |            |             | 500         | 500        |          |
 | JVM heap size           |               |            |             |             |            |          |
 | Postgres max connection |               |            |             |             |            |1633      |

## Integration test servers

Router, RMS and Postgres database are installed on the same machine: rmtest.nextlabs.solutions

Router: https://rmtest.nextlabs.solutions/router

RMS: https://rmtest.nextlabs.solutions/rms

 |                         | *Router / viewer / RMS/ Database* |
 |-------------------------|-----------------------------------|
 | IP                      | 54.208.120.56                     |
 | DNS                     | rmtest.nextlabs.solutions         |
 | CPU                     | 2                                 |
 | Memory                  | 4G                                |
 | Disk                    | 30G                               |
 | File handler limit      | 8192                              |
 | tomcat connection limit | 2000                              |
 | jdbc connection pool    | 1000                              |
 | JVM heap size           | 2G                                |
 | Postgres max connection | 2000                              |


## Router url

 |            |  *URL*                             |
 |------------|---------------------------------------|
 | Production | https://r.skydrm.com/router           | 
 | Staging    | https://r.testdrm.com/router     | 
 | testing    | https://rmtest.nextlabs.solutions/rms | 


## Tenants login url

 |            |  *Skydrm*                             |   *jt2go*                                   |
 |------------|---------------------------------------|---------------------------------------------|
 | Production | https://www.skydrm.com/rms            | https://jt2go.skydrm.com/rms                |
 | Staging    | https://www.testdrm.com/rms           | https://jt2go.testdrm.com/rms                 |
 | testing    | https://rmtest.nextlabs.solutions/rms | https://jt2go.rmtest.nextlabs.solutions/rms |

