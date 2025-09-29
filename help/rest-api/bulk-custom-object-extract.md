---
title: Extrahera anpassat objekt gruppvis
feature: REST API, Custom Objects
description: Guide till Marketo Bulk Custom Object Extract REST API:er för export av länkade anpassade objekt med updateAt- och listfilter, markerade fält och..
exl-id: 86cf02b0-90a3-4ec6-8abd-b4423cdd94eb
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '1315'
ht-degree: 0%

---

# Extrahera anpassat objekt gruppvis

[Slutpunktsreferens för extrahering av anpassat objekt gruppvis](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects)

Uppsättningen REST API:er för Bulk Custom Object Extract är ett programmatiskt gränssnitt för att hämta stora uppsättningar anpassade objektposter från Marketo. Det här är det rekommenderade gränssnittet för användningsfall som kräver kontinuerligt datautbyte mellan Marketo och ett eller flera externa system för ETL, datalagerhantering och arkivering.

Detta API stöder export av anpassade Marketo-objektposter på första nivån som är länkade direkt till ett lead. Ange namnet på det anpassade objektet och en lista med leads som objektet är länkat till. För varje lead i listan skrivs de länkade anpassade objektsposterna som matchar det angivna anpassade objektnamnet som rader till exportfilen. Anpassade objektdata kan visas på fliken [Eget objekt på leadets detaljsida i Marketo-gränssnittet](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/administration/marketo-custom-objects/understanding-marketo-custom-objects).

## Behörigheter

API:erna för extrahering av anpassat objekt i grupp kräver att API-användaren har en roll med antingen ett eller båda behörigheterna &quot;Skrivskyddat anpassat objekt&quot; eller &quot;Skrivskyddat anpassat objekt&quot;.

## Filter

Extrahering av anpassade objekt stöder flera filteralternativ som används för att ange en lista med leads som är länkade till det anpassade objektet. Om en lead i listan är länkad till anpassade objektposter som matchar ett angivet namn skrivs posterna i exportfilen. Endast en filtertyp kan anges per exportjobb.

| Filtertyp | Datatyp | Anteckningar |
|---|---|---|
| `updatedAt` | Datumintervall | Accepterar ett JSON-objekt med medlemmarna `startAt` och `endAt` &amp;nbsp.;`startAt` accepterar ett datetime-värde som representerar den låga vattenstämpeln, och `endAt` accepterar ett datetime-värde som representerar den övre vattenstämpeln. Intervallet måste vara högst 31 dagar. Jobb med den här filtertypen returnerar alla tillgängliga poster som uppdaterats inom datumintervallet. Datumtider ska vara i ISO-8601-format, utan millisekunder. |
| `staticListName` | Sträng | Accepterar namnet på en statisk lista. Jobb med den här filtertypen returnerar alla tillgängliga poster som är medlemmar i den statiska listan när jobbet börjar bearbetas. Hämta statiska listnamn med slutpunkten Hämta listor. |
| `staticListId` | Heltal | Accepterar ID:t för en statisk lista. Jobb med den här filtertypen returnerar alla tillgängliga poster som är medlemmar i den statiska listan när jobbet börjar bearbetas. Hämta statiska list-ID:n med slutpunkten Hämta listor. |
| `smartListName`* | Sträng | Accepterar namnet på en smart lista. Jobb med den här filtertypen returnerar alla tillgängliga poster som är medlemmar i de smarta listorna när jobbet börjar bearbetas. Hämta namn på smarta listor med slutpunkten Hämta smarta listor. |
| `smartListId`* | Heltal | Accepterar ID:t för en smart lista. Jobb med den här filtertypen returnerar alla tillgängliga poster som är medlemmar i de smarta listorna när jobbet börjar bearbetas. Hämta smarta list-ID:n med slutpunkten Hämta smarta listor. |

Filtertypen är inte tillgänglig för vissa prenumerationer. Om du inte är tillgänglig för din prenumeration får du ett felmeddelande när du anropar slutpunkten Skapa Lead-jobb (&quot;1035, filtertypen stöds inte för målprenumerationen&quot;). Kunder kan kontakta Marketo Support för att aktivera den här funktionen i prenumerationen.

