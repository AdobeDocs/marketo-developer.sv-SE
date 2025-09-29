---
title: Utlösare
description: Använd RTP-utlösare i Web Personalization för att köra funktioner i rtp-läge, inklusive userContextReady, med syntax, parametrar och ett platsexempel.
feature: Javascript
exl-id: 588836fa-1e4d-41f3-aec5-5cd17eb16071
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '100'
ht-degree: 2%

---

# Utlösare

Lägger till möjligheten att utlösa funktioner i vissa lägen för det globala rtp-objektet.

Du måste vara webbkund och ha [RTP-taggen ](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) distribuerad till din webbplats innan du kan använda API:t för användarkontext.

## Användning

`rtp('triggerName', function_to_trigger);`

| Parameter | Valfritt/obligatoriskt | Typ | Beskrivning |
|---------------------|-------------------|----------|----------------------|
| triggerName | Obligatoriskt | Sträng | Metodnamn. |
| function_to_trigger | Obligatoriskt | Funktion | Funktion som ska utlösas. |

### Utlösare för användarkontext

Ställer in en anpassad variabel baserat på användarens plats. Den här funktionen anropas när det globala objektet &quot;rtpUserContext&quot; är klart.

```javascript
rtp('userContextReady', function() {
    if (rtpUserContext.location.state == 'CA') {
        rtp('set', 'custom1', 'productA');
    }
});
```
