---
title: Mönstermatchning
description: Använd verktyget RTP rtp.checkPattern för att testa strängmönster med jokertecken i procent, se synkroniseringsbegränsningar, exempel på användning och URL samt nödvändig konfiguration av RTP-taggar.
feature: Javascript
exl-id: 4ebd13e3-375b-449b-850f-3b18f570ca75
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '155'
ht-degree: 1%

---

# Mönstermatchning

RTP visar en verktygsfunktion som kontrollerar om mönstret matchar en viss sträng. Verktyget kan inte användas i asynkront eftersom det returnerar en indikation om det finns en matchning eller inte.

Du måste bli kund hos Web Personalization och ha [RTP-taggen &#x200B;](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) distribuerad till din webbplats innan du kan använda API:t för användarkontext.

## Användning

> rtp.checkPattern(check_to, pattern);

| Parameter | Valfritt/obligatoriskt | Typ | Beskrivning |
|---|---|---|---|
| check_anti | Obligatoriskt | Sträng | Sträng som matchar mönstret. Exempel: aktuell sidadress, produktnamn. |
| mönster | Obligatoriskt | Sträng | Lägg till % för jokertecken. Mönstret kan vara :start med behållarfull matchning |

## Exempel

Ange en anpassad variabel i index 1 om den aktuella sidans URL slutar med &quot;productA&quot;.

```javascript
if (rtp.checkPattern(window.location.href, '%productA')) {
    rtp('set', 'custom1', 'productA');
}
```

Den aktuella URL-sökvägen är /products/productB. Det här exemplet kontrollerar om sökvägen innehåller &quot;products&quot; och anger en anpassad variabel.

```javascript
var currentURLPath = '/products/productB';
if (rtp.checkPattern(currentURLPath, '%products%')) {
    rtp('set', 'custom1', 'products');
}
```
