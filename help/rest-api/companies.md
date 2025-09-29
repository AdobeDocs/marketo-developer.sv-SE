---
title: Företag
feature: REST API
description: Använd Marketo Companies REST API för att beskriva, fråga efter och synkronisera företagsposter, hantera fält och ta bort av externalCompanyId och för att anteckna i CRM-synkronisering som skrivskyddad.
exl-id: 80e514a2-1c86-46a7-82bc-e4db702189b0
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '582'
ht-degree: 0%

---

# Företag

[Företagets slutpunktsreferens](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies)

Företagen representerar den organisation som lead-posterna tillhör. Leads läggs till i ett företag genom att fylla i motsvarande `externalCompanyId`-fält med hjälp av [Synkronisera leads](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/syncLeadUsingPOST) eller [Importera massleads](bulk-lead-import.md) . När en lead har lagts till i ett företag kan du inte ta bort den från det företaget (om du inte lägger till den i ett annat företag). Leads som är länkade till en företagspost ärver värdena direkt från en företagspost som om värdena fanns på leadets egen post.

Företags-API:er är skrivskyddade för prenumerationer som har [SFDC Sync](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync.html?lang=sv-SE) eller [Microsoft Dynamics Sync](https://experienceleague.adobe.com/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync.html?lang=sv-SE) aktiverat.

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

Mönstret för [frågeföretag](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompaniesUsingGET) följer noga det för lead-API:t med den extra begränsningen att parametern `filterType` accepterar fälten som listas i arrayen searchableFields i anropet till Describe Companies eller dedupeFields.

`filterType` och `filterValues` är obligatoriska frågeparametrar.  `fields`, `nextPageToken` och `batchSize` är valfria parametrar.  Parametrarna fungerar på samma sätt som motsvarande parametrar i API:erna för leads och säljprojekt. När en lista med `fields` begärs, om ett visst fält begärs men inte returneras, anges värdet som null.

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

Slutpunkten [Synkronisera företag](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/syncCompaniesUsingPOST) accepterar den obligatoriska parametern `input` som innehåller en array med företagsobjekt. Precis som möjligheter finns det tre lägen för att skapa och uppdatera företag: createOnly, updateOnly och createOrUpdate.  Lägen anges i parametern `action` i begäran. Både parametern `dedupeBy` och parametern `action` är valfria, och som standard används lägena dedupeFields och createOrUpdate.

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

Med följande slutpunkter kan du fråga fält på företagsobjektet. Dessa API:er kräver att den ägande API-användaren har en roll med en eller båda behörigheterna `Read-Write Schema Standard Field` eller `Read-Write Schema Custom Field`.

### Frågefält

Att fråga efter företagsfält är enkelt. Du kan fråga ett enskilt företagsfält efter API-namn eller fråga uppsättningen med alla företagsfält.

#### Efter namn

Slutpunkten [Hämta företagsfält efter namn](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompanyFieldByNameUsingGET) hämtar metadata för ett enskilt fält i företagsobjektet. Sökvägsparametern `fieldApiName` som krävs anger fältets API-namn. Svaret liknar slutpunkten för Beskriv företag men innehåller ytterligare metadata som attributet `isCustom` som anger om fältet är ett anpassat fält.

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

Slutpunkten [Hämta företagsfält](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/getCompanyFieldsUsingGET) hämtar metadata för alla fält i företagsobjektet. Som standard returneras högst 300 poster. Du kan använda frågeparametern `batchSize` för att minska det här talet. Om attributet `moreResult` är true innebär det att fler resultat är tillgängliga. Fortsätt anropa den här slutpunkten tills attributet moreResult returnerar false, vilket betyder att det inte finns några tillgängliga resultat. `nextPageToken` som returneras från detta API ska alltid återanvändas för nästa iteration av det här anropet.

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

Borttagningsvillkoren anges i arrayen `input` som innehåller en lista med sökvärden.  Borttagningsmetoden har angetts i parametern `deleteBy`.  Tillåtna värden är: dedupliceringsfält, idField.  Standard är dedupliceringsfält.

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
