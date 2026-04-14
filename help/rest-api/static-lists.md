---
title: Statiska listor
feature: REST API, Static Lists
description: Använd Marketo REST API:er för att fråga efter, skapa, uppdatera och ta bort statiska listor med slutpunkter för ID, namn och bläddring, mappomfattningar, sidindelning och datumfilter.
exl-id: 20679fd2-fae2-473e-84bc-cb4fdf2f5151
source-git-commit: 59684e1c5a8082ad12f1e4bfc854c0d2dde35d2a
workflow-type: tm+mt
source-wordcount: '496'
ht-degree: 0%

---

# Statiska listor

[Slutpunktsreferens för statiska listor](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists)

Marketo erbjuder en uppsättning REST API:er för att utföra CRUD-åtgärder på statiska listor. Dessa API:er följer standardgränssnittsmönstret för resurs-API:er med alternativen Fråga, Skapa, Uppdatera och Ta bort.

Mer information om åtgärder för Lead-databaser för listmedlemmar finns i [Lista medlemskap](list-membership.md).

## Fråga

Om du frågar efter statiska listor följer du standardfrågetyperna för resurserna [via ID](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists/operation/getStaticListByIdUsingGET), [efter namn](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists/operation/getStaticListByNameUsingGET) och [bläddra](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists/operation/getStaticListsUsingGET).

### Efter ID

[Fråga efter ID](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists/operation/getStaticListByIdUsingGET) tar en enda statisk lista `id` som sökvägsparameter och returnerar en enda statisk listpost.

```http
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

[Fråga efter namn &#x200B;](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists/operation/getStaticListByNameUsingGET) tar en statisk lista `name` som parameter och returnerar en enda statisk listpost. En exakt strängmatchning utförs mot alla statiska listnamn i instansen och returnerar ett resultat för den statiska listan som matchar det namnet.

```http
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

Statiska listor kan också [hämtas i grupper](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists/operation/getStaticListsUsingGET). Parametern `folder` kan användas för att ange den överordnade mapp som frågan ska utföras under och formateras som ett JSON-objekt som innehåller `id` och `type`. Precis som andra slutpunkter för hämtning av gruppresurser är `offset` och `maxReturn` valfria parametrar som kan användas för sidindelning. Med parametrarna `earliestUpdatedAt` och `latestUpdatedAt` kan du ange låg- och högtidsvattenstämplar för att returnera statiska listor som skapats eller uppdaterats inom det angivna intervallet. Datumtidsvärden måste vara giltiga ISO-8601-strängar och får inte innehålla millisekunder.

```http
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

[Skapandet av en statisk lista &#x200B;](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists/operation/createStaticListUsingPOST) körs med en `application/x-www-form-urlencoded` POST med två obligatoriska parametrar. Parametern `folder` används för att ange den överordnade mappen som den statiska listan ska skapas i och formateras som ett JSON-objekt som innehåller `id` och `type`. Parametern `name` används för att namnge den statiska listan och måste vara unik. Parametern `description` kan också användas för att beskriva den statiska listan.

```http
POST /rest/asset/v1/staticLists.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
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

[Uppdateringen av en statisk lista](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists/operation/updateStaticListUsingPOST) görs via en separat slutpunkt med två valfria parametrar. Parametern `description` kan användas för att uppdatera den statiska listbeskrivningen. Parametern `name` kan användas för att uppdatera det statiska listnamnet och måste vara unik.

```http
POST /rest/asset/v1/staticList/{id}.json
```

```text
Content-Type: application/x-www-form-urlencoded
```

```text
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

## Ta bort

[Om du tar bort en statisk lista &#x200B;](https://developer.adobe.com/marketo-apis/api/asset#tag/Static-Lists/operation/deleteStaticListByIdUsingPOST) används en enda statisk lista `id` som sökvägsparameter. Det går inte att ta bort statiska listor som används av en import- eller exportåtgärd, eller som används av andra resurser.

```http
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
