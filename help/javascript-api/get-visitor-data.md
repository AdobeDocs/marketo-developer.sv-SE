---
title: Hämta besöksdata
description: Hämta besöksdata
feature: Javascript
exl-id: 39a2446d-8a31-461e-bbe6-a7edf24b4d52
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '184'
ht-degree: 0%

---

# Hämta besöksdata

Den här metoden används för att hämta besöksidentifieringsdata i realtid.

- Du måste bli kund hos Web Personalization och ha [RTP-taggen ](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) distribuerad till din webbplats innan du kan använda API:t för användarkontext.
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
