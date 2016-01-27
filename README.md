# Installing SSL in Tomcat

### Generate a Keystore and CSR in Tomcat	 

```
#!java

keytool -keysize 2048 -genkey -alias opentestsystems.org -keyalg RSA -keystore tomcat.keystore
```


US

District of Columbia

Washington

American Institutes for Research

Assessment

*.ci.opentestsystem.org (this will be different for each CSR)

astwebadmin@air.org

### Create a CSR

```
#!java

keytool -certreq -keyalg RSA -alias opentestsystems.org -file opentestsystems.csr -keystore tomcat.keystore
```

### Install the root certificate

```
#!java

keytool -import -alias root -keystore tomcat.keystore -trustcacerts -file gdroot-g2.crt -storepass SOMEPASSWORD
```


### Install the intermediate certificate

```
#!java

keytool -import -alias intermed -keystore tomcat.keystore -trustcacerts -file gdig2.crt -storepass SOMEPASSWORD
```

### Install the issued certificate

```
#!java

keytool -import -alias opentestsystems.org -keystore tomcat.keystore -trustcacerts -file /path/to/certificateFromVendor
```

**-----------------------------------------------**

### Replacing keystores in Tomcat
* For each server using the SSL certs being updated, replace the old tomcat.keystore with the newly generated one (e.g. newtomcat.keystore).
    * Remember to name it exactly `tomcat.keystore`
    * Remember to make the file's ownership `tomcat.tomcat`
    * Remove world read rights on that file.
    * Changes won't take effect until tomcat is restarted. Do not restart until all three updates have been made (tomcat.keystore, samlKeystore.jks, and .truststore.aws)

## Updating tomcat truststore files (.truststore.aws)
* Trust stores contain the public certs for trusted servers. In the case of wildcards, all that's needed is the single cert.
    * Create a new trust store file with the new public cert (or import it into an existing one):
`keytool -import -trustcacerts -alias opentestsystem.org -file /path/to/server.crt -keystore .truststore.aws
`
        * Remember to name it exactly `.truststore.aws`
        * Remember to make the file's ownership `tomcat.tomcat`
        * Changes won't take effect until tomcat is restarted. Do not restart until all three updates have been made (tomcat.keystore, samlKeystore.jks, and .truststore.aws)    

## Updating samlKeystore.jks
* This contains a number of certificates and is used by Tomcat web servers exposing a UI. It is located in ~tomcat/resources/security/ folder.
    * Remove the old alias from the store file by running the following command `keytool -delete -alias "*.opentestsystem.org (go daddy secure certification authority)" -keystore samlKeystore.jks -storepass yoursecretpasswd`
    * Add the new alias to the store file by running the following command `keytool -import -trustcacerts -alias "*.opentestsystem.org (go daddy secure certification authority)" -file /path/to/server.crt -keystore samlKeystore.jks`
    * Remember to name it exactly `samlKeystore.jks`
    * Remember to make the file's ownership `tomcat.tomcat`
    * Remove world read rights on that file.
    * Changes won't take effect until tomcat is restarted. Do not restart until all three updates have been made (tomcat.keystore, samlKeystore.jks, and .truststore.aws)
