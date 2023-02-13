+++
title = "SSL and Let's Encrypt For Jenkins"
date = "2023-02-13T09:57:31+0000"
authors = ["kn"]
cover = "posts/SSL-LetsEncrypt-jenkins/cover.png"
tags = ["Jenkins", "ssl", "https", "certbot"]
keywords = ["Jenkins", "ssl", "https", "certbot"]
description = "Enabling HTTPS on a Jenkins server by configuring SSL"
showFullContent = false
+++

## Jenkins (standalone) SSL + Let's Encrypt

In this tutorial, I will show how to use Let's Encrypt free SSL with a standalone Jenkins in CentOS 7 AWS

### Install Certbot on AWS EC2 CentOS

```bash
amazon-linux-extras install epel
yum install certbot python2-certbot-apache mod_ssl
```

### Generate Certificates

Run the command to generate the certificate and key files.

```bash
sudo certbot certonly --standalone --preferred-challenges http -d example.com
```

**Just in case you got this response:**

```
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator standalone, Installer None
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for example.com
Cleaning up challenges
Problem binding to port 80: Could not bind to IPv4 or IPv6.
```
You need to stop your web server and try again.

### Convert the certificate to JKS keystore

Go to your certificate folder

```bash
cd /etc/letsencrypt/live/example.com
```

Convert the certificate to PKCS12 file:

```bash
openssl pkcs12 -inkey privkey.pem -in fullchain.pem -export -out keys.pkcs12
```

*If you are renewing the certificates, make sure to delete the existing /var/lib/jenkins/jenkins.jks file first.*

Then convert to JKS file

```bash
keytool -importkeystore -srckeystore keys.pkcs12 -srcstoretype pkcs12 -destkeystore /var/lib/jenkins/jenkins.jks
```

Enter export and import passwords and answer "yes" if asked to overwrite an existing alias

```
Enter Export Password:
Verifying - Enter Export Password:
root@example:/etc/letsencrypt/live/example.com# keytool -importkeystore -srckeystore keys.pkcs12 -srcstoretype pkcs12 -destkeystore /var/lib/jenkins/jenkins.jks
Importing keystore keys.pkcs12 to /var/lib/jenkins/jenkins.jks...
Enter destination keystore password:  
Enter source keystore password:  
Existing entry alias 1 exists, overwrite? [no]:  yes
Entry for alias 1 successfully imported.
Import command completed:  1 entries successfully imported, 0 entries failed or cancelled
```

The /var/lib/jenkins/ path is accessible to the jenkins user by default. However, **we have to change the owner of our .jks file:**

```bash
sudo chown jenkins:jenkins /var/lib/jenkins/jenkins.jks
```

### Configure Jenkins for SSL Communication

Older method is changing environment variable in `/etc/default/jenkins` but **this method is depricated**

So in order to set up Jenkins with SSL, we need to use an HTTPS keystore, an HTTPS port, and a password with `systemd`. 


To list available service option try:

```bash
systemctl cat jenkins
```

To override these settings, 

```bash
systemctl edit jenkins
```

to set each:

```bash
[Service]
Environment="JENKINS_HTTPS_PORT=8443"
Environment="JENKINS_HTTPS_KEYSTORE=/var/lib/jenkins/jenkins.jks"
Environment="JENKINS_HTTPS_KEYSTORE_PASSWORD=this-is-your-secure-password"
```

At this point, HTTPS is set up in Jenkins.

### Restart the Jenkins Service
So far, we've made all the changes to the configuration. To apply them, we reload the daemon and restart Jenkins:

```bash
sudo systemctl daemon-reload
sudo systemctl restart jenkins.service 
```
Now, our SSL certificate is active for the Jenkins server. Hence, HTTPS is up and running, securing our data.


## Automated Let's encrypt + Jenkins ssl renewal script with cron

1. Create the script

Create a file named `renew-ssl-jenkins.sh` anywhere accessible and enter the code below:

```bash
#!/bin/bash

SSLPASS=This-is-a-secure-password

certbot renew || true

cd /etc/letsencrypt/live/mysite.com
rm /var/lib/jenkins/jenkins.jks
openssl pkcs12 -inkey privkey.pem -in fullchain.pem -export -out keys.pkcs12 -password pass:$SSLPASS || true
keytool -importkeystore -srcstorepass $SSLPASS -deststorepass $SSLPASS -noprompt -v -srckeystore keys.pkcs12 -srcstoretype pkcs12 -destkeystore /var/lib/jenkins/jenkins.jks || true

sudo service jenkins restart || true
```

2. Execute the script every month using cron

```bash
crontab -e
```

```cron
0 1 1 * * /root/renew-ssl-jenkins.sh > /var/log/renew-ssl-jenkins.log
```
