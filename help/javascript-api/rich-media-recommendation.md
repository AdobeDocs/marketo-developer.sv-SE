---
title: Rich Media Recommendation
description: Rich Media Recommendation
feature: Javascript
exl-id: ee92e46d-e529-40a2-a0d0-ee233916f004
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '812'
ht-degree: 1%

---

# Rich Media Recommendation

Följande taggar och API-anrop måste ställas in på sidan som du vill visa rekommendationsmallen för multimedia.

1. I sidhuvudet
   1. Har RTP-taggen installerad
   1. Lägg till GET-anropet på sidan för att fylla i rekommendationerna
   1. Lägg till SET-anropet för att konfigurera mallen
1. I sidans brödtext
   1. Placera malltaggen (klassen div) på den plats där du vill att mallen ska visas

Mer information finns [här](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/predictive-content/enabling-predictive-content/enable-predictive-content-for-web-rich-media).

## Malltagg

| Attribut | Valfritt/obligatoriskt | Beskrivning |
|---|---|---|
| class | Obligatoriskt | Ange att det här div-elementet för HTML är RTP-rekommendation-div. |
| data-rtp-template-id | Obligatoriskt | Mallens ID. Detta avgör justeringen av din rekommendation. Använd &quot;template1&quot; för vågrät justering, &quot;template2&quot; för lodrät justering eller &quot;template3&quot; för lodrät justering som endast innehåller rubrik och beskrivning. Skriptet infogar den matchande mallen i följande `div.Permissible`-värden: template1, template2, template3. |

### Exempel

Om du vill visa dina rekommendationer i vågrät justering använder du &quot;template1&quot;.

```html
<div class="RTP_RCMD2" data-rtp-template-id="template1"></div>
```

Om du vill visa dina rekommendationer i lodrät justering använder du &quot;template2&quot;.

```html
<div class="RTP_RCMD2" data-rtp-template-id="template2"></div>
```

Om du vill visa dina rekommendationer i lodrät justering med enbart rubrik och beskrivning använder du &quot;template3&quot;.

```html
<div class="RTP_RCMD2" data-rtp-template-id="template3"></div>
```

