---
title: Landningssidor
feature: REST API, Landing Pages
description: Använd Marketo REST API för att fråga efter metadata och innehåll, skapa, uppdatera, godkänna, ta bort och klona landningssidor, inklusive guidade och friformstyper.
exl-id: 2f986fb0-0a6b-469f-b199-1c526cd5a882
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '1018'
ht-degree: 0%

---

# Landningssidor

[Referens för startsidans slutpunkt](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Pages)

Landningssidor är webbsidor som lagras av Marketo.

## Fråga

Liksom de flesta andra resurser kan landningssidor efterfrågas [med namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Pages/operation/getLandingPageByNameUsingGET), [med id](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Pages/operation/getLandingPageByIdUsingGET) och med [bläddring](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Pages/operation/browseLandingPagesUsingGET). Dessa frågor returnerar endast metadata, och listan över avsnitt med innehåll för en landningssida måste efterfrågas separat av ID:t för landningssidan.

Om du frågar innehållet på landningssidan returneras en lista med avsnitt som är tillgängliga på landningssidan. Det måste finnas ett avsnitt i innehållslistan på en sida för att innehållet ska kunna uppdateras:

```
GET /rest/asset/v1/landingPage/{id}/content.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "6307#154ea1689d7",
    "result": [
        {
            "id": "67",
            "type": "Form",
            "index": 1,
            "content": {
                "content": "189",
                "contentType": "Form",
                "contentUrl": "https://app-devlocal1.marketo.com/#FO189A1ZN13LA1"
            },
            "formattingOptions": {
                "zIndex": 15,
                "left": "359px",
                "top": "122px"
            }
        }
    ]
}
```

Resultaten skiljer sig åt mellan guidade och friformsmallar, eftersom guidade landningssidor innehåller en uppsättning avsnitt som definieras av mallen som de härleds från, medan friformssidor inte innehåller fördefinierade avsnitt, och deras innehåll måste läggas till innan de redigeras.  Observera att formatet för attributet &quot;content&quot; kan variera beroende på attributet &quot;type&quot; och huruvida fältet är statiskt eller dynamiskt.

## Skapa och uppdatera

[Landningssidor skapas](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Pages/operation/createLandingPageUsingPOST) genom att referera tillbaka till en mall. De enda obligatoriska fälten som ska skapas är namn, mall (ID för mallen) och mappen där sidan ska placeras. Ytterligare metadata som kan fyllas i finns i slutpunktsreferensen.

Giltiga innehållstyper för [landningssidans innehåll](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Content) är: richText, HTML, Form, Image, Rectangle, Snippet.

```
POST rest/asset/v1/landingPages.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=createLandingPage&folder={"type": "Folder", "id": 11}&template=1&description=this is a test&workspace=default&title=test create&keywords=awesome&formPrefill=false
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "7a39#154cf7922c6",
    "result": [
        {
            "id": 27,
            "name": "createLandingPage",
            "description": "this is a test",
            "createdAt": "2016-05-20T18:41:43Z+0000",
            "updatedAt": "2016-05-20T18:41:43Z+0000",
            "folder": {
                "type": "Folder",
                "value": 11,
                "folderName": "Landing Pages"
            },
            "workspace": "Default",
            "status": "draft",
            "template": 1,
            "title": "test create",
            "keywords": "awesome",
            "robots": "index, nofollow",
            "formPrefill": false,
            "mobileEnabled": false,
            "URL": "https://app-devlocal1.marketo.com/lp/622-LME-718/createLandingPage.html",
            "computedUrl": "https://app-devlocal1.marketo.com/#LP27B2"
        }
    ]
}
```

Metadata för landningssidor kan uppdateras med [Update Landing Page Metadata endpoint](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Pages/operation/updateLandingPageUsingPOST).

## Godkännande

Landningssidor följer standardmodellen för utkast, där det kan finnas ett utkast till version och/eller en godkänd version. När uppdateringarna tillämpas på en sida tillämpas de alltid först på utkastversionen och visas bara direkt när sidan har godkänts.

## Ta bort

Om du vill ta bort en landningssida måste den först vara ur bruk och inte refereras till av några andra Marketo-resurser, samt vara icke-godkänd. Sidorna tas bort individuellt med slutpunkten [Ta bort landningssida](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Pages/operation/deleteLandingPageByIdUsingPOST) . Det går inte att ta bort landningssidor med inbäddade sociala knappar via det här API:t.

## Klona

Marketo erbjuder en enkel metod för kloning av en landningssida. Detta är en program/x-www-url-formencoded POST-begäran.

Sökvägsparametern `id` anger ID:t för den Landing Page som ska klonas.

Parametern `name` används för att ange namnet på den nya landningssidan.

Parametern `folder` används för att ange den överordnade mappen där en ny landningssida skapas. Detta sker i form av ett inbäddat JSON-objekt som innehåller `id` och `type`.

