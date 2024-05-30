---
title: "Hämta besöksdata"
description: "Hämta besöksdata"
feature: Javascript
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '184'
ht-degree: 0%

---


# Hämta besöksdata

Den här metoden används för att hämta besöksidentifieringsdata i realtid.

- Du måste bli webbkund och ha [RTP-taggen har distribuerats](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) på din webbplats innan du använder API:t för användarkontext.
- RTP stöder inte kontobaserade marknadslistor för namngivna konton. ABM-listor och kod gäller endast de överförda kontolistorna (CSV-filer) som hanteras i RTP.

Om ett fel inträffar visas ett felmeddelande som en del av JSON-svaret. Om en 500-kod returneras kontaktar du supporten med din förfrågan.

| Parameter | Valfritt/obligatoriskt | Typ | Beskrivning |
|---|---|---|---|
| `get` | Obligatoriskt | Sträng | Metodåtgärd. |
| `visitor` | Obligatoriskt | Sträng | Metodnamn. |
| `callback` | Obligatoriskt | Funktion | Återanropsfunktion som ska aktiveras för varje returnerad kampanj. |

## Exempel

Hämta besöksidentifieringsdata:

```javascript
function callbackFunction() {
    console.log('RTP is awesome!');
}
rtp('get', 'visitor', callbackFunction);
```

Svar med segmentmatchning:

Nedan visas ett exempelsvar som returneras om besökaren matchade realtidssegment före anropet till Get Visitor Data API.

```json
{
    "status": 200,
    "results": {
        "matchedSegments": [
            {
                "name": "first click",
                "id": 177
            }
        ],
        "abm": [
            {
                "code": 4,
                "name": "abm_saleforce_customers"
            },
            {
                "code": 5,
                "name": "abm_top_customers"
            }
        ],
        "org": "Marketo",
        "location": {
            "country": "United States",
            "city": "San Mateo",
            "state": "CA"
        },
        "industries": [
            "Software & Internet"
        ],
        "isp": false
    }
}
```

Svar utan segmentmatchning:

Nedan visas ett exempelsvar som returneras om besökaren inte matchade några realtidssegment före anropet till Get Visitor Data API.

```json
{
    "status": 200,
    "results": {
        "abm": [
            {
                "code": 4,
                "name": "abm_saleforce_customers"
            },
            {
                "code": 5,
                "name": "abm_top_customers"
            }
        ],
        "org": "Marketo",
        "location": {
            "country": "United States",
            "city": "San Mateo",
            "state": "CA"
        },
        "industries": [
            "Software & Internet"
        ],
        "isp": false
    }
}
```
