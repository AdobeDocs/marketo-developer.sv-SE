---
title: Massimport
feature: REST API
description: Batchimportera persondata.
exl-id: f7922fd2-8408-4d04-8955-0f8f58914d24
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '592'
ht-degree: 0%

---

# Massimport

Marketo tillhandahåller gränssnitt för infogning av stora uppsättningar med person- och personrelaterade data, som kallas massimport. För närvarande finns gränssnitt för tre objekttyper:

- Leads (personer)
- Anpassade objekt
- Programmedlemmar

Massimport utförs genom att ett jobb skapas och sedan väntar på att jobbet ska slutföra läsningen av en fil. Dessa jobb körs asynkront och kan avfrågas för att hämta status för importen. Filer överförs med HTTP multipart/form-data per RFC 2399.

Massor-API-slutpunkter har inte prefixet&quot;/rest&quot; som andra slutpunkter.

## Autentisering

Massimport-API:erna använder samma OAuth 2.0-autentiseringsmetod som andra Marketo REST API:er.  Detta kräver en giltig åtkomsttoken som skickas som HTTP-huvud `Authorization: Bearer {_AccessToken_}`.

>[!IMPORTANT]
>
>Stöd för autentisering med frågeparametern **access_token** tas bort den 30 juni 2025. Om ditt projekt använder en frågeparameter för att skicka åtkomsttoken bör den uppdateras så att rubriken **Authorization** används så snart som möjligt. Ny utveckling bör endast använda rubriken **Authorization**.

## Gränser

- Maximalt antal samtidiga importjobb: 2
- Maximalt antal importjobb i kö (inklusive pågående importjobb): 10
- Maximal importfilstorlek: 10 MB

## Behörigheter

Vid import i grupp används samma behörighetsmodell som Marketo REST API, och inga ytterligare specialbehörigheter krävs för att kunna använda, även om specifika behörigheter krävs för varje uppsättning slutpunkter.

## Poståtgärder

Massimport är en poståtgärd av typen&quot;infoga eller uppdatera&quot;. Om en matchande post hittas i databasen uppdateras den. Annars skapas en ny post. Svaret på bulkimport anger inte om en viss post har uppdaterats eller infogats.

## Skapa ett jobb

Marketo-API:er för bulkimport använder begreppet jobb för att utföra dataimport. Låt oss titta på hur du skapar ett enkelt lead-importjobb med slutpunkten [Importera leads](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads/operation/importLeadUsingPOST).  Observera att den här slutpunkten använder [multipart/form-data som innehållstyp](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html). Detta kan vara svårt att få rätt, så det bästa sättet är att använda ett HTTP-supportbibliotek för det språk du föredrar.  Om du håller på att bli blöt rekommenderar vi att du använder [curl](https://curl.se/).

```
POST /bulk/v1/leads.json?format=csv
```

```
Content-Type: multipart/form-data; boundary=--------------------------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Length: 311
Host: <munchkinId>.mktorest.com
```

```
------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Disposition: form-data; name="file"; filename="leads.csv"
Content-Type: text/csv

firstName,lastName,email
Able,Baker,ablebaker@marketo.com
Charlie,Dog,charliedog@marketo.com
Easy,Fox,easyfox@marketo.com
------WebKitFormBoundaryBQACkJZyaiIAXogC--
```

Denna begäran skapar ett jobb som importerar värden i CSV-filen med namnet &quot;leads.csv&quot; med kolumnrubrikerna &quot;FirstName&quot;, &quot;LastName&quot;, &quot;Email&quot; och &quot;Company&quot;.

```json
{
    "requestId": "d01f#15d672f8560",
    "result": [
        {
            "batchId": 3404,
            "importId": "3404",
            "status": "Queued"
        }
    ],
    "success": true
}
```

När vi skickar jobbet returneras ett batchId, som vi sedan kan använda för att kontrollera dess status.

### Gemensamma parametrar

Varje jobbskapandeslutpunkt delar några vanliga parametrar för konfiguration av filformat, fältnamn och filter för ett bulkextraheringsjobb.  Varje deltyp av extraheringsjobb kan ha ytterligare parametrar:

| Parameter | Datatyp | Anteckningar |
|---|---|---|
| format | Sträng | Bestämmer filformatet för importerade data med alternativ för kommaavgränsade värden, tabbavgränsade värden och semikolonavgränsade värden. Accepterar något av följande: CSV, SSV, TSV. Standardformatet är CSV. |
| fil | Sträng | Data anges via formulärdata som består av flera delar i filen. |

## Avsökningsjobbstatus

Det är enkelt att avgöra jobbets status med slutpunkten [Hämta status för importlead](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads/operation/getImportLeadStatusUsingGET).

```
GET /bulk/v1/leads/batch/{batchId}.json
```

```json
{
    "requestId": "1f63#15d6738fd15",
    "result": [
        {
            "batchId": 3404,
            "importId": "3404",
            "status": "Complete",
            "numOfLeadsProcessed": 3,
            "numOfRowsFailed": 0,
            "numOfRowsWithWarning": 0,
            "message": "Import succeeded, 3 records imported (3 members)"
        }
    ],
    "success": true
}
```

Den inre `status`-medlemmen visar förloppet för jobbet och kan vara något av följande värden: Köad, Importerar, Slutförd, Misslyckad. I det här fallet har vårt jobb slutförts, så vi kan sluta fråga.

## Fel

Fel indikeras av attributet `numOfRowsFailed` i Get Import Lead Status-svaret. Om `numOfRowsFailed` är större än noll anger det värdet antalet fel som uppstod.

Om du vill hämta poster och orsaker till felaktiga rader måste du hämta felfilen med slutpunkten [Hämta lead-fel](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads/operation/getImportLeadFailuresUsingGET).

```
GET /bulk/v1/leads/batch/{batchId}/failures.json
```

Filen anger vilka rader som misslyckades, tillsammans med ett meddelande om varför posten misslyckades.
