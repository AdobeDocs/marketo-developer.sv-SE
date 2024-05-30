---
title: "Utlösare"
description: "Utlösare"
feature: Javascript
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '80'
ht-degree: 0%

---


# Utlösare

Lägger till möjligheten att utlösa funktioner i vissa lägen för det globala rtp-objektet.

Du måste vara webbkund och ha [RTP-taggen har distribuerats](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) på webbplatsen innan du använder API:t för användarkontext.

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
