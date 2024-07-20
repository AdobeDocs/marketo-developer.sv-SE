---
title: Namngivna konton
feature: REST API
description: Hantera namngivna konton via API:t.
exl-id: 2aa1d2a0-9e54-4a9a-abb1-0d0479ed3558
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '679'
ht-degree: 0%

---

# Namngivna konton

[Slutpunktsreferens för namngivna konton](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Named-Accounts)

Marketo erbjuder en uppsättning API:er för att utföra CRUD-åtgärder på namngivna konton som kan användas med Marketo ABM. Dessa API:er följer standardgränssnittsmönstret för lead-databas-API:er, med alternativen Beskrivning, Skapa/Uppdatera, Ta bort och Fråga.

För närvarande är de enda ABM-relaterade funktionerna som är tillgängliga via Marketo API:er CRUD-åtgärder för namngivna konton. Leads kan inte länkas till namngivna konton via några API:er.

## Beskriv

Beskrivningen av namngivna konton returnerar metadata som relaterar till användningen av namngivna konton via Marketo API:er, inklusive en lista över giltiga sökbara fält vid frågor och en lista över alla fält som är tillgängliga för API-användning. `idField` för ett namngivet konto är alltid `marketoGUID` och den enda tillgängliga `dedupeField`, och nyckeln för att skapa är objektets `name`-fält.

```
GET /rest/v1/namedaccounts/describe.json
```

```json
{  
   "requestId":"d65e#156c27ac57d",
   "result":[  
      {  
         "name":"Named Account",
         "description":"Marketo standard account attribute map",
         "createdAt":"2016-08-18T20:16:41Z",
         "updatedAt":"2016-08-18T20:16:41Z",
         "idField":"marketoGUID",
         "dedupeFields":[  
            "name"
         ],
         "searchableFields":[
            [
               "marketoGUID",
            ], 
            [  
               "annualRevenue"
            ],
            [  
               "city"
            ],
            [  
               "country"
            ],
            [  
               "domainName"
            ],
            [  
               "industry"
            ],
            [  
               "logoUrl"
            ],
            [  
               "membershipCount"
            ],
            [  
               "name"
            ],
            [  
               "numberOfEmployees"
            ],
            [  
               "opptyAmount"
            ],
            [  
               "opptyCount"
            ],
            [  
               "score1"
            ],
            [  
               "score2"
            ],
            [  
               "score3"
            ],
            [  
               "score4"
            ],
            [  
               "score5"
            ],
            [  
               "sicCode"
            ],
            [  
               "state"
            ]
         ],
         "fields":[  
            {  
               "name":"marketoGUID",
               "displayName":"Marketo GUID",
               "dataType":"string",
               "length":36,
               "updateable":false
            },
            {  
               "name":"annualRevenue",
               "displayName":"annualRevenue",
               "dataType":"currency",
               "updateable":true
            },
            {  
               "name":"city",
               "displayName":"city",
               "dataType":"string",
               "length":255,
               "updateable":true
            },
            {  
               "name":"country",
               "displayName":"country",
               "dataType":"string",
               "length":255,
               "updateable":true
            }
         ]
      }
   ],
   "success":true
}
```

### Fråga

Frågorna efter namngivna konton baseras på användningen av en filterType och en uppsättning på upp till 300 kommaseparerade filterValues. `filterType` kan vara vilket enskilt fält som helst som returneras i `searchableFields`-medlemmen av det beskrivande resultatet för namngivna konton, medan filterValues kan vara vilken giltig inmatning som helst för fältets datatyp. Om du vill returnera en viss uppsättning fält från måste en fälteparameter skickas, där värdet är en kommaavgränsad lista med fält som ska returneras i svaret. Precis som för andra frågealternativ är det maximala antalet poster för en enskild frågesida 300, och ytterligare poster i uppsättningen måste begäras med användningen av nextPageToken som returneras av anropet.

```
GET /rest/v1/namedaccounts.json?filterType=name&filterValues=Google,Yahoo
```

```json
{
    "requestId": "6dac#157d4ddc9d7",
    "result": [
        {
            "seq": 0,
            "marketoGUID": "16efafdd-0148-4ea7-8782-f451d7c6345d",
            "createdAt": "2016-10-17T22:49:04Z",
            "name": "Google",
            "updatedAt": "2016-10-17T22:49:04Z"
        },
        {
            "seq": 1,
            "marketoGUID": "44d62353-7f9d-4d43-b9cc-7ef0f7a09137",
            "createdAt": "2016-10-17T22:49:04Z",
            "name": "Yahoo",
            "updatedAt": "2016-10-17T22:49:04Z"
        }
    ],
    "success": true
}
```

### Skapa och uppdatera

När du skapar och uppdaterar namngivna konton används standarddatabasmönstret lead. Poster måste skickas i indatamedlemmen för en JSON-brödtext i en POST-begäran. `input` är den enda obligatoriska medlemmen med `action` och `dedupeBy` som valfria medlemmar. Upp till 300 poster kan inkluderas i indata. Åtgärden kan vara en av createOnly, updateOnly eller createOrUpdate. Om det inte anges används åtgärden som standard createOrUpdate. dedupeBy kan bara anges när action är updateOnly, och bara accepterar en av dedupeFields eller idField, som motsvarar fälten name och marketoGUID.

