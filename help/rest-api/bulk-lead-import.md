---
title: "Importera massutskick"
feature: REST API
description: "Batchimport av lead-data."
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '803'
ht-degree: 0%

---


# Import av massutr

[Slutpunktsreferens för bulkimport](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads)

För stora mängder lead-poster kan leads importeras asynkront med [bulk-API](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads/operation/importLeadUsingPOST). Det gör att du kan importera en lista med poster till Marketo med hjälp av en platt fil med avgränsarna (komma, tabb eller semikolon). Filen kan innehålla ett valfritt antal poster, så länge filen är mindre än 10 MB. Poståtgärden är bara&quot;infoga eller uppdatera&quot;.

## Bearbetningsgränser

Du får skicka in mer än en bulkimportbegäran, med vissa begränsningar. Varje begäran läggs till som ett jobb i en FIFO-kö som ska bearbetas. Högst två jobb bearbetas samtidigt. Högst tio jobb tillåts i kön vid en given tidpunkt (inklusive de två som för närvarande bearbetas). Om du överskrider det maximala antalet tio jobb returneras felet &quot;1016, för många importer&quot;.

## Importera fil

Den första raden i filen måste vara en rubrik som listar motsvarande REST API-fält som värdena för varje rad ska mappas till. En vanlig fil skulle följa detta grundläggande mönster:

```
email,firstName,lastName
test@example.com,John,Doe
```

The `externalCompanyId` kan användas för att länka lead-posten till en företagspost. The `externalSalesPersonId` fält kan användas för att länka lead-posten till en säljpersonspost.

Själva anropet görs med `multipart/form-data` innehållstyp.

Den här typen av begäran kan vara svår att implementera, så vi rekommenderar att du använder en befintlig biblioteksimplementering.

## Skapa ett jobb

Om du vill göra en begäran om massimport måste du ange innehållstyphuvudet till multipart/form-data och inkludera minst en filparameter med filinnehållet, samt en formatparameter med värdet csv, tsv eller ssv som anger filformatet.

```
POST /bulk/v1/leads.json?format=csv
```

```
Content-Type: multipart/form-data; boundary=------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Length: 311
Host: <munchkinId>.mktorest.com
```

```
------WebKitFormBoundaryBQACkJZyaiIAXogC
Content-Disposition: form-data; name="file"; filename="leads.csv"
Content-Type: text/csv

FirstName,LastName,Email,Company
Able,Baker,ablebaker@marketo.com,Marketo
Charlie,Dog,charliedog@marketo.com,Marketo
Easy,Fox,easyfox@marketo.com,Marketo
------WebKitFormBoundaryBQACkJZyaiIAXogC--
```

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

Den här slutpunkten använder [multipart/form-data som innehållstyp](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html). Detta kan vara svårt att få rätt, så det bästa sättet är att använda ett HTTP-supportbibliotek för det språk du föredrar. Ett enkelt sätt att göra detta med cURL från kommandoraden ser ut så här:

```
curl -i -F format=csv -F file=@lead_data.csv -F access_token=<Access Token> <REST API Endpoint Base URL>/bulk/v1/leads.json
```

Där importfilen &quot;lead_data.csv&quot; innehåller följande:

```
FirstName,LastName,Email,Company
Able,Baker,ablebaker@marketo.com,Marketo
Charlie,Dog,charliedog@marketo.com,Marketo
Easy,Fox,easyfox@marketo.com,Marketo
```

Du kan även inkludera `lookupField`, `listId`och `partitionName` parametrar i din begäran. `lookupField` Med kan du markera ett specifikt fält som du vill deduplicera, precis som med Synkronisera leads, och som standard med e-post. Du kan ange `id` as `lookupField` för att ange en åtgärd som endast uppdaterar. `listId` gör att du kan välja en statisk lista att importera listan med leads till. Detta gör att leads i listan blir medlemmar i den statiska listan, förutom eventuella skapelser eller uppdateringar som orsakas av importen. `partitionName` väljer en specifik partition att importera till. Mer information finns i avsnittet Arbetsytor och partitioner.

Observera i svaret på vårt anrop att det inte finns en lista över lyckade eller misslyckade åtgärder som Sync Leads, utan ett batchId och ett statusfält för posten i resultatarrayen. Detta beror på att detta API är asynkront och kan returnera statusen Köat, Importerat eller Misslyckat. Du måste behålla batchId för att få status för importjobbet och för att kunna hämta fel och/eller varningar när det är klart. batchId är giltigt i sju dagar.

## Avsökningsjobbstatus

Det är bäst att avsöka jobbet var 5-30:e sekund, beroende på fördröjning och API-anropsbegränsningar, för att se importjobbets status. Du kan göra det med API:t Hämta status för importlead.

```
GET /bulk/v1/leads/batch/{id}.json
```

```json
{
   "requestId":"8136#146daebc2ed",
   "success":true,
   "result":[
      {
         "batchId":1022,
         "status":"Complete",
         "numOfLeadsProcessed":2,
         "numOfRowsFailed":1,
         "numOfRowsWithWarning":0,
         "message":"Import completed with errors, 2 records imported (2 members), 1 failed"
      }
   ]
}
```

Det här svaret visar en slutförd import, men statusen kan vara någon av:

- Complete
- Köad
- Importerar
- Misslyckades

Om jobbet har slutförts har du en lista med antalet rader som har bearbetats, misslyckats, på rader med varningar. Meddelandeparametern kan också ge felmeddelandet om statusen är Misslyckad.

## Fel

Fel anges av attributet numOfRowsFailed i Get Import Lead Status-svaret. Om numOfRowsFailed är större än noll visar det värdet antalet fel som uppstod.

Om du vill hämta poster och orsaker till misslyckade rader måste du hämta felfilen:

```
GET /bulk/v1/leads/batch/{id}/failures.json
```

API:t svarar med en fil som anger vilka rader som misslyckades, tillsammans med ett meddelande som anger varför posten misslyckades. Filformatet är detsamma som anges i formatparametern när jobb skapas. Ett extra fält läggs till i varje post med en beskrivning av felet.

## Varningar

Varningar indikeras av attributet &quot;numOfRowsWithWarning&quot; i Get Import Lead Status-svaret. Om &quot;numOfRowsWithWarning&quot; är större än noll visar det värdet antalet varningar som inträffade.

Hämta varningsfilen om du vill hämta poster och orsaker till varningsrader:

```
GET /bulk/v1/leads/batch/{id}/warnings.json
```

API:t svarar med en fil som anger vilka rader som orsakade varningar, tillsammans med ett meddelande som anger varför posten misslyckades. Filformatet är detsamma som anges i formatparametern när jobb skapas. Ett extra fält läggs till för varje post med en beskrivning av varningen.
