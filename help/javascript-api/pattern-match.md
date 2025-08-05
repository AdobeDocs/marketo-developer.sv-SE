---
title: Mönstermatchning
description: Mönstermatchning
feature: Javascript
exl-id: 4ebd13e3-375b-449b-850f-3b18f570ca75
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '133'
ht-degree: 2%

---

# Mönstermatchning

RTP visar en verktygsfunktion som kontrollerar om mönstret matchar en viss sträng. Verktyget kan inte användas i asynkront eftersom det returnerar en indikation om det finns en matchning eller inte.

Du måste bli kund hos Web Personalization och ha [RTP-taggen ](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) distribuerad till din webbplats innan du kan använda API:t för användarkontext.

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
