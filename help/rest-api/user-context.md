---
title: Användarsammanhang
feature: REST API
description: Lär dig hur du aktiverar och använder Marketo RTP User Context API för att ställa in anpassade variabler, läsa användardata mellan besök samt spåra visade och klickade kampanjer.
exl-id: b8daace2-07a5-4621-aa3a-03fa9f66ea73
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '286'
ht-degree: 1%

---

# Användarsammanhang

JavaScript-API:t för användarkontext exponerar data på användar- och besökarnivå över flera sessioner för att möjliggöra avancerad personalisering med hjälp av historiska användarbeteenden och data. API:t är mer än bara läsning av data och visar anpassade variabler som gör att du kan skicka meningsfulla data och händelser till RTP-serverdelen för avancerad segmentering och personalisering. Ytterligare funktioner: [Utlösare](../javascript-api/triggers.md), [Mönstermatchning](../javascript-api/pattern-match.md).

- Du måste bli kund hos Web Personalization och ha [RTP-taggen &#x200B;](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) distribuerad på din webbplats innan du kan använda API:t för användarkontext.
- API:t för användarkontext är en funktion som måste aktiveras av Marketo Support på begäran. När API:t är aktiverat visas ett userContext-objekt under det globala RTP-objektet.

## Användarkontextattribut

| Namn | Typ | Beskrivning |
|------------------|-------------|------|
| customVar[1-5] | Sträng | Anpassade data har sparats i användarkontext. |
| displayedCampaigns | Kampanj-ID:n som kommaavgränsad sträng | Visade kampanjer i aktuella eller tidigare besök. |
| klickadeKampanjer | Kampanj-ID:n som kommaavgränsad sträng | Klickade via kampanjer i aktuella eller tidigare besök. |

## Ange anpassade variabler

Lägga till anpassade data i användarkontexten.

### Användning

`rtp('set', 'customVar'[1-5], my_custom_value);`

| Parameter | Valfritt/obligatoriskt | Typ | Beskrivning |
|-----------------|-------------------|--------|-----------------|
| set | Obligatoriskt | Sträng | Metodåtgärd. |
| customVar | Obligatoriskt | Sträng | Anpassat variabelnamn. |
| my_custom_value | Obligatoriskt | Sträng | Anpassat värde som ska sparas på en anpassad variabel i index 1-5. |

Obs! Anpassade variabler skickas endast till RTP i visningsanrop, så du bör ange anpassade variabler innan vyn anropas. Annars skickas den bara i nästa vyanrop.

Anpassade variabelbegränsningar

- Den anpassade variabellängden får inte vara längre än 100 tecken.
- Kampanjdata är begränsade till de senaste tio besöken med tio kampanjer per besök.

### Användning

`rtp('set', 'customVar', 'A');`

```javascript
// Set and get customVars
rtp('set', 'customVar1', 'foo');

// Read location
if (rtp.userContext.location.state == 'CA')  {
    // Do something
}

// Check if user viewed campaign id 45:
// The campaign id is exposed in the RTP UI when hovering over a campaign name.
if (rtp.userContext.viewedCampaign('45')) {
    // Do something
}
```
