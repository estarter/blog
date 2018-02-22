---
layout: post
title: "SSL Debugging"
date: 2017-03-31 12:26:06
date: 2017-10-11 11:53:57
categories: linux mac
---
Show server's ssl certs:

```bash
openssl s_client -showcerts -connect 127.0.0.1:8444
```

Get certificate (from [here](https://confluence.atlassian.com/jira/connecting-to-ssl-services-117455.html)):

```bash
openssl s_client -connect localhost:8443 < /dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p'
```
Save the content from `BEGIN CERTIFICATE` to `END CERTIFICATE` inclusive to the `server.crt` file.
Then you can convert it to the jks file:

```bash
$ cat server.crt # an example how the file should look like
-----BEGIN CERTIFICATE-----
MIIDdzCCAl+gAwIBAgIEEoaVPzANBgkqhkiG9w0BAQsFADBsMRAwDgYDVQQGEwdV
bmtub3duMRAwDgYDVQQIEwdVbmtub3duMRAwDgYDVQQHEwdVbmtub3duMRAwDgYD
VQQKEwdVbmtub3duMRAwDgYDVQQLEwdVbmtub3duMRAwDgYDVQQDEwdVbmtub3du
MB4XDTE4MDIyMTE1MDU0MFoXDTI4MDIxOTE1MDU0MFowbDEQMA4GA1UEBhMHVW5r
bm93bjEQMA4GA1UECBMHVW5rbm93bjEQMA4GA1UEBxMHVW5rbm93bjEQMA4GA1UE
ChMHVW5rbm93bjEQMA4GA1UECxMHVW5rbm93bjEQMA4GA1UEAxMHVW5rbm93bjCC
ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBALykWgm+XOINMD8ZVYQbABj5
aJcRErGqmLIg2wLH3IL8kjjFSrslmc/QcoYN+/NUp1HtPcM+mGbn++h4MkUwiTJR
G92sgO+ZYoB/hwRL0uyXO5srBQ4r0Uv6lcQlcSBa3pJoHfM/LEF9nsGQjzHrnvtS
muwgUTb/lCTQyONg44WORVnTHS09yMliXpfKMTq5vkev5DXYGy/lzDFBJ6eHWN/x
SF0zWD2Yb1OylkBBghYl+/+WDWNUmeLeW+gACgKvhm13gED+RajEFIt3T9FVakJI
BE+jMdDkwh+sHwYBQey81fBc6MkcsJ/zzyrq3/lBhDH9XttROenIrPNrMwARZi8C
AwEAAaMhMB8wHQYDVR0OBBYEFAXzn1lYd1vW2FNCe+WQmc/utFbnMA0GCSqGSIb3
DQEBCwUAA4IBAQAJzC4vMt2fufbkPDSSqePSxgl3ddfAhEq4UdNyRriTbWJwFtdF
bDScrc3EbIS00duspKkMj2PbM/pM3mqRfXc1RavQpMBTSW/yR3fTjOy+RcoNSnVX
RXmnlE57hRfh+vALd5v2vrI51l+nOK3qn6nLY9+uJzJ/ZTb+JY+bWuU+0EST1Kyk
0Mi0GC9faXBEKZqJd/NLCuzFfE8fn2YN9FZC/1pxxPo0NoHGo1KX18t99GePIzzT
Ot4+kQZ7KhrYzcT3eO/9VryFVAbj+qRgu6yLU9U1mSX6/ZqaLr9FQma+P26jVsjC
CQS+77jHXzGTw/Yg5YWwkf47guhzJvk2GjAr
-----END CERTIFICATE-----

$ keytool -genkey -keyalg RSA -keysize 2048  -alias localhost -keypass CHANGEME -dname "CN=Unknown, OU=truststore, O=Unknown, L=Unknown, ST=Unknown, C=Unknown" -keystore truststore.jks -storepass CHANGEME
$ keytool -list -keystore truststore.jks -storepass CHANGEME # shows content of jks -> original key is there
$ keytool -delete -alias localhost -keystore truststore.jks -storepass CHANGEME
$ keytool -list -keystore truststore.jks -storepass CHANGEME # jks is empty
$ keytool -import -alias SERVER_NAME -keystore truststore.jks -file server.crt -storepass CHANGEME -noprompt
$ keytool -list -keystore truststore.jks -storepass CHANGEME # jks contains server's certificate

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
2. Add **-Djavax.net.debug=ssl** or **-Djavax.net.debug=all** to debug the traffic
3. Add custom private certificate or truststore using java properties:

   -Djavax.net.ssl.keyStoreType=pkcs12 -Djavax.net.ssl.keyStore=clientcertificate.p12 -Djavax.net.ssl.keyStorePassword=CHANGEME
   
   -Djavax.net.ssl.trustStoreType=jks -Djavax.net.ssl.trustStore=gridserver.keystore -Djavax.net.ssl.trustStorePassword=CHANGEME

