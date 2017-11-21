# Certificates

# Installing a TERENA \(Comodo\) Multi-Domain SSL Certificate in Payara

**IMPORTANT NOTES BEFORE YOU START:**

To work around this, the admin console should be left to run using the self-signed “s1as” certificate and you should modify your https settings \(typically http-listener-2\) to use the “Certificate NickName” you use when importing it into the Payara keystore.

The password on the certificate you import into the Payara keystore MUST match the password on the Java keystore itself.

**Assumptions:**

These instructions assume you have received a zip file containing the following files \(or similar\):

* Root CA Certificate – AddTrustExternalCARoot.crt
* Intermediate CA Certificate – UTNAddTrustServer\_CA.crt
* Intermediate CA Certificate – TERENASSLCA.crt
* Your TERENA Multi-Domain SSL Certificate – 13669092.crt

You also need to have the key file which you used to generate the Certificate Signing Request.

**Instructions:**

Create a certificate chain file:

```
cat AddTrustExternalCARoot.crt UTNAddTrustServer_CA.crt TERENASSLCA.crt > cert-chain.txt
```

Create a PKCS12 file containing your certificate and the certificate chain:

* Ensure that you set a password of 6 or more characters.
* This needs to match the password on your Payara keystore.jks
* If the password on your Payara keystore is still the default \(“changeit”\) then set a new password on the p12 file and change the password on the Payara keystore as shown further down.

```
openssl pkcs12 -export -out myserver.p12 -inkey myserver.key -name myserver-terena -chain -CAfile cert-chain.txt  < 13669092.crt
```

where “myserver.key” is the key file used to generate the Certificate Signing Request,

and “myserver-terena” is an alias/nickname given to the certificate

You can inspect the contents of the p12 file using the Java keytool application:

```
keytool -v -list -storetype pkcs12 -keystore myserver.p12
```

Locate your Payara keystore. This will be located in the config directory of the domain you are running eg:

```
~/payara41/glassfish/domains/domain1/config/keystore.jks
```

You can confirm this by looking for the following setting reported in the Payara server.log upon startup:

```
-Djavax.net.ssl.keyStore=
```

Import the contents of the p12 file into your Payara keystore:

```
keytool -importkeystore -srckeystore myserver.p12 -srcstoretype pkcs12 -destkeystore keystore.jks -deststoretype jks -v
```

You can inspect the contents of keystore.jks and you should see an entry called “myserver-terena”:

```
keytool -list -keystore keystore.jks
```

Adding the –v option to this will show you all the details of the certificates, including the certificate chain.

If you need to change the password on the Payara keystore, make sure the server is NOT running and then:

```
asadmin change-master-password --savemasterpassword=true domain1
```

This MUST be set to the same password you set when creating the p12 file.

Start the server and load the Payara admin console at:[https://www.myserver.com:4848](https://www.myserver.com:4848/)

Open “Configurations” &gt; server-config &gt; Network Config &gt; Network Listeners &gt; http-listener-2

Verify in the “General” tab that this is the https listener \(typically port 8181\).

Select the “SSL” tab and enter “myserver-terena” as the Certificate NickName.

To check the setup is correct, point a web browser to[https://www.myserver.com:8181](https://www.myserver.com:8181/)

You should not get any errors or warnings about the authenticity of the web server’s certificate.

Clicking on the padlock icon at the top of your browser, you should be able to see the certificate details and the certification path.

If your server’s https port is open to the internet, then use a service such as the following to validate the certificate:

[http://www.sslshopper.com/ssl-checker.html](http://www.sslshopper.com/ssl-checker.html)

If you did NOT add the complete certificate chain when creating the p12 file, then this utility will report that the certificate chain is not complete and it will not be trusted in all web browsers.

If you have followed the instructions above then the complete certificate chain should be reported.

