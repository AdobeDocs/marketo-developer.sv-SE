---
title: Anpassade objekt
feature: REST API, Custom Objects
description: Skapa och hantera anpassade Marketo-objekt.
exl-id: 88e8829b-f8f1-46d7-a753-5aa6e20e2c40
source-git-commit: 981ed9b254f277d647a844803d05a1a2549cbaed
workflow-type: tm+mt
source-wordcount: '2909'
ht-degree: 0%

---

# Anpassade objekt

[**Referens för anpassade objektslutpunkter**](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects) Med Marketo kan användare definiera anpassade Marketo-objekt som är relaterade till Marketo standardobjekt (leads, företag) eller andra anpassade Marketo-objekt.  Marketo anpassade objekt kan skapas med Marketo-gränssnittet enligt beskrivningen [här](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/create-marketo-custom-objects) eller med API:t för anpassade objektmetadata enligt beskrivningen nedan.

En lämplig prenumerationstyp för Marketo krävs för att komma åt API:t för anpassade objektmetadata.  Mer information finns i din CSM.

## Lista

Förutom standardanropen för beskrivning, fråga, uppdatering och borttagning för lead-databasobjekt har anpassade objekt ett [listanrop](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectsUsingGET) tillgängligt.  Om du anropar den här slutpunkten returneras ett svar med en lista över anpassade objekt som är tillgängliga i målinstansen, tillsammans med ytterligare metadata om objekten.

```
GET /rest/v1/customobjects.json
```

```json
{
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[
      {
         "name":"Car",
         "displayName":"Car",
         "description":"Car owner",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"marketoGUID",
         "dedupeFields":["vin"],
         "searchableFields":[
            ["vin"],
            ["marketoGUID"],
            ["siebelId"]
         ],
         "relationships":[
            {
               "field":"siebelId",
               "type":"parent",
               "relatedTo":{
                  "name":"Lead",
                  "field":"siebelId"
               }
            }
         ]
      }
   ]
}
```

Svaret ger en lista över de relationer som finns för varje objekt.  En relation kommer att ha en `field`-medlem som anger fältet på objektet som innehåller länkvärdet, en `type`-medlem som anger om relationen är till ett överordnat eller underordnat typobjekt och ett `relatedTo`-objekt som anger namnet på det relaterade objektet samt länkfältet på det objektet.

## Beskriv

[Beskriv anropet](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/describeUsingGET_1) för anpassade objekt följer samma mönster som för säljprojekt och företag, där arrayen `relationships` läggs till i svaret och en `apiName` path-parameter i URI:n som tar API-namnet för den anpassade objekttypen som ska beskrivas.  Precis som för listanropet listas alla relationer som är tillgängliga för den här anpassade objekttypen.

```
GET /rest/v1/customobjects/{apiName}/describe.json
```

```json
{
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[
      {
         "name":"Car",
         "displayName":"Car",
         "description":"Car owner",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"marketoGUID",
         "dedupeFields":["vin"],
         "searchableFields":[
            ["vin"],
            ["marketoGUID"],
            ["siebelId"]
         ],
         "relationships":[
            {
               "field":"siebelId",
               "type":"parent",
               "object":{
                  "name":"Lead",
                  "field":"siebelId"
               }
            }
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
               "name":"vin",
               "displayName":"VIN",
               "description":"Vehicle Identification Number",
               "dataType":"string",
               "length":36,
               "updateable":false
            },
            {
               "name":"siebelId",
               "displayName":"External Id",
               "description":"External Id",
               "dataType":"string",
               "length":36,
               "updateable":true
            },
            {
               "name":"make",
               "displayName":"Make",
               "dataType":"string",
               "length":36,
               "updateable":true
            },
            {
               "name":"model",
               "displayName":"Model",
               "description":"Vehicle Model",
               "dataType":"string",
               "length":255,
               "updateable":true
            },
            {
               "name":"year",
               "displayName":"Year",
               "dataType":"integer",
               "updateable":true
            },
            {
               "name":"color",
               "displayName":"Color",
               "description":"Vehicle color",
               "dataType":"String",
               "length": 255,
               "updateable":true
            }
         ]
      }
   ]
}
```

## Fråga

[Att fråga efter anpassade objekt](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectsUsingGET) skiljer sig något från andra API:er för Lead-databaser och tar `apiName` sökvägsparametrar som beskrivits.  För en normal filterType-parameter är frågan en enkel GET-fråga, precis som andra typer av poster, och kräver en `filterType` och `filterValues`.  Det kan också acceptera parametrarna `**fields**`, `batchSize` och `nextPageToken`.  När du begär en lista med fält, om ett visst fält begärs men inte returneras, anges värdet som null.

```
GET /rest/v1/customobjects/{apiName}.json?filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fa,dff23271-f996-47d7-984f-f2676861b5fb
```

```
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fa",
         "vin":"19UYA31581L000000",
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      },
      {
         "seq":1,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb",
         "vin":"29UYA31581L000000",
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      },
   ]
}
```

När du frågar med sammansatta nycklar fungerar API:t som API:t för säljprojektsroller och accepterar en POST med en JSON-brödtext.  JSON-brödtexten kan ha samma medlemmar som en GET-fråga, förutom `filterValues`.  I stället för filtervärden finns det en `input`-array som tar objekt som innehåller en medlem som namnges för var och en av objekttypens `dedupeFields`.

