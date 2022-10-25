## Download Gerrit Code Review
Download latest war file from: https://gerrit-releases.storage.googleapis.com/index.html
For example:

```
curl 'https://gerrit-releases.storage.googleapis.com/gerrit-2.12.2.war' > gerrit-2.12.2.war
```

## Install Gerrit Code Review

```
$ sudo mkdir /usr/share/gerrit
$ sudo chown gerrit2:gerrit2 /usr/share/gerrit

$ sudo adduser gerrit2
$ sudo su gerrit2
$ java -jar gerrit.war init -d /usr/share/gerrit
```
For more detail see: https://gerrit-documentation.storage.googleapis.com/Documentation/2.12.2/install.html

## Upgrade Gerrit Code Review
copy gerrit-2.12.2.war file to /usr/share/gerrit/bin

```
$ cp gerrti-2.12.2.war /usr/share/gerrit/bin/gerrit.war
$ java -jar /usr/share/gerrit/bin/gerrit.war init -d /usr/share/gerrit
$ java -jar /usr/share/gerrit/bin/gerrit.war reindex -d /usr/share/gerrit
```
