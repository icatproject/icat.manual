# Certificates

### Overview

Some information on this page is taken from the[ Payara blog](http://blog.payara.fish/securing-payara-server-with-custom-ssl-certificate) on adding SSL certificates.

### How to install a certificate in Payara 4.1

This tutorial assumes you have your public \(mycert.crt\) and your private key \(mycert.key\), either self-signed or from a valid certificate authority. However, using a self-signed certificate is not advised due to[ issues with icat manager](https://github.com/icatproject/manager.icat-manager/issues/3), python being stricter, browser warnings as well as the security risk.

First of all you need to create a PKCS12 bundle:

In linux this is done with the following command:

```
openssl pkcs12 -export -in mycert.crt -inkey mycert.key -out mycert.p12 -name mydomain_certificate
```

Next the certificate you just generated needs adding to the Payara keystore. The default keystore for trusted certificates can be found at &lt;Payara\_install&gt;/glassfish/domains/mydomain/config/cacert.jks.

The default password for the Payara keystore is "changeit"

```
keytool -importkeystore -destkeystore cacerts.jks -srckeystore mycert.p12 -srcstoretype PKCS12 -alias mydomain_certificate
```

The certificate is now installed in the keystore with the alias mydomain\_certificate.