## Alternativ

Slutpunkten [Skapa anpassat objektjobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects/operation/createExportCustomObjectsUsingPOST) innehåller flera formateringsalternativ. Dessa alternativ ger användaren möjlighet att:

- Ange vilka fält som ska inkluderas i den exporterade filen
- Byt namn på kolumnrubriker i dessa fält
- Ange format för den exporterade filen

| Parameter | Datatyp | Obligatoriskt | Anteckningar |
|---|---|---|---|
| `fields` | Array[String] | Ja | En matris med strängar som innehåller värdet för attributnamnet för det anpassade objektet som returneras av slutpunkten Beskriv anpassat objekt. De listade fälten inkluderas i den exporterade filen. |
| `columnHeaderNames` | Objekt | Nej | Ett JSON-objekt som innehåller nyckelvärdepar med fält- och kolumnrubriknamn. Nyckeln måste vara namnet på ett fält som ingår i exportjobbet. Värdet är namnet på den exporterade kolumnrubriken för det fältet. |
| `format` | Sträng | Nej | Accepterar något av följande: CSV, TSV, SSV. Den exporterade filen återges som en fil med kommaseparerade värden, tabbseparerade värden eller blankstegsavgränsade värden, om en sådan anges. Standardvärdet är CSV om den tas bort. |

## Skapa ett jobb

Parametrarna för jobbet definieras innan exporten avbryts med slutpunkten [Skapa anpassat objektjobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects/operation/createExportCustomObjectsUsingPOST).

Sökvägsparametern `apiName` som krävs är det anpassade objektnamnet som returneras av slutpunkten [Beskriv anpassat objekt](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/describeUsingGET_1). Detta anger vilket anpassat Marketo-objekt som ska exporteras. Egna CRM-objekt tillåts inte. Den obligatoriska parametern `filter` innehåller listan med leads som är länkade till det anpassade objektet. Detta kan referera till en statisk lista eller en smart lista. Den obligatoriska parametern `fields` innehåller API-namnen för de anpassade objektattribut som ska inkluderas i exportfilen. Vi kan också definiera `format` för filen och `columnHeaderNames`.

Låt oss anta att vi har skapat ett anpassat objekt med namnet &quot;Car&quot; med följande fält: Color, Make, Model, VIN. Länkfältet är lead-ID och dedupliceringsfältet är VIN.

Anpassad objektdefinition

![Eget objekt](assets/custom-object-car.png)

Anpassade objektfält

![Anpassade objektfält](assets/custom-object-car-fields.png)

Vi kan anropa [Beskriv anpassat objekt](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/describeUsingGET_1) för att programmässigt kontrollera de anpassade objektattribut som visas i attributet `fields` i svaret.

```
GET /rest/v1/customobjects/car_c/describe.json
```

