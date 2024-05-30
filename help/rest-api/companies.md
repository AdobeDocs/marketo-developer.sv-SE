---
title: "Företag"
feature: REST API
description: "Konfigurera företagsdata med Marketo API:er."
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '564'
ht-degree: 0%

---


# Företag

[Slutpunktsreferens för företag](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies)

Företagen representerar den organisation som lead-posterna tillhör. Leads läggs till i ett företag genom att fylla i deras motsvarande `externalCompanyId` fält använda [Synkronisera leads](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/syncLeadUsingPOST) eller [Import av massutr](bulk-lead-import.md) slutpunkter. När en lead har lagts till i ett företag kan du inte ta bort den från det företaget (om du inte lägger till den i ett annat företag). Leads som är länkade till en företagspost ärver värdena direkt från en företagspost som om värdena fanns på leadets egen post.

Företags-API:er är skrivskyddade för prenumerationer som har [SFDC-synkronisering](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync.html?lang=en) eller [Microsoft Dynamics Sync](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync.html?lang=en) är aktiverade.

## Beskriv

Genom att beskriva företagsobjektet får du all information du behöver interagera med dem.

```
GET /rest/v1/companies/describe.json
```

```json
{  
   "success":true,
   "requestId":"5847#14d44113ad7",
   "result":[  
      {  
         "name":"Company",
         "description":"Company object",
         "createdAt":"2015-05-11T17:11:32Z",
         "updatedAt":"2015-05-11T17:11:32Z",
         "idField":"id",
         "dedupeFields":[  
            "externalCompanyId"
         ],
         "searchableFields":[  
            [  
               "externalCompanyId"
            ],
            [  
               "id"
            ],
            [  
               "company"
            ]
         ],
         "fields":[  
            {  
               "name":"createdAt",
               "displayName":"Created At",
               "dataType":"datetime",
               "updateable":false
            },
            {  
               "name":"externalCompanyId",
               "displayName":"External Company Id",
               "dataType":"string",
               "length":100,
               "updateable":false
            },
            {  
               "name":"id",
               "displayName":"Id",
               "dataType":"integer",
               "updateable":false
            },
            {  
               "name":"updatedAt",
               "displayName":"Updated At",
               "dataType":"datetime",
               "updateable":false
            },
            {  
               "name":"annualRevenue",
               "displayName":"Annual Revenue",
               "dataType":"currency",
               "updateable":true
            }
            {  
               "name":"company",
               "displayName":"Company Name",
               "dataType":"string",
               "length":255,
               "updateable":true
            }
         ]
      }
   ]
}
```

## Fråga

Mönstret för [fråga företag](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompaniesUsingGET) följer det som finns i API:t för leads med den extra begränsningen att `filterType` parametern godkänner fälten som listas i arrayen searchableFields i anropet till Describe Companies eller dedupeFields.

`filterType` och `filterValues` är obligatoriska frågeparametrar.  `fields`, `nextPageToken`och `batchSize` är valfria parametrar.  Parametrarna fungerar på samma sätt som motsvarande parametrar i API:erna för leads och säljprojekt. När du begär en lista med `fields`om ett visst fält begärs, men inte returneras, anges värdet som null.

Om parametern fields utelämnas returneras följande standarduppsättning med fält:

- id
- dedupeFields
- updatedAt
- createdAt

```
GET /rest/v1/companies.json?filterType=id&filterValues=3433,5345
```

```json
{  
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[  
      {  
         "seq":0,
         "id":3433,
         "externalCompanyId":"19UYA31581L000000",
         "company":"Google"
      },
      {  
         "seq":1,
         "id":5345,
         "externalCompanyId":"29UYA31581L000000",
         "company":"Yahoo"
      }
   ]
}
```

## Skapa och uppdatera

The [Synkronisera företag](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/syncCompaniesUsingPOST) slutpunkten accepterar det som krävs `input` parameter som innehåller en array med företagsobjekt. Precis som möjligheter finns det tre lägen för att skapa och uppdatera företag: createOnly, updateOnly och createOrUpdate.  Lägen anges i `action` parametern för begäran. Båda `dedupeBy` och `action` är valfria parametrar och standard är dedupeFields- och createOrUpdate-lägena.

