Code sign tool on [[Window OS]]

Tools:
- signtool.exe
- makecert.exe
- cert2spc.exe
- pvk2pfx.exe
- certutil.exe
- certmgr.exe

## EV code sign vs Regular code sign

| EV code sign | Regular code sign |
| ------------ | ----------------- |
| It’s only issued after a thorough vetting of the publisher in strict accordance with CA/Brower forum guidelines. | Has a fairly simple and standard vetting process. |
| The private key must be stored in an external hardware token,thus preventing any unauthorized personnel from accessing them. | The private key is stored in the developer’s workstation. |
| Builds instant trust with Microsoft SmartScreen filter. | Does not build instant trust with Microsoft SmartScreen filter. |

## Cross certificate

[Cross-Certificates for Kernel Mode Code Signing - Windows drivers](https://docs.microsoft.com/en-us/windows-hardware/drivers/install/cross-certificates-for-kernel-mode-code-signing)

[Deprecation of Software Publisher Certificates, Commercial Release Certificates, and Commercial Test Certificates - Windows drivers](https://docs.microsoft.com/en-us/windows-hardware/drivers/install/deprecation-of-software-publisher-certificates-and-commercial-release-certificates)

## Signature checking snippet

### Method 1

```powershell
signtool verify -v -pa <File(s)>
```

### Method 2

```powershell
Get-AuthenticodeSignature -FilePath <File(s)>
```

## Signing snippet

### Method 1

```powershell
signtool.exe sign -v -f <PFX file> -fd sha256 -p <Password> -tr <Timestamp server> -td sha256 -d <Description> <File(s)>
```

### Method 2

```powershell
New-SelfSignedCertificate
```

## Timestamp (TSA)

### Why timestamp?

If signed with timestamp, the file signature will valid even after the certificate expired.

[https://kroki.io/plantuml/svg/eNqVkDsOwjAMhvecwmN7hXagEhMz4gARcYql1K1il8LtSRoBKrDgwfLj_-RHJ2qjzkMwa2AMeahOgg6UBky1YYLlggxCPRP3uxo0p9UdpTaQLAN7jEqezlYRFisgSiHA1QZybxrdF4tBECoeS5atOXDBMmF1jti-eqLjVDB25M0W_lwDbxPFHyP_HbL65vgUAm2ua82q75Is_fAB_-5oYQ==](TSA flow)

Link: [https://comodosslstore.com/resources/how-to-avoid-code-signing-certificate-expired-issues/](https://comodosslstore.com/resources/how-to-avoid-code-signing-certificate-expired-issues/)

### List of server

Last update: 2021/07/12

- **Digicert**:
    
    [](http://timestamp.digicert.com/)[http://timestamp.digicert.com](http://timestamp.digicert.com)
    
    Credible: Yes . [Adobe Approved Trust List] and [Windows Cert Store].
    
    Hash: up to SHA512
    
- **GlobalSign**:
    
    [http://aatl-timestamp.globalsign.com/tsa/aohfewat2389535fnasgnlg5m23](http://aatl-timestamp.globalsign.com/tsa/aohfewat2389535fnasgnlg5m23)
    
    Credible: Yes . [Adobe Approved Trust List] and [Windows Cert Store].
    
    Hash: up to SHA512
    
- **Sectigo**:
    
    [](https://timestamp.sectigo.com/)[https://timestamp.sectigo.com](https://timestamp.sectigo.com)
    
    Credible: Yes . [Adobe Approved Trust List] and [Windows Cert Store].
    
    Hash: up to SHA512
    
    Note: wait 15 seconds between each request.
    
- **Entrust:**
    
    [http://timestamp.entrust.net/TSS/RFC3161sha2TS](http://timestamp.entrust.net/TSS/RFC3161sha2TS)
    
    Credible: Yes . [Adobe Approved Trust List] and [Windows Cert Store].
    
    Hash: up to SHA512
    
- **SwissSign**:
    
    [](http://tsa.swisssign.net/)[http://tsa.swisssign.net](http://tsa.swisssign.net)
    
    Credible: Yes . [Adobe Approved Trust List] and [Windows Cert Store].
    
    Hash: up to SHA512
    
    Note: only 10 requests per day. For bigger quantities contact the company.
    
- **IDnomic**:
    
    [http://kstamp.keynectis.com/KSign/](http://kstamp.keynectis.com/KSign/)
    
    Credible: Yes . [Adobe: European Union Trusted Lists] and [Windows Cert Store].
    
    Hash: up to SHA512
    
- **QuoVadis + Digicert**:
    
    [http://tsa.quovadisglobal.com/TSS/HttpTspServer](http://tsa.quovadisglobal.com/TSS/HttpTspServer)
    
    Credible: Yes . [Adobe Approved Trust List] and [Windows Cert Store].
    
    Hash: up to SHA512
    
- **IRN**:
    
    [http://ts.cartaodecidadao.pt/tsa/server](http://ts.cartaodecidadao.pt/tsa/server)
    
    Credible: Yes . [Adobe: European Union Trusted Lists] and [Windows Cert Store].
    
    Hash: only SHA256
    
    Note: only allows 20 requests in 20 minutes, if more requests are done the IP address will be blocked and legal consequences may happen.
    
- **ACCV**:
    
    [http://tss.accv.es:8318/tsa](http://tss.accv.es:8318/tsa)
    
    Credible: Yes . [Adobe: European Union Trusted Lists] and [Windows Cert Store].
    
    Hash: up to SHA512
    
- **IZENPE**:
    
    [](http://tsa.izenpe.com/)[http://tsa.izenpe.com](http://tsa.izenpe.com)
    
    Credible: Yes . [Adobe: European Union Trusted Lists].
    
    Hash: up to SHA512
    
- **CERTUM**:
    
    [](http://time.certum.pl/)[http://time.certum.pl](http://time.certum.pl)
    
    Credible: Yes . [Windows Cert Store]
    
    Hash: up to SHA512
    
- **DFN**:
    
    [](http://zeitstempel.dfn.de/)[http://zeitstempel.dfn.de](http://zeitstempel.dfn.de)
    
    Credible: Yes . [Windows Cert Store]
    
    Hash: up to SHA512
    
    Note: commercial use forbidden.
    
- **CatCert**:
    
    [http://psis.catcert.cat/psis/catcert/tsp](http://psis.catcert.cat/psis/catcert/tsp)
    
    Credible: Yes . [Windows Cert Store]
    
    Hash: up to SHA512
    
- **Symantec**
    
    [http://sha256timestamp.ws.symantec.com/sha256/timestamp](http://sha256timestamp.ws.symantec.com/sha256/timestamp)
    
    Credible: Yes . [Windows Cert Store]
    
    Hash: up to SHA512
    
- **GlobaSign**:
    
    [http://rfc3161timestamp.globalsign.com/advancedhttp://timestamp.globalsign.com/tsa/r6advanced1](http://rfc3161timestamp.globalsign.com/advancedhttp://timestamp.globalsign.com/tsa/r6advanced1)
    
    Credible: Yes . [Windows Cert Store]
    
    Hash: up to SHA512
    
- **Apple**:
    
    [http://timestamp.apple.com/ts01](http://timestamp.apple.com/ts01)
    
    Credible: Yes. [Apple CA]
    
    Hash: up to SHA512
    
- **FreeTSA**:
    
    [https://freetsa.org/tsr](https://freetsa.org/tsr)
    
    Credible: No.
    
    Hash: up to SHA512
    
- **SafeStamper**:
    
    [https://www.safestamper.com/tsa](https://www.safestamper.com/tsa)
    
    Credible: No.
    
    Hash: up to SHA512
    
    Note: up to 5 requests per day. For bigger quantities contact the company.
    
- **MeSign**:
    
    [](http://tsa.mesign.com/)[http://tsa.mesign.com](http://tsa.mesign.com)
    
    Credible: No.
    
    Hash: up to SHA512
    
- **WoTrust**:
    
    [](https://tsa.wotrus.com/)[https://tsa.wotrus.com](https://tsa.wotrus.com)
    
    Credible: No.
    
    Hash: only SHA256
    
    Note: wait 15 seconds between each request.
    
- **Lex-Persona**:
    
    [http://tsa.lex-persona.com/tsa](http://tsa.lex-persona.com/tsa)
    
    Credible: No.
    
    Hash: up to SHA512

## Self signing snippet

1. Make certificate and private key
    
    ```powershell
    makecert -r -pe -ss PrivateCertStore -n "CN=<Company name>" -h 0 -eku 1.3.6.1.5.5.7.3.3 -e <Expire date mm/dd/yyyy> -sv <Private key file name (.pvk)> <Certificate file (.cer)>
    ```
    
2. Make Personal exchange format file (pfx file)
    
    ```powershell
    pvk2pfx -pvk <PVK file> -pi <PVK password> -spc <CER of SPC file> -pfx <PFX file output (.pfx)>
    ```
    
3. Signing file [Signing snippet](https://www.notion.so/Signing-snippet-4830be4dde474b09a833442b9a4eeaf4?pvs=21)
    

Eg:

```powershell
makecert -r -pe -ss PrivateCertStore -n CN=Emotive -h 0 -eku 1.3.6.1.5.5.7.3.3 -e 03/31/2022 -sv EmotiveKey.pvk EmotiveCer.cer
pvk2pfx -pvk .\\EmotiveKey.pvk -pi 123456789 -spc .\\EmotiveCer.cer -pfx Emotive.pfx
signtool.exe sign -v -f ".\\Emotive.pfx" -p "123456789" -t "<http://timestamp.digicert.com>" -d "Some description" .\\OpenTestFrameworkSetup_6.x.x.xxxxx.msi
```

## Signing with EV certificate

[https://www.digicert.com/kb/code-signing/ev-authenticode-certificates.htm](https://www.digicert.com/kb/code-signing/ev-authenticode-certificates.htm)

## Install certificates

Use admin command prompt

### To local machine

Import certificate to Trusted Root Certification Authorities on Local Machine:

```bash
certutil -addstore -enterprise -f -v root <PFX> NoExport
```

Import pfx to Personal on local machine

```bash
certutil -f -p <password> -importpfx <PFX> NoExport
```

Import pfx to Trusted People on local machine - [Link](http://home.fnal.gov/~jklemenc/importpfx.html) to importpfx.exe

```bash
importpfx.exe -f <PFX> -p <password> -t MACHINE -s "TRUSTEDPEOPLE"
```

Import certificate to Trusted People on local machine

```bash
certutil -addstore -f "TRUSTEDPEOPLE" <CERT>
```

### To Current User

Import certificate to Trusted Root Certification Authorities for Current User:

```bash
certutil -f -user -p <password> -importpfx root <PFX> NoExport
```

Import certificate to Trusted People for Current User:

```bash
certutil -f -user -p <password> -importpfx TrustedPeople <PFX> NoExport
```

Import certificate to Personal for Current User:

```bash
certutil -f -user -p <password> -importpfx <PFX> NoExport
```

Import certificate to Trusted Root Certification Authorities on Local Machine:

```bash
certutil -f -user -p <password> -enterprise -importpfx root <PFX> NoExport
```

Import certificate to Trusted People on Local Machine:

```bash
certutil -f -user -p <password> -enterprise -importpfx TrustedPeople <PFX> NoExport
```

## Sign without password

### Sign with thumbprint

```bash
signtool.exe sign -v -a -fd sha256 -d <Description> -sha1 <thumbprint> <FILEs>
```

## Link

[certutil](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/certutil)

## Link

[How to create an app package signing certificate - Win32 apps](https://docs.microsoft.com/en-us/windows/win32/appxpkg/how-to-create-a-package-signing-certificate)

[Microsoft Defender SmartScreen overview (Windows 10) - Windows security](https://docs.microsoft.com/en-us/windows/security/threat-protection/microsoft-defender-smartscreen/microsoft-defender-smartscreen-overview)

[Tools to Create, View, and Manage Certificates - Win32 apps](https://docs.microsoft.com/en-us/windows/win32/seccrypto/tools-to-create-view-and-manage-certificates)

[Code signing (Microsoft Authenticode)](https://stackoverflow.com/questions/3580349/code-signing-microsoft-authenticode)