Se skärmbilder av malljusteringar [här](#example_of_rich_media_recommendation_template_1).

## Fyll i rekommendation

Den här metoden fyller i alla multimedia `<divs>` på sidan med rekommendationer.

### Användning

`rtp('get', 'rcmd', 'richmedia');`

| Parameter | Valfritt/obligatoriskt | Typ | Beskrivning |
|---|---|---|---|
| get | Obligatoriskt | Sträng | Metodåtgärd. |
| rcmd | Obligatoriskt | Sträng | Metodnamn. |
| &#39;richmedia&#39; | Obligatoriskt | Sträng | Undermetodnamn. |

## Ändra mallkonfiguration

Den här metoden ändrar standardkonfigurationen för mallen.

Obs! När du använder den här metoden måste den anropas innan du anropar rtp(&#39;get&#39;,&#39;rcmd&#39;, &#39;richmedia&#39;);

### Användning

`rtp('set', 'rcmd', 'richmedia', 'template_id', conf_obj);`

| Parameter | Valfritt/obligatoriskt | Typ | Beskrivning |
|---|---|---|---|
| set | Obligatoriskt | Sträng | Metodåtgärd. |
| rcmd | Obligatoriskt | Sträng | Metodnamn. |
| &#39;richmedia&#39; | Obligatoriskt | Sträng | Namn på undermetod. |
| template_id | Valfritt | Sträng | Mallens ID för konfigurationsändringar. Används för att ange inställningsändringar för endast en mall. |
| conf_obj | Obligatoriskt | Objekt | Den nya konfigurationen. Objektet innehåller alla konfigurationer som nyckel/värde-par. |

### Exempel

Det här kodfragmentet ändrar rubriktexten för en mall.

```javascript
rtp("set", "rcmd", "richmedia","template1",
    {
        "rcmd.title.text": "RECOMMENDED CONTENT"
    }
);
```

Det här kodfragmentet visar inställningar av kategorier med flera konfigurationer för en mall.

```javascript
rtp("set", "rcmd", "richmedia",
    {
        "template1":
        {
            "rcmd.title.text": "RECOMMENDED CONTENT",
            "rcmd.general.font.family": "arial",
            "category":
            [
                "webinar",
                "blog posts",
                "pricing_page_category",
                "product_a_category"
            ]
        }
    }
);
```

Obs! Använd&quot;category&quot; för att filtrera innehåll som visas i resultatet av rekommendationer för prediktivt innehåll. Om du vill tillämpa prediktivt innehåll på alla aktiverade innehållskomponenter lämnar du&quot;kategorin&quot; tom. Om du bara vill rekommendera specifikt innehåll för utdata i mallen Multimedia lägger du till en kategori för innehållet på sidan Ange innehåll och associerar den kategorin i rekommendationsmallkoden. Kategorisera relevant innehåll enligt avsnitt på webbplatsen (produkter eller lösningar).

I det här kodfragmentet visas hur du ställer in flera mallkonfigurationer för en mall.

```javascript
rtp("set", "rcmd", "richmedia",
    {
        "template1":
        {
            "rcmd.title.text": "RECOMMENDED CONTENT",
            "rcmd.general.font.family": "arial"
        }
    }
);
```

#### Konfigurationsegenskaper

| Konfiguration | Exempel | Beskrivning |
|---|---|---|
| rcmd.general.font.family | &quot;rcmd.general.font.family&quot; : &quot;arial&quot; | Ändrar teckensnittsfamiljen för all text i mallen. Den här egenskapen stöder alla CSS-värden efter webbläsartyp. Det går att använda en anpassad teckensnittsfamilj om den finns på sidan. |
| rcmd.content.background.color | &quot;rcmd.content.background.color&quot; : &quot;black&quot; | Ändrar bakgrundsfärgen för mallens innerrutor. Den här egenskapen stöder alla CSS-värden efter webbläsartyp. |
| rcmd.title.text | &quot;rcmd.title.text&quot; : &quot;REKOMMENDERAT INNEHÅLL&quot; | Ändrar mallens rubrik. |
| rcmd.title.background.color | &quot;rcmd.title.background.color&quot; : &quot;blue&quot; | Ändrar bakgrundsfärgen för titelrutan. Den här egenskapen stöder alla CSS-färgvärden (färgnamn, rgb, ...) |
| rcmd.title.font.size | &quot;rcmd.title.font.size&quot; : &quot;26px&quot; | Ändrar teckenstorleken för titeln. Egenskapen stöder alla möjliga CSS-värden för teckensnittsstorlekar (px, em, ...) |
| rcmd.title.font.color | &quot;rcmd.title.font.color&quot; : &quot;white&quot; | Ändrar teckenfärgen i titeln. Den här egenskapen stöder alla teckensnittsfärgvärden (rgb, hex, ...) |
| rcmd.description.font.color | &quot;rcmd.description.font.color&quot; : &quot;white&quot; | Ändrar teckenfärgen för beskrivningen. Den här egenskapen stöder alla teckensnittsfärgvärden (rgb, hex, ...) |
| rcmd.cta.background.color | &quot;rcmd.cta.background.color&quot; : &quot;green&quot; | Ändrar knappens bakgrundsfärg. Den här egenskapen stöder alla CSS-färgvärden (färgnamn, rgb, ...) |
| rcmd.cta.font.color | &quot;rcmd.cta.font.color&quot; : &quot;rgb(90, 84, 164)&quot; | Ändrar färg på knappteckensnitt. Den här egenskapen stöder alla teckensnittsfärgvärden (rgb, hex, ...) |
| rcmd.cta.text | &quot;rcmd.cta.text&quot; : &quot;Push&quot; | Ändrar knapptexten. Texten är densamma för alla knappar. |
| kategori | &quot;category&quot; : [&quot;one category&quot;] | Ändrar den rekommendationskategori som den här mallen stöder. I mallen visas endast rekommendationer med en av kategorierna som anges i den här konfigurationen. |

Obs! Konfigurationsstödet kan ändras per mall.

#### Grundläggande exempel

Det här exemplet har en mall med tre rekommendationer. Kopiera det här exemplet till en HTML-sida och ersätt sedan RTP-taggen med taggen .

```html
<!DOCTYPE>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>RTP recommendation</title>
<!-- RTP tag -->
<script type='text/javascript'>

// This tag needs to be replaced with your account tag
(function(c,h,a,f,i,e){c[a]=c[a]||function(){(c[a].q=c[a].q||[]).push(arguments)};
c[a].a=i;c[a].e=e;var g=h.createElement("script");g.async=true;g.type="text/javascript";
g.src=f+'?aid='+i;var b=h.getElementsByTagName("script")[0];b.parentNode.insertBefore(g,b);
})(window,document,"rtp","//example.rtp.com/rtp-api/v1/rtp.js","account_id");

// Send page view (required by  the recommendation)
rtp('send','view');
// Populate recommendation
rtp('get','rcmd', 'richmedia');
</script>
<!-- End of RTP tag -->
</head>
<body>
<div class="RTP_RCMD2" data-rtp-template-id="template1"></div>
</body>
</html>
```

#### Avancerat exempel

Det här exemplet har en mall med tre rekommendationer. Malltiteln är &quot;REKOMMENDERAT INNEHÅLL&quot; och knapptexten &quot;Läs mer&quot;. Kopiera det här exemplet till en HTML-sida och ersätt sedan RTP-taggen med taggen .

```html
<!DOCTYPE>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>RTP recommendation</title>
<!-- RTP tag -->
<script type='text/javascript'>

// This tag needs to be replaced with your account tag
(function(c,h,a,f,i,e){c[a]=c[a]||function(){(c[a].q=c[a].q||[]).push(arguments)};
c[a].a=i;c[a].e=e;var g=h.createElement("script");g.async=true;g.type="text/javascript";
g.src=f+'?aid='+i;var b=h.getElementsByTagName("script")[0];b.parentNode.insertBefore(g,b);
})(window,document,"rtp","//example.rtp.com/rtp-api/v1/rtp.js","account_id");

// Send page view (required by  the recommendation)
rtp('send','view');
// Populate the recommendation zone
rtp('get', 'campaign',true);
// Change template configuration
rtp('set', 'rcmd', 'richmedia',
    {
        template1 :
        {
            "rcmd.title.text" : "RECOMMENDED CONTENT",
            "rcmd.cta.text" : "Read More"
        }
    }
);
// Populate recommendation
rtp('get','rcmd', 'richmedia');
</script>
<!-- End of RTP tag -->
</head>
<body>
<div class="RTP_RCMD2" data-rtp-template-id="template1"></div>
</body>
</html>
```

#### Exempel på rekommendationsmall 1 för multimedia

**Namn**: mall1 **Beskrivning**: Vågrätt innehåll inklusive bild, titel, beskrivning och call to action-knapp.

![Multimediamall](assets/rich-media-template1.png)

#### Exempel på rekommendationsmall 2 för multimedia

**Namn**: mall2 **Beskrivning**: Lodrätt innehåll inklusive bild, titel, beskrivning och call to action-knapp.

![Multimediamall](assets/rich-media-template2.png)

#### Exempel på rekommendationsmall 3 för multimedia

**Namn**: mall3 **Beskrivning**: Lodrätt innehåll som endast innehåller rubrik och beskrivning. Vid hovring med musen ändrar sidhuvudet färg och är länkat till innehålls-URL. Beskrivningen länkar också till innehåll utan färgändring. ![Multimediamall](assets/rich-media-template3.png)
