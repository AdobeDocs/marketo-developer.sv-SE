---
title: "Möjligheter"
feature: REST API
description: " Konfigurera möjligheter med Marketo API."
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '786'
ht-degree: 0%

---


# Möjligheter

[Slutpunktsreferens för affärsmöjlighet](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities)

Marketo visar API:er för att läsa, skriva, skapa och uppdatera poster för affärsmöjligheter. I Marketo länkas affärsmöjlighetsposter till lead- och kontaktposter via det mellanliggande säljprojektsrollobjektet, så att en affärsmöjlighet kan länkas till många enskilda leads.  Båda dessa objekttyper exponeras via API, och precis som de flesta objekttyperna för Lead-databasen har de båda ett motsvarande Describe-anrop som returnerar metadata om objekttyperna.

API:er för affärsmöjligheter är skrivskyddade för prenumerationer som har [SFDC-synkronisering](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync.html?lang=en) eller [Microsoft Dynamics Sync](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync.html?lang=en) är aktiverade.

## Beskriv

Beskrivningen av säljprojektsposter följer standardmönstret för lead-databasobjekt.

```
GET /rest/v1/opportunities/describe.json
```

```json
{  
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[  
      {  
         "name":"opportunity",
         "displayName":"Opportunity",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"marketoGUID",
         "dedupeFields":[  
            "externalOpportunityId"
         ],
         "searchableFields":[  
            [  
               "externalOpportunityId"
            ],
            [  
               "marketoGUID"
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
               "name":"createdAt",
               "displayName":"Created At",
               "dataType":"datetime",
               "updateable":false
            },
            {  
               "name":"updatedAt",
               "displayName":"Updated At",
               "dataType":"datetime",
               "updateable":false
            },
            {  
               "name":"externalOpportunityId",
               "displayName":"External Opportunity Id",
               "dataType":"string",
               "length":50,
               "updateable":false
            }
         ]
      }
   ]
}
```

De viktigaste fälten för den här svarstypen är `idField`, `dedupeFields`och `searchableFields`.  idField anger primärnyckeln för affärsmöjligheter, marketoGUID.  Detta är en systemgenererad unik nyckel som kan användas för läs- och uppdateringsåtgärder, men inte för infogningar, eftersom den är systemhanterad.  Arrayen dedupliceringsfält anger vilka fält som är giltiga nycklar för infogningsåtgärder. I fråga om affärsmöjligheter är detta bara externalOpportunityId.  Arrayen searchableFields ger dig den uppsättning fält som är giltiga för frågor, externalOpportunityId och marketoGUID.

## Fråga

Mönstret för [fråga efter möjligheter](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities/operation/getOpportunitiesUsingGET) följer det som finns i API:t för leads med den extra begränsningen att `filterType` parametern godkänner fälten som anges i `searchableFields` eller för motsvarande describe-anrop, eller dedupeFields.  Observera att om du använder anpassade affärsmöjlighetsfält, kommer endast anpassade affärsmöjlighetsfält av typen String eller Integer att listas i sökbar fältsarray.

```
GET /rest/v1/opportunities.json?filterType=marketoGUID&filterValues=dff23271-f996-47d7-984f-f2676861b5fa&dff23271-f996-47d7-984f-f2676861b5fc,dff23271-f996-47d7-984f-f2676861b5fb
```

```json
{  
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[  
      {  
         "seq":0,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fa ",
         "externalOpportunityId":"19UYA31581L000000",
         "name":"Chairs",
         "description":"Chairs",
         "amount":"1604.47",
         "source":"Inbound Sales Call/Email"
      },
      {  
         "seq":1,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fc ",
         "externalOpportunityId":"29UYA31581L000000",
         "name":"Big Dog Day Care-Phase12",
         "description":"Big Dog Day Care-Phase12",
         "amount":"1604.47",
         "source":"Email"
      }
   ]
}
```

Du kan även inkludera valfria frågeparametrar `fields`, för att returnera ytterligare affärsmöjlighetsfält, `nextPageToken`, för sidindelning genom uppsättningar som är större än gruppstorleken, `batchSize`, som har standardvärdet och som har högst 300.  När du begär en lista med `fields`om ett visst fält begärs, men inte returneras, anges värdet som null.

## Skapa och uppdatera

Affärsmöjligheterna följer ofta leads-API-mönstret, med vissa begränsningar.  Tillgängliga värden för `action` är: createOnly, createOrUpdate och updateOnly.  När du använder createOnly- eller createOrUpdate-läget måste fältet externalOpportunityId inkluderas i varje post.  För updateOnly-läget kan antingen marketoGUID eller externalOpportunityId användas.  Läget är som standard createOrUpdate om det inte anges.

The `lookupField` parametern från leads-API är inte tillgänglig och har ersatts av parametern dedupeBy, som bara är giltig om åtgärden är updateOnly.  De värden som är tillgängliga för dedupeBy är antingen dedupeFields eller idField som anges av description-anropet som externalOpportunityId respektive marketoGUID.  Om dedupeBy inte anges används standardläget dedupeFields.  Fältet &#39;name&#39; får inte vara null.