Parametern `template` används för att ange källsidmallens ID.

Den valfria parametern `description` används för att beskriva den nya landningssidan.

```
POST /rest/asset/v1/landingPage/{id}/clone.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=MyNewLandingPage&folder={"type":"Program","id":1119}&template=57
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "1078d#1683e4881c6",
    "warnings": [],
    "result": [
        {
            "id": 3291,
            "name": "MyNewLandingPage",
            "createdAt": "2019-01-11T18:59:25Z+0000",
            "updatedAt": "2019-01-11T18:59:25Z+0000",
            "folder": {
                "type": "Program",
                "value": 1119,
                "folderName": "DefaultProgramWithGuidedLP"
            },
            "workspace": "Default",
            "status": "draft",
            "template": 57,
            "robots": "index, nofollow",
            "formPrefill": false,
            "mobileEnabled": false,
            "URL": "http://na-abm.marketo.com/lp/284-RPR-133/DefaultProgramWithGuidedLPPerkutoTestLP-Clone-1.html",
            "computedUrl": "https://app-abm.marketo.com/#LP3291A1LA1"
        }
    ]
}
```

## Hantera innehållsavsnitt

Innehållsavsnitt ordnas efter deras indexegenskap och läggs slutligen ut enligt de CSS-regler som tillämpas när de visas av klienten. Innehållsavsnitt inkluderas och hanteras med motsvarande slutpunkter för [Lägg till](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Content/operation/addLandingPageContentUsingPOST), [Uppdatera](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Content/operation/updateLandingPageContentUsingPOST) och [Ta bort](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Content/operation/removeLandingPageContentUsingPOST) startsidans innehåll, och kan frågas med [Hämta startsidans innehåll](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Content/operation/getLandingPageContentUsingGET). Varje avsnitt har en typ och en värdeparameter. Typen avgör vad som ska sättas in i värdet.  För dessa slutpunkter skickas data som POST x-www-form-urlencoded, inte som JSON.

**Avsnittstyper**

| Typ | Värde |
|--- |--- |
| DynamicContent | ID för segmenteringen. |
| Formulär | Formulärets ID. |
| HTML | Text i HTML. |
| Bild | ID för bildresursen. |
| Rektangel | Tom. |
| RichText | Text i HTML.  Får endast innehålla formaterade textelement. |
| Fragment | Utdragets id. |
| SocialButton | ID för  den sociala knappen. |
| Video | Videons id. |

För frihandssidor måste alla önskade innehållsavsnitt läggas till och bäddas in i div-elementet med ID:t `mktoContent`. För guidade sidor kan det finnas en lista med fördefinierade element i listan från slutpunkten [Hämta startsidans innehåll](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Content/operation/getLandingPageContentUsingGET). Mer kan läggas till eller deras [innehåll uppdateras](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Content/operation/updateLandingPageContentUsingPOST) via deras respektive slutpunkter.

### Dynamiskt innehåll

Om du vill skapa ett avsnitt med dynamiskt innehåll måste det redan finnas i landningssidans innehållslista. [Slutpunkten för uppdatering av innehållet på landningssidan](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Content/operation/updateLandingPageContentUsingPOST) måste sedan användas för att ange typen till DynamicContent. När ett avsnitt är inställt på dynamiskt innehåll skapas underliggande dynamiska avsnitt i innehållsavsnittet som alla ärver det konverterade elementets bastyp. Varje dynamiskt avsnitt ärver också innehållet från det konverterade avsnittet.

```
GET /rest/asset/v1/landingPage/{id}/dynamicContent/RVMtNDg=.json
```

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "46e#1560fa169d9",
  "result": [
    {
      "createdAt": "2016-07-21",
      "updatedAt": "2016-07-21",
      "segmentation": 1007,
      "segments": [
        {
          "segmentId": 1018,
          "segmentName": "Default",
          "type": "RichText",
          "content": "\n\t\t\t\t\t\t\tAlice was beginning to get very tired of sitting by her sister on the bank, and having nothing to do: once or twice she had peeped into the book her sister was reading, but it had no pictures or conversations in it.\n\t\t\t\t\t\t"
        },
        {
          "segmentId": 1017,
          "segmentName": "New Segment",
          "type": "RichText",
          "content": "\n\t\t\t\t\t\t\tAlice was beginning to get very tired of sitting by her sister on the bank, and having nothing to do: once or twice she had peeped into the book her sister was reading, but it had no pictures or conversations in it.\n\t\t\t\t\t\t"
        }
      ]
    }
  ]
}
```

[Uppdatering av innehållet](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Page-Content/operation/updateLandingPageDynamicContentUsingPOST) för varje enskilt segment görs utifrån segment-ID:t.

```
POST /rest/asset/v1/landingPage/{id}/dynamicContent/{dynamicContentId}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
segment=New Segment&value=New Content
```

```json
 {
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "7516#14e08fe7cbbc",
  "result": [
    {
      "id": 1012
    }
  ]
}
```

## Variabel

En av de funktioner som introducerats på guidade landningssidor är redigerbara variabler.  Variabler innehåller värden för element på en landningssida.  Variabler kan enkelt ändras med landningssidans redigerare enligt nedan:

![Landing Page Variables](assets/landing-page-variables.png)

Variabler definieras som metataggar inuti elementet `<head>` i en landningssidmall i guidat läge. Det finns tre typer av variabler: String, Color och Boolean.  Här är ett exempel på tre variabeldefinitioner:

```html
<head>
  <meta charset="utf-8">
  <meta class="mktoString" mktoName="My String Variable" id="stringVar" default="Hello World!">
  <meta class="mktoColor" mktoName="My Color Variable" id="colorVar" default="#ffffff">
  <meta class="mktoBoolean" mktoName="My Boolean Variable" id="boolVar" default="true">
