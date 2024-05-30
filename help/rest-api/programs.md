---
title: "Program"
feature: REST API, Programs
description: "Skapa och redigera programinformation."
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '843'
ht-degree: 0%

---


# Program

[Referens för programslutpunkt](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs)

Program är en viktig del i organisationen av Marketo marknadsföringsaktiviteter. De kan vara överordnade till de flesta typer av resurser och göra det möjligt att spåra medlemskap och framgångar för leads inom ramen för enskilda marknadsföringsinitiativ. Program kan vara överordnade alla typer av poster förutom LP, E-postmallar och Filer.

## Programtyper

Det finns fem huvudtyper av program inom Marketo:

- Standard
- Händelse
- Händelse med webbinarium
- Engagemang
- E-post

Åtagandeprogram kan vara direkt kopplade till varandra, medan Standard, Event och Event med webbinarium bara kan vara direkt kopplade till e-postprogram.

Programmen har alltid en kanal, de hämtar möjliga programmedlemskapsstatusar från den kanal de skapades med, som kan hämtas med API:t Get Channels. Ett program kan också ha en uppsättning associerade taggar. Taggar är anpassningsbara fält som kan konfigureras som valfria eller obligatoriska för en viss typ av program, där ett värde väljs från en lista som konfigurerats i Marketo Admin.

## Fråga

Programmen följer standardmönstret för resursfrågor med ytterligare ett alternativ för att fråga efter taggtyp och värden. Tillgängliga taggar och värden kan hämtas med [Hämta taggtyper](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tags/operation/getTagTypesUsingGET).

### Efter ID

The [Hämta program efter ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5) slutpunkten kräver `id` path-parameter.

Program-ID kan hämtas från URL:en för programmet i användargränssnittet, där URL:en liknar `https://app-\*\*\*.marketo.com/#PG1001A1`. I den här URL:en `id` är 1001. Den kommer alltid att vara mellan den första teckenuppsättningen i URL:en och den andra teckenuppsättningen.

```
GET /rest/asset/v1/program/{id}.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "948f#14db037ec71",
    "result": [
        {
            "id": 1107,
            "name": "AAA2QueryProgramName",
            "description": "AssetAPI: getProgram tests",
            "createdAt": "2015-05-21T22:45:13Z+0000",
            "updatedAt": "2015-05-21T22:45:13Z+0000",
            "url": "https://app-devlocal1.marketo.com/#PG1107A1",
            "type": "Default",
            "channel": "Online Advertising",
            "folder": {
                "type": "Folder",
                "value": 1910,
                "folderName": "ProgramQueryTestFolder"
            },
            "status": "",
            "workspace": "Default",
            "tags": [
                {
                    "tagType": "AAA1 Required Tag Type",
                    "tagValue": "AAA1 RT1"
                }
            ],
            "costs": null,
            "headStart": false
        }
    ]
}
```

### Efter namn

