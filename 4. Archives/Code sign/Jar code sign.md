---
para: archive
was: resource
---
Code sign for [[Java]] package [[Jar]]

## Windows

Step tested on [[Window OS]]

Verify signature

```bash
jarsigner -verify -verbose <FILE>
```

Sign with JKS

```bash
jarsigner -tsa <TIME_STAMP_SERVER> -keystore <JKS FILE> <JAR FILE> <ALIAS>
```

PFX → JKS

Since Java 9 the default keystore type is PKCS12; JKS is legacy.

```bash
keytool -importkeystore -srckeystore <PFX FILE> -srcstoretype pkcs12 -destkeystore <JKS FILE> -deststoretype jks -storepass <PASSWORD>
```

Get info from JKS

```bash
keytool -list -v -keystore <JKS FILE>
```

Get info from PFX

```bash
keytool -list -v -storetype pkcs12 -keystore <PFX FILE>
```

Sign with PFX

```bash
jarsigner -tsa <TIME_STAMP_SERVER> -storetype pkcs12 -keystore <PFX FILE> -storepass <PASSWORD> <JAR FILE> <ALIAS>
```