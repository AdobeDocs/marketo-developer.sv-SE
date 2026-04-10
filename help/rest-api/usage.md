---
title: Användning
feature: REST API
description: Övervaka Marketo REST API-användning och fel med statusslutpunkter per dag och senaste veckan, inklusive antal per användare och felkodssummor.
exl-id: 935a00a4-1e1e-4b48-ae9c-72c5e578312a
source-git-commit: e2606d6cb12c572603ff069617de58417e43ca63
workflow-type: tm+mt
source-wordcount: '393'
ht-degree: 5%

---

# Användning

[Referens för användningsslutpunkt](https://developer.adobe.com/marketo-apis/api/mapi#tag/Usage)

API:erna för användning ger en sammanfattning av användningen av REST API och felaktiviteten för din prenumeration. Dessa slutpunkter är användbara för att övervaka integreringar, spåra dagliga samtalsvolymer och identifiera feltrender över tid.

Användningsdata omfattar totalt antal API-anrop och per användare. Feldata innehåller ett totalt antal fel och en uppdelning per felkod.

Användnings-API:erna använder samma autentiseringsmetod som andra Marketo REST API:er. Skicka åtkomsttoken i huvudet `Authorization: Bearer {accessToken}`.

## Slutpunkter

| Metod | Bana | Beskrivning |
| --- | --- | --- |
| GET | `/rest/v1/stats/usage.json` | Hämtar API-användning för den aktuella dagen. |
| GET | `/rest/v1/stats/usage/last7days.json` | Hämtar API-användning de senaste 7 dagarna. |
| GET | `/rest/v1/stats/errors.json` | Hämtar API-fel för den aktuella dagen. |
| GET | `/rest/v1/stats/errors/last7days.json` | Hämtar API-fel de senaste 7 dagarna. |

## Daglig användning

Hämtar API-användning för den aktuella dagen.

```http
GET /rest/v1/stats/usage.json
```

```json
{
   "requestId": "5f7f#17d7d8f2b6f",
   "success": true,
   "result": [
      {
         "date": "2015-10-17",
         "total": 1120,
         "users": [
            {
               "userId": "some.body@yahoo.com",
               "count": 200
            },
            {
               "userId": "some.body@marketo.com",
               "count": 200
            },
            {
               "userId": "some.body@gmail.com",
               "count": 720
            }
         ]
      }
   ]
}
```

Varje objekt i `result`-arrayen innehåller en dags summor för användning och en beskrivning per användare.

## Användning de senaste 7 dagarna

Hämtar API-användning de senaste 7 dagarna. Varje element i arrayen `result` representerar en dag.

```http
GET /rest/v1/stats/usage/last7days.json
```

## Dagliga fel

Hämtar API-fel för den aktuella dagen.

```http
GET /rest/v1/stats/errors.json
```

```json
{
   "requestId": "5f7f#17d7d8f2b6f",
   "success": true,
   "result": [
      {
         "date": "2015-10-17",
         "total": 73,
         "errors": [
            {
               "errorCode": "604",
               "count": 1
            },
            {
               "errorCode": "609",
               "count": 56
            },
            {
               "errorCode": "610",
               "count": 16
            }
         ]
      }
   ]
}
```

Varje objekt i `result`-arrayen innehåller en dag med felsummor och en uppdelning efter felkod.

## Fel under de senaste 7 dagarna

Hämtar API-fel de senaste 7 dagarna. Varje element i arrayen `result` representerar en dag.

```http
GET /rest/v1/stats/errors/last7days.json
```

## Svarsmedlemmar

### Användningsresultatobjekt

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| `date` | Sträng | Datumet för användningssammanfattningen i formatet `YYYY-MM-DD`. |
| `total` | Heltal | Totalt antal API-anrop för den dagen. |
| `users` | Array | Lista över användningsantal per användare för den dagen. |

### Användarobjekt

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| `userId` | Sträng | API-användaridentifierare. |
| `count` | Heltal | Antal API-anrop som gjorts av den användaren för dagen. |

### Felresultatsobjekt

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| `date` | Sträng | Datumet för felsammanfattningen i formatet `YYYY-MM-DD`. |
| `total` | Heltal | Totalt antal API-fel för den dagen. |
| `errors` | Array | Lista över antal felkoder per dag. |

### Felobjekt

| Namn | Datatyp | Beskrivning |
| --- | --- | --- |
| `errorCode` | Sträng | Marketo felkod. |
| `count` | Heltal | Antal gånger som felet uppstod för dagen. |

## Anteckningar

Var och en av dina API-användare rapporteras individuellt i användningssvaret. Genom att dela upp integreringar mellan olika API-användare blir det enklare att identifiera vilken tjänst som förbrukar kvoter och skapar fel.