```json
{
    "requestId": "148ef#1793e00f64f",
    "result": [
        {
            "name": "car_c",
            "displayName": "Car",
            "description": "It's a car.",
            "createdAt": "2021-05-05T16:14:41Z",
            "updatedAt": "2021-05-05T16:14:42Z",
            "idField": "marketoGUID",
            "dedupeFields": [
                "vIN"
            ],
            "searchableFields": [
                [
                    "vIN"
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
                        "field": "Id"
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
                    "name": "color",
                    "displayName": "Color",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
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
                    "name": "vIN",
                    "displayName": "VIN",
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

Skapa flera anpassade objektposter och länka vart och ett till ett annat lead med slutpunkten [Synkronisera anpassade objekt](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/syncCustomObjectsUsingPOST) . En lead kan länkas till många anpassade objektposter. Detta kallas en &quot;en till många&quot;-relation.

```
POST /rest/v1/customobjects/car_c.json
```

```json
{
   "action":"createOrUpdate",
   "input":[
       {
           "leadId": 11,
           "color": "Pearl White",
           "make": "Tesla",
           "model": "Model S",
           "vIN": "5YJSA1E41FF156789"
       },
       {
           "leadId": 12,
           "color": "Midnight Silver Metallic",
           "make": "Tesla",
           "model": "Model X",
           "vIN": "LRWXB2B41FF198765"
       },
       {
           "leadId": 13,
           "color": "Fusion Red",
           "make": "Tesla",
           "model": "Roadster",
           "vIN": "SFGRC3C41FF154321"
       }
    ]
}
```

```json
{
    "requestId": "50d9#1793e066088",
    "result": [
        {
            "seq": 0,
            "marketoGUID": "d911eaa1-fd0b-4a99-9b71-c6a7233c782c",
            "status": "created"
        },
        {
            "seq": 1,
            "marketoGUID": "20d04ffb-51f0-4336-924c-c783b9bb4215",
            "status": "created"
        },
        {
            "seq": 2,
            "marketoGUID": "e7da4331-8e7a-473b-85c8-047638eb6c7f",
            "status": "created"
        }
    ],
    "success": true
}
```

Var och en av de tre leads som det hänvisas till ovan tillhör en statisk lista med namnet&quot;Bil-köpare&quot; vars `id` är 1081, vilket visas nedan genom att anropa slutpunkten för [Hämta leads efter lista-ID](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/getLeadsByListIdUsingGET_1) .

```
GET /rest/v1/lists/1081/leads.json
```

```json
{
    "requestId": "d023#1793e1e982b",
    "result": [
        {
            "id": 11,
            "firstName": "Hanna",
            "lastName": "Crawford",
            "email": "208161Hanna.Crawford@pookmail.com",
            "updatedAt": "2020-01-16T02:38:22Z",
            "createdAt": "2017-07-27T01:38:42Z"
        },
        {
            "id": 12,
            "firstName": "Bertha",
            "lastName": "Fulton",
            "email": "208160Bertha.Fulton@trashymail.com",
            "updatedAt": "2020-01-16T02:38:22Z",
            "createdAt": "2017-07-27T01:38:42Z"
        },
        {
            "id": 13,
            "firstName": "Faith",
            "lastName": "England",
            "email": "208159Faith.England@dodgit.com",
            "updatedAt": "2020-01-16T02:38:22Z",
            "createdAt": "2017-07-27T01:38:42Z"
        }
    ],
    "success": true
}
```

Nu ska vi skapa ett exportjobb för att hämta posterna. Med slutpunkten [Skapa anpassat objektjobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects/operation/createExportCustomObjectsUsingPOST) anger vi anpassade objektattribut i parametern `fields` och ett statiskt list-ID i parametern `filter`.

```
POST /bulk/v1/customobjects/car_c/export/create.json
```

```json
{
    "fields": [
        "leadId",
        "color",
        "make",
        "model",
        "vIN"
    ],
    "filter": {
        "staticListId": 1081
    }
}
```

```json
{
    "requestId": "8d2f#1793e289e87",
    "result": [
        {
            "exportId": "f2c03f1d-226f-47c1-a557-357af8c2b32a",
            "format": "CSV",
            "status": "Created",
            "createdAt": "2021-05-05T20:12:01Z"
        }
    ],
    "success": true
}
```

Detta returnerar en status i svaret som anger att jobbet har skapats. Jobbet har definierats och skapats, men har ännu inte startats. Om du vill göra det måste slutpunkten [Återställ anpassat objektjobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects/operation/enqueueExportCustomObjectsUsingPOST) anropas med `apiName` och `exportId` från statussvaret för skapandet.

```
POST /bulk/v1/customobjects/car_c/export/f2c03f1d-226f-47c1-a557-357af8c2b32a/enqueue.json
```

```json
{
    "requestId": "cfaf#1793e2a0762",
    "result": [
        {
            "exportId": "f2c03f1d-226f-47c1-a557-357af8c2b32a",
            "format": "CSV",
            "status": "Queued",
            "createdAt": "2021-05-05T20:12:01Z",
            "queuedAt": "2021-05-05T20:13:32Z"
        }
    ],
    "success": true
}
```

Detta svarar med en inledande `status` av &quot;Köad&quot;, efter vilken inställningen &quot;Bearbetar&quot; anges när det finns en tillgänglig exportplats.

## Avsökningsjobbstatus

Status kan bara hämtas för jobb som har skapats av samma API-användare.

Eftersom detta är en asynkron slutpunkt måste vi, när vi har skapat jobbet, undersöka dess status för att avgöra dess förlopp. Avsök med slutpunkten [Hämta status för anpassat objektjobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects/operation/getExportCustomObjectsStatusUsingGET). Statusen uppdateras endast en gång var 60:e sekund, så en lägre avsökningsfrekvens rekommenderas inte och är i nästan alla fall fortfarande för hög. Statusfältet kan svara med något av följande: Skapat, Köat, Bearbetning, Avbrutet, Slutfört eller Misslyckat.

```
GET /bulk/v1/customobjects/{apiName}/export/{exportId}/status.json
```

```json
{
    "requestId": "14daa#1793e2cf9de",
    "result": [
        {
            "exportId": "f2c03f1d-226f-47c1-a557-357af8c2b32a",
            "format": "CSV",
            "status": "Processing",
            "createdAt": "2021-05-05T20:12:01Z",
            "queuedAt": "2021-05-05T20:13:32Z",
            "startedAt": "2021-05-05T20:14:15Z"
        }
    ],
    "success": true
}
```

Statusslutpunkten svarar som anger att jobbet fortfarande bearbetas, så filen är inte tillgänglig för hämtning än. När jobbet `status` ändras till Slutfört är det tillgängligt för hämtning.

```json
{
    "requestId": "14daa#1793e2cf9de",
    "result": [
        {
            "exportId": "f2c03f1d-226f-47c1-a557-357af8c2b32a",
            "format": "CSV",
            "status": "Completed",
            "createdAt": "2021-05-05T20:12:01Z",
            "queuedAt": "2021-05-05T20:13:32Z",
            "startedAt": "2021-05-05T20:14:15Z",
            "finishedAt": "2021-05-05T20:14:28Z",
            "numberOfRecords": 3,
            "fileSize": 182,
            "fileChecksum": "sha256:fac0cabc2352229c12e18b2fde03d1f24178bc71e9e926f520ae8d61bbe98c01"
        }
    ],
    "success": true
}
```

## Hämtar data

Om du vill hämta filen för en slutförd anpassad objektexport anropar du [Get Export Custom Object File](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects/operation/getExportCustomObjectsFileUsingGET) -slutpunkten med `apiName` och `exportId`.

Svaret innehåller en fil som är formaterad på det sätt som jobbet konfigurerades. Slutpunkten svarar med filens innehåll. Om ett begärt anpassat objektattribut är tomt (innehåller inga data) placeras `null` i motsvarande fält i exportfilen.

```
GET /bulk/v1/customobjects/car_c/export/f2c03f1d-226f-47c1-a557-357af8c2b32a/file.json
```

```csv
leadId,color,make,model,vIN
11,Pearl White,Tesla,Model S,5YJSA1E41FF156789
12,Midnight Silver Metallic,Tesla,Model X,LRWXB2B41FF198765
13,Fusion Red,Tesla,Roadster,SFGRC3C41FF154321
```

Om du vill ha stöd för delvis och återanvändningsvänlig hämtning av extraherade data, kan filslutpunkten (om det behövs) ha stöd för HTTP-huvudet `Range` av typen `bytes`. Om rubriken inte är inställd returneras hela innehållet. Du kan läsa mer om hur du använder intervallhuvudet i Marketo [Massextrahering](bulk-extract.md).

## Avbryta ett jobb

Om ett jobb konfigurerades felaktigt eller blir onödigt kan det enkelt avbrytas med slutpunkten [Avbryt export av anpassat objektjobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Custom-Objects/operation/getExportCustomObjectsFileUsingPOST) . Detta svarar med en `status` som anger att jobbet har avbrutits.

```
POST /bulk/v1/customobjects/car_c/export/f2c03f1d-226f-47c1-a557-357af8c2b32a/cancel.json
```

```json
{
    "requestId": "e5f9#179391286a7",
    "result": [
        {
            "exportId": "4a8cdd80-0d16-4dd6-9923-6ec97e30e91b",
            "format": "CSV",
            "status": "Cancelled",
            "createdAt": "2021-05-04T20:24:33Z"
        }
    ],
    "success": true
}
```
