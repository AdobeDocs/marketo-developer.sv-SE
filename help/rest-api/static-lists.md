---
title: Statiska listor
feature: REST API, Static Lists
description: Använd Marketo REST API:er för att fråga efter, skapa, uppdatera och ta bort statiska listor med slutpunkter för ID, namn och bläddring, mappomfattningar, sidindelning och datumfilter.
exl-id: 20679fd2-fae2-473e-84bc-cb4fdf2f5151
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '760'
ht-degree: 0%

---

# Statiska listor

[Slutpunktsreferens för statiska listor](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists)

[Lista slutpunktsreferens för medlemskap](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)

Marketo erbjuder en uppsättning REST API:er för att utföra CRUD-åtgärder på statiska listor. Dessa API:er följer standardgränssnittsmönstret för resurs-API:er med alternativen Fråga, Skapa, Uppdatera och Ta bort.

## Fråga

Om du frågar efter statiska listor följer du standardfrågetyperna för resurserna [via ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET), [efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET) och [bläddra](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListsUsingGET).

### Efter ID

[Fråga efter ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET) tar en enda statisk lista `id` som sökvägsparameter och returnerar en enda statisk listpost.

```
GET /rest/asset/v1/staticList/{id}.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "843c#1641f969e96",
    "result": [
        {
            "id": 1021,
            "name": "Foundation Seed List",
            "createdAt": "2017-07-27T01:38:33Z+0000",
            "updatedAt": "2017-07-27T01:39:26Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1021A1"
        }
    ]
}
```

#### Efter namn

[Fråga efter namn ](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Lists/operation/getSmartListByNameUsingGET) tar en statisk lista `name` som parameter och returnerar en enda statisk listpost. En exakt strängmatchning utförs mot alla statiska listnamn i instansen och returnerar ett resultat för den statiska listan som matchar det namnet.

```
GET /rest/asset/v1/staticList/byName.json?name=Foundation Seed List
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "28ab#1641fa246b9",
    "result": [
        {
            "id": 1021,
            "name": "Foundation Seed List",
            "createdAt": "2017-07-27T01:38:33Z+0000",
            "updatedAt": "2017-07-27T01:39:26Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1021A1"
        }
    ]
}
```

#### Bläddra

Statiska listor kan också [hämtas i grupper](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListsUsingGET). Parametern `folder` kan användas för att ange den överordnade mapp som frågan ska utföras under och formateras som ett JSON-objekt som innehåller id och typ. Precis som andra slutpunkter för hämtning av gruppresurser är `offset` och `maxReturn` valfria parametrar som kan användas för sidindelning. Med parametrarna `earliestUpdatedAt` och `latestUpdatedAt` kan du ange låg- och högtidsvattenstämplar för att returnera statiska listor som skapats eller uppdaterats inom det angivna intervallet. Datumtidsvärden måste vara giltiga ISO-8601-strängar och får inte innehålla millisekunder

```
GET /rest/asset/v1/staticLists.json?folder={"id":13,"type":"Folder"}
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "2dc0#1641f846633",
    "result": [
        {
            "id": 1021,
            "name": "Foundation Seed List",
            "createdAt": "2017-07-27T01:38:33Z+0000",
            "updatedAt": "2017-07-27T01:39:26Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1021A1"
        },
        {
            "id": 1022,
            "name": "Blacklist Seed List",
            "createdAt": "2017-07-27T23:19:33Z+0000",
            "updatedAt": "2017-07-27T23:21:29Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1022A1"
        },
        {
            "id": 1023,
            "name": "Possible Duplicates Seed List",
            "createdAt": "2017-07-28T00:10:02Z+0000",
            "updatedAt": "2017-07-28T00:11:22Z+0000",
            "folder": {
                "id": 13,
                "type": "Folder"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1023A1"
        }
    ]
}
```

## Skapa och uppdatera

[Skapandet av en statisk lista](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/createStaticListUsingPOST) körs med en POST med två obligatoriska parametrar för application/x-www-form-urlencoded. Parametern `folder` används för att ange den överordnade mappen som den statiska listan ska skapas i och formateras som ett JSON-objekt som innehåller id och typ. Parametern `name` används för att namnge den statiska listan och måste vara unik. Parametern `description` kan också användas för att beskriva den statiska listan.

```
POST /rest/asset/v1/staticLists.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
folder={"id":1034,"type":"Program"}&name=My Static List
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "1269d#164209d6e1e",
    "result": [
        {
            "id": 1027,
            "name": "My Static List",
            "createdAt": "2018-06-21T04:32:25Z+0000",
            "updatedAt": "2018-06-21T04:32:25Z+0000",
            "folder": {
                "id": 1034,
                "type": "Program"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1027A1"
        }
    ]
}
```

[Uppdateringar av en statisk lista](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/updateStaticListUsingPOST) görs via en separat slutpunkt med två valfria parametrar. Parametern `description` kan användas för att uppdatera den statiska listbeskrivningen. Parametern `name` kan användas för att uppdatera det statiska listnamnet och måste vara unik.

```
POST /rest/asset/v1/staticList/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
description=This is a static list used for testing
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "f84f#16420b4c746",
    "result": [
        {
            "id": 1027,
            "name": "My Static List",
            "description": "This is a static list used for testing",
            "createdAt": "2018-06-21T04:32:26Z+0000",
            "updatedAt": "2018-06-21T04:57:55Z+0000",
            "folder": {
                "id": 1034,
                "type": "Program"
            },
            "computedUrl": "https://app-sjqe.marketo.com/#ST1027A1"
        }
    ]
}
```

