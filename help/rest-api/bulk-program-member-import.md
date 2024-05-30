---
title: Import av satsprogrammedlem
feature: REST API
description: "Batchimport av medlemsdata."
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '838'
ht-degree: 0%

---


# Import av satsprogrammedlem

[Slutpunktsreferens för import av satsprogrammedlem](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members)

För stora mängder programmedlemsposter kan programmedlemmar importeras asynkront med [bulk-API](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members). Det gör att du kan importera en lista med poster till Marketo med hjälp av en platt fil med avgränsarna (komma, tabb eller semikolon). Filen kan innehålla ett valfritt antal poster, så länge filen är mindre än 10 MB. Poståtgärden är bara&quot;infoga eller uppdatera&quot;.

## Bearbetningsgränser

Du får skicka in mer än en bulkimportbegäran, med vissa begränsningar. Varje begäran läggs till som ett jobb i en FIFO-kö som ska bearbetas. Högst två jobb bearbetas samtidigt. Högst tio jobb tillåts i kön vid en given tidpunkt (inklusive de två som för närvarande bearbetas). Om du överskrider det maximala antalet tio jobb returneras felet &quot;1016, för många importer&quot;.

## Importera fil

Den första raden i filen måste vara en rubrik som listar motsvarande REST API-namn som fält som värdena för varje rad ska mappas till. REST API-namn kan hämtas med [Beskriv lead](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2) och/eller [Beskriv programmedlem](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeProgramMemberUsingGET) slutpunkter. Poster kan innehålla lead-fält, anpassade lead-fält och anpassade programmedlemsfält.

En vanlig fil skulle följa detta grundläggande mönster:

```
email,firstName,lastName
test@example.com,John,Doe
```

Själva anropet görs med `multipart/form-data` innehållstyp.

Den här typen av begäran kan vara svår att implementera, så vi rekommenderar att du använder en befintlig biblioteksimplementering.

## Skapa ett jobb

The [Importera programmedlemmar](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/importProgramMemberUsingPOST) slutpunkten läser en fil som innehåller programmedlemsposter och lägger till dem i ett program med en viss status. Posterna kan innehålla både lead-fält och anpassade fält för programmedlemmar. Alla poster måste innehålla e-postfältet, som används för borttagning av dubbletter.

The `programId` path-parametern anger programmet som medlemmarna läggs till i.

Det finns tre obligatoriska frågeparametrar. The `format` -parametern anger importfilformatet (CSV, TSV eller SSV), `programMemberStatus` parametern anger programstatus för de medlemmar som läggs till i programmet, och `file` -parametern innehåller namnet på importfilen som innehåller programmedlemsposter.

```
POST /bulk/v1/program/{programId}/members/import.json?format=csv&programMemberStatus=On List
```

```
Content-Type: multipart/form-data; boundary=--------------------------118046853683028616211319
Content-Length: 772
Host: <munchkinId>.mktorest.com
```

```
----------------------------118046853683028616211319
Content-Disposition: form-data; name="file"; filename="Lead-House-Lannister.csv"
Content-Type: text/csv

firstName,lastName,email,title,company,leadScore
Joanna,Lannister,Joanna@Lannister.com,Lannister,House Lannister,0
Tywin,Lannister,Tywin@Lannister.com,Lannister,House Lannister,0
Cersei,Lannister,Cersei@Lannister.com,Lannister,House Lannister,0
Jamie,Lannister,Jamie@Lannister.com,Lannister,House Lannister,0
Tyrion,Lannister,Tyrion@Lannister.com,Lannister,House Lannister,0
Kevan,Lannister,Kevan@Lannister.com,Lannister,House Lannister,0
Dorna,Lannister,Dorna@Lannister.com,Lannister,House Lannister,0
Lancel,Lannister,Lancel@Lannister.com,Lannister,House Lannister,0

----------------------------118046853683028616211319--
```

```json
{
    "requestId": "17f4a#16f87f87325",
    "result": [
        {
            "batchId": 1040,
            "importId": "1040",
            "status": "Queued"
        }
    ],
    "success": true
}
```

Meddelande i svaret på vårt samtal att det finns en `batchId` och `status` -fält för posten i resultatarrayen. Eftersom den här slutpunkten är asynkron kan den returnera statusen Köad, Importerad eller Misslyckad. Du måste behålla `batchId` för att hämta status för importjobbet och för att hämta fel och/eller varningar när det är klart. The `batchId` gäller i sju dagar.

I exemplet ovan kan du enkelt anropa slutpunkten genom att använda cURL från kommandoraden:

```bash
curl -i -F format='csv' -F programMemberStatus='On List' -F file='@Lead-House-Lannister.csv' -F access_token='<Access Token>' <REST API Endpoint Base URL>/bulk/v1/program/{programId}/members/import.json
```

Om importfilen &quot;Lead-House-Lannister.csv&quot; innehåller följande:

```
firstName,lastName,email,title,company,leadScore
Joanna,Lannister,Joanna@Lannister.com,Lannister,House Lannister,0
Tywin,Lannister,Tywin@Lannister.com,Lannister,House Lannister,0
Cersei,Lannister,Cersei@Lannister.com,Lannister,House Lannister,0
Jamie,Lannister,Jamie@Lannister.com,Lannister,House Lannister,0
Tyrion,Lannister,Tyrion@Lannister.com,Lannister,House Lannister,0
Kevan,Lannister,Kevan@Lannister.com,Lannister,House Lannister,0
Dorna,Lannister,Dorna@Lannister.com,Lannister,House Lannister,0
Lancel,Lannister,Lancel@Lannister.com,Lannister,House Lannister,0
```

