## Overview
When using a custom keystore with Kinesis Data Analytics for Flink, the keystore format needs to be JKS, as opposed to PKCS12 (you'll get an "invalid keystore format" error if you supply a PKCS12 formatted keystore). This howto describes the steps for converting a PKCS12 formatted keystore to JKS.

### The `keytool` utility

The [`keytool`](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) utility helps manage keystores used in Java environments, and comes bundled with standard JDK distributions.

### Generating a keystore

You can use `keytool` to generate keystores as shown in the example below:

```
> keytool -genkey -alias domain -keyalg RSA -validity 365 -keystore keystore.jks
```
You'll be prompted for some information such as your name, organization, etc... along with a password to protect the private key. Once the keystore is generated, we can turn around and use the `keytool` utility to inspect the format of the generated keystore.

```
> keytool -list -v -keystore keystore.jks

Enter keystore password:

Enter keystore password:  
Keystore type: PKCS12
Keystore provider: SUN

Your keystore contains 1 entry

...

```
As you can see, the keystore type is PKCS12.

### Converting from PKSC12 to JKS

In order to be able to use this in KDA/Flink, we need to convert this to JKS using the command below:

```
keytool -importkeystore -srckeystore keystore.jks -destkeystore keystorenew.jks -srcstoretype PKCS12 -deststoretype JKS -deststorepass [DEST_PASS]
```
Make sure you supply a password (in place of DEST_PASS) for the destination keystore. You will also be prompted for the same password you used to generate the source keystore. Once the desination keystore has been written to disk, let's inspect the newly generated keystore to confirm that its format is indeed JKS:

```

> keytool -list -v -keystore keystorenew.jks

Enter keystore password:

Enter keystore password:  
Keystore type: JKS
Keystore provider: SUN

Your keystore contains 1 entry

...

```

As you can see above, the new keystore (`keystorenew.jks`) was written in JKS format.