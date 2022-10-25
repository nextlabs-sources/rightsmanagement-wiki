Certificate on RMTEST is created with letsencrypt(https://certbot.eff.org/#centos6-other).

The steps for creating/renewing certificate are listed below.

#Installing CertBot
cd /opt/certbot  
wget https://dl.eff.org/certbot-auto  
chmod a+x certbot-auto  


#Generating Cert
./certbot-auto certonly --standalone -d rmtest.nextlabs.solutions

#Importing to JKS
cd /etc/letsencrypt/live/rmtest.nextlabs.solutions  
openssl pkcs12 -export -in fullchain.pem -inkey privkey.pem -out pkcs.p12 -name rms -password pass:123next!  
/opt/jdk1.8.0_101/bin/keytool -importkeystore -deststorepass 123next! -destkeypass 123next! -destkeystore /opt/tomcat/conf/keystore.jks -srckeystore pkcs.p12 -srcstoretype PKCS12 -srcstorepass 123next! -alias rms  
rm -f  pkcs.p12  


#Renewing Certs
service tomcat stop  
/opt/certbot/certbot-auto renew  
cd /etc/letsencrypt/live/rmtest.nextlabs.solutions  
openssl pkcs12 -export -in fullchain.pem -inkey privkey.pem -out pkcs.p12 -name rms -password pass:123next!  
/opt/jdk1.8.0_101/bin/keytool -importkeystore -deststorepass 123next! -destkeypass 123next! -destkeystore /opt/tomcat/conf/keystore.jks -srckeystore pkcs.p12 -srcstoretype PKCS12 -srcstorepass 123next! -alias rms  
rm -f  pkcs.p12  
service tomcat start

#Changing admin email
certbot-auto register --update-registration --email new_email@example.com