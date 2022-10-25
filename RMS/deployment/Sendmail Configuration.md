## Sendmail Configuration
The Sendmail program is a very widely used Mail Transport Agent (MTA). MTAs send mail from one machine to another. Sendmail is not a client program, which you use to read your email. Sendmail is a behind-the-scenes program which actually moves your email over networks or the Internet to where you want it to go.



## Installation
Take centos system as example

### Install dependent package: 
```
# yum install m4 telnet mailx
```
### Check if sendmail is installed or not: 
```
# rpm -qa | grep sendmail
```
### If it is not installed, then install the sendmail package using following command: 
```
# yum install sendmail sendmail-cf
```

## Configuration
### Basic Configuration
By default sendmail configuration files are located at /etc/mail

By default sendmail listen on local interface i.e 127.0.0.1, check using this command:
```
# ps -ef | grep -v grep | grep -i sendmail
   root      3595     1  0 00:20 ?        00:00:00 sendmail: accepting connections
   smmsp     3604     1  0 00:20 ?        00:00:00 sendmail: Queue runner@01:00:00 for /var/spool/clientmqueue

# netstat -an | grep :25 | grep tcp
   tcp        0      0 127.0.0.1:25                0.0.0.0:*                   LISTEN

```
To configure the sendmail to listen to all interface at the host, just comment the following line in “sendmail.mc”:
```
# vi /etc/mail/sendmail.mc
   From:
   DAEMON_OPTIONS(`Port=smtp,Addr=127.0.0.1, Name=MTA')dnl

   To
   dnl DAEMON_OPTIONS(`Port=smtp,Addr=127.0.0.1, Name=MTA')dnl
```
Now we need to build the “sendmail.cf” file using m4 macro:
```
# m4 /etc/mail/sendmail.mc > /etc/mail/sendmail.cf
```
Restart Sendmail Service:
```
# service sendmail restart
```
Check using this command:
```
# netstat -an | grep :25 | grep tcp
   tcp        0      0 0.0.0.0:25                  0.0.0.0:*                   LISTEN
```
Test sending email using sendmail:
```
echo "test sendmail" | sendmail your-test-email-address
```
You can check the Maillog "/var/log/maillog" in case of any issue
```
# tail /var/log/maillog
```
You can also check the mail queue by command:
```
# mailq
```
Some times the port for sending mail might be blocked, then you need set an additional iptable to open firewall port:
```
# iptables -A INPUT -p tcp -m tcp -dport 25 -j ACCEPT
```
then save the Iptables rules and restart it:
```
# service iptables save
# service iptables restart
```
### Advanced Configuration
#### Use multiple queues

Sendmail will use by default a single mail queue. But for high traffic mail servers it might be useful to split the queue over several directories, as thousands of files in a single directory will become a performance penalty at some point and also processing the queue sequentially will become very slow. 
Assuming we want to use 8 mail queues. First thing is to create the actual directories as sendmail will not do this by default:
```
# mkdir /var/spool/mqueue/q{1,2,3,4,5,6,7,8}
```
And fix the permissions to the ones of the original folder /var/spool/mqueue. For ex. this might look like:
```
# chown -R smmta:smmsp /var/spool/mqueue/q*
```
Next, we need to enable the multiple queues in the sendmail configuration. For this we will edit sendmail.mc (normally found under /etc/mail) and append one line:
```
define(`QUEUE_DIR', `/var/spool/mqueue/q*')dnl
```
After restarting sendmail, it will start using the multiple queues we defined. Running mailq will output each of the queues
```
mailq
/var/spool/mqueue/q6 is empty
/var/spool/mqueue/q4 is empty
/var/spool/mqueue/q3 is empty
/var/spool/mqueue/q2 is empty
/var/spool/mqueue/q5 is empty
/var/spool/mqueue/q1 is empty
/var/spool/mqueue/q7 is empty
/var/spool/mqueue/q8 is empty
Total requests: 0
```
#### Performance Tuning
Several important parameters for performance tuning:
```
define(`confSEPARATE_PROC', `False')dnl
```
Forking During Queue Runs:
By setting the ForkEachJob ( Y) option, sendmail will fork before each individual message while running the queue. This option was used with earlier releases to prevent sendmail from consuming large amounts of memory. It should no longer be necessary with sendmail 8.12. If the ForkEachJob option is not set, sendmail will keep track of hosts that are down during a queue run, which can improve performance dramatically.

If the ForkEachJob option is set, sendmail cannot use connection caching.
```
define(`confMAX_QUEUE_CHILDREN', `5')dnl
```
Limits the maximum number of concurrent queue runners active. This is to keep system resources used within a reasonable limit. Relates to Queue Groups and ForkAllJobs.

Queue Runner:

One can gain effective parallelism from multiple queue runners. When sendmail starts, one parameter like -q30m can be given to it on command line. This parameter indicates that every 30 minutes master sendmail daemon will fork a child process; the child process scans the entire queue looking for messages to deliver. With multiple queues, one queue runner process exists per queue directory.
```
define(`confMAX_DAEMON_CHILDREN', 20)dnl
define(`confQUEUE_LA', `18')dnl
define(`confREFUSE_LA', `24')dnl
```
The first setting instructs Sendmail to have at most 20 processes running at any one time. The next two determine at what system load levels Sendmail only queues up E-mail for later processing (confQUEUE_LA) and at what load level it rejects E-mail entirely. These two are useful in preventing a flood of spam from bringing your server to its knees.
```
define(`confTO_CONNECT', `30s')dnl
define(`confTO_IDENT', `0')dnl
define(`confTO_COMMAND', `1m')dnl
```
These set the timeouts for the initial connection and in between commands to be 30 seconds and 1 minute respectively. It also disables checking for ident information by setting it to zero. This can easily bog down a server as it takes awhile for the ident check to timeout and most servers aren't running it these days.

```
Some timeout values:

define(`confTO_MAIL', `30s')dnl //The timeout waiting for a response to the MAIL command.
define(`confTO_RSET', `30s')dnl //The timeout waiting for a response to the RSET command.
define(`confTO_QUIT', `30s')dnl //The timeout waiting for a response to the QUIT command.
define(`confTO_CONTROL', `30s')dnl //The timeout for a complete control socket transaction to complete.
define(`confTO_DATAINIT', `30s')dnl //The timeout waiting for a 354 response from the DATA command.
define(`confTO_CONNECT', `30s')dnl //The timeout waiting for an initial connect() to complete. This can only shorten connection timeouts; the kernel silently enforces an absolute maximum (which varies depending on the system).
define(`confTO_ICONNECT', `30s')dnl //Like Timeout.connect, but applies only to the very first attempt to connect to a host in a message. This allows a single very fast pass followed by more careful delivery attempts in the future.
define(`confTO_ACONNECT', `30s')dnl //The overall timeout waiting for all connection for a single delivery attempt to succeed. If 0, no overall limit is applied.
define(`confTO_INITIAL', `30s')dnl //The timeout waiting for a response on the initial connect.

These values are trival, it's not clear how much influence will be caused by them.
```
```
define(`confCONNECTION_RATE_THROTTLE', `15')dnl 
```
The maximum number of connections permitted per second per daemon. After this many connections are accepted, further connections will be delayed. If not set or <= 0, there is no limit.
```
dnl # define(`confBAD_RCPT_THROTTLE',`3')dnl
```
 If set and the specified number of recipients in a single SMTP transaction have been rejected, sleep for one second after each subsequent RCPT command in that transaction.
```
define(`confTO_QUEUERETURN', `12h')dnl //The timeout before a message is returned as undeliverable. helps reduce the size of queue
define(`confTO_QUEUEWARN', `1h')dnl //The timeout before a warning message is sent to the sender telling them that the message has been deferred. reduce number of messages
sent
```