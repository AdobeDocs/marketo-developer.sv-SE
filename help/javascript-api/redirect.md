---
title: "Omdirigera"
description: "Omdirigera"
feature: Javascript
source-git-commit: 2185972a272b64908d6aac8818641af07c807ac2
workflow-type: tm+mt
source-wordcount: '468'
ht-degree: 0%

---


# Omdirigering

Med RTP:s omdirigerings-API kan du omdirigera segmenterade målgrupper till en mål-URL.

- Du måste bli webbkund och ha [RTP-taggen har distribuerats](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript) på din webbplats innan du använder API:t för användarkontext.
- RTP stöder inte kontobaserade marknadslistor för namngivna konton. ABM-listor och kod gäller endast de överförda kontolistorna (CSV-filer) som hanteras i RTP.

## Användning

`rtp('send' , 'redirect' , 'field_name' , [ 'values_array' , '...' , '...' ] , 'www.redirect_url.com' , true/false )`

| Parameter | Valfritt/obligatoriskt | Typ | Beskrivning |
|---------------------------|-------------------|---------|-----------------------------|
| send | Obligatoriskt | Sträng | Metodåtgärd. |
| &quot;redirect&quot; | Obligatoriskt | Sträng | Metodnamn. |
| field_name | Obligatoriskt | Sträng | Fältnamn att matcha mot. Exempel: &quot;abm.name&quot; (se nedan). |
| values_array | Obligatoriskt | Array | Lista med värden som matchar fältet mot (inte skiftlägeskänsliga). |
| redirect_url | Obligatoriskt | Sträng | Mål-URL för att omdirigera besökare som matchade villkoret. |
| redirect_matched_visits | Valfritt | Boolean | Om true omdirigeras villkorsmatchade besökare. Om värdet är false omdirigeras oöverträffade besökare. Standard: true. |

Organisation, bransch, ABM-listor, plats, ISP, matchade segment

