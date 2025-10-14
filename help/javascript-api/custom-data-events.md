---
title: Anpassade datahändelser
description: Skicka anpassade händelser med RTP JavaScript API för Web Personalization, med parametrar, sträng- eller matrisdata på upp till fyra objekt och klickbaserade utlösare.
feature: Javascript
exl-id: ef7cab9c-3bd0-450e-9247-9324b1e6f9ab
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '243'
ht-degree: 1%

---

# Anpassade datahändelser

Den här metoden skickar anpassade händelser för spårning och personalisering i realtid. Den kan användas för att skicka data från tredje part eller för att utlösa en egen anpassad händelse utifrån besökarens beteende. Anpassade datahändelser räknas en gång i en besökares session.

Du måste bli kund hos Web Personalization och ha [RTP-taggen &#x200B;](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) distribuerad på din webbplats innan du kan använda API:t för användarkontext.

| Parameter | Valfritt/obligatoriskt | Typ | Beskrivning |
|---|---|---|---|
| `send` | Obligatoriskt | Sträng | Metodåtgärd. |
| `event` | Obligatoriskt | Sträng | Metodnamn. |
| `customData` | Obligatoriskt | Sträng eller Array | Anpassade data. |

## Exempel

### Skicka händelse med String för anpassade data

```javascript
var customData = {value: 'MyEvent'};
rtp('send', 'event', customData);
```

### Skicka händelse med en array med strängar för anpassade data

Den anpassade datarrayen kan innehålla maximalt fyra element.  Om du måste skicka mer än fyra element anropar du Send Event API upprepade gånger (med högst fyra objekt) tills alla objekt har skickats.

```javascript
var customData = {value: ['MyEvent', 'download - example whitepaper']};
rtp('send', 'event', customData);
```

### Skicka händelse baserad på knappklickning

Marketo personaliserar innehållet på sin webbplats för webbbesökare som laddar ned en viss rapport. De gör detta genom att hämta besökarens klickning på knappen för hämtning av rapport, som skickar en anpassad datahändelse. RTP segmenterar i realtid alla besökare som klickade på knappen för att ladda ned rapport, och visar varje besökare en personlig kampanj med två klick senare. Detta uppnås genom att visa ett annat innehåll som hör till det inlästa vitt pappret.

```html
<button id="download-whitepaper" onclick="rtp('send', 'event', {value :'download - example whitepaper'})">Download</button>
```
