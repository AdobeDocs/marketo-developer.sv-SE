---
title: Statiska listor
feature: REST API, Static Lists
description: "Utför CRUD-åtgärder på statiska listor."
source-git-commit: e8bb45a7b3bee71c3d0ab6117296a75c8959d72e
workflow-type: tm+mt
source-wordcount: '741'
ht-degree: 0%

---


# Statiska listor

[Slutpunktsreferens för statiska listor](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists)

[Slutpunktsreferens för listmedlemskap](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)

Marketo erbjuder en uppsättning REST API:er för att utföra CRUD-åtgärder på statiska listor. Dessa API:er följer standardgränssnittsmönstret för resurs-API:er med alternativen Fråga, Skapa, Uppdatera och Ta bort.

## Fråga

När du frågar statiska listor följer standardfrågetyperna för resurser i [efter ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET), [efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET)och [bläddra](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListsUsingGET).

### Efter ID

[Fråga efter ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET) tar en enda statisk lista `id` som en sökvägsparameter och returnerar en enda statisk listpost.

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

[Fråga efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Lists/operation/getSmartListByNameUsingGET) tar en statisk lista `name` som en parameter och returnerar en enda statisk listpost. En exakt strängmatchning utförs mot alla statiska listnamn i instansen och returnerar ett resultat för den statiska listan som matchar det namnet.

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

Statiska listor kan också [hämtat i batchar](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListsUsingGET). The `folder` -parametern kan användas för att ange den överordnade mapp som frågan ska utföras under och formateras som ett JSON-objekt som innehåller id och typ. Precis som andra slutpunkter för hämtning av bulkresurser, `offset` och `maxReturn` är valfria parametrar som kan användas för sidindelning. The `earliestUpdatedAt` och `latestUpdatedAt` Med -parametrar kan du ange låga och höga datetime-vattenstämplar för att returnera statiska listor som skapats eller uppdaterats inom det angivna intervallet. Datumtidsvärden måste vara giltiga ISO-8601-strängar och får inte innehålla millisekunder

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

[Skapa en statisk lista](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/createStaticListUsingPOST) körs med en application/x-www-form-urlencoded-POST med två obligatoriska parametrar. The `folder` -parametern används för att ange den överordnade mappen som den statiska listan ska skapas i och formateras som ett JSON-objekt som innehåller id och typ. The `name` parametern används för att namnge den statiska listan och måste vara unik. Alternativt `description` kan användas för att beskriva den statiska listan.

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

[Uppdateringar i en statisk lista](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/updateStaticListUsingPOST) görs via en separat slutpunkt med två valfria parametrar. The `description` kan användas för att uppdatera den statiska listbeskrivningen. The `name` kan användas för att uppdatera namnet på den statiska listan och måste vara unikt.

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

[Ta bort en statisk lista](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/deleteStaticListByIdUsingPOST) tar en enda statisk lista `id` som en sökvägsparameter. Det går inte att ta bort statiska listor som används av en import- eller exportåtgärd, eller som används av andra resurser.

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

The [Lägg till i listan](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/addLeadsToListUsingPOST) slutpunkten används för att lägga till en eller flera medlemmar i en lista. Slutpunkten har en obligatorisk `listId` path-parameter och en eller flera id-frågeparametrar som innehåller lead-ID (maximalt tillåtet är 300).

Svaret innehåller en `result` arrayen består av JSON-objekt med statusen för varje lead-ID som angavs i begäran.

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

The [Ta bort från lista](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/removeLeadsFromListUsingDELETE) slutpunkten används för att ta bort en eller flera medlemmar från en lista. Slutpunkten har en obligatorisk `listId` sökvägsparameter och en eller flera `id` frågeparametrar som innehåller lead-ID (högst 300).

Svaret innehåller en `result` arrayen består av JSON-objekt med statusen för varje lead-ID som angavs i begäran.

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

The [Hämta leads efter list-ID](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/getLeadsByListIdUsingGET) slutpunkten används för att hämta medlemmar i en lista. Slutpunkten har en obligatorisk `listId` path-parameter, och tillåter flera valfria frågeparametrar för att ange filtervillkor.

The `batchSize` används för att ange antalet lead-poster som ska returneras i ett enda anrop (standard och max 300).

The `nextPageToken` används för att paginera genom stora resultatuppsättningar. Den här parametern skickas inte i det första anropet, utan endast i efterföljande anrop för sidnumreringen.

The `fields` parametern innehåller en kommaavgränsad lista med fältnamn som ska returneras i svaret. Om parametern fields inte ingår i denna begäran returneras följande standardfält: email, updatedAt, createdAt, lastName, firstName och id.

Svaret innehåller en `result` en array som består av JSON-objekt som innehåller de lead-fält som har angetts i begäran.

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

The [List-medlem](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/areLeadsMemberOfListUsingGET) slutpunkten används för att se om en eller flera leads är medlemmar i en lista. Slutpunkten har en obligatorisk `listId` sökvägsparameter och en eller flera `id` frågeparametrar som innehåller lead-ID (högst 300).

Svaret innehåller en `result` arrayen består av JSON-objekt med statusen för varje lead-ID som angavs i begäran.

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
