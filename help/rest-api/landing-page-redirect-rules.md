---
title: Regler för omdirigering av startsida
feature: REST API, Landing Pages
description: Konfigurera omdirigeringsregler för landningssidor via API:t.
exl-id: f63aa5ef-5872-4401-be75-6fb9b2977734
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '641'
ht-degree: 0%

---

# Regler för omdirigering av startsida

[Slutpunktsreferens för omdirigeringsregler för landningssida](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules)

Marketo erbjuder en uppsättning REST API:er för att utföra CRUD-åtgärder på URL:er för omdirigering av landningssidor. Dessa API:er följer standardgränssnittsmönstret för resurs-API:er med alternativen Fråga, Skapa, Uppdatera och Ta bort.

Regler för omdirigering av landningssidor ger möjlighet att omdirigera en URL för en landningssida till en annan sidas URL. Du kan dirigera om Marketo landningssidor, icke-Marketo landningssidor eller kombinationer av dessa. Ytterligare information om reglerna för omdirigeringssidan finns [här](https://experienceleague.adobe.com/docs/marketo/using/home.html).

## Fråga

När du frågar efter omdirigeringsregler för landningssidor följer standardfrågetyperna för resurserna [efter ID](#by_id) och [bläddring](#browse).

### Efter ID

Slutpunkten [Get Landing Page Redirect Rules by Id](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/getLandingPageRedirectRuleByIdUsingGET) tar en enkel sökvägsparameter för omdirigering av landningssidans regel `id` och returnerar en enda regel för omdirigering av landningssidor.

```
GET /rest/asset/v1/redirectRule/{id}.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "3d0#1707b2521e4",
    "warnings": [],
    "result": [
        {
            "id": 20,
            "redirectFromUrl": "https://calqeauto.com/DefDelPro1_LandingPage1.html",
            "hostname": "calqeauto.com",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5483
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5559
            },
            "redirectToUrl": "https://calqeauto.com/DefDelPro1_LandingPage2.html",
            "createdAt": "2020-02-25T06:56:44Z+0000",
            "updatedAt": "2020-02-25T06:56:44Z+0000"
        }
    ]
}
```

### Bläddra

Slutpunkten [Get Landing Page Redirect Rules](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/getLandingPageRedirectRulesUsingGET) returnerar en lista med regelposter för omdirigering av landningssidor.

Det finns flera valfria frågeparametrar som kan skickas för att filtrera resultaten.

Parametern `offset` är ett heltal som anger det maximala antalet poster som ska returneras (standard är 20). Max är 200. Parametern `maxReturn` är ett heltal som anger var poster ska hämtas. Kan användas tillsammans med förskjutning (standardvärdet är 0).

Parametern `hostname` kan användas för att filtrera efter startsidornas värdnamn.

`redirectToLandingPageId` är ett heltal som kan användas för att filtrera på ID:t för landningssidan som du omdirigerar till. `redirectToPath` kan användas för att filtrera på sökvägen till de landningssidor som du omdirigerar till.

Med parametrarna `earliestUpdatedAt` och `latestUpdatedAt` kan du ange vattenstämplar för låg och hög datetime för att returnera omdirigeringsregler för landningssidor som antingen har uppdaterats eller skapats initialt inom det angivna intervallet.

```
GET /rest/asset/v1/redirectRules.json&maxReturn=3
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "12213#1707b27efb5",
    "warnings": [],
    "result": [
        {
            "id": 5,
            "redirectFromUrl": "https://www.kirtideep.contact/LandingPage2.html",
            "hostname": "www.kirtideep.contact",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5406
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5404
            },
            "redirectToUrl": "https://www.kirtideep.contact/www.showLogs.com.html",
            "createdAt": "2019-11-14T06:26:29Z+0000",
            "updatedAt": "2019-11-14T06:26:29Z+0000"
        },
        {
            "id": 6,
            "redirectFromUrl": "https://www.kirtideep.contact/www.showLogs.com.html",
            "hostname": "www.kirtideep.contact",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5404
            },
            "redirectTo": {
                "type": "url",
                "value": "www.contactLogs.com"
            },
            "redirectToUrl": "www.contactLogs.com",
            "createdAt": "2019-11-14T06:27:10Z+0000",
            "updatedAt": "2019-11-14T06:27:10Z+0000"
        },
        {
            "id": 7,
            "redirectFromUrl": "https://www.kirtideep.contact/contact/log/check",
            "hostname": "www.kirtideep.contact",
            "redirectFrom": {
                "type": "path",
                "value": "/contact/log/check"
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5404
            },
            "redirectToUrl": "https://www.kirtideep.contact/www.showLogs.com.html",
            "createdAt": "2019-11-14T06:27:49Z+0000",
            "updatedAt": "2019-11-14T06:27:49Z+0000"
        }
    ]
}
```

## Skapa

Slutpunkten [Skapa regel för omdirigering av startsida](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/createLandingPageRedirectRuleUsingPOST) körs med en program/x-www-form-urlencoded-POST som har följande tre obligatoriska parametrar.

Parametern `hostname` anger värdnamnet för landningssidan. Detta bör tillhöra en varumärkesdomän eller ett alias. Maxlängden är 255 tecken.

Parametern `redirectFrom` anger källans landningssida. Detta är ett JSON-objekt som innehåller ett typ/värde-par som avgör om källan är en Marketo-landningssida eller en icke-Marketo-landningssida. Attributet `type` kan vara antingen&quot;landingPageId&quot; eller&quot;path&quot;.

| Parameter | Valfritt/obligatoriskt | Typ | Beskrivning |
|---|---|---|---|
| get | Obligatoriskt | Sträng | Metodåtgärd. |
| &#39;visitor&#39; | Obligatoriskt | Sträng | Metodnamn. |
| callback | Obligatoriskt | Funktion | Återanropsfunktion som ska aktiveras för varje returnerad kampanj. |


Parametern `redirectTo` anger målstartsidan. Detta är ett JSON-objekt som innehåller ett typ/värde-par som avgör om källan är en Marketo-landningssida eller en icke-Marketo-landningssida. Attributet `type` kan vara antingen&quot;landingPageId&quot; eller&quot;url&quot;.

| Typ av landningssida | redirectTo-typ | Exempel |
|---|---|---|
| Marketo | landingPageId | {&quot;type&quot;:&quot;landingPageId&quot;,&quot;value&quot;:&quot;1774&quot;} |
| Ej Marketo | url | {&quot;type&quot;:&quot;url&quot;,&quot;value&quot;:&quot;www.contactLogs.com&quot;} |

Mer information om hur du skapar omdirigeringsregler för landningssidor finns [här](https://experienceleague.adobe.com/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-actions/redirect-a-marketo-landing-page-to-another-page.html).

```
POST /rest/asset/v1/redirectRules.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
hostname=calqeauto.com&redirectFrom={"type":"landingPageId", "value":"5483"}&redirectTo={"type":"landingPageId", "value":"5559"}
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "d7c6#1707b223522",
    "warnings": [],
    "result": [
        {
            "id": 20,
            "redirectFromUrl": "https://calqeauto.com/DefDelPro1_LandingPage1.html",
            "hostname": "calqeauto.com",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5483
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5559
            },
            "redirectToUrl": "https://calqeauto.com/DefDelPro1_LandingPage2.html",
            "createdAt": "2020-02-25T06:56:44Z+0000",
            "updatedAt": "2020-02-25T06:56:44Z+0000"
        }
    ]
}
```

## Uppdatera

Slutpunkten [Uppdatera regler för omdirigering av landningssida](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/updateLandingPageRedirectRuleUsingPOST) har en enda sökvägsparameter för omdirigeringsregel `id` för landningssidan. Slutpunkten körs med en application/x-www-form-urlencoded POST.

Precis som med anropet för att skapa som beskrivs ovan skickas en eller flera av följande frågeparametrar för att ange vilket attribut för regeln som ska uppdateras: `hostname`, `redirectFrom`, `redirectTo`.

Den uppdaterade posten för omdirigeringsregel för landningssidan returneras i svaret.

```
POST /rest/asset/v1/redirectRule/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
redirectTo={"type":"landingPageId", "value":"5561"}
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "57b2#1707b3852d7",
    "warnings": [],
    "result": [
        {
            "id": 20,
            "redirectFromUrl": "https://calqeauto.com/DefDelPro1_LandingPage1.html",
            "hostname": "calqeauto.com",
            "redirectFrom": {
                "type": "landingPageId",
                "value": 5483
            },
            "redirectTo": {
                "type": "landingPageId",
                "value": 5561
            },
            "redirectToUrl": "https://calqeauto.com/DefDelPro1_LandingPage3.html",
            "createdAt": "2020-02-25T06:56:44Z+0000",
            "updatedAt": "2020-02-25T07:20:53Z+0000"
        }
    ]
}
```

## Ta bort

[Slutpunkten Ta bort regel för omdirigering av landningssida med ID ](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/deleteLandingPageRedirectRuleUsingPOST) tar en sökvägsparameter för omdirigering av enskild landningssidregel `id` .

```
POST /rest/asset/v1/redirectRule/{id}/delete.json
```

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "d505#154d01c8364",
  "result": [
    {
      "id": 2
    }
  ]
}
```

## Bläddra bland domäner för landningssidor

Slutpunkten [Hämta domäner för landningssida](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Redirect-Rules/operation/getLandingPageDomainsUsingGET) returnerar en lista med domänposter för landningssidor.

Det finns två valfria frågeparametrar som kan skickas för att filtrera resultaten.

Parametern `offset` är ett heltal som anger det maximala antalet poster som ska returneras (standard är 20, maximalt 200).

Parametern `maxReturn` är ett heltal som anger var poster ska hämtas. Kan användas tillsammans med `offset` (standardvärdet är 0).

```
POST /rest/asset/v1/landingPageDomains.json?maxReturn=3
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "6eb8#1707b43d3cb",
    "warnings": [],
    "result": [
        {
            "hostname": "calqeauto.com",
            "type": "domain"
        },
        {
            "hostname": "www.google.com",
            "type": "domain-alias"
        },
        {
            "hostname": "www.kirti.com",
            "type": "domain-alias"
        }
    ]
}
```