### Ta bort

[Om du tar bort en statisk lista ](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/deleteStaticListByIdUsingPOST) används en enda statisk lista `id` som sökvägsparameter. Det går inte att ta bort statiska listor som används av en import- eller exportåtgärd, eller som används av andra resurser.

```
POST /rest/asset/v1/staticList/{id}/delete.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "2c79#16420ded0e9",
    "result": [
        {
            "id": 1027
        }
    ]
}
```

## Listmedlemskap

Slutpunkterna för listmedlemskap ger möjlighet att lägga till, ta bort och fråga statiska listmedlemmar. Dessutom kan du fråga efter statiskt listmedlemskap.

### Lägg till i listan

Slutpunkten [Lägg till i listan](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/addLeadsToListUsingPOST) används för att lägga till en eller flera medlemmar i en lista. Slutpunkten har en obligatorisk `listId`-sökvägsparameter och en eller flera ID-frågeparametrar som innehåller lead-ID (maximalt tillåtet är 300).

Svaret innehåller en `result`-matris som består av JSON-objekt med statusen för varje lead-ID som angavs i begäran.

```
POST /rest/v1/lists/{listId}/leads.json?id=318594&id=318595
```

```json
{
    "requestId": "6860#1706170ba29",
    "result": [
        {
            "id": 318594,
            "status": "added"
        },
        {
            "id": 318595,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1004",
                    "message": "Lead not found"
                }
            ]
        }
    ],
    "success": true
}
```

### Ta bort från lista

Slutpunkten [Ta bort från listan](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/removeLeadsFromListUsingDELETE) används för att ta bort en eller flera medlemmar från en lista. Slutpunkten har en obligatorisk `listId`-sökvägsparameter och en eller flera `id`-frågeparametrar som innehåller lead-ID (maximalt tillåtet är 300).

Svaret innehåller en `result`-matris som består av JSON-objekt med statusen för varje lead-ID som angavs i begäran.

```
DELETE /rest/v1/lists/{listId}/leads.json?id=318603&id=318595&id=999999
```

```
{
    "requestId": "9e79#17061689ac3",
    "result": [
        {
            "id": 318603,
            "status": "removed"
        },
        {
            "id": 318595,
            "status": "removed"
        },
        {
            "id": 999999,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1004",
                    "message": "Lead not found"
                }
            ]
        }
    ],
    "success": true
}
```

### Frågelista

Slutpunkten [Hämta leads efter list-ID](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/getLeadsByListIdUsingGET) används för att hämta medlemmar i en lista. Slutpunkten har en obligatorisk `listId`-sökvägsparameter och tillåter flera valfria frågeparametrar att ange filtervillkor.

Parametern `batchSize` används för att ange antalet lead-poster som ska returneras i ett enskilt anrop (standard och max 300).

Parametern `nextPageToken` används för att paginera genom stora resultatuppsättningar. Den här parametern skickas inte i det första anropet, utan endast i efterföljande anrop för sidnumreringen.

Parametern `fields` innehåller en kommaavgränsad lista med fältnamn som ska returneras i svaret. Om parametern fields inte ingår i denna begäran returneras följande standardfält: email, updatedAt, createdAt, lastName, firstName och id.

Svaret innehåller en `result`-matris som består av JSON-objekt som innehåller de lead-fält som har angetts i begäran.

```
GET /rest/v1/lists/{listId}/leads.json?batchSize=3
```

```json
{
    "requestId": "ddae#170615ba0cc",
    "result": [
        {
            "id": 318594,
            "firstName": "Hanna",
            "lastName": "Crawford",
            "email": "208161Robert.L.Deacon@pookmail.com",
            "updatedAt": "2015-04-06T17:13:50Z",
            "createdAt": "2015-04-06T17:13:50Z"
        },
        {
            "id": 318595,
            "firstName": "Bertha",
            "lastName": "Fulton",
            "email": "208160Tyrone.V.Dyer@trashymail.com",
            "updatedAt": "2015-04-06T17:13:50Z",
            "createdAt": "2015-04-06T17:13:50Z"
        },
        {
            "id": 318596,
            "firstName": "Faith",
            "lastName": "England",
            "email": "208159Rex.M.Bailey@dodgit.com",
            "updatedAt": "2015-04-06T17:13:50Z",
            "createdAt": "2015-04-06T17:13:50Z"
        }
    ],
    "success": true,
    "nextPageToken": "PS5VL5WD4UOWGOUCJR6VY7JQO24LC2U5DRBU4WO4RQMPHDHTK2T3BEZOR75VLQXYB3245WW2GMDSK==="
}
```

#### Frågelistemedlemskap efter lead-ID

[Medlemmen i List](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/areLeadsMemberOfListUsingGET)-slutpunkten används för att se om en eller flera leads är medlemmar i en lista. Slutpunkten har en obligatorisk `listId`-sökvägsparameter och en eller flera `id`-frågeparametrar som innehåller lead-ID (maximalt tillåtet är 300).

Svaret innehåller en `result`-matris som består av JSON-objekt med statusen för varje lead-ID som angavs i begäran.

```
GET /rest/v1/lists/{listId}/leads/ismember.json?id=309901&id=318603&id=999999
```

```json
{
    "requestId": "693a#17061475cf9",
    "result": [
        {
            "id": 309901,
            "status": "memberof"
        },
        {
            "id": 318603,
            "status": "notmemberof"
        },
        {
            "id": 999999,
            "status": "skipped",
            "reasons": [
                {
                    "code": "1004",
                    "message": "Lead not found"
                }
            ]
        }
    ],
    "success": true
}
```
