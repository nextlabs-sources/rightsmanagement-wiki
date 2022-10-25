# RMC Java Client

RMC java client is pack as a single executable jar file. It can run in Both Grahpic and command line mode. 

## Download

You can download latest jar file: [rmc.jar](rmc.jar)

## Requirement

JCE Unlimited Strength policy must be installed to your JRE.

for Java 7: http://www.oracle.com/technetwork/java/javase/downloads/jce-7-download-432124.html

for Java 8: http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html 

## Logs and Config

RMC Java Client will store config and logs in a folder named `.rmc` under user's home folder. You might need delete this folder sometime if config files are corrupted due to bugs in earlier version.

## RMC API Simulator (Graphic mode)

If you double click on the `rmc.jar` or execute `java -jar rmc.jar` without any parameter, Graphic mode will be launched. You can view detailed API protocol in this simulator.

In "Settings" menu, you can configure which Central Router and RMS the client want to connect.
With "New Client" menu, you can simulator up to 5 independent clients. Each client can belongs to different tenant.

## Command Line

RMC Java Client use standard POSIX style command line arguments. Most of parameter support both long name and short name.

You can pass "--help | -h" parameter to show help in command line:

```
$ java -jar rmc.jar -h
usage: java -jar rmc.jar [options]
-c,--cmd    Supported commands: LoadTest | Encrypt | Decrypt
```

### Load Test

```
$ java -jar rmc.jar --cmd=LoadTest -h
usage: java -jar rmc.jar --cmd LoadTest
-cc,--clientCount <arg>   number of clients, default: 100
   --clean                Clean cached clients, default: false
   --delay <arg>          request delay in milliseconds, default: 0
   --duration <arg>       Max duration in seconds, default: 600
-h,--help                 help
-m,--requestLimit <arg>   Requests per client, default: 1000
```

Each RMC client will cache settings on disk, the settings include:

- user_id
- ticket
- membership DH key pair and iCA issued certificate
- resolved tenant's RMS server url
- heartbeat items checksum

use **--clean** option to clean local cache if cached data is corrupted or incompatible with new version.

### Encrypt

```
$ java -jar rmc.jar --cmd=Encrypt -h
usage: java -jar rmc.jar -c Encrypt
-d,--destination <arg>   destination
-h,--help                help
-s,--src <arg>           src
```

### Decrypt

```
$ java -jar rmc.jar --cmd=Decrypt -h
usage: java -jar rmc.jar -c Decrypt
-d,--destination <arg>   destination
-h,--help                help
-s,--src <arg>           src
```