```
POST /rest/v1/namedaccounts.json
```

```
Content-Type: application/json
```

```json
{  
   "action":"updateOnly",
   "dedupeBy":"dedupeFields",
   "input":[  
      {  
         "name":"Google",
         "domainName":"www.google.com"
      },
      {  
         "name":"Yahoo",
         "domainName":"www.yahoo.com"
      }
   ]
}
```

```json
{  
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[  
      {  
         "seq":0,
         "status":"updated",
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb"
      },
      {  
         "seq":1,
         "status":"created",
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fc"
      }
   ]
}
```

### Fält

Det namngivna kontoobjektet innehåller en uppsättning fält. Varje fältdefinition består av en uppsättning attribut som beskriver fältet. Exempel på attribut är visningsnamn, API-namn och dataType. Dessa attribut kallas tillsammans för metadata.

Med följande slutpunkter kan du fråga fält på företagsobjektet. Dessa API:er kräver att den ägande API-användaren har en roll med ett av eller båda behörigheterna för Läs-skriv-schemastandarden eller Anpassat fält för läs- och skrivschema.

### Frågefält

Det är enkelt att fråga efter namngivna kontofält. Du kan fråga ett enskilt namngivet kontofält efter API-namn eller fråga uppsättningen med alla företagsfält.

#### Efter namn

Slutpunkten [Hämta namngivet kontofält med namn](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Named-Accounts/operation/getNamedAccountFieldByNameUsingGET) hämtar metadata för ett enskilt fält i det namngivna kontoobjektet. Den obligatoriska parametern fieldApiName path anger fältets API-namn. Svaret är som slutpunkten för Beskriv namngivet konto men innehåller ytterligare metadata som attributet isCustom som anger om fältet är ett anpassat fält.

```
GET /rest/v1/namedaccounts/schema/fields/annualRevenue.json
```

```json
{
    "requestId": "371c#17e979c5d1f",
    "result": [
        {
            "displayName": "Annual Revenue",
            "name": "annualRevenue",
            "description": null,
            "dataType": "currency",
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        }
    ],
    "success": true
}
```

#### Bläddra

Slutpunkten [Hämta fält för namngivet konto](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Named-Accounts/operation/getNamedAccountFieldByNameUsingGET) hämtar metadata för alla fält i det namngivna kontoobjektet. Som standard returneras högst 300 poster. Du kan använda frågeparametern batchSize för att minska det här talet. Om attributet moreResult är true betyder det att fler resultat är tillgängliga. Fortsätt anropa den här slutpunkten tills attributet moreResult returnerar false, vilket betyder att det inte finns några tillgängliga resultat. nextPageToken som returneras från detta API ska alltid återanvändas för nästa iteration av det här anropet.

```
GET /rest/v1/namedaccounts/schema/fields.json?batchSize=5
```

```json
{
    "requestId": "f287#17e995bd0c5",
    "result": [
        {
            "displayName": "Name",
            "name": "name",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Domain Name",
            "name": "domainName",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Industry",
            "name": "industry",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "SIC Code",
            "name": "sicCode",
            "description": null,
            "dataType": "string",
            "length": 40,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "City",
            "name": "city",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        }
    ],
    "success": true,
    "nextPageToken": "N42LHXWEULHZ3N2I77DKOJUVOY======",
    "moreResult": true
}
```

### Ta bort

Borttagningar görs via en JSON-POST-begäran och har en obligatorisk indatamedlem och en valfri deleteBy-medlem. deleteBy kan vara en av &quot;dedupeFields&quot; eller &quot;idField&quot;, som motsvarar name eller marketoGUID, och dedupeFields tas bort som standard om den är ur. Indatamedlemmen accepterar en array med upp till 300 poster, som innehåller en medlem vardera, antingen name eller marketoGUID beroende på inställningen för deleteBy.

```
POST /rest/v1/namedaccounts/delete.json
```

```
Content-Type: application/json
```

```json
{  
   "deleteBy":"dedupeFields",
   "input":[  
      {  
         "name":"Google"
      },
      {  
         "name":"Yahoo"
      },
      {  
         "name":"Marketo"
      }
   ]
}
```

```json
{  
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[  
      {  
         "seq":0,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb",
         "status":"deleted"
      },
      {  
         "seq":1,
         "id":"dff23271-f996-47d7-984f-f2676861b5fc",
         "status":"deleted"
      },
      {  
         "seq":2,
         "status":"skipped",
         "reasons":[  
            {  
               "code":"1013",
               "message":"Record not found"
            }
         ]
      }
   ]
}
```

## Timeout

- Slutpunkter för namngivna konton har en timeout på 30 sekunder om inget annat anges nedan
   - Synkronisera namngivna konton: 120-tal 
   - Ta bort namngivna konton: 60 s
