# To be done on RMS instances that will be sending out emails #
____

## Step 1: Rename the host ##
    hostnamectl set-hostname --static skydrm.com

## Step 2: Install OpenDKIM ##
    wget -P /tmp http://mirror.pnl.gov/epel/6/i386/epel-release-6-8.noarch.rpm
    rpm -Uvh /tmp/epel-release-6-8.noarch.rpm
    rm -f /tmp/epel-release-6-8.noarch.rpm

    yum install -y opendkim

## Step 2: Setup opendkim.conf ##

### Take backup of opendkim.conf ###
    cp /etc/opendkim.conf{,.orig}


### Update configuration ###
    cat << EoF >> /etc/opendkim.conf

    ##
    # Updated with content from
    # https://www.rosehosting.com/blog/how-to-install-and-integrate-opendkim-with-postfix-on-a-centos-6-vps
    ##
    AutoRestart             Yes
    AutoRestartRate         10/1h
    LogWhy                  Yes
    Syslog                  Yes
    SyslogSuccess           Yes
    Mode                    sv
    Canonicalization        relaxed/simple
    ExternalIgnoreList      refile:/etc/opendkim/TrustedHosts
    InternalHosts           refile:/etc/opendkim/TrustedHosts
    KeyTable                refile:/etc/opendkim/KeyTable
    SigningTable            refile:/etc/opendkim/SigningTable
    SignatureAlgorithm      rsa-sha256
    Socket                  inet:8891@skydrm.com
    PidFile                 /var/run/opendkim/opendkim.pid
    UMask                   022
    UserID                  opendkim:opendkim
    TemporaryDirectory      /var/tmp

    EoF

## Create the opendkim keys ##
    mkdir -p /etc/opendkim/keys/skydrm.com
    opendkim-genkey -D /etc/opendkim/keys/skydrm.com -d skydrm.com -s default
    chown -R opendkim. /etc/opendkim/keys
    mv /etc/opendkim/keys/skydrm.com/default.private /etc/opendkim/keys/skydrm.com/default

IF SETUP IS BEING REPLICATED TO OTHER RMS NODES, COPY CONTENTS OF /etc/opendkim /etc/opendkim FROM FIRST SERVER


## Create opendkim.service systemd configuration ##

    cat << EoF >> /etc/systemd/system/opendkim.service
    [Unit]
    Documentation=man:systemd-sysv-generator(8)
    SourcePath=/etc/rc.d/init.d/opendkim
    Description=LSB: Start and stop OpenDKIM
    Before=runlevel2.target runlevel3.target runlevel4.target runlevel5.target shutdown.target
    After=network-online.target
    Conflicts=shutdown.target

    [Service]
    Type=forking
    Restart=no
    TimeoutSec=5min
    IgnoreSIGPIPE=no
    KillMode=process
    GuessMainPID=no
    RemainAfterExit=no
    PIDFile=/var/run/opendkim/opendkim.pid
    ExecStart=/etc/rc.d/init.d/opendkim start
    ExecStop=/etc/rc.d/init.d/opendkim stop
    ExecReload=/etc/rc.d/init.d/opendkim reload
    User=opendkim
    Group=opendkim

    [Install]
    WantedBy=multi-user.target

    EoF


## Setup opendkim as a system service ##
systemctl enable opendkim.service
systemctl daemon-reload

## Update opendkim Keytable ##
    cat << EoF >> /etc/opendkim/KeyTable

    default._domainkey.skydrm.com skydrm.com:default:/etc/opendkim/keys/skydrm.com/default

    EoF

## Update opendkim SigningTable ##
    cat << EoF >> /etc/opendkim/SigningTable

    *@skydrm.com default._domainkey.skydrm.com

    EoF

## Update opendkim TrustedHosts ##
    cat << EoF >> /etc/opendkim/TrustedHosts
    127.0.0.1
    skydrm.com
    EoF


## Start opendkim service, and check its status ##
    systemctl start opendkim.service
    systemctl status opendkim.service

## Install and configure sendmail: ##
Information excerpted from: https://bitbucket.org/nxtlbs-devops/rightsmanagement-wiki/wiki/RMS/deployment/Sendmail%20Configuration

    cat << EoF >> /etc/mail/sendmail.mc
    INPUT_MAIL_FILTER(`dkim-filter', `S=inet:8891@skydrm.com')
    EoF

    m4 /etc/mail/sendmail.mc > /etc/mail/sendmail.cf

    mkdir /var/spool/mqueue/q{1,2,3,4,5,6,7,8}

    systemctl restart sendmail

----

Add SPF & DKIM record in Route-53

Create a txt record with the key which can be found in /etc/opendkim/keys/testdrm.com/defaults.txt


```
#!python

e.g default._domainkey.testdrm.com    IN      TXT     "v=DKIM1; k=rsa; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCbt5/idQCv9j9PneAX2STvXC+Ki4+Bxj4siAREmZ5On8PeZz9LED6Peenp0Mx0hb2NJrVDamA7Y1NfR2+BsFZKhS/TdMyGtsebqJMhLrNhZNJ74ZrvWW35rZciFjOf+aS6ydBV4Mh289tKcRTlEuGvL0t6QiwhbH0J8lgmly5u7QIDAQAB" )
```