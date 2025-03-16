Code sign for [[Java]] package [[Jar]]

## Windows

Step tested on [[Window OS]]

Verify signature

```bash
jarsigner -verify -verbose <FILE>
```

Sign with JSK

```bash
jarsigner -tsa <TIME_STAMP_SERVER> -keystore <JSK FILE> <JAR FILE> <ALIAS>
```

PFX → JSK

```bash
keytool -importkeystore -srckeystore <PFX FILE> -srcstoretype pkcs12 -destkeystore <JKS FILE> -deststoretype jks -storepass <PASSWORD>
```

Get info from JSK

```bash
keytool -list -v -keystore <JSK FILE>
```

Get info from PFX

```bash
keytool -list -v -storetype pkcs12 -keystore <PFX FILE>
```

Sign with PFX

```bash
jarsigner -tsa <TIME_STAMP_SERVER> -storetype pkcs12 -keystore <PFX FILE> -storepass <PASSWORD> <JAR FILE> <ALIAS>
```