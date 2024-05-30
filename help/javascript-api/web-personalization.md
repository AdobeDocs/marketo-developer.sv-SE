---
title: "Webbanpassning"
description: "Webbanpassning"
feature: Web Personalization, Javascript
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '401'
ht-degree: 0%

---


# Webbanpassning

JavaScript-API:t för webbpersonalisering utökar plattformens automatiserade personaliseringsfunktion. Det gör det möjligt att spåra händelser och anpassa en webbsida dynamiskt. Ytterligare funktioner: [Anpassade datahändelser](custom-data-events.md), [Dynamiskt innehåll](web-personalization.md), [Hämta besöksdata](get-visitor-data.md), [Exkludera tagg för specifika startpunkter](#exclude_tag_for_specific_bots).

- Du måste bli webbkund och ha [RTP-taggen har distribuerats](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) på webbplatsen innan du använder API:t för användarkontext.
- RTP stöder inte kontobaserade marknadslistor för namngivna konton. ABM-listor och kod gäller endast de överförda kontolistorna (CSV-filer) som hanteras i RTP.

## Tagginställningar

RTP-taggen ska infogas i sidhuvudet på den anpassade sidan.

```javascript
<!-- RTP tag --> 
<script type='text/javascript'>
(function(c,h,a,f,e,i){c[a]=c[a]||function(){(c[a].q=c[a].q||[]).push(arguments)};
c[a].p=e;c[a].a=i;var g=h.createElement("script");g.async=true;g.type="text/javascript";
g.src=f;var b=h.getElementsByTagName("script")[0];b.parentNode.insertBefore(g,b)})
(window,document,"rtp","[rtp-js-cdn-url]","[pod-url]","[accountId]");
</script>
<!-- End of RTP tag -->
```

## Kontoinställning

Den här metoden anropas automatiskt på taggnivå för att ange det relevanta konto-ID:t. Du kan ange konto-ID när du vill dela upp mellan olika domäner.

| Parameter | Valfritt/obligatoriskt | Typ | Beskrivning |
|--------------|-------------------|--------|--------------|
| &#39;setAccount&#39; | Obligatoriskt | Sträng | Metodnamn. |
| accountId | Obligatoriskt | Sträng | Konto-ID. |


```javascript
var accountId = '561-HYG-937';
rtp('setAccount', accountId);
```

## Funktioner för att skicka händelser

Den här metoden skickar en view-händelse, som används för sidspårning. I exemplet nedan spåras den aktuella sid-URL:en som en besökarsidvy.

Genom att skicka den valfria parametern &quot;page&quot; i den här metoden kan den aktuella sidan åsidosättas.

| Parameter | Valfritt/obligatoriskt | Typ | Beskrivning |
|-----------|-------------------|--------|---------------------------------|
| send | Obligatoriskt | Sträng | Metodåtgärd. |
| view&#39; | Obligatoriskt | Sträng | Metodnamn. |
| page | Valfritt | Sträng | Relativ sökväg eller URL för hel sida. |


```javascript
// Example for Default Page
rtp('send', 'view');

// Example for Overriding Default Page
var page = 'my-page?param=1';
rtp('send', 'view', page);
```

## Exkludera tagg för specifika startpunkter (användaragenter)

Om du vill utesluta vissa webbläsare från att skicka data till webbpersonaliseringsplattformen (när det gäller identifierade botar) lägger du till följande IF-programsats i taggskriptet.

I kodexemplet nedan används&quot;Googlebot|msnbot&quot; som båda exempel för att exkludera webbpersonaliseringsaktiviteter.

```javascript
<!-- RTP tag --> 
<script type='text/javascript'>
if(navigator.userAgent.match(/.(Googlebot|msnbot)./gi) == null){
    (function(c,h,a,f,i){c[a]=c[a]||function(){(c[a].q=c[a].q||[]).push(arguments)};
    c[a].a=i;var g=h.createElement("script");g.async=true;g.type="text/javascript";
    g.src=f+'?rh='+c.location.hostname+'&aid='+i;var b=h.getElementsByTagName("script")[0];b.parentNode.insertBefore(g,b);
})(window,document,"rtp","//[cdn-pod-X-url]/rtp-api/v1/rtp.js","[accountId]");

    rtp('send','view');
    rtp('get', 'campaign', true);
}
</script>
<!-- End of RTP tag -->
```

## JavaScript-anrop förklaras

Beskrivning av JavaScript som läggs till på en webbplats när du använder webbpersonalisering och prediktivt innehåll.

### Core/Dependent JavaScript

| Namn | Beskrivning | Kontroll |
|---------------------------|-------------|--------------------------------------------------------|
| rtp.js | - | Kontrolleras av Marketo |
| jquery.min.js | v1.8.3 | Kan inaktiveras genom att kontakta Marketo kundsupport |
| jquery-custom-ui-min.js | v1.9.2 | Kan inaktiveras genom att kontakta Marketo kundsupport |
| query-ui-1.8.17-dialog.js | v1.9.2* | Kan inaktiveras genom att kontakta Marketo kundsupport |


*Används endast om dialogrutan jQuery-gränssnitt saknas

### On Demand JavaScript

| Namn | Beskrivning | Kontroll |
|-------------------------|-----------------------------------------------------------------------|-----------------------|
| ga-integration-2.0.1.js | Används om integreringen mellan Google Analytics/Facebook/SiteCatalyst är aktiverad | Kontrolleras av Marketo |
| insightera-bar-2.1.js | Används om rekommendationsfältet för prediktivt innehåll är aktiverat | Kontrolleras av Marketo |
| froogaloop2.min.js | Används om innehållsspårning är aktiverat och Vimeo-spelaren finns på sidan | - |
| iframe-api-v1.js | Används om innehållsspårning är aktiverat och YouTube-spelaren finns på sidan | - |

