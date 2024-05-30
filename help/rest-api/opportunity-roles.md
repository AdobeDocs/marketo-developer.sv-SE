---
title: "Roller för säljprojekt"
feature: REST API
description: "Hantera affärsmöjlighetsroller i Marketo."
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '253'
ht-degree: 0%

---


# Roller för affärsmöjlighet

[Slutpunktsreferens för affärsmöjlighetsroller](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities/operation/getOpportunityRolesUsingGET)

Leads är kopplade till affärsmöjligheter via mellanliggande `opportunityRole` -objekt.

API:er för säljprojektsroller visas bara för prenumerationer som inte har en intern CRM-synkronisering aktiverad.

## Beskriv

Som affärsmöjligheter visas ett beskrivet anrop och CRUD-åtgärder för affärsmöjlighetsroller.

```
GET /rest/v1/opportunities/roles/describe.json
```

```json
{  
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[  
      {  
         "name":"opportunityRole",
         "displayName":"Opportunity Role",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"marketoGUID",
         "dedupeFields":[  
            "externalOpportunityId",
            "leadId",
            "role"
         ],
         "searchableFields":[  
            [  
               "externalOpportunityId",
               "leadId",
               "role"
            ],
            [  
               "marketoGUID"
            ],
            [  
               "leadId"
            ],
            [  
               "externalOpportunityId"
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
               "name":"externalOpportunityId",
               "displayName":"External Opportunity Id",
               "dataType":"string",
               "length":50,
               "updateable":false
            },
            {  
               "name":"leadId",
               "displayName":"Lead Id",
               "dataType":"integer",
               "updateable":false
            },
            {  
               "name":"role",
               "displayName":"Role",
               "dataType":"string",
               "length":50,
               "updateable":false
            },
            {  
               "name":"isPrimary",
               "displayName":"Is Primary",
               "dataType":"boolean",
               "updateable":true
            },
            {  
               "name":"externalCreatedDate",
               "displayName":"External Created Date",
               "dataType":"datetime",
               "updateable":true
            }
         ]
      }
   ]
}
```

## Fråga

Observera att båda `dedupeFields` och `searchableFields` skiljer sig lite från möjligheter. `dedupeFields` innehåller en sammansatt nyckel, där alla tre `externalOpportunityId`, `leadId`och `role` krävs. Både affärsmöjlighets- och lead-länken för ID-fälten måste finnas i målinstansen för att posten ska kunna skapas. För `searchableFields`, `marketoGUID`, `leadId`och `externalOpportunityId` gäller alla frågor fristående och använder ett mönster som är identiskt med säljprojekt, men det finns ytterligare ett alternativ att använda den sammansatta nyckeln för att fråga, vilket kräver att ett JSON-objekt skickas via POST, med den extra frågeparametern `_method=GET`.

```
POST /rest/v1/opportunities/roles.json?_method=GET
```

```json
{  
   "filterType": "dedupeFields",
   "fields": [  
      "marketoGuid",
      "externalOpportunityId",
      "leadId",
      "role"
   ],
   "input": [  
      {  
        "externalOpportunityId": "Opportunity1",
        "leadId": 1,
        "role": "Captain"
      },
      {  
        "externalOpportunityId": "Opportunity2",
        "leadId": 1872,
        "role": "Commander"
      },
      {  
        "externalOpportunityId": "Opportunity3",
        "leadId": 273891,
        "role": "Lieutenant Commander"
      }
   ]
}
```

Detta ger samma typ av svar som en vanlig GET-fråga, det har helt enkelt ett annat gränssnitt för att göra begäran.

## Skapa och uppdatera

Affärsmöjlighetsroller har samma gränssnitt för att skapa och uppdatera poster som affärsmöjligheter.

```
POST /rest/v1/opportunities/roles.json
```

```json
{
   "action": "createOrUpdate",
   "dedupeBy": "dedupeFields",
   "input": [
      {  
         "externalOpportunityId": "19UYA31581L000000",
         "leadId": 456783,
         "role": "Technical Buyer",
         "isPrimary": false
      },
      {
         "externalOpportunityId": "19UYA31581L000000",
         "leadId": 456784,
         "role": "Technical Buyer",
         "isPrimary": false
      }
   ]
}
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result":[
      {
         "seq": 0,
         "status": "updated",
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
      },
      {
         "seq": 1,
         "status": "created",
         "marketoGUID": "cff23271-f996-47d7-984f-f2676861b5fb"
      }
   ]
}
```

## Ta bort

Du kan ta bort affärsmöjlighetsroller genom att ta bort fält eller ID-fält. Ange med parametern deleteBy med värdet för dedupeFields eller idField. Om inget anges är dedupliceringsfält standard. Begärandetexten innehåller en indatamatris med affärsmöjlighetsroller som ska tas bort. Högst 300 affärsmöjlighetsroller per samtal tillåts.

```
POST /rest/v1/opportunities/roles/delete.json
```

```json
{  
   "deleteBy": "dedupeFields",
   "input": [  
      {  
        "externalOpportunityId": "19UYA31581L000000",
        "leadId": 456783,
        "role": "Technical Buyer"
      }
   ]
}
```

```json
{
    "requestId": "10f7c#173264db42d",
    "result": [
        {
            "seq": 0,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
            "status": "deleted"
        }
    ]
    "success": true
}
```

## Timeout

- Slutpunkter för säljprojektsroll har en timeout på 30 sekunder om inget annat anges nedan
   - Roller för synkroniseringsmöjlighet: 60-tal 
   - Ta bort säljprojektsroller: 60-tal
