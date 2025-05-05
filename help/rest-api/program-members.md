---
title: Programmedlemmar
feature: REST API
description: Skapa och hantera programmedlemmar.
exl-id: 22f29a42-2a30-4dce-a571-d7776374cf43
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '1712'
ht-degree: 0%

---

# Programmedlemmar

[Slutpunktsreferens för programmedlemmar](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members)

Marketo visar API:er för att läsa, skapa, uppdatera och ta bort programmedlemsposter. Poster för programmedlemmar är relaterade till lead-poster via fältet lead-id. Posterna består av en uppsättning standardfält och eventuellt upp till 20 ytterligare anpassade fält. Fälten innehåller programspecifika data för varje medlem och kan användas i formulär, filter, utlösare och flödesåtgärder. Dessa data kan visas på fliken [Medlemmar](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/core-marketo-concepts/programs/working-with-programs/manage-and-view-members) i användargränssnittet för Marketo Engage.

## Beskriv

Slutpunkten [Beskriv programmedlem](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2) följer standardmönstret för lead-databasobjekt. Arrayen `searchableFields` ger dig en uppsättning fält som är giltiga för frågor. Arrayen `fields` innehåller fältmetadata, inklusive REST API-namn, visningsnamn och möjlighet att uppdatera fält.

```
GET /rest/v1/programs/members/describe.json
```

```json
{
    "requestId": "f813#1791563c7cc",
    "result": [
        {
            "name": "API Program Membership",
            "description": "Map for API program membership fields",
            "createdAt": "2021-03-20T01:30:05Z",
            "updatedAt": "2021-03-20T01:30:05Z",
            "dedupeFields": [
                "leadId",
                "programId"
            ],
            "searchableFields": [
                [
                    "leadId"
                ],
                [
                    "myCustomField"
                ],
                [
                    "reachedSuccess"
                ],
                [
                    "statusName"
                ]
            ],
            "fields": [
                {
                    "name": "acquiredBy",
                    "displayName": "acquiredBy",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "attendanceLikelihood",
                    "displayName": "attendanceLikelihood",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "createdAt",
                    "displayName": "createdAt",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "isExhausted",
                    "displayName": "isExhausted",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "leadId",
                    "displayName": "leadId",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "membershipDate",
                    "displayName": "membershipDate",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "nurtureCadence",
                    "displayName": "nurtureCadence",
                    "dataType": "string",
                    "length": 4,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "program",
                    "displayName": "program",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "programId",
                    "displayName": "programId",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "reachedSuccess",
                    "displayName": "reachedSuccess",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "reachedSuccessDate",
                    "displayName": "reachedSuccessDate",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "registrationLikelihood",
                    "displayName": "registrationLikelihood",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "statusName",
                    "displayName": "statusName",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "statusReason",
                    "displayName": "statusReason",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "trackName",
                    "displayName": "trackName",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "updatedAt",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "waitlistPriority",
                    "displayName": "waitlistPriority",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "myCustomField",
                    "displayName": "myCustomField",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "registrationCode",
                    "displayName": "registrationCode",
                    "dataType": "string",
                    "length": 100,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "webinarUrl",
                    "displayName": "webinarUrl",
                    "dataType": "string",
                    "length": 2000,
                    "updateable": true,
                    "crmManaged": false
                }
            ]
        }
    ],
    "success": true
}
```

## Fråga

Med slutpunkten [Hämta programmedlemmar](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/getProgramMembersUsingGET) kan du hämta medlemmar i ett program. Det kräver en `programId`-sökvägsparameter samt `filterType`- och `filterValues`-frågeparametrar.

`programId` används för att ange vilket program som ska sökas igenom.

`filterType` används för att ange vilket fält som ska användas som sökfilter. Det accepterar alla fält i listan &quot;searchableFields&quot; som returneras av slutpunkten [Beskriv programmedlem](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2). Om du anger en filterType som är ett anpassat fält måste det anpassade fältets dataType vara antingen &quot;string&quot; eller &quot;integer&quot;. Om du anger en annan filterType än leadId kan högst 100 000 programmedlemsposter bearbetas av begäran. Beroende på hur din Marketo-instans är konfigurerad får du ett av följande fel:

