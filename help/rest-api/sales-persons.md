---
title: Säljare
feature: REST API
description: Läs data om säljare.
exl-id: f8ed5aa5-63c1-4c5b-8683-bf47eed1ea18
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '303'
ht-degree: 0%

---

# Säljare

[Slutpunktsreferens för säljare](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Sales-Persons)

API:er för säljare är skrivskyddade för prenumerationer som har [SFDC-synkronisering](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync) eller [Microsoft Dynamics Sync](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync) aktiverat. Säljare är en typ av personpost som är säljare av lead-poster. De är relaterade till Lead-poster av fältet externalSalesPersonId på varje Lead-post. När en lead är relaterad till en säljare via ett ifyllt externalSalesPersonId-fält fylls motsvarande sökfält för lead-ägare i för den lead-posten i Marketo, vilket tillåter användning av motsvarande filter och tokens.

Säljare är relaterade till Lead-poster genom att använda slutpunkten [Synkronisera leads](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/syncLeadUsingPOST) och skicka attributet externalSalesPersonId.

Säljare är relaterade till säljprojektsposter genom att använda slutpunkten [Synkroniseringsmöjligheter](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities/operation/syncOpportunitiesUsingPOST) och skicka attributet externalSalesPersonId.

Säljare är relaterade till företagsposter genom att använda slutpunkten [Synkronisera företag](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/syncCompaniesUsingPOST) och skicka attributet externalSalesPersonId.

Försäljningspersonsposter kan bara redigeras via API:t.

## Beskriv

Beskrivningen av poster för säljare följer standardmönstret för lead-databasobjekt.

```
GET /rest/v1/salespersons/describe.json
```

```json
{  
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[  
      {  
         "name":"SalesPerson",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"id",
         "dedupeFields":[  
            "externalSalesPersonId"
         ],
         "searchableFields":[  
            [  
               "email"
            ],
            [  
               "id"
            ],
            [
               "externalSalesPersonId"
            ]
         ],
         "fields":[  
            {  
               "name":"id",
               "displayName":"Marketo Id",
               "dataType":"integer",
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
               "name":"email",
               "displayName":"Email",
               "dataType":"string",
               "length":255,
               "updateable":false
            },
            {  
               "name":"externalSalesPersonId",
               "displayName":"External Sales Person Id",
               "dataType":"string",
               "length":255,
               "updateable":false
            }
         ]
      }
   ]
}
```

Som standard är `idField` för Sales People &quot;id&quot; och `dedupeFields` bara &quot;externalSalesPersonId&quot;.

## Fråga

Säljare som använder standardfrågemönstret för enkla nycklar. I det här exemplet visas användarens e-postadress som används som externalSalesPersonId. Som standard returnerar frågan alla fält som fylls i för de returnerade posterna.

```
GET /rest/v1/salespersons.json?filterType=dedupeFields&filterValues=david@test.com,sam@test.com
```

```json
 {  
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[  
      {  
         "seq":0,
         "id":53453,
         "externalSalesPersonId":"sam@test.com",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:23Z"
      },
      {  
         "seq":1,
         "id":53454,
         "externalSalesPersonId":"david@test.com",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:23Z"
      }
   ]
}
```

## Skapa och uppdatera

Uppdateringsmönstret är standard.

```
POST /rest/v1/salespersons.json
```

```json
{
   "action":"createOrUpdate",
   "dedupeBy":"dedupeFields",
   "input":[
      {
         "externalSalesPersonId":"sam@test.com",
         "email":"sam@test.com",
         "firstName":"Sam",
         "lastName":"Sanosin"
      },
      {
         "externalSalesPersonId":"david@test.com",
         "email":"david@test.com",
         "firstName":"David",
         "lastName":"Aulassak"
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
         "status": "updated",
         "id":45232
      },
      {
         "seq":1,
         "status": "created",
         "id":45236
      }
   ]
}
```

## Ta bort

Mönstret för borttagningar är standard.

Det är inte tillåtet att ta bort säljare när de används. I det här fallet hoppas säljaren över. Exempel:

- När säljaren är associerad med aktiva leads
- När säljaren är associerad med ett företag som har tagits bort

```
POST /rest/v1/salespersons/delete.json
```

```json
{  
   "deleteBy":"dedupeFields",
   "input":[  
      {  
         "externalSalesPersonId":"sam@test.com"
      },
      {  
         "externalSalesPersonId":"david@test.com"
      },
      {  
         "externalSalesPersonId":"raj@test.com"
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
         "id":56343,
         "status": "deleted"
      },
      {
         "seq":1,
         "id":53453,
         "status": "deleted"
      },
      {
         "seq":2,
         "status": "skipped"
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

- Slutpunkter för säljare har en timeout på 30 sekunder om inget annat anges nedan
   - Synkronisera säljare: 60-tal
   - Ta bort säljare: 60-tal