```
POST /rest/v1/companies.json
```

```
Content-Type: application/json
```

```json
{  
   "action":"createOrUpdate",
   "dedupeBy":"dedupeFields",
   "input":[  
      {  
         "externalCompanyId":"19UYA31581L000000",
         "company":"Google"
      },
      {  
         "externalCompanyId":"29UYA31581L000000",
         "company":"Yahoo"
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
         "id":1232
      },
      {  
         "seq":1,
         "status":"created",
         "id":1323
      }
   ]
}
```

### Fält

Företagsobjektet innehåller en uppsättning fält. Varje fältdefinition består av en uppsättning attribut som beskriver fältet. Exempel på attribut är visningsnamn, API-namn och dataType. Dessa attribut kallas tillsammans för metadata.

Med följande slutpunkter kan du fråga fält på företagsobjektet. Dessa API:er kräver att den ägande API-användaren har en roll med en eller båda av `Read-Write Schema Standard Field` eller `Read-Write Schema Custom Field` behörigheter.

### Frågefält

Att fråga efter företagsfält är enkelt. Du kan fråga ett enskilt företagsfält efter API-namn eller fråga uppsättningen med alla företagsfält.

#### Efter namn

The [Hämta företagsfält efter namn](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompanyFieldByNameUsingGET) slutpunkten hämtar metadata för ett enskilt fält i företagsobjektet. Obligatoriskt `fieldApiName` path-parametern anger fältets API-namn. Svaret liknar slutpunkten för Beskriv företag men innehåller ytterligare metadata som `isCustom` -attribut som anger om fältet är ett anpassat fält.

```
GET /rest/v1/companies/schema/fields/industry.json
```

```json
{
    "requestId": "88f6#17e976d6ab4",
    "result": [
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
        }
    ],
    "success": true
}
```

#### Bläddra

The [Hämta företagsfält](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompanyFieldsUsingGET) slutpunkten hämtar metadata för alla fält i företagsobjektet. Som standard returneras högst 300 poster. Du kan använda `batchSize` frågeparameter för att minska det här talet. Om `moreResult` -attributet är true, vilket betyder att fler resultat är tillgängliga. Fortsätt anropa den här slutpunkten tills attributet moreResult returnerar false, vilket betyder att det inte finns några tillgängliga resultat. The `nextPageToken` som returneras från detta API ska alltid återanvändas för nästa iteration av det här anropet.

```
GET /rest/v1/companies/schema/fields.json?batchSize=5
```

```json
{
    "requestId": "b50e#17e995c2d35",
    "result": [
        {
            "displayName": "Company Name",
            "name": "company",
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
            "displayName": "Site",
            "name": "site",
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
            "displayName": "Website",
            "name": "website",
            "description": null,
            "dataType": "url",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Main Phone",
            "name": "mainPhone",
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
            "displayName": "Annual Revenue",
            "name": "annualRevenue",
            "description": null,
            "dataType": "currency",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        }
    ],
    "success": true,
    "nextPageToken": "L7XD3EFJ3OLFZKXKJBWYULOTRA======",
    "moreResult": true
}
```

### Ta bort

Borttagningsvillkoren anges i `input` -array, som innehåller en lista med sökvärden.  Borttagningsmetoden anges i `deleteBy` parameter.  Tillåtna värden är: dedupliceringsfält, idField.  Standard är dedupliceringsfält.

```
Content-Type: application/json
```

```
POST /rest/v1/companies/delete.json
```

```json
{  
   "deleteBy":"dedupeFields",
   "input":[  
      {  
         "externalCompanyId":"19UYA31581L000000"
      },
      {  
         "externalCompanyId":"29UYA31581L000000"
      },
      {  
         "externalCompanyId":"39UYA31581L000000"
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
         "id":1234,
         "status":"deleted"
      },
      {  
         "seq":1,
         "id":56456,
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

- Företagets slutpunkter har en timeout på 30-tal om inget annat anges nedan
   - Synkronisera företag: 60-tal 
   - Ta bort företag: 60-tal