- Om det totala antalet programmedlemmar överstiger 100 000 returneras ett fel: &quot;1 003, total medlemsstorlek: 100 001 överskrider gränsen som tillåts 100 000 för filtret&quot;.
- Om det totala antalet programmedlemmar _som matchar filtret_ överstiger 100 000 returneras ett fel: &quot;1 003, Matchande medlemsstorlek: 100 001 överskrider den tillåtna gränsen (100 000) för det här API:t&quot;.

Om du vill fråga ett program vars medlemsantal överskrider gränsen använder du [API:t för programmedlemsutdrag](bulk-program-member-extract.md) i stället.

`filterValues` används för att ange vilka värden som ska sökas efter och godkänner upp till 300 värden i ett kommaavgränsat format. Anropet söker efter poster där programmedlemmens fält matchar ett av de inkluderade filterValues.

Du kan också filtrera efter datumintervall genom att ange `updatedAt` som filterType med parametrarna `startAt` och `endAt` datetime. Intervallet måste vara minst sju dagar. Datumtider ska vara i ISO-8601-format, utan millisekunder.

Den valfria frågeparametern `fields` accepterar en kommaavgränsad lista med fält-API-namn som returneras av slutpunkten [Beskriv programmedlem](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2). När de inkluderas innehåller varje post i svaret de angivna fälten. När det utelämnas är standarduppsättningen med returnerade fält `acquiredBy`, `leadId`, `membershipDate`, `programId` och `reachedSuccess`.

Som standard returneras högst 300 poster. Du kan använda frågeparametern `batchSize` för att minska det här talet. Om attributet **moreResult** är true innebär det att fler resultat är tillgängliga. Fortsätt anropa den här slutpunkten tills attributet moreResult returnerar false, vilket betyder att det inte finns några tillgängliga resultat. `nextPageToken` som returneras från detta API ska alltid återanvändas för nästa iteration av det här anropet.