The [Hämta program efter namn](https://developer.adobe.com/marketo-apis/api/asset/) slutpunkten kräver `name` frågeparameter. Valfria booleska frågeparametrar `includeTags` och `includeCosts` som används för att returnera programtaggar och programkostnader.

```
GET /rest/asset/v1/program/byName.json?name=TestProgramName&includeTags=true
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "16026#14db03e070c",
    "result": [
        {
            "id": 1107,
            "name": "AAA2QueryProgramName",
            "description": "AssetAPI: getProgram tests",
            "createdAt": "2015-05-21T22:45:13Z+0000",
            "updatedAt": "2015-05-21T22:45:13Z+0000",
            "url": "https://app-devlocal1.marketo.com/#PG1107A1",
            "type": "Default",
            "channel": "Online Advertising",
            "folder": {
                "type": "Folder",
                "value": 1910,
                "folderName": "ProgramQueryTestFolder"
            },
            "status": "",
            "workspace": "Default",
            "tags": [
                {
                    "tagType": "AAA1 Required Tag Type",
                    "tagValue": "AAA1 RT1"
                }
            ],
            "costs": null,
            "headStart": false
        }
    ]
}
```

### Bläddra

The [Hämta program](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5) kan du söka efter program.

Valfritt `status` kan du filtrera efter programstatus. Den här parametern gäller endast för engagemangs- och e-postprogram. Möjliga värden är &quot;på&quot; och &quot;av&quot; för engagemangsprogram och &quot;olåst&quot; för e-postprogram.

Valfritt `maxReturn` -parametern styr antalet program som ska returneras (max 200, standard 20). Valfritt `offset` parameter som används för sidindelningsresultat (standard är 0).

Observera att taggar som är kopplade till ett program inte returneras av den här slutpunkten. Programtaggar kan hämtas med antingen [Hämta program efter ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByIdUsingGET) eller [Hämta program efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByNameUsingGET).

```
GET /rest/asset/v1/programs.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "7a39#1511bf8a41c",
    "result": [
        {
            "id": 1035,
            "name": "clone it",
            "description": "",
            "createdAt": "2015-11-18T15:25:35Z+0000",
            "updatedAt": "2015-11-18T15:25:46Z+0000",
            "url": "https://app-devlocal1.marketo.com/#NP1035A1",
            "type": "Engagement",
            "channel": "Nurture",
            "folder": {
                "type": "Folder",
                "value": 28,
                "folderName": "Nurturing"
            },
            "status": "on",
            "workspace": "Default",
            "headStart": false
        },
        {
            "id": 1032,
            "name": "email prog",
            "description": "",
            "createdAt": "2015-11-18T14:56:28Z+0000",
            "updatedAt": "2015-11-18T14:56:28Z+0000",
            "url": "https://app-devlocal1.marketo.com/#EBP1032A1",
            "type": "Email",
            "channel": "Email Send",
            "folder": {
                "type": "Folder",
                "value": 26,
                "folderName": "Data Management"
            },
            "status": "unlocked",
            "workspace": "Default",
            "headStart": false
        }
    ]
}
```

### Efter datumintervall

The `earliestUpdatedAt` och `latestUpdatedAt` parametrar till [Hämta program](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5) kan du ange vattenstämplar för låg och hög datetime för att returnera program som antingen har uppdaterats eller skapats i det angivna intervallet.

```
GET /rest/asset/v1/programs.json?earliestUpdatedAt=2017-01-01T00:00:00-05:00&latestUpdatedAt=2017-01-30T00:00:00-05:00
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "1225a#15f82a83875",
    "warnings": [],
    "result": [
        {
            "id": 1070,
            "name": "Bulk Import - Test",
            "description": "",
            "createdAt": "2017-01-13T19:34:17Z+0000",
            "updatedAt": "2017-01-13T19:34:18Z+0000",
            "url": "https://app-abm.marketo.com/#PG1070A1",
            "type": "Default",
            "channel": "Content",
            "folder": {
                "type": "Folder",
                "value": 637,
                "folderName": "Avention"
            },
            "status": "",
            "workspace": "Default",
            "headStart": false
        },
        {
            "id": 1069,
            "name": "Program With Email",
            "description": "",
            "createdAt": "2017-01-03T22:53:14Z+0000",
            "updatedAt": "2017-01-03T22:53:15Z+0000",
            "url": "https://app-abm.marketo.com/#EBP1069A1",
            "type": "Email",
            "channel": "Email Send",
            "folder": {
                "type": "Folder",
                "value": 621,
                "folderName": "Smartling"
            },
            "status": "unlocked",
            "workspace": "Default",
            "headStart": false
        },
        {
            "id": 1071,
            "name": "Program with Guided Landing Page Template",
            "description": "",
            "createdAt": "2017-01-24T22:59:21Z+0000",
            "updatedAt": "2017-01-24T22:59:22Z+0000",
            "url": "https://app-abm.marketo.com/#PG1071A1",
            "type": "Default",
            "channel": "Content",
            "folder": {
                "type": "Folder",
                "value": 621,
                "folderName": "Smartling"
            },
            "status": "",
            "workspace": "Default",
            "headStart": false
        },
        {
            "id": 1047,
            "name": "ReachForce List Update",
            "description": "",
            "createdAt": "2016-05-24T19:38:35Z+0000",
            "updatedAt": "2017-01-13T19:28:09Z+0000",
            "url": "https://app-abm.marketo.com/#PG1047A1",
            "type": "Default",
            "channel": "Content",
            "folder": {
                "type": "Folder",
                "value": 407,
                "folderName": "Everly Tests"
            },
            "status": "",
            "workspace": "Default",
            "headStart": false
        }
    ]
}
```

### Efter taggtyp

The [Hämta program efter tagg](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramListByTagUsingGET) slutpunkten hämtar en lista med program som matchar taggtypen och de taggvärden som anges.

Det finns två obligatoriska parametrar, `tagType` vilken typ av tagg som ska filtreras på, och `tagValue` vilket är taggvärdet som ska filtreras.  Ett valfritt heltal `maxReturn` parameter som styr antalet program som ska returneras (max 200, standard 20) och ett valfritt heltal `offset` parameter som används för sidindelningsresultat (standard är 0).  Resultaten returneras i slumpmässig ordning.

```
GET /rest/asset/v1/program/byTag.json?tagType=Presenter&tagValue=Dennis
```

```json
{
    "success" : true,
    "warnings" : [],
    "errors" : [],
    "requestId" : "13b6d#152b38d5be4",
    "result" : [{
            "id" : 1004,
            "name" : "It's a Program",
            "description" : "",
            "createdAt" : "2013-02-26T00:37:37Z+0000",
            "updatedAt" : "2013-03-11T15:32:02Z+0000",
            "url" : "https://app-sjst.marketo.com/#PG1004A1",
            "type" : "Default",
            "channel" : "Email Blast",
            "folder" : {
                "type" : "Folder",
                "value" : 38,
                "folderName" : "Test"
            },
            "status" : "",
            "workspace" : "Default",
            "tags" : [{
                    "tagType" : "Presenter",
                    "tagValue" : "Dennis"
                }
            ],
                        "headStart": false
    ]
}
```

## Skapa och uppdatera

[Skapar]https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/createProgramUsingPOST) och [uppdatera](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/updateProgramUsingPOST) program följer det vanliga resursmönstret och har `folder`, `name`, `type` och `channel` som obligatoriska parametrar, med `description`, `costs` och `tags` är valfritt. Kanal och typ kan bara anges när programmet skapas. Endast beskrivning, namn, `tags` och `costs` kan uppdateras efter skapandet, med ytterligare `costsDestructiveUpdate` -parameter tillåts. Lösning `costsDestructiveUpdate` som sant kommer alla befintliga kostnader att rensas och ersättas med alla kostnader som ingår i samtalet. Observera att taggar kan behövas för vissa programtyper i vissa prenumerationer, men detta är konfigurationsberoende och bör först kontrolleras med Hämta taggar för att se om det finns instansspecifika krav.

