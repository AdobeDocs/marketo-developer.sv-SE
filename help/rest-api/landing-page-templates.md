---
title: "Landing Page Templates"
feature: REST API, Landing Pages
description: "Bygg och redigera mallar för landningssidor."
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '533'
ht-degree: 0%

---


# Mallar för landningssidor

[Slutpunktsreferens för landningssidmall](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates)

Landningssidmallar är en överordnad resurs och beroende för enskilda Marketo landningssidor. Landningssidor härleder innehållets struktur från den överordnade mallen.

## Malltyper

Marketo har två typer av Landing Page Templates, kostnadsfria och guidade. Mallar för landningssidor i kostnadsfri form ger en löst strukturerad redigeringsupplevelse för sidor som härrör från dem. Med guidade mallar får du en mycket strukturerad upplevelse, där elementtyperna och platserna kan begränsas på mallnivå. Mer information om skillnaderna finns i [det här dokumentet](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/understanding-landing-pages/understanding-free-form-vs-guided-landing-pages).

## Fråga

Mallar för landningssidor har stöd för standardfrågetyper för resurser i [efter ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/getLandingPageTemplateByIdUsingGET), [efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/getLandingPageTemplateByNameUsingGET)och [surfning](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/getLandingPageTemplatesUsingGET). Dessa slutpunkter returnerar metadata för mallarna. Hämtning av mallarnas HTML-innehåll måste göras per mall via dess ID.

## Skapa och uppdatera

Mallar skapas som tomma resurser med associerade metadata. När du skapar en mall måste du inkludera ett namn och en mapp tillsammans med en valfri beskrivning, templateType och enableMunchkin-parameter. templateType kan vara antingen frihandsritad eller guidad och som standard används freeForm. Skillnader mellan typerna finns i avsnittet Guidad kontra Fri form. enableMunchkin har standardvärdet false och när det är aktiverat förhindras Munchkin-spårning från att utföras på mallens underordnade landningssidor.

```
POST /rest/asset/v1/landingPageTemplates.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=New LPT - PHP&folder={"id":12,"type":"Folder"}
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "11b7#14dfe1e3bcf",
    "result": [
        {
            "id": 286,
            "name": "assetAPITest",
            "description": "test",
            "createdAt": "2015-06-16T20:45:03Z+0000",
            "updatedAt": "2015-06-16T20:45:03Z+0000",
            "url": "https://app-devlocal1.marketo.com/#LT286B2ZN12",
            "folder": {
                "type": "Folder",
                "value": 12,
                "folderName": "Templates"
            },
            "status": "draft",
            "workspace": "Default"
        }
    ]
}
```

Innehållet i mallen måste fyllas i separat via [Uppdatera innehåll för landningssidmall](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/updateLandingPageTemplateContentUsingPOST) slutpunkt.

### Uppdatera metadata

Metadata för landningssidmallar kan uppdateras via [Uppdatera metadata för landningssidmall](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Templates/operation/updateLpTemplateUsingPOST) slutpunkt. Namn, beskrivning och enableMunchkin-inställning kan uppdateras på det här sättet.

### Uppdatera innehåll

Innehåll i Landing Page Templates görs till en destruktiv uppdatering av hela HTML-innehållet. Innehållet måste skickas som multipart/form-data med den enda parametern som namnges content.

```
POST /rest/asset/v1/landingPageTemplate/286/content.json
```

```
content-type: multipart/form-data; boundary=--------------------------435851813185237176536801
----------------------------435851813185237176536801
Content-Disposition: form-data; name="content"; filename="content.txt"
Content-Type: text/plain

<html>
<head>
</head>
<body>
<div>Placeholder Content</div>
</body>
</html>
----------------------------435851813185237176536801--
```

```
 {
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "7516#14e0dc60bbc",
  "result": [
    {
      "id": 286
    }
  ]
}
```

## Klona

Marketo erbjuder en enkel metod för kloning av landningssidmallar. Det här är en begäran om POST av typen application/x-www-url-formencoded.

The `id` path-parametern anger ID:t för den Landing Page Template som ska klonas.

The `name` -parametern används för att ange namnet på den nya mallen för landningssida.

The `folder` -parametern används för att ange den överordnade mappen där den nya mallen för landningssida finns. Detta är i form av ett inbäddat JSON-objekt som innehåller  `id` och `type`.

Valfritt `description` används för att beskriva den nya mallen för landningssidor.

```
POST /rest/asset/v1/landingPageTemplate/{id}/clone.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Standard Template Clone&folder={"type": "Folder", "id": 732}
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "dee6#1683e9fd410",
    "warnings": [],
    "result": [
        {
            "id": 61,
            "name": "Standard Template Clone",
            "createdAt": "2019-01-11T20:34:48Z+0000",
            "updatedAt": "2019-01-11T20:34:48Z+0000",
            "url": "https://app-abm.marketo.com/#LT61B2ZN732",
            "folder": {
                "type": "Folder",
                "value": 732,
                "folderName": "Test LP Template Clone"
            },
            "status": "draft",
            "workspace": "Default",
            "templateType": "freeForm",
            "enableMunchkin": true
        }
    ]
}
```

## Godkännande

Mallar för landningssidor följer standardmodellen för utkast, där det kan finnas ett utkast till version och/eller en godkänd version. När uppdateringarna tillämpas på en mall tillämpas de alltid först på utkastversionen och visas bara live när mallen har godkänts.

För att en mall ska kunna godkännas måste den följa reglerna för sin typ, antingen i form av kostnadsfria guider. Mer information om kraven för att skapa och godkänna mallar av respektive typ finns i respektive dokument:

- [Mallar för kostnadsfria startsidor](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/create-a-free-form-landing-page-template)
- [Guided Landing Page Templates](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/create-a-guided-landing-page-template)
- [Exempel på guidade mallar](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/guided-landing-page-template-list)

## Ta bort

Om du vill ta bort en mall måste den vara oanvändbar och ej godkänd, vilket innebär att ingen underordnad landningssida får referera till den.  Landningssidmallar med inbäddade sociala knappar kan inte tas bort med denna API.