Om den totala längden på din GET-begäran överstiger 8 kB returneras ett HTTP-fel: &quot;414, URI för lång&quot; (per [RFC 7231](https://datatracker.ietf.org/doc/html/rfc72316.5.12)). Som en tillfällig lösning kan du ändra din GET till POST, lägga till parametern `_method=GET` och placera frågesträngen i begärandetexten.

```
GET /rest/v1/programs/{programId}/members.json?filterType=statusName&filterValues=Influenced
```

```json
{
    "requestId": "109da#17915eec072",
    "result": [
        {
            "seq": 0,
            "leadId": 1789,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 1,
            "leadId": 1790,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 2,
            "leadId": 1791,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 3,
            "leadId": 1792,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 4,
            "leadId": 1793,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 5,
            "leadId": 1794,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 6,
            "leadId": 1795,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 7,
            "leadId": 1796,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 8,
            "leadId": 1797,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 9,
            "leadId": 1798,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 10,
            "leadId": 1799,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        },
        {
            "seq": 11,
            "leadId": 1800,
            "reachedSuccess": true,
            "programId": 1044,
            "acquiredBy": true,
            "membershipDate": "2020-01-08T18:10:26Z"
        }
    ],
    "success": true,
    "moreResult": false
}
```

## Skapa och uppdatera

Det finns två slutpunkter som har stöd för att skapa/uppdatera för programmedlemmar. Det ena gör att du bara kan uppdatera programmedlemmens status. Den andra gör att du kan uppdatera en uppsättning programmedlemsfält som är markerade som&quot;uppdateringsbara&quot;. Med båda slutpunkterna kan du ändra upp till 300 programmedlemsposter per anrop.

### Status för programmedlem

Slutpunkten [Synkronisera programmedlemmens status](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/syncProgramMemberStatusUsingPOST) används för att skapa eller uppdatera programstatusen för en eller flera medlemmar.

Sökvägsparametern `programId` som krävs anger programmet som innehåller medlemmar som ska skapas eller uppdateras.

Den obligatoriska parametern `statusName` anger vilken programstatus som ska användas på en lista med leads. StatusName måste matcha en tillgänglig status för programmets kanal. Giltiga statusvärden kan hämtas med slutpunkten [Hämta kanaler](https://developer.adobe.com/marketo-apis/api/asset/#tag/Channels/operation/getAllChannelsUsingGET). Om en leads status har ett större stegvärde än den angivna statusName hoppas denna lead över.

Den obligatoriska parametern `input` är en matris av `leadId` som motsvarar programmedlemmar. Du kan skicka upp till 300 leadIds per samtal. En upsert-åtgärd utförs för varje post. Om leadId är associerat med en programmedlem uppdateras medlemskapsstatusen. Om inte skapas en ny programmedlemspost, posten kopplas till leadId och medlemskapsstatusen tilldelas.

Slutpunkten svarar med `status` av &quot;uppdaterad&quot;, &quot;skapad&quot; eller &quot;överhoppad&quot;. Om den hoppas över inkluderas även en `reasons`-matris. Slutpunkten kommer också att svara med ett `seq`-fält som är ett index som kan användas för att korrelera skickade poster till svarsordningen.

Om anropet lyckas skrivs aktiviteten Ändra programstatus till leadets aktivitetslogg.

```
POST /rest/v1/programs/{programId}/members/status.json
```

```
Content-Type: application/json
```

```json
{
    "statusName":"Influenced",
    "input":[
        {
            "leadId": 1800
        },
        {
            "leadId": 1801
        },
        {
            "leadId": 1235
        }
    ]
}
```

```json
{
    "requestId": "14b2d#17916378ec5",
    "result": [
        {
            "seq": 0,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1037",
                    "message": "Lead skipped because it is already in or past this status"
                }
            ]
        },
        {
            "seq": 1,
            "status": "updated",
            "leadId": 1801
        },
        {
            "seq": 2,
            "status": "created",
            "leadId": 1235
        }
    ],
    "success": true
}
```

### Programmedlemsdata

Slutpunkten [Synkronisera programmedlemsdata](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/syncProgramMemberDataUsingPOST) används för att uppdatera programmedlemsfältdata för en eller flera medlemmar. Du kan ändra anpassade fält eller standardfält som är&quot;uppdateringsbara&quot; (se [Beskriv programmedlemmens](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2) slutpunkt).

Sökvägsparametern `programId` som krävs anger programmet som innehåller medlemmar som ska uppdateras.

Den obligatoriska parametern `input` är en matris. Varje matriselement innehåller ett `leadId` och ett eller flera fält som ska uppdateras (med API-namn). En uppdateringsåtgärd utförs för varje post. LeadId måste associeras med en programmedlem. Fälten måste vara uppdateringsbara. Du kan skicka upp till 300 leadIds per samtal.

Slutpunkten svarar med `status` &quot;uppdaterad&quot; eller &quot;överhoppad&quot;. Om den hoppas över inkluderas även en `reasons`-matris. Slutpunkten kommer också att svara med ett `seq`-fält som är ett index som kan användas för att korrelera skickade poster till svarsordningen.

Om samtalet lyckas skrivs aktiviteten &quot;Change Program Member Data&quot; till leadets aktivitetslogg.

```
POST /rest/v1/programs/{programId}/members.json
```

```
Content-Type: application/json
```

```json
{
    "input":[
        {
            "leadId": 1789,
            "registrationCode": "dcff5f12-a7c7-11eb-bcbc-0242ac130002"
        },
        {
            "leadId": 1790,
            "registrationCode": "c0404b78-d3fd-47bf-82c4-d16f3852ab3a"
        },
        {
            "leadId": 1003,
            "registrationCode": "aa880c57-75b8-426b-a33a-fbf6302d7cb4"
        }
    ]
}
```

```json
{
    "requestId": "edc3#1791659b8d2",
    "result": [
        {
            "seq": 0,
            "status": "updated",
            "leadId": 1789
        },
        {
            "seq": 1,
            "status": "updated",
            "leadId": 1790
        },
        {
            "seq": 2,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1013",
                    "message": "Membership not found"
                }
            ]
        }
    ],
    "success": true
}
```

## Fält

Programmedlemsobjektet innehåller standardfält och valfria anpassade fält. Standardfält finns i alla Marketo Engage-prenumerationer medan anpassade fält skapas av användaren efter behov. Varje fältdefinition består av en uppsättning attribut som beskriver fältet. Exempel på attribut är visningsnamn, API-namn och dataType. Dessa attribut kallas tillsammans för metadata.

Med följande slutpunkter kan du fråga efter, skapa och uppdatera fält i programmedlemsobjektet. Dessa API:er kräver att den ägande API-användaren har en roll med ett eller båda av behörigheterna **Läs/skriv schema, standardfält** eller **Läs/skriv schema, anpassat fält**.

### Frågefält

Det är enkelt att fråga programmedlemsfält. Du kan fråga ett enskilt programmedlemsfält efter API-namn eller fråga uppsättningen med alla programmedlemsfält. Både standardfält och anpassade fält kan hämtas, beroende på vilka rollbehörigheter som används. Dolda fält hämtas också.

#### Efter namn

Slutpunkten [Hämta programmedlemsfält efter namn](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/getProgramMemberFieldByNameUsingGET) hämtar metadata för ett enskilt fält i programmedlemsobjektet. Sökvägsparametern `fieldApiName` som krävs anger fältets API-namn. Svaret är som Beskriv programmedlemmens slutpunkt men innehåller ytterligare metadata som attributet `isCustom` som anger om fältet är ett anpassat fält.

```
GET /rest/v1/programs/members/schema/fields/{fieldApiName}.json
```

```json
{
    "requestId": "15416#17e955554de",
    "result": [
        {
            "displayName": "Status",
            "name": "statusName",
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

Slutpunkten [Hämta programmedlemsfält](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/getProgramMemberFieldsUsingGET) hämtar metadata för alla fält i programmedlemsobjektet. Som standard returneras högst 300 poster. Du kan använda frågeparametern `batchSize` för att minska det här talet. Om attributet `moreResult` är true innebär det att fler resultat är tillgängliga. Fortsätt anropa den här slutpunkten tills attributet moreResult returnerar false, vilket betyder att det inte finns några tillgängliga resultat. `nextPageToken` som returneras från detta API ska alltid återanvändas för nästa iteration av det här anropet.

```
GET /rest/v1/programs/members/schema/fields.json?batchSize=5
```

```json
{
    "requestId": "102f6#17e9557f123",
    "result": [
        {
            "displayName": "Acquired By",
            "name": "acquiredBy",
            "description": null,
            "dataType": "boolean",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Nurture Cadence",
            "name": "nurtureCadence",
            "description": null,
            "dataType": "string",
            "length": 4,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Nurture Exhausted",
            "name": "isExhausted",
            "description": null,
            "dataType": "boolean",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Member Date",
            "name": "membershipDate",
            "description": null,
            "dataType": "datetime",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": false,
            "isCustom": false,
            "isApiCreated": false
        },
        {
            "displayName": "Program",
            "name": "program",
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
    "nextPageToken": "BC7J6EPVLT6T4B5FKUU3APCYN4======",
    "moreResult": true
}
```

### Skapa fält

Slutpunkten [Skapa programmedlemsfält](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/createProgramMemberFieldUsingPOST) skapar ett eller flera anpassade fält i programmedlemsobjektet. Den här slutpunkten innehåller funktioner som är jämförbara med vad som är [tillgängligt i användargränssnittet i Marketo Engage ](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/core-marketo-concepts/programs/working-with-programs/program-member-custom-fields). Du kan skapa upp till 20 anpassade fält med den här slutpunkten.

Tänk noga igenom varje fält som du skapar i din produktionsinstans av Marketo Engage med API:t. När ett fält har skapats kan du inte ta bort det ([du kan bara dölja det](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/administration/field-management/delete-a-custom-field-in-marketo)). Oanvända fält sprids ofta på ett dåligt sätt, vilket gör att instansen blir rörig.

Den obligatoriska parametern `input` är en matris med programmedlemsfältobjekt. Varje objekt innehåller ett eller flera attribut. Attribut som krävs är `displayName`, `name` och `dataType` som motsvarar fältets gränssnittsvisningsnamn, fältets API-namn och fälttypen. Du kan också ange `description`, `isHidden`, `isHtmlEncodingInEmail` och `isSensitive`.

Det finns några regler som är associerade med namngivning av `name` och `displayName`. Attributet `name` måste vara unikt, börja med en bokstav och bara innehålla bokstäver, siffror eller understreck. *`isplayName` måste vara unik och får inte innehålla specialtecken. En vanlig namngivningsregel är att tillämpa [kamelskiftläge](https://en.wikipedia.org/wiki/Camel_case#) på `displayName` för att skapa `name`. Ett `displayName` av &quot;Mitt anpassade fält&quot; skulle till exempel generera `name` av &quot;myCustomField&quot;.

```
POST /rest/v1/programs/members/schema/fields.json
```

```json
{
  "input": [
    {
        "displayName": "PMCF Custom Field 03",
        "name": "pMCFCustomField03",
        "description": "My third custom field",
        "dataType": "string"
    }
  ]
}
```

```json
{
    "requestId": "13a7#17e955fcb44",
    "result": [
        {
            "name": "pMCFCustomField03",
            "status": "created"
        }
    ],
    "success": true
}
```

### Uppdatera fält

Slutpunkten [Uppdatera programmedlemsfält](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/updateProgramMemberFieldUsingPOST) uppdaterar ett anpassat fält i programmedlemsobjektet. I allmänhet går det att utföra fältuppdateringsåtgärder som utförs med användargränssnittet i Marketo Engage med API:t. I tabellen nedan sammanfattas några skillnader.

| Attribut | Kan uppdateras av API? | Kan uppdateras av användargränssnittet? | Kan uppdateras av API? | Kan uppdateras av användargränssnittet? |
|---|---|---|---|---|
| dataType | no | no | no | ja |
| description | ja | ja | ja | ja |
| displayName | no | no | ja | ja |
| isCustom | no | no | no | no |
| isHidden | no | ja | ja (om den har skapats av API) | ja |
| isHtmlEncodingInEmail | ja | ja | ja | ja |
| isSensitive | ja | ja | ja | ja |
| length | no | no | no | no |
| name | no | no | no | no |

Sökvägsparametern `fieldApiName` som krävs anger API-namnet för det fält som ska uppdateras. Den obligatoriska parametern `input` är en array som innehåller ett enskilt lead-fältobjekt. Fältobjektet innehåller ett eller flera attribut.

```
POST /rest/v1/programs/members/schema/fields/pMCFCustomField03.json
```

```json
{
  "input": [
      {
        "displayName": "Lunch Preference",
        "description": "Attendee food preference",
        "isHtmlEncodingInEmail": true
      }
  ]
}
```

```json
{
    "requestId": "215f#17e95663955",
    "result": [
        {
            "name": "pMCFCustomField03",
            "status": "updated"
        }
    ],
    "success": true
}
```

## Ta bort

Slutpunkten [Ta bort programmedlemmar](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/deleteProgramMemberUsingPOST) används för att ta bort programmedlemsposter. Sökvägsparametern `programId` som krävs anger programmet som innehåller medlemmar som ska tas bort. Begärandetexten innehåller en `input`-matris med lead-ID:n. Högst 300 blyid  per samtal tillåts.

Slutpunkten svarar med `status` av &quot;deleted&quot; eller &quot;Skipped&quot;. Om den hoppas över inkluderas även en `reasons`-matris. Slutpunkten kommer också att svara med ett `seq`-fält som är ett index som kan användas för att korrelera skickade poster till svarsordningen.

```
POST /rest/v1/programs/{programId}/members/delete.json
```

```
Content-Type: application/json
```

```json
{
    "input":[
        {
            "leadId": 1235
        },
        {
            "leadId": 77
        }
    ]
}
```

```json
{
    "requestId": "302a#17916619417",
    "result": [
        {
            "seq": 0,
            "status": "deleted",
            "leadId": 1235
        },
        {
            "seq": 1,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1037",
                    "message": "Lead not in program"
                }
            ]
        }
    ],
    "success": true
}
```
