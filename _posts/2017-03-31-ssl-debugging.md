---
layout: post
title: "SSL Debugging"
date: 2017-03-31 12:26:06.000000000 +02:00
date: 2017-10-11 11:53:57
categories: linux mac
---
Show server's ssl certs:

```bash
openssl s_client -showcerts -connect 127.0.0.1:8444
```

Get certificate (from [here](https://confluence.atlassian.com/jira/connecting-to-ssl-services-117455.html)):


```bash
openssl s_client -connect localhost:8443 &lt; /dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p'
```

UI for certs: [portecle](http://portecle.sourceforge.net/) (see also [frequently used ssl commands](https://shib.kuleuven.be/docs/ssl_commands.shtml))

```bash
# show certs in jks
keytool -keystore myks.jks --list
# export cert from jdk
keytool -keystore myks.jks -exportcert -alias key -file key.der
# show cert info
openssl x509 -noout -text -in key.der -inform der
# convert der to pem
openssl x509 -inform der -in key.der -out key.pem
# test a server ssl
openssl s_client -connect 172.17.0.1:8444 -prexit -cert key.pem
```

### Troubleshooting

1. The **Signature does not match** error is the symptom that the server identity is unknown to the client, ie the client truststore does not have the server certificate.
2. Add **-Djavax.net.debug=all** to debug the traffic
