---
title: Listmedlemskap (statiska listor)
feature: REST API, Static Lists
description: Använd Marketo Lead Database REST API:er för att lägga till leads till statiska listor, ta bort leads, hämta listmedlemmar och medlemskap för checklistor.
exl-id: 2a91b0f3-5ba1-4b0c-b5e7-a19ab9a7fdc3
source-git-commit: 73fa4c85ecabd4cfd24bc6591aad11dc4e75010a
workflow-type: tm+mt
source-wordcount: '482'
ht-degree: 0%

---

# Listmedlemskap (statiska listor)

[Slutpunktsreferens för listmedlemskap](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)

API:erna för listmedlemskap innehåller Lead Database-slutpunkter för arbete med statiska listmedlemmar. Dessa slutpunkter kan användas för att lägga till leads till en lista, ta bort leads från en lista, hämta medlemmar i en lista och avgöra om en eller flera leads är medlemmar i en lista.

## Slutpunkter

| Slutpunkt | Metod | Bana |
| --- | --- | --- |
| Lägg till i listan | POST | `/rest/v1/lists/{listId}/leads.json` |
| Ta bort från lista | DELETE | `/rest/v1/lists/{listId}/leads.json` |
| Hämta leads efter list-ID | GET | `/rest/v1/lists/{listId}/leads.json` |
| List-medlem | GET | `/rest/v1/lists/{listId}/leads/ismember.json` |

## Lägg till i listan

Slutpunkten [Lägg till i listan](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/addLeadsToListUsingPOST) används för att lägga till en eller flera medlemmar i en lista. Slutpunkten har en obligatorisk `listId`-sökvägsparameter och en eller flera `id`-frågeparametrar som innehåller lead-ID (maximalt tillåtet är 300).

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

## Ta bort från lista

Slutpunkten [Ta bort från listan](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/removeLeadsFromListUsingDELETE) används för att ta bort en eller flera medlemmar från en lista. Slutpunkten har en obligatorisk `listId`-sökvägsparameter och en eller flera `id`-frågeparametrar som innehåller lead-ID (maximalt tillåtet är 300).

Svaret innehåller en `result`-matris som består av JSON-objekt med statusen för varje lead-ID som angavs i begäran.

```
DELETE /rest/v1/lists/{listId}/leads.json?id=318603&id=318595&id=999999
```

```json
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

## Hämta leads efter list-ID

Slutpunkten [Hämta leads efter list-ID](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists/operation/getLeadsByListIdUsingGET) används för att hämta medlemmar i en lista. Slutpunkten har en obligatorisk `listId`-sökvägsparameter och tillåter flera valfria frågeparametrar att ange filtervillkor.

Parametern `batchSize` används för att ange antalet lead-poster som ska returneras i ett enskilt anrop. Standardvärdet och maxvärdet är 300.

Parametern `nextPageToken` används för att paginera genom stora resultatuppsättningar. Den här parametern skickas inte i det första anropet, utan endast i efterföljande sidnumreringsanrop.

Parametern `fields` innehåller en kommaavgränsad lista med fältnamn som ska returneras i svaret. Om parametern `fields` inte ingår i denna begäran returneras följande standardfält: `email`, `updatedAt`, `createdAt`, `lastName`, `firstName` och `id`.

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

## List-medlem

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