## Avsökningsjobbstatus

När importjobbet har skapats måste du kontrollera dess status. Det är bäst att avsöka importjobbet var 5-30:e sekund. Gör detta genom att skicka `batchId` path-parametern till [Hämta medlemsstatus för importprogram](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET) slutpunkt.

```
GET /bulk/v1/program/members/import/{batchId}/status.json
```

```json
{
    "requestId": "e0cb#16f87f8b177",
    "result": [
        {
            "batchId": 1040,
            "importId": "1040",
            "status": "Complete",
            "numOfLeadsProcessed": 8,
            "numOfRowsFailed": 0,
            "numOfRowsWithWarning": 0,
            "message": "Import succeeded, 8 records imported (8 members)"
        }
    ],
    "success": true
}
```

Det här svaret visar en slutförd import. Statusen kan vara: Slutförd, Köad, Importerad, Misslyckad.

Om jobbet har slutförts finns en lista med antalet rader som har bearbetats, misslyckats eller med varningar. Meddelandeparametern kan också ge felmeddelandet om statusen är Misslyckad.

## Fel

Fel anges av `numOfRowsFailed` attribute in [Hämta medlemsstatus för importprogram](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET) svar. Om numOfRowsFailed är större än noll visar det värdet antalet fel som uppstod.

Använd [Fel vid import av programmedlemmar](http://TODO) slutpunkt för att hämta poster och orsaker till misslyckade rader genom att skicka `batchId` path-parameter.

```
GET /bulk/v1/program/members/import/{batchId}/failures.json
```

Slutpunkten svarar med en fil som anger vilka rader som misslyckades, tillsammans med ett meddelande som anger varför posten misslyckades. Filformatet är detsamma som anges i `format` parametern när jobb skapas. Ett extra fält läggs till i varje post med en beskrivning av felet.

Anta till exempel att du importerar följande fil med ett ogiltigt lead-poäng:

```
firstName,lastName,email,title,company,leadScore
Aerys,Targaryen,Aerys@Targaryen.com,Targaryen,House Targaryen,TEXT_VALUE_IN_INTEGER_FIELD
```

När du kontrollerar jobbstatus visas `numOfRowsFailed` är 1 vilket anger att ett fel uppstod:

```
GET /bulk/v1/program/members/import/{batchId}/status.json
```

```json
{
    "requestId": "4c2d#16f8b32c8ef",
    "result": [
        {
            "batchId": 1046,
            "importId": "1046",
            "status": "Complete",
            "numOfLeadsProcessed": 0,
            "numOfRowsFailed": 1,
            "numOfRowsWithWarning": 0,
            "message": "Import completed with errors, 0 records imported (0 members), 1 failed"
        }
    ],
    "success": true
}
```

Hämta sedan felfilen för mer information om felet:

```
GET /bulk/v1/program/members/import/{batchId}/failures.json
```

```
firstName,lastName,email,title,company,leadScore,Import Failure Reason
Aerys,Targaryen,Aerys@Targaryen.com,Targaryen,House Targaryen,TEXT_VALUE_IN_INTEGER_FIELD,Invalid data type in field Lead Score
```

## Varningar

Varningar indikeras av `numOfRowsWithWarning` attribute in [Hämta medlemsstatus för importprogram](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberStatusUsingGET) svar. If `numOfRowsWithWarning` är större än noll, anger det värdet antalet varningar som inträffade.

Använd [Få medlemsvarningar för importprogram](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Program-Members/operation/getImportProgramMemberWarningsUsingGET) slutpunkt för att hämta poster och orsaker till varningsrader genom att skicka `batchId` path-parameter.

```
GET /bulk/v1/program/members/import/{batchId}/warnings.json
```

Slutpunkten svarar med en fil som anger vilka rader som genererade varningar, tillsammans med ett meddelande som anger varför posten genererade en varning. Filformatet är detsamma som anges i `format` parametern när jobb skapas. Ett extra fält läggs till för varje post med en beskrivning av varningen.

Anta att du importerar följande fil med en ogiltig e-postadress:

```
firstName,lastName,email,title,company,leadScore
Aerys,Targaryen,INVALID_EMAIL,Targaryen,House Targaryen,0
```

När du kontrollerar jobbstatus visas `numOfRowsWithWarning` är 1 vilket anger att en varning har inträffat:

```
GET /bulk/v1/program/members/import/{batchId}/status.json
```

```json
{
   "requestId":"4ca1#16f883c2003",
   "result":[
      {
         "batchId":1041,
         "importId":"1041",
         "status":"Complete",
         "numOfLeadsProcessed":1,
         "numOfRowsFailed":0,
         "numOfRowsWithWarning":1,
         "message":"Import succeeded, 1 records imported (1 members), 1 warning."
      }
   ],
   "success":true
}
```

Du kan sedan hämta varningsfilen om du vill ha mer information om varningen:

```
GET /bulk/v1/program/members/import/{batchId}/warnings.json
```

```
firstName,lastName,email,title,company,leadScore,Import Warning Reason
Aerys,Targaryen,INVALID_EMAIL,Targaryen,House Targaryen,0,Invalid email address
```