</head>
```

Mer information finns i avsnittet Redigerbar variabel i dokumentationen för [Skapa en guidad startsidmall](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/demand-generation/landing-pages/landing-page-templates/create-a-guided-landing-page-template).

### Fråga

Hämta variabler för en guidad landningssida genom att skicka landningssidans ID till slutpunkten Get Landing Page Variables.

```
GET /rest/asset/v1/landingPage/{id}/variables.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "10843#15a6d7e5fa1",
    "result": [
        {
            "id": "stringVar",
            "value": "Hello World!",
            "type": "string"
        },
        {
            "id": "colorVar",
            "value": "#FFFFFF",
            "type": "color"
        },
        {
            "id": "boolVar",
            "value": "true",
            "type": "boolean"
        }
    ]
}
```

I  I det här exemplet innehåller den guidade landningssidan tre variabler: stringVar, colorVar, boolVar.

### Uppdatering

Uppdatera en variabel för en guidad landningssida genom att skicka landningssidans ID, variabel-ID och variabelvärdet för att uppdatera slutpunkten för landningssidans variabel.

```
POST /rest/asset/v1/landingPage/{id}/variable/{variableId}.json?value={newValue}
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "2b07#15a6db77da3",
    "result": [
        {
            "id": "stringVar",
            "value": "Hello Brave New World!",
            "type": "String"
        }
    ]
}
```

## Förhandsgranska landningssida

Marketo tillhandahåller slutpunkten [Get Landing Page Full Content](https://developer.adobe.com/marketo-apis/api/asset/#tag/Landing-Pages/operation/getLandingPageFullContentUsingGET) för att hämta en direktförhandsvisning av en landningssida som den skulle återges i en webbläsare. Det finns en obligatorisk parameter, `id` path-parametern, som är ID:t för landningssidan som du vill förhandsgranska. Det finns ytterligare två valfria frågeparametrar:

- segmentering: Accepterar en array med JSON-objekt som innehåller attributen segmentationId och segmentId. När det är inställt förhandsvisas landningssidan som om du var en lead som matchar de segmenten.
- leadId:  Accepterar heltals-ID för en lead. När det är inställt förhandsgranskar landningssidan som om den betraktades av det valda leadet.

```
GET /rest/asset/v1/landingPage/{id}/fullContent.json?leadId=1001&segmentation=[{"segmentationId":1030,"segmentId":1103}]
```

```json
{
  "success": true,
  "errors": [],
  "requestId": "119ab#17692849f1e",
  "warnings": [],
  "result": [
    {
      "id": 1023,
      "content": "<!DOCTYPE html>\n<html>\n <head>\n <meta charset=\"utf-8\">\n \n \n <meta name=\"robots\" content=\"index, nofollow\">\n <title></title>\n <style>\n body {background:#FFFFFF} \n #myConditionalDisplayArea {\n display: true;\n }\n </style>\n <link rel=\"shortcut icon\" href=\"/favicon.ico\" type=\"image/x-icon\" >\n<link rel=\"icon\" href=\"/favicon.ico\" type=\"image/x-icon\" >\n\n\n<style>.mktoGen.mktoImg {display:inline-block; line-height:0;}</style>\n </head>\n <body id=\"bodyId\">\n \n Hello Brave New World!\n <div class=\"mktoText\" id=\"exampleText\"><div>This is an example editable text area.</div>\n<div>Lead Full Name = Hanna Crawford</div>\n<div><br /></div>\n <script type=\"text/javascript\" src=\"//munchkin.marketo.net//munchkin.js\"></script><script>Munchkin.init('123-ABC-456', {customName: 'Test-Landing-Page-APIs_Guided-Landing-Page---deverly', PURL_VISIT_TOKEN, wsInfo: 'j1RR'});</script>\n<div id=\"mktoClickBlockingDiv\"></div>\n </body>\n</html>\n"
    }
  ]
}
```
