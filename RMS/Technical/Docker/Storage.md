# Docker Persistent Data Storage Options  

[Reference](https://docs.docker.com/storage/)  

Docker currently supports two types of mounts for persistent data storage from containers.   

![types-of-mounts.png](https://bitbucket.org/repo/dBgzdj/images/886637935-types-of-mounts.png)
  

For portability and security reasons, it is preferred to use volume mounts over bind mounts. More information is available on the [Docker volume mounts page](https://docs.docker.com/storage/volumes/).  

The page also describes the process of using a volume driver to share the persistent store across docker containers.