När du skapar eller uppdaterar ett e-postprogram `startDate` och `endDate` kan också passeras.

### Skapa

```
POST /rest/asset/v1/programs.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=API Test Program&folder={"id":1035,"type":"Folder"}&description=Sample API Program&type=Default&channel=Email Blast&costs=[{"startDate":"2015-01-01","cost":2000}]
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "d505#14d9bd96352",
    "result": [
        {
            "id": 1207,
            "name": "newProgram",
            "description": "This is a test",
            "createdAt": "2015-05-28T18:47:15Z+0000",
            "updatedAt": "2015-05-28T18:47:15Z+0000",
            "url": "https://app-devlocal1.marketo.com/#ME1207A1",
            "type": "Event",
            "channel": "channelOne",
            "folder": {
                "type": "Folder",
                "value": 59,
                "folderName": "blah blah"
            },
            "status": "",
            "workspace": "Default",
            "headStart": false
            "tags": null,
            "costs": [
                {
                    "startDate":"2015-01-01",
                    "cost":2000
                }
            ]
        }
    ]
}
```

### Uppdatera

När du uppdaterar programkostnaderna lägger du bara till dem i `costs` array. Om du vill utföra en förstörande uppdatering skickar du de nya kostnaderna tillsammans med parametern `costsDestructiveUpdate` ange till `true`. Om du vill rensa alla kostnader från ett program ska du inte skicka `costs` parameter, och skicka `costsDestructiveUpdate` ange till `true`.

```
POST /rest/asset/v1/program/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
description=This is an updated description&name=Updated Program Name&costs=[{"startDate":"2016-01-01","cost":200,"note":"Google Adwords"}]
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "5c37#14db05608aa",
    "result": [
        {
            "id": 1110,
            "name": "Updated Program Name",
            "description": "This is a updated description",
            "createdAt": "2015-05-21T22:45:14Z+0000",
            "updatedAt": "2015-06-01T18:13:58Z+0000",
            "url": "https://app-devlocal1.marketo.com/#NP1110A1",
            "type": "Engagement",
            "channel": "Nurture",
            "folder": {
                "type": "Folder",
                "value": 1910,
                "folderName": "ProgramQueryTestFolder"
            },
            "status": "on",
            "workspace": "Default",
            "headStart": false,
            "tags": [
                {
                    "tagType": "AAA1 Required Tag Type",
                    "tagValue": "AAA1 RT1"
                },
                {
                    "tagType": "tagTypeOne",
                    "tagValue": "tagTypeValue1"
                }
            ],
            "costs": [
                {
                    "startDate": "2016-01-01",
                    "cost": 200,
                    "note": "Google Adwords"
                }
            ]
        }
    ]
}
```

## Godkännande

E-postprogram kan fjärrgodkännas eller inte godkännas, vilket gör att programmet körs vid angivet startDate och avslutas vid angivet endDate. Båda dessa måste vara inställda för att godkänna programmet, samt ha en giltig och godkänd e-postadress och smart lista konfigurerad via användargränssnittet.

### Godkänn

```
POST /rest/asset/v1/program/{id}/approve.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "16026#150b5bf7692",
    "result": [
        {
            "id": 11062
        }
    ]
}
```

### Ogodkänd

```
POST /rest/asset/v1/program/{id}/unapprove.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "16026#150b5bf7692",
    "result": [
        {
            "id": 11062
        }
    ]
}
```

## Klona

[Kloningsprogram](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/cloneProgramUsingPOST) följer det vanliga objektmönstret det nya namnet och mappen som obligatoriska parametrar och en valfri beskrivning.  The `name` parametern måste vara globalt unik och får inte innehålla fler än 255 tecken.  The `folder` är den överordnade mappen.  The `folder` Parametertypsattributet måste anges till &quot;Mapp&quot; och målmappen måste finnas i samma arbetsyta som det program som ska klonas.

Program som innehåller vissa typer av resurser kan inte klonas via detta API, bland annat push-meddelanden, meddelanden i appen, rapporter och sociala resurser. Program i appen kan inte klonas via denna API.

```
POST /rest/asset/v1/program/{id}/clone.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Cloned Program - PHP&folder={"id":5562,"type":"Folder"}&description=Description
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "3a7f#14db06990cc",
    "result": [
        {
            "id": 1221,
            "name": "cloneProgram",
            "description": "This is a description for the cloned program",
            "createdAt": "2015-06-01T18:36:57Z+0000",
            "updatedAt": "2015-06-01T18:36:57Z+0000",
            "url": "https://app-devlocal1.marketo.com/#PG1221A1",
            "type": "Default",
            "channel": "Blog",
            "folder": {
                "type": "Folder",
                "value": 59,
                "folderName": "blah blah"
            },
            "status": "",
            "workspace": "Default",
            "headStart": false
            "tags": null,
            "costs": null
        }
    ]
}
```

## Ta bort program

När du tar bort program används standardmönstret för borttagning av resurser.

```
POST /rest/asset/v1/program/{id}/delete.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "16501#14db042c6b7",
    "result": [
        {
            "id": 1109
        }
    ]
}
```