```
POST /rest/v1/customobjects/{apiName}.json?_method=GET
```

```json
{
   "filterType":"dedupeFields",
   "fields":[
      "marketoGuid",
      "Bedrooms",
      "yearBuilt"
   ],
   "input":[
      {
         "mlsNum":"1962352",
         "houseOwnerId":"42645756"
      },
      {
         "mlsNum":"2962352",
         "houseOwnerId":"52645756"
      },
      {
         "mlsNum":"3962352",
         "houseOwnerId":"62645756"
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
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fa",
         "Bedrooms":3,
         "yearBuilt":1948,
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      },
      {
         "seq":1,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb",
         "Bedrooms":4,
         "yearBuilt":1956,
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      },
      {
         "seq":2,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fc",
         "Bedrooms":3,
         "yearBuilt":2001,
         "createdAt":"2015-02-23T18:21:53Z",
         "updatedAt":"2015-02-23T18:23:41Z"
      }
   ]
}
```

## Skapa och uppdatera

Använd slutpunkten [Synkronisera anpassade objekt](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/syncCustomObjectsUsingPOST) för att skapa eller uppdatera anpassade objekt, du kan ange åtgärden med parametern `action`.  Upp till 300 poster kan skapas eller uppdateras i ett samtal.  Värdena som används i arrayen `input` baseras i huvudsak på informationen som returneras av slutpunkten [Beskriv anpassade objekt](https://experienceleague.adobe.com/en/docs/marketo-developer/marketo/rest/endpoint-reference#!/Custom_Objects/describeUsingGET_1) . I ett bildobjekt finns det bara ett dedupliceringsfält, `vin`.  För att kunna uppdatera eller skapa poster i läget dedupeFields måste varje post i inmatningsarrayen innehålla minst ett `vin`-fält.

```
POST /rest/v1/customobjects/{apiName}.json
```

```json
{
   "action":"updateOnly",
   "dedupeBy":"dedupeFields",
   "input":[
      {
         "vin":"19UYA31581L000000",
         "siebelId":"f2676861b5fb",
         "make":"BMW",
         "model":"3-Series 330i",
         "year":2003
      },
      {
         "vin":"29UYA31581L000000",
         "siebelId":"f2676861b5fc",
         "make":"BMW",
         "model":"3-Series 330i",
         "year":2003
      },
      {
         "vin":"39UYA31581L000000",
         "siebelId":"f2676861b5fd",
         "make":"BMW",
         "model":"3-Series 330i",
         "year":2003
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
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb"
      },
      {
         "seq":1,
         "status": "created",
         "marketoGUID":"cff23271-f996-47d7-984f-f2676861b5fb"
      },
      {
         "seq":2,
         "status": "skipped"
         "reasons":[
            {
               "code":"1004",
               "message":"Lead not found"
            }
         ]
      }
   ]
}
```

När du utför uppdateringar i `idField`-läge kommer `idField` alltid att vara `marketoGUID`, så varje post kräver alltid ett `marketoGUID`-fält.  Kom ihåg att `idField` bara är giltigt för åtgärdstypen updateOnly, eftersom det här fältet alltid är systemhanterat.  Svaret inkluderar **status** för varje enskild post i resultatarrayen, och antingen en `marketoGUID` - eller en `reasons` -array beroende på om åtgärden lyckades för en enskild post eller inte.

## Ta bort

[Det är mycket enkelt att ta bort poster](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/deleteCustomObjectsUsingPOST).  Välj `deleteBy`-läge, antingen `idField` eller `dedupeFields`, och ta med motsvarande fält i varje post i `input`-arrayen. Högst 300 poster per anrop tillåts.

```
POST /rest/v1/customobjects/{apiName}/delete.json
```

```json
{
   "deleteBy":"dedupeFields",
   "input":[
      {
         "vin":"19UYA31581L000000"
      },
      {
         "vin":"29UYA31581L000000"
      },
      {
         "vin":"39UYA31581L000000"
      }
   ]
}

{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb",
         "status": "deleted"
      },
      {
         "seq":1,
         "marketoGUID":"da42707c-4dc4-4fc1-9fef-f30a3017240a",
         "status": "deleted"
      },
      {
         "seq":2,
         "status": "skipped"
         "reasons":[
            {
               "code":"1013",
               "message":"Object not found"
            }
         ]
      }
   ]
}
```

När du uppdaterar kommer resultatet att innehålla en status för varje enskild post och antingen en `marketoGUID`- eller en `reasons`-array beroende på om borttagningen lyckades eller inte.

## Anpassade objekttyper

Med API:t för anpassade objektmetadata kan du fjärrhantera anpassade objektscheman.  Med API kan du skapa en ny anpassad objekttyp eller ändra en befintlig.  När den anpassade objekttypen har skapats eller ändrats måste den godkännas för användning.  Mer information om anpassade objekt finns i produktdokumentationen [här](https://experienceleague.adobe.com/en/docs/marketo/using/home).

* Anpassade objekttyper som skapas av API:t kan inte ändras med Marketo-gränssnittet
* Högsta tillåtna antal anpassade objekttyper är 10
* Det högsta tillåtna antalet anpassade objektfält är 50 per typ
* API-namn och visningsnamn för anpassade objekttyper kan innehålla alfanumeriska tecken och understreck&quot;_&quot;

### Frågetyp

Det finns två sätt att hämta metadata för anpassad objekttyp: Beskriv anpassad objekttyp, som returnerar  Posten för en anpassad objekttyp, som kan filtreras efter godkännandetillstånd, och Lista anpassade objekttyper, som returnerar en lista över alla anpassade objekttyper i prenumerationen och som kan filtreras efter namn och godkännandetillstånd.

### Beskriv typ

Slutpunkten [Beskriv anpassad objekttyp](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/describeUsingGET_1) returnerar metadata för en anpassad objekttyp. Den obligatoriska `apiName`-sökvägsparametern är API-namnet för den anpassade objekttypen som beskrivs.  Om det finns en godkänd version returneras den.  Annars returneras utkastversionen.  Den valfria parametern `state` används för att ange vilken version som ska returneras: `draft`, `approved` eller `approvedWithDraft`.

```
GET /rest/v1/customobjects/schema/{apiName}/describe.json?state=approved
```

```json
{
    "requestId": "d9bf#16876fa84b9",
    "result": [
        {
            "state": "approved",
            "version": "approved",
            "displayName": "Car",
            "description": "Automobile owned",
            "apiName": "car",
            "idField": "marketoGUID",
            "createdAt": "2019-01-22T19:12:18Z",
            "updatedAt": "2019-01-22T19:12:18Z",
            "dedupeFields": [
                "vin"
            ],
            "searchableFields": [
                [
                    "vin"
                ],
                [
                    "marketoGUID"
                ],
                [
                    "leadID"
                ]
            ],
            "relationships": [
                {
                    "field": "leadID",
                    "type": "child",
                    "relatedTo": {
                        "name": "Lead",
                        "field": "id"
                    }
                }
            ],
            "fields": [
                {
                    "name": "createdAt",
                    "displayName": "Created At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "marketoGUID",
                    "displayName": "Marketo GUID",
                    "dataType": "string",
                    "length": 36,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "Updated At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "leadID",
                    "displayName": "Lead ID",
                    "dataType": "integer",
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "make",
                    "displayName": "Make",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "model",
                    "displayName": "Model",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "vin",
                    "displayName": "VIN",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "year",
                    "displayName": "Year",
                    "dataType": "integer",
                    "updateable": true,
                    "crmManaged": false
                }
            ]
        }
    ],
    "success": true
}
```

Här ser vi följande attribut:

* Metadata: state, displayName, description, apiName, idField, createdAt, updatedAt, dedupeFields, searchableFields, relationer
* Standardfält: marketoGUID, createdAt, updatedAt
* Anpassade fält leadId, vin, make,  modell, år

### Listtyper

Slutpunkten [Lista anpassade objekttyper](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/listCustomObjectTypesUsingGET) returnerar metadata för alla anpassade objekttyper som är tillgängliga i målinstansen.  Observera att den här slutpunkten liknar [Visa anpassade objekt](https://experienceleague.adobe.com/docs/marketo-developer/marketo/soap/custom-objects/custom-objects.html?lang=en), men är mer omfattande och innehåller ytterligare metadata som tillstånd, relationer och fält. Om det finns en godkänd version returneras den.  Annars returneras utkastversionen.  Den valfria parametern **state** används för att ange vilken version av den anpassade objekttypen som ska returneras: **draft**, **approved** eller **approvedWithDraft**.  Den valfria parametern **names** används för att ange specifika namn på anpassade objekttyper som ska returneras. Den är strukturerad som en kommaavgränsad lista med API-namn.

```
GET /rest/v1/customobjects/schema.json?names=purchaseHistory
```

```json
{
    "requestId": "a181#167ebe94703",
    "result": [
        {
            "state": "approved",
            "displayName": "Purchases",
            "description": "Purchase data",
            "apiName": "purchaseHistory",
            "idField": "marketoGUID",
            "createdAt": "2014-09-12T16:13:37Z",
            "updatedAt": "2014-09-12T16:13:42Z",
            "dedupeFields": [
                "lead_id",
                "product_name"
            ],
            "searchableFields": [
                [
                    "lead_id",
                    "product_name"
                ],
                [
                    "marketoGUID"
                ],
                [
                    "lead_id"
                ]
            ],
            "relationships": [
                {
                    "field": "lead_id",
                    "type": "child",
                    "relatedTo": {
                        "name": "Lead",
                        "field": "lead_id"
                    }
                }
            ],
            "fields": [
                {
                    "name": "marketoGUID",
                    "displayName": "marketoGUID",
                    "dataType": "string",
                    "length": 36,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "amount",
                    "displayName": "Amount",
                    "dataType": "float",
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "lead_id",
                    "displayName": "lead_id",
                    "dataType": "integer",
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "product_name",
                    "displayName": "Product Name",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "purchase_date",
                    "displayName": "Transaction Date",
                    "dataType": "datetime",
                    "updateable": true,
                    "crmManaged": false
                }
            ]
        },
        {
            "state": "approved",
            "version": "approved",
            "displayName": "Car",
            "description": "No really, it's a car!",
            "apiName": "car_c",
            "idField": "marketoGUID",
            "createdAt": "2017-02-22T19:55:51Z",
            "updatedAt": "2018-12-11T23:52:56Z",
            "dedupeFields": [
                "vin"
            ],
            "searchableFields": [
                [
                    "vin"
                ],
                [
                    "marketoGUID"
                ]
            ],
            "relationships": [],
            "fields": [
                {
                    "name": "createdAt",
                    "displayName": "Created At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "marketoGUID",
                    "displayName": "Marketo GUID",
                    "dataType": "string",
                    "length": 36,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "Updated At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "color",
                    "displayName": "Color",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "make",
                    "displayName": "Make",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "model",
                    "displayName": "Model",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "vin",
                    "displayName": "VIN",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "year",
                    "displayName": "Year",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                }
            ]
        }
    ],
    "success": true
}
```

### Skapa och uppdatera typ

#### Skapa typ

Slutpunkten [Synkronisera anpassad objekttyp](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/syncCustomObjectsUsingPOST) används för att skapa eller uppdatera en anpassad objekttyp.  Poståtgärden som ska utföras styrs av det valfria attributet **action** som kan vara **createOnly**, **createOrUpdate** eller **updateOnly**.  Standardinställningen är createOrUpdate. Attributen **displayName** och **apiName** krävs, utom när updateOnly används som åtgärd.   Båda typerna måste vara unika för att undvika kollisioner med kundtilldelade typer.  Om du är en LaunchPoint-partner bör du lägga till ett representativt namnutrymme till dessa namn.  För apiName är regeln att använda gemener eller camelCase för att skilja mellan andra textsträngar. Det valfria attributet **pluralName** anger den plurala formen av displayName.  Det valfria attributet **description** används för att beskriva den anpassade objekttypen.  Det booleska attributet **showInLeadDetail** (valfritt) används för att aktivera visning av anpassade objektdata på Lead Database-sidan i Marketo-gränssnittet.  Standardinställningen är false.

Var försiktig när du namnger anpassade objekt. När du skapar ett nytt anpassat objekt bör du lägga till en sträng som anger företagets namn (alfanumeriskt eller understreck tillåts) som prefix för namnet. Det gör det anpassade objektet enkelt sökbart i användargränssnittet för MLM och hjälper även till att vara osäker på att namnet är unikt.

Här är ett exempel på hur du skapar en ny anpassad objekttyp med API  Namnet&quot;transaktion&quot;.

```
POST /rest/v1/customobjects/schema.json
```

```json
{
  "action":"createOnly",
  "displayName": "Transaction",
  "apiName": "transaction",
  "description": "Commerce happens"
}
```

```json
{
    "requestId": "fb9d#167f2879557",
    "result": [],
    "success": true
}
```

Här följer ett anrop som beskriver den nyskapade typen.

```
GET /rest/v1/customobjects/schema/transaction/describe.json
```

```json
{
    "requestId": "cf9b#167f28db0a9",
    "result": [
        {
            "state": "draft",
            "displayName": "Transaction",
            "description": "Commerce happens",
            "apiName": "transaction",
            "idField": null,
            "createdAt": null,
            "updatedAt": null,
            "dedupeFields": [],
            "searchableFields": [
                []
            ],
            "relationships": [],
            "fields": [
                {
                    "name": "marketoGUID",
                    "displayName": "Marketo GUID",
                    "dataType": "string",
                    "length": 36,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "createdAt",
                    "displayName": "Created At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "Updated At",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                }
            ]
        }
    ],
    "success": true
}
```

Här ser vi följande anpassade objektrelaterade data:

* Metadata: state, displayName, description, apiName, idField, createdAt, updatedAt, dedupeFields, searchableFields, relationer
* Standardfält: marketoGUID, createdAt, updatedAt

#### Uppdateringstyp

Här är ett exempel på hur du uppdaterar beskrivningen för en befintlig typ vars API-namn är &quot;transaction&quot;.  Attributet **apiName** krävs.  Här antar vi att typen redan finns och använder updateOnly för det valfria **action**-attributet.  Förutom **apiName** kan de attribut som är tillgängliga för att skapas uppdateras.

```
POST /rest/v1/customobjects/schema.json
```

```json
{
  "action":"updateOnly",
  "apiName": "transaction",
  "description":"No really, commerce happens!"
}
```

```json
{
    "requestId": "103c3#167f2223fd7",
    "result": [],
    "success": true
}
```

## Typgodkännande

Anpassade objekttyper måste godkännas innan de kan användas. När en ny anpassad objekttyp skapas med slutpunkten [Synkronisera anpassad objekttyp](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/syncCustomObjectTypeUsingPOST) skapas den som ett utkast. När du är klar med att lägga till anpassade fält måste du godkänna utkastet. Detta skapar en godkänd version och tar bort utkastet. När en befintlig anpassad objekttyp ändras med slutpunkten Synkronisera anpassad objekttyp, eller genom att använda någon av ändpunkterna Lägg till/Uppdatera/Ta bort anpassat objekttypfält, skapas ett utkast. Alla ändringar av typen eller av fälten påverkar bara utkastversionen. När du är klar med ändringarna måste du godkänna utkastet. Detta ersätter den godkända versionen med utkastversionen och tar bort utkastversionen. Mer information om godkännande av anpassade objekt finns i produktdokumentationen [här](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/approve-a-custom-object).

När en anpassad objekttyp har godkänts kan du inte:

* Uppdatera `displayName` eller `apiName`
* Lägga till eller ta bort ett länkfält
* Lägga till eller ta bort ett dedupliceringsfält

Av dessa skäl är det viktigt att noga tänka igenom schemat och namnkonventionen som du tänker använda.

### Godkänn typ

Använd slutpunkten [Godkänn anpassad objekttyp](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/approveCustomObjectTypeUsingPOST) för att publicera ett utkast som den nya godkända versionen.  **apiName** är den enda obligatoriska parametern som en sökvägsparameter.  En typ kan inte godkännas om den inte är i utkastläge och uppfyller en uppsättning valideringsregler som beskrivs [här](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/approve-a-custom-object).

```
POST /rest/v1/customobjects/schema/{apiName}/approve.json
```

```json
{
    "requestId": "11d86#1685304a983",
    "result": [],
    "success": true
}
```

### Ignorera typ

Använd slutpunkten [Ignorera anpassad objekttyp för utkast](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/discardCustomObjectTypeUsingPOST) för att ta bort ett utkast. `apiName` är den enda obligatoriska parametern som en sökvägsparameter. En typ måste vara i utkasttillstånd för att kasseras, dvs. en godkänd typ kan inte ignoreras.

```
POST /rest/v1/customobjects/schema/{apiName}/discardDraft.json
```

```json
{
    "requestId": "5228#1684edde793",
    "result": [],
    "success": true
}
```

### Ta bort typ

Använd slutpunkten [Ta bort anpassad objekttyp](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/deleteCustomObjectsUsingPOST) för att ta bort en godkänd version.  `apiName` är den enda obligatoriska parametern som en sökvägsparameter.  Observera att detta är en destruktiv åtgärd som inte kan ångras.  En typ kan inte tas bort om den inte har tagits bort från användning av resurser, som utlösare eller filter.  Assets-slutpunkten Hämta anpassat objektberoende kan användas för att hämta en lista med beroende resurser för en viss typ.

POST /rest/v1/customobjects/schema/{apiName}/delete.json

```json
{
    "requestId": "14e36#1684efc4227",
    "result": [],
    "success": true
}
```

## Anpassade objektfält

Som standard innehåller alla anpassade objekttyper följande standardfält:

* Marketo GUID - Unik identifierare för anpassad objekttyp
* Skapad den - datum när en anpassad objekttyp skapades
* Uppdaterades den - datum då den anpassade objekttypen senast uppdaterades

Du kan lägga till/ändra/ta bort anpassade fält med slutpunkterna som beskrivs nedan.

* Högsta tillåtna antal fält är 50
* När ett anpassat objekt har godkänts kan du lägga till högst 20 ytterligare fält till det anpassade objektet
* Minst ett dedupliceringsfält krävs, högst 3 tillåts
* Fält-API-namn och visningsnamn kan innehålla alfanumeriska tecken och understreck &quot;_&quot;

Mer information om anpassade objektfält finns i produktdokumentationen [här](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields).

### Lägg till fält

Med slutpunkten [Lägg till anpassade objekttypfält](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/addCustomObjectTypeFieldsUsingPOST) kan du lägga till ett eller flera fält i det anpassade objektet.  Begärandetexten innehåller en `input`-matris med ett eller flera element.  Varje element är ett JSON-objekt med attribut som beskriver ett fält. Det obligatoriska attributet `name` är fältets API-namn och måste vara unikt för det anpassade objektet.   Konventionen är att använda gemener eller camelCase för att skilja mellan andra textsträngar. Det obligatoriska attributet `displayName` är fältets läsbara namn och måste vara unikt för det anpassade objektet. Det obligatoriska `dataType`-attributet är fältets datatyp.  A  lista över tillåtna datatyper kan hämtas genom anrop av slutpunkten [Hämta datatyper för anpassade objektfält](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeFieldDataTypesUsingGET).  Anpassade objekt kan innehålla fält med datatypen &quot;link&quot;.  Länkfält används för att skapa relationer mellan anpassade objekt och andra objekttyper i systemet, t.ex. Lead, Company.  Mer information om länkfält finns [här](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields). Det valfria attributet `description` är beskrivningen av fältet. Det booleska attributet `isDedupeField` (valfritt) anger om fältet används för borttagning av dubbletter under anpassade objektuppdateringsåtgärder.  Standardinställningen är false.  För en-till-många-relationer krävs ett dedupliceringsfält. Det valfria objektattributet `relatedTo` anger ett länkfält.  För en-till-många-relationer innehåller det här objektet ett `name`-attribut som är &quot;link object&quot; eller det överordnade objektet att länka till, och ett `field`-attribut som är &quot;link field,&quot;  eller fältet i det överordnade objektet som ska användas som nyckelattribut.  Anropa slutpunkten [Hämta länkade objekt för anpassat objekt](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeLinkableObjectsUsingGET) för att hämta en lista över tillåtna läntobjekt.  Mer information om länkfält finns i produktdokumentationen [här](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-fields). Ett anpassat objekt kan inte länka till ett annat anpassat objekt som har ett befintligt länkfält.

### En-till-många-relation

För en anpassad objektstruktur med en-till-många använder du ett länkfält i ett anpassat objekt för att ansluta det till ett standardobjekt: Lead eller Company. Med hjälp av bilägarexemplet från Marketo produktdokumentation [här](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-link-fields#AddMarketoCustomObjectLinkFields-CreateaLinkFieldforaOne-to-ManyStructure) skapar vi ett anpassat objekt som innehåller bilrelaterad information för att ansluta till leads.

1. Skapa ett **bildobjekt**
1. Lägg till fält i **Car**-objektet: ta bort på **VIN**, länka till **Lead***/Lead-ID**
1. Godkänn **bil**-objekt

Skapa först den anpassade objekttypen som innehåller bilspecifik information.

```
POST /rest/v1/customobjects/schema.json
```

```json
{
    "action":"createOnly",
    "displayName": "Car",
    "pluralName": "Cars"
    "apiName": "car",
    "description": "Automobile owned",
    "showInLeadDetail": true
}
```

```json
{
    "requestId": "cbaa#16876dd3da6",
    "result": [],
    "success": true
}
```

Lägg nu till fält i den anpassade objekttypen Car. Vi använder ett länkfält för att ange både objektet och fältet som ska anslutas. I det här fallet är läntobjektet Lead och länkfältet är ID. Använd ett strängfält för deduplicering (VIN).  Vi ska lägga till ytterligare tre fält för att lagra ytterligare diagramattribut (Märke, Modell, År).

```
POST /rest/v1/customobjects/schema/car/addField.json
```

```json
{
  "input": [
    {
      "displayName": "Lead ID",
      "description": "Link field to Lead object",
      "name": "leadID",
      "dataType": "link",
      "relatedTo": {
        "field": "id",
        "name": "lead"
      }
    },
    {
      "displayName": "VIN",
      "description": "Vehicle ID number",
      "name": "vin",
      "dataType": "string",
      "isDedupeField": true
    },
    {
      "displayName": "Make",
      "description": "Vehicle make",
      "name": "make",
      "dataType": "string"
    },
    {
      "displayName": "Model",
      "description": "Vehicle model",
      "name": "model",
      "dataType": "string"
    },
    {
      "displayName": "Year",
      "description": "Vehicle year",
      "name": "year",
      "dataType": "integer"
    }
  ]
}

{
    "requestId": "b359#16876f17996",
    "result": [],
    "success": true
}
```

Godkänn slutligen den anpassade objekttypen.

```
POST /rest/v1/customobjects/schema/course/approve.json
```

```json
{
    "requestId": "460b#16896055fa3",
    "result": [],
    "success": true
}
```

### Många-till-många-relation

Många-till-många-relationer representeras med ett&quot;brygga&quot;, eller mellanliggande, anpassat objekt mellan ett anpassat standardobjekt, som Lead eller Company, och ett anpassat&quot;edge&quot;-objekt. edge-objektet är den primära entiteten som innehåller beskrivande attribut (fält). Bryggobjektet innehåller de data som behövs för att lösa objektrelationerna med hjälp av två länkfält.  Ett länkfält pekar tillbaka till det överordnade standardobjektet precis som i en  1:N-relationskonfiguration.  Det andra länkfältet pekar på kantobjektet, som är ett anpassat objekt utan länkar.  Bryggobjektet kan också innehålla beskrivande attribut (fält). Med hjälp av exemplet på högskoleregistrering från Marketo produktdokumentation [här](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/add-marketo-custom-object-link-fields#AddMarketoCustomObjectLinkFields-CreateaLinkFieldforaOne-to-ManyStructure) skapar vi ett anpassat kantobjekt som innehåller kursrelaterad information och ett registreringsbryggobjekt som används för att ansluta kurser med leads. Så här gör du:

1. Skapa ett **Kurs**-kantobjekt
1. Lägg till fält i **Kurs:** deduplicera på **Kurs-ID**
1. Godkänn **kurs**
1. Skapa ett **Registreringsbryggobjekt**
1. Lägg till fält i **Registrering:** deduplicering på **Registrerings-ID**, länka till fältet **Kurs***/Kurs-ID** och länka till fältet **Lead****/Lead-ID**
1. Godkänn **registrering**

Skapa först kantobjekttypen som innehåller kursspecifik information:

```
POST /rest/v1/customobjects/schema.json
```

```json
{
    "action":"createOnly",
    "displayName": "Course",
    "pluralName": "Courses",
    "apiName": "course",
    "description": "Modeling a college course, an edge object in Marketo",
    "showInLeadDetail": true
}
```

```json
{
    "requestId": "4aec#168879ede00",
    "result": [],
    "success": true
}
```

Sedan lägger vi till anpassade fält till kantobjektstypen.  I det här exemplet ska vi lägga till följande fyra anpassade fält för att skapa en högskolekurs: Kurs-ID, Kursinstruktör, Kursplats, Kursnamn.  Observera att vi anger kurs-ID som dedupliceringsfält, eftersom minst ett dedupliceringsfält krävs.

```
POST /rest/v1/customobjects/schema/course/addField.json
```

```json
{
    "input": [
        {
            "displayName": "Course ID",
            "name": "courseID",
            "dataType": "string",
            "isDedupeField": true
        },
        {
            "displayName": "Course Instructor",
            "name": "courseInstructor",
            "dataType": "string"
        },
        {
            "displayName": "Course Location",
            "name": "courseLocation",
            "dataType": "string"
        },
        {
            "displayName": "Course Name",
            "name": "courseName",
            "dataType": "string"
        }
    ]
}
```

```
{
    "requestId": "cc36#16895b82a41",
    "result": [],
    "success": true
}
```

Nu måste vi godkänna kantobjekttypen så att vi kan referera till den senare när vi länkar till bryggans objekttyp.  Observera att anpassade objekttyper måste godkännas för att kunna markeras som läntobjekt.

```
POST /rest/v1/customobjects/schema/course/approve.json
```

```json
{
    "requestId": "460b#16896055fa3",
    "result": [],
    "success": true
}
```

Kantobjektet är färdigt.  Låt oss nu gå vidare och skapa en bryggobjekttyp som innehåller registreringsspecifik information.

```
POST /rest/v1/customobjects/schema.json
```

```json
{
    "action": "createOnly",
    "displayName": "Enrollment",
    "pluralName": "Enrollments",
    "apiName": "enrollment",
    "description": "Bridge object for Course custom object",
    "showInLeadDetail": true
}
```

```json
{
    "requestId": "8fbb#168960f671b",
    "result": [],
    "success": true
}
```

Om du vill lägga till anpassade fält till överbryggningsobjekttypen lägger du till två länkfält: ett som länkar till Lead-objektet och ett annat som länkar till det kursobjekt som vi nyss skapade. Använd fältet Lead-ID om du vill länka till Lead-objektet. Om du vill länka till kursobjektet använder du fältet Kurs-ID.  Lägg sedan till en unik identifierare för registrerings-ID som dedupliceringsfält eftersom minst ett dedupliceringsfält krävs. Lägg slutligen till ett klassfält som visar hur eleven klarade sig.

```
POST /rest/v1/customobjects/schema/enrollment/addField.json
```

```json
{
    "input": [
        {
            "displayName": "Lead ID",
            "description": "Link field to Lead object",
            "name": "leadID",
            "dataType": "link",
            "relatedTo": {
                "field": "id",
                "name": "lead"
            }
        },
        {
            "displayName": "Course ID",
            "description": "Link field to Course object",
            "name": "courseID",
            "dataType": "link",
            "relatedTo": {
                "field": "courseID",
                "name": "course"
            }
        },
        {
            "displayName": "Enrollment ID",
            "description": "Unique ID for deduplication",
            "name": "enrollmentID",
            "dataType": "string",
            "isDedupeField": true
        },
        {
            "displayName": "Grade",
            "description": "Grade for the course",
            "name": "grade",
            "dataType": "string"
        }
    ]
}
```

```json
{
    "requestId": "7be5#168973f5052",
    "result": [],
    "success": true
}
```

Godkänn till sist bryggobjektet.

```
POST /rest/v1/customobjects/schema/enrollment/approve.json
```

```json
{
    "requestId": "9a76#16897b0e84b",
    "result": [],
    "success": true
}
```

Du kan fylla i anpassade objektposter programmatiskt med [Synkronisera anpassat objekt](#create_and_update) eller [Importera anpassat objekt gruppvis](https://experienceleague.adobe.com/docs/marketo-developer/marketo/rest/bulk-import/bulk-custom-object-import.html?lang=en). Du kan också använda Marketo-gränssnittsfunktionen [Importera anpassade objektdata](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-custom-objects/import-custom-object-data).

## Uppdatera fält

Med slutpunkten [Uppdatera fält för anpassad objekttyp](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/updateCustomObjectTypeFieldUsingPOST) kan du uppdatera ett fält i utkastet till anpassat objekt.  Den obligatoriska sökvägsparametern `apiName` är API-namnet för den anpassade objekttypen.  Den obligatoriska sökvägsparametern `fieldAPIName` är API-namnet för det anpassade objekttypsfältet.  Begärandetexten innehåller ett JSON-objekt som innehåller nyckel/värde-par som anger de fältattribut som ska uppdateras.

```
POST /rest/v1/customobjects/schema/{apiName}/{fieldApiName}/updateField.json
```

```json
{
  "displayName": "Very Long Title",
  "dataType": "text"
}
```

```json
{
    "requestId": "d523#1684f355db9",
    "result": [],
    "success": true
}
```

## Ta bort fält

Med slutpunkten [Ta bort anpassade objekttypfält](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/deleteCustomObjectTypeFieldsUsingPOST) kan du ta bort ett eller flera fält från det anpassade objektet.  Den obligatoriska sökvägsparametern `apiName` är API-namnet för den anpassade objekttypen.  Begärandetexten innehåller JSON-objekt med en `input`-matris med ett eller flera element.  Varje element är ett JSON-objekt med ett `name`-attribut som anger API-namnet för det fält som ska tas bort.

```
POST /rest/v1/customobjects/schema/{apiName}/deleteField.json
```

```json
{
    "input":
    [
        {
            "name": "title"
        },
        {
            "name": "author"
        }
    ]
}
```

```json
{
"requestId": "b359#19934f17996",
"result": [],
"success": true
}
```

## Datatyper för listfält

Slutpunkten [Hämta datatyper för anpassade objekttyper](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeFieldDataTypesUsingGET) returnerar listan över alla tillåtna fältdatatyper. Detta är användbart när du modellerar din anpassade objekttyp för att identifiera de anpassade fälttyper som stöds.

```
GET /rest/v1/customobjects/schema/fieldDataTypes.json
```

```json
{
    "requestId": "c405#167ed49e866",
    "result": [
        "string",
        "boolean",
        "integer",
        "float",
        "link",
        "email",
        "currency",
        "date",
        "datetime",
        "phone",
        "text"
    ],
    "success": true
}
```

## Lista länkade anpassade objekt

Slutpunkten [Hämta länkade objekt för anpassat objekt](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeLinkableObjectsUsingGET) returnerar en lista över alla tillåtna läntobjekt och deras länkfält.  Listan innehåller standardobjekt (Lead, Company) och alla anpassade objekt som har skapats i instansen.

```
GET /rest/v1/customobjects/schema/linkableObjects.json
```

```json
{
    "requestId": "11e62#167f1160e4e",
    "result": [
        {
            "name": "lead",
            "displayName": "Lead",
            "fields": [
                {
                    "name": "Account Balance",
                    "displayName": "Account Balance",
                    "dataType": "integer"
                },
                {
                    "name": "Email Address",
                    "displayName": "Email Address",
                    "dataType": "email"
                },
                {
                    "name": "Id",
                    "displayName": "Id",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Facebook Display Name",
                    "displayName": "Marketo Social Facebook Display Name",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Facebook Id",
                    "displayName": "Marketo Social Facebook Id",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Facebook Photo URL",
                    "displayName": "Marketo Social Facebook Photo URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Facebook Profile URL",
                    "displayName": "Marketo Social Facebook Profile URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Facebook Reach",
                    "displayName": "Marketo Social Facebook Reach",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Facebook Referred Enrollments",
                    "displayName": "Marketo Social Facebook Referred Enrollments",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Facebook Referred Visits",
                    "displayName": "Marketo Social Facebook Referred Visits",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Gender",
                    "displayName": "Marketo Social Gender",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Display Name",
                    "displayName": "Marketo Social LinkedIn Display Name",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Id",
                    "displayName": "Marketo Social LinkedIn Id",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Photo URL",
                    "displayName": "Marketo Social LinkedIn Photo URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Profile URL",
                    "displayName": "Marketo Social LinkedIn Profile URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social LinkedIn Reach",
                    "displayName": "Marketo Social LinkedIn Reach",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social LinkedIn Referred Enrollments",
                    "displayName": "Marketo Social LinkedIn Referred Enrollments",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social LinkedIn Referred Visits",
                    "displayName": "Marketo Social LinkedIn Referred Visits",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Syndication Id",
                    "displayName": "Marketo Social Syndication Id",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Total Referred Enrollments",
                    "displayName": "Marketo Social Total Referred Enrollments",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Total Referred Visits",
                    "displayName": "Marketo Social Total Referred Visits",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Twitter Display Name",
                    "displayName": "Marketo Social Twitter Display Name",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Twitter Id",
                    "displayName": "Marketo Social Twitter Id",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Twitter Photo URL",
                    "displayName": "Marketo Social Twitter Photo URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Twitter Profile URL",
                    "displayName": "Marketo Social Twitter Profile URL",
                    "dataType": "string"
                },
                {
                    "name": "Marketo Social Twitter Reach",
                    "displayName": "Marketo Social Twitter Reach",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Twitter Referred Enrollments",
                    "displayName": "Marketo Social Twitter Referred Enrollments",
                    "dataType": "integer"
                },
                {
                    "name": "Marketo Social Twitter Referred Visits",
                    "displayName": "Marketo Social Twitter Referred Visits",
                    "dataType": "integer"
                }
            ]
        },
        {
            "name": "company",
            "displayName": "Company",
            "fields": [
                {
                    "name": "Id",
                    "displayName": "Id",
                    "dataType": "integer"
                }
            ]
        },
        {
            "name": "car_c",
            "displayName": "Car",
            "fields": [
                {
                    "name": "marketoGUID",
                    "displayName": "Marketo GUID",
                    "dataType": "string"
                },
                {
                    "name": "vin",
                    "displayName": "VIN",
                    "dataType": "string"
                }
            ]
        }
    ],
    "success": true
}
```

## Hämta anpassat objektberoende Assets

Slutpunkten [Hämta anpassat objektberoende Assets](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectTypeDependentAssetsUsingGET) returnerar en lista med beroende resurser av en anpassad objekttyp, inklusive deras plats i instansen.  Detta är användbart när du tar bort en integrering och du måste identifiera var som helst att en anpassad objekttyp används.

```
GET /rest/v1/customobjects/schema/{apiName}/dependentAssets.json
```


```json
{
    "requestId": "71cf#16a21f30ed6",
    "result": [
        {
            "assetType": "Smart Campaign",
            "assetId": 3773,
            "assetName": "CarTest.HasCar (Smart List)"
        },
        {
            "assetType": "Smart Campaign",
            "assetId": 3773,
            "assetName": "CarTest.HasCar (Smart List)",
            "usedFields": [
                "leadID",
                "make",
                "model",
                "vin",
                "year"
            ]
        }
    ],
    "success": true
}
```

## Timeout

* Slutpunkter för anpassade objekt har en timeout på 30 sekunder om inget annat anges nedan
   * Synkronisera anpassade objekt: 120s 
   * Ta bort anpassade objekt: 60s
