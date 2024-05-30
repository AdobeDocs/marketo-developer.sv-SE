---
title: "Avancerat läge för säkerhetsåtkomst"
feature: "Mobile Marketing"
description: "Information om avancerat säkerhetsåtkomstläge"
source-git-commit: 2185972a272b64908d6aac8818641af07c807ac2
workflow-type: tm+mt
source-wordcount: '303'
ht-degree: 0%

---


# Avancerat läge för säkerhetsåtkomst

Marketo SDK visar metoder för att ange och ta bort säkerhetssignaturen. Det finns också en verktygsmetod för att hämta enhets-ID. Enhets-ID:t ska skickas tillsammans med e-postmeddelandet vid inloggning till kundservern för användning vid beräkning av säkerhetssignaturen. SDK ska använda den nya slutpunkten som pekar på den algoritm som anges ovan för att hämta de fält som krävs för att instansiera signaturobjektet. Det är nödvändigt att ange den här signaturen i SDK om säkerhetsåtkomstläget har aktiverats i Marketo Mobile Admin.

## Inställningar för säkert åtkomstläge

Installationen måste implementeras innan läget för säker åtkomst har aktiverats via Marketo Admin > Mobile Apps &amp; Devices. I följande steg beskrivs processen som krävs för att slutföra säkerhetsvalideringsprocessen:

Säkert åtkomstläge kräver implementering av signaturalgoritmen på kundserversidan som ger en slutpunkt för att hämta åtkomstnyckeln, den beräknade signaturen, tidsstämpeln för förfallodatum och e-post. Den här algoritmen kräver användaråtkomstnyckeln, åtkomsthemligheten, e-postadressen, tidsstämpeln och enhets-ID för att kunna utföra beräkningen. Kunden ansvarar för att ställa in slutpunkten, implementera algoritmen för att utföra signaturberäkningar och också hålla förfallotidsstämpeln aktuell.

```python
import argparse
import datetime
import hashlib
import hmac


ACCESS_KEY = 'Your Access Key'
ACCESS_SECRET = 'Your access secret'

# Key should not be unicode
def get_signing_key(timestamp):
    return 'MKTO' + ACCESS_SECRET + str(timestamp)

def get_string_to_sign(email, uuid):
    return email + uuid

def get_hmac(key, string_to_sign):
    return hmac.new(key, string_to_sign.encode('utf-8'), hashlib.sha256).hexdigest()

def get_epoch_plus_day():
    epoch = datetime.datetime.utcfromtimestamp(0)
    valid_until_dt = datetime.datetime.utcnow() + datetime.timedelta(days=1)
    return long((valid_until_dt - epoch).total_seconds())

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument("-e", "--email", required=True, help="email address")
    parser.add_argument("-u", "--uuid", required=True, help="Device install id")
    parser.add_argument("-t", "--timestamp", type=int, help="Valid until timestamp")
    args = parser.parse_args()
    string_to_sign = get_string_to_sign(args.email, args.uuid)
    if not args.timestamp:
        valid_until = get_epoch_plus_day()
    else:
        valid_until = args.timestamp
    signing_key = get_signing_key(valid_until)
    hmac_string = get_hmac(signing_key, string_to_sign)
    print 'HMAC is ', hmac_string
```

Marketo SDK visar nya metoder för att ange och ta bort säkerhetssignaturen. Det finns också en verktygsmetod för att hämta enhets-ID. Enhets-ID:t ska skickas tillsammans med e-postmeddelandet vid inloggning till kundservern för användning vid beräkning av säkerhetssignaturen. SDK ska använda den nya slutpunkten som pekar på den algoritm som anges ovan för att hämta de fält som krävs för att instansiera signaturobjektet. Det är nödvändigt att ange den här signaturen i SDK om säkerhetsåtkomstläget har aktiverats i Marketo Mobile Admin.

### iOS

```
Marketo * sharedInstance =[Marketo sharedInstance];

// set secure signature
MKTSecuritySignature *signature =
[[MKTSecuritySignature alloc] initWithAccessKey:<ACCESS_KEY> signature:<SIGNATURE_TOKEN> timestamp:<EXPIRY_TIMESTAMP> email:<EMAIL>];
[sharedInstance setSecureSignature:signature];

// remove signature
[sharedInstance removeSecureSignature];

// get device id
[sharedInstance getDeviceId];
```

```
let sharedInstance = Marketo.sharedInstance()

 // set secure signature
let signature = MKTSecuritySignature(accessKey: <ACCESS_KEY>, signature: <SIGNATURE_TOKEN> , timestamp: <EXPIRY_TIMESTAMP>, email: <EMAIL>)
sharedInstance.setSecureSignature(signature)

// remove signature
[sharedInstance removeSecureSignature];

// get device id
sharedInstance.getDeviceId()
```

### Android

```
Marketo sdk = Marketo.getInstance(getApplicationContext());

// set signature
MarketoConfig.SecureMode secureMode = new MarketoConfig.SecureMode();
secureMode.setAccessKey(<ACCESS_KEY>);
secureMode.setEmail(<EMAIL_ADDRESS>);
secureMode.setSignature(<SIGNATURE_TOKEN>);
secureMode.setTimestamp(<EXPIRY_DATE>);
if (secureMode.isValid()) {
  sdk.setSecureSignature(secureMode);
}

// remove signature
sdk.removeSecureSignature();

// get device id
sdk.getDeviceId();
```