| Villkor | Dataherarki | Exempel |
|-------------------------------------------------|----------------------|------------------------------------------------------------------------------------------------------------------|
| Matchade segment (fungerar endast efter första klickningen) | matchedSegments.name | rtp(&#39;send&#39;, &#39;redirect&#39; , &#39;matchedSegments.name&#39; , [Fortune 1.000, Enterprise] , &#39;http://www.marketo.com&#39;); |
| Matchade segment (fungerar endast efter första klickningen) | matchedSegments.id | rtp(&#39;send&#39;, &#39;redirect&#39; , &#39;matchedSegments.id&#39; , [106, 107, 190] , &#39;http://www.marketo.com&#39;); |
| ABM-listor | abm.name | rtp(&#39;send&#39;, &#39;redirect&#39; , &#39;abm.name&#39; , [top_key_accounts, active_customers] , &#39;http://www.marketo.com&#39;); |
| ABM-listor | abm.code | rtp(&#39;send&#39;, &#39;redirect&#39; , &#39;abm.code&#39; , [13, 15] , &#39;http://www.marketo.com&#39;); |
| Organisationer | org | rtp(&#39;send&#39;, &#39;redirect&#39;, &#39;org&#39;, [&#39;ebay&#39;], &#39;http://www.marketo.com&#39;); |
| Plats | location.country | rtp(&#39;send&#39;, &#39;redirect&#39; , &#39;location.country&#39; , [&#39;USA&#39;], &#39;http://www.marketo.com&#39;); |
| Plats | location.state | rtp(&#39;send&#39;, &#39;redirect&#39;, &#39;location.state&#39;, [ca], &#39;http://www.marketo.com&#39;); |
| Plats | location.city | rtp(&#39;send&#39;, &#39;redirect&#39;, &#39;location.city&#39;, [San Mateo], &#39;http://www.marketo.com&#39;); |
| Branscher | branscher | rtp(&#39;send&#39;, &#39;redirect&#39; , &#39;industry&#39; , [&#39;Utbildning&#39;], &#39;http://www.marketo.com&#39;); |
| ISP | isp | rtp(&#39;send&#39;, &#39;redirect&#39; , isp , [&#39;Falskt&#39;], &#39;http://www.marketo.com&#39;); |


## Anteckningar

- Om omdirigeringsregeln/omdirigeringsvillkoret baseras på Firmographics (företag, bransch, plats) kan du infoga omdirigeringskoden före rtp(&#39;send&#39;, &#39;view&#39;) och rtp(&#39;get&#39;,&#39;campaign&#39;) för att minska fördröjningen.
- Omdirigering via JavaScript är en omdirigering som görs via webbläsaren och som är beroende av webbplatsens inläsning och optimering för att uppnå maximal hastighet.
- Det bästa sättet är att ange omdirigeringskoden direkt efter rtp-taggen och placera den i sidhuvudet.
- Se till att du inte kör en självomdirigering (det finns ett säkerhetsnät i rtp för att blockera cykliska omdirigeringssamtal).

```html
<!DOCTYPE html>
<html lang="en-US">
<head>
<!-- RTP tag --> 
<script type='text/javascript'>

// This tag needs to be replaced with your account tag
(function(c,h,a,f,i){c[a]=c[a]||function(){(c[a].q=c[a].q||[]).push(arguments)};
c[a].a=i;var g=h.createElement("script");g.async=true;g.type="text/javascript";
g.src=f+'?rh='+c.location.hostname+'&aid='+i;var b=h.getElementsByTagName("script")[0];b.parentNode.insertBefore(g,b);
})(window,document,"rtp","//xyz.marketo.com/rtp-api/v1/rtp.js","xyz");
 
// START REDIRECT EXAMPLE 
//   - Using a helper redirect function
//   - Redirect based on named account
rtp('send','redirect','org', ['microsoft'],'http://www.marketo.com');
 
// Redirect based on named account list (ABM)
rtp('send','redirect','abm.name', {
    // Redirect visitors that match 'first_abm' list to www.marketo.com
    'http://www.marketo.com' : ['first_abm'],
    // Redirect visitors that match 'second_abm' list to blog.marketo.com
    'http://blog.marketo.com' : ['second_abm'] 
});
// END REDIRECT EXAMPLE
rtp('send','view');
rtp('get','campaign');
</script>
<!-- End of RTP tag -->
```

## Omdirigera spårade besökare

1. Lägg till en parameter i slutet av mål-URL:en: t.ex. www.marketo.com?rtp=redirect
1. Skapa ett segment med namnet &quot;Omdirigerad av RTP&quot;
1. Använd parametern&quot;Specifika sidor&quot; för att rikta in besökare som visar en sida med parametern som visas nedan.

![spåra omdirigerade-besök](assets/tracking-redirected-vistors.png)

## Definiera mer än ett villkor med olika mål-URL:er

Omdirigeringsanropet har stöd för flera samtal. Detta gör det möjligt att omdirigera med flera fält och skapa komplexa villkor med olika URL-adresser och värden.

### Användning

`rtp('send', 'redirect', field_name, url_values_map);`

| Parameter | Valfritt/obligatoriskt | Typ | Beskrivning |
|---|---|---|---|
| send | Obligatoriskt | Sträng | Metodåtgärd. |
| &quot;redirect&quot; | Obligatoriskt | Sträng | Metodnamn. |
| field_name | Obligatoriskt | Sträng | Fältnamn att matcha mot. Exempel: &quot;abm.name&quot; (se ovan). |
| url_values_map | Obligatoriskt | Objekt | Mappa mellan omdirigerings-URL och värdelista. Exempel:{&#39;http://marketo.com&#39;: [first_abm, second_abm]} |


#### Exempel

```javascript
rtp('send','redirect','abm.name', {
    // Redirect visitors that match 'first_abm' list to www.marketo.com
    'http://www.marketo.com' : ['first_abm'],
    // Redirect visitors that match 'second_abm' list to blog.marketo.com
    'http://blog.marketo.com' : ['second_abm']
});
rtp('send','redirect','org', {
    // Redirect visitors from 'Microsoft' to www.marketo.com/enterprise
    'http://www.marketo.com/enterprise' : ['microsoft']
});
```