Du kan skicka upp till 300 poster åt gången.

```
POST /rest/v1/opportunities.json
```

```json
{  
   "action":"createOrUpdate",
   "dedupeBy":"dedupeFields",
   "input":[  
      {  
         "externalOpportunityId":"19UYA31581L000000",
         "name":"Chairs",
         "description":"Chairs",
         "amount":"1604.47",
         "source":"Inbound Sales Call/Email"
      },
      {  
         "externalOpportunityId":"29UYA31581L000000",
         "name":"Big Dog Day Care-Phase12",
         "description":"Big Dog Day Care-Phase12",
         "amount":"1604.47",
         "source":"Email"
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
         "marketoGUID":"cff23271-f996-47d7-984f-f2676861b5fb"
      }
   ]
}
```

API:t svarar med `marketoGUID` för varje post, samt `status` fält, som anger om varje post har lyckats eller inte, och `seq` fält som används för att korrelera inskickade poster till svarsordningen.  Numret i fältet är indexvärdet för den post som har skickats i begäran.

### Fält

Företagsobjektet innehåller en uppsättning fält.  Varje fältdefinition består av en uppsättning attribut som beskriver fältet.  Exempel på attribut är visningsnamn, API-namn och dataType.  Dessa attribut kallas tillsammans för metadata.

Med följande slutpunkter kan du fråga fält på företagsobjektet. Dessa API:er kräver att den ägande API-användaren har en roll med en eller båda av `Read-Write Schema Standard Field` eller `Read-Write Schema Custom Field` behörigheter.

### Frågefält

Det är enkelt att fråga efter affärsmöjlighetsfält.  Du kan fråga ett enskilt företagsfält efter API-namn eller fråga uppsättningen med alla företagsfält.

#### Efter namn

The [Hämta fält för affärsmöjlighet efter namn](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities/operation/getOpportunityFieldByNameUsingGET) slutpunkten hämtar metadata för ett enskilt fält i företagsobjektet.  Obligatoriskt `fieldApiName` path-parametern anger fältets API-namn.  Svaret är som slutpunkten för Beskriv säljprojekt men innehåller ytterligare metadata som `isCustom` -attribut som anger om fältet är ett anpassat fält.

```
GET /rest/v1/opportunities/schema/fields/externalOpportunityId.json
```

```json
{
    "requestId": "12331#17e9779cb4b",
    "result": [
        {
            "displayName": "SFDC Oppty Id",
            "name": "externalOpportunityId",
            "description": null,
            "dataType": "string",
            "length": 50,
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

The [Hämta fält för affärsmöjlighet](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities/operation/getOpportunityFieldsUsingGET) slutpunkten hämtar metadata för alla fält i företagsobjektet.  Som standard returneras högst 300 poster.  Du kan använda `batchSize` frågeparameter för att minska det här talet.  Om `moreResult` -attributet är true, vilket betyder att fler resultat är tillgängliga.  Fortsätt anropa den här slutpunkten tills attributet moreResult returnerar false, vilket betyder att det inte finns några tillgängliga resultat.  The `nextPageToken` som returneras från detta API ska alltid återanvändas för nästa iteration av det här anropet.

```
GET /rest/v1/opportunities/schema/fields.json?batchSize=5
```

```json
{
    "requestId": "b4a#17e995b31da",
    "result": [
        {
            "displayName": "SFDC Oppty Id",
            "name": "externalOpportunityId",
            "description": null,
            "dataType": "string",
            "length": 50,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
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
            "displayName": "Description",
            "name": "description",
            "description": null,
            "dataType": "string",
            "length": 2000,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Type",
            "name": "type",
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
            "displayName": "Stage",
            "name": "stage",
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
    "nextPageToken": "E5ZONGE4SAHALYYW6FS25KB5BM======",
    "moreResult": true
}
```

#### Ta bort

Du kan ta bort affärsmöjligheter genom att ta bort fält eller ID-fält. Ange med `deleteBy` -parameter med ett värde på dedupeFields eller idField. Om inget anges är dedupliceringsfält standard. Begärandetexten innehåller en `input` matris med möjligheter att ta bort. Högst 300 möjligheter per samtal tillåts.

```
POST /rest/v1/opportunities/delete.json
```

```json
{ 
   "deleteBy":"dedupeFields",
   "input":[ 
      { 
         "externalOpportunityId":"19UYA31581L000000"
      },
      { 
         "externalOpportunityId":"29UYA31581L000000"
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
         "marketoGUID":"cff23271-f996-47d7-984f-f2676861b5fb",
         "status":"deleted"
      }
   ]
}
```

## Timeout

- Slutpunkter för affärsmöjlighet har en timeout på 30 sekunder såvida det inte anges nedan
   - Synkroniseringsmöjligheter: 60-tal 
   - Ta bort affärsmöjligheter: 60-tal
