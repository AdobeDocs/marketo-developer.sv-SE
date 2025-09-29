---
title: E-post
feature: REST API
description: Lär dig hur du använder Marketo Asset REST API för att fråga efter och hantera e-postresurser efter ID, namn eller mappbläddring, med information om prediktivt innehåll och A/B-testbegränsningar.
exl-id: 6875730d-c74a-42cf-a3d2-dad7a3ac535d
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '1971'
ht-degree: 0%

---

# E-post

[Referens för e-postslutpunkt](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails) En fullständig uppsättning REST-slutpunkter tillhandahålls för att hantera e-postresurser.

Obs! Om du använder [Marketo Predictive Content](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/predictive-content/working-with-predictive-content/understanding-predictive-content) misslyckas följande slutpunkter om de refererar till ett e-postmeddelande som innehåller prediktivt innehåll: [Get Email Content](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/getEmailContentByIdUsingGET), [Update Email Content Section](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/updateEmailComponentContentUsingPOST), [Approve Email Draft](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/approveDraftUsingPOST). Anropet returnerar en 709-felkod och motsvarande felmeddelande.

## Fråga

Frågemönstret för e-post är identiskt med det för mallar, vilket tillåter frågor [efter ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/getEmailByIdUsingGET), [efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/getEmailByNameUsingGET) och [bläddring](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/getEmailUsingGET) samt filtrering baserat på mapp med API:erna för bläddring och efter namn.

Obs! Om ett e-postmeddelande är en del av ett e-postprogram som använder [A/B-testning](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/email-programs/email-program-actions/email-test-a-b-test/add-an-a-b-test) är det e-postmeddelandet inte tillgängligt för fråga med följande slutpunkter: [Hämta e-post med ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/getEmailByIdUsingGET), [Hämta e-post med namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/getEmailByNameUsingGET), [Hämta e-post](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/getEmailUsingGET). Anropet anger att det lyckades, men det innehåller följande varning:&quot;Inga resurser hittades för de angivna sökvillkoren.&quot;

### Efter ID

```
GET /rest/asset/v1/email/1351.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"9ad0#14a1832af8c",
   "result":[
      {
         "id":1356,
         "name":"sakZxhxkwV",
         "description":"sample description",
         "createdAt":"2014-12-05T02:06:21Z+0000",
         "updatedAt":"2014-12-05T02:06:21Z+0000",
         "subject":{
            "type":"Text",
            "value":"sample subject"
         },
         "fromName":{
            "type":"Text",
            "value":"RBxEtmdQZz"
         },
         "fromEmail":null,
         "replyEmail":{
            "type":"Text",
            "value":"Qlikf@testmail.com"
         },
         "folder":{
            "type":"folder",
            "value":10421
         },
         "operational":false,
         "textOnly":false,
         "publishToMSI":false,
         "webView":false,
         "status":false,
         "template":338,
         "workspace":"Default",
         "isOpenTrackingDisabled": false,
         "version": 2,
         "autoCopyToText": true,
         "ccFields": [
            {
              "attributeId": "157",
              "objectName": "lead",
              "displayName": "Lead Owner Email Address",
              "apiName": null
            }
          ],
         "preHeader": "My awesome preheader!"
      }
   ]
}
```

### Efter namn

Du kan t.ex. skicka en mapp som du bara vill söka i i den mappen.

```
GET /rest/asset/v1/email/byName.json?name=My Email&folder={"id":1056,"type"="Folder"}
```

```json
{
   "success":true,
   "warnings":[
   ],
   "errors":[
   ],
   "requestId":"3a7f#14c484de875",
   "result":[
      {
         "id":1032,
         "name":"My Email",
         "description":"eCjxjIHmYPLtecoSphkvIXlrygOBDLhgyQKnsKMpiKWgSCKhkPMUFvFPUvEylmFiLjQGnffXGaiNLxAwiFOmIDvxEINoaSYascJw",
         "createdAt":"2015-03-23T20:23:25Z+0000",
         "updatedAt":"2015-03-23T20:23:25Z+0000",
         "subject":{
            "type":"Text",
            "value":"ezyKBmDcyCcUIrXASrLSvRuWQgWpRZxQstJoStgMSLEBASGKMpAnVeWrgJsaVFoFJUEXhEIPpDAWpzajzingUruFpiMcRRwtoBzU"
         },
         "fromName":{
            "type":"Text",
            "value":"dAiqRNJOdY"
         },
         "fromEmail":{
            "type":"Text",
            "value":"ilZxG@testmail.com"
         },
         "replyEmail":{
            "type":"Text",
            "value":"VYsCS@testmail.com"
         },
         "folder":{
            "type":"folder",
            "value":1056
         },
         "operational":false,
         "textOnly":false,
         "publishToMSI":false,
         "webView":false,
         "status":"draft",
         "template":32,
         "workspace":"Default",
         "isOpenTrackingDisabled": false,
        "version": 2,
         "autoCopyToText": true,
         "ccFields": [
            {
              "attributeId": "157",
              "objectName": "lead",
              "displayName": "Lead Owner Email Address",
              "apiName": null
            }
          ],
         "preHeader": "My awesome preheader!"
      }
   ]
}
```

### Bläddra

Att bläddra bland mappar fungerar på samma sätt som andra Resurs-API:ts bläddringsslutpunkter och tillåter valfri filtrering på `status`, `folder`, `earliestUpdatedAt`/`latestUpdatedAt`, `maxReturn` och `offset`. `status` är antingen Godkänd eller Utkast. `folder` är ett JSON-objekt som innehåller `id` och `type`. `maxReturn` är ett heltal som begränsar antalet resultat (standardvärdet är 20, maxvärdet är 200) och `offset` är ett heltal som kan användas med `maxReturn` för att läsa igenom stora resultatuppsättningar (standardvärdet är 0).

```
GET /rest/asset/v1/emails.json?maxReturn=3&folder={"id":341,"type":"Folder"}
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "17576#14e22eb29cb",
    "result": [
        {
            "id": 2137,
            "name": "Social Sharing in Email",
            "description": "",
            "createdAt": "2011-03-04T17:12:42Z+0000",
            "updatedAt": "2011-03-04T19:04:36Z+0000",
            "url": null,
            "subject": {
                "type": "Text",
                "value": "Republish this content to your favorite social site!"
            },
            "fromName": {
                "type": "Text",
                "value": "Demo Master Marketo"
            },
            "fromEmail": {
                "type": "Text",
                "value": "demomaster@marketo.com"
            },
            "replyEmail": {
                "type": "Text",
                "value": "demomaster@marketo.com"
            },
            "folder": {
                "type": "Folder",
                "value": 341,
                "folderName": "Social Media"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": true,
            "status": "approved",
            "template": null,
            "workspace": "Default",
            "isOpenTrackingDisabled": false,
            "version": 2,
            "autoCopyToText": true,
            "ccFields": [
               {
                 "attributeId": "157",
                 "objectName": "lead",
                 "displayName": "Lead Owner Email Address",
                 "apiName": null
               }
             ],
            "preHeader": "My awesome preheader!"
        }
    ]
}
```

## Frågeinnehåll

Du kan [hämta tillgängliga redigerbara avsnitt](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/getEmailContentByIdUsingGET) för ett e-postmeddelande genom att fråga efter dess innehåll och eventuellt filtrera efter status för att hämta avsnitten för antingen Godkänd eller Utkast.

```
GET /rest/asset/v1/email/1356/content.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"8a44#14c484de8c8",
   "result":[
      {
         "htmlId":"edit_text_3",
         "value":[
            {
               "type":"HTML",
               "value":"Content from testCreateEmailTemplate2"
            },
            {
               "type":"Text",
               "value":"Content from testCreateEmailTemplate2"
            }
         ],
         "contentType":"Text"
      }
   ]
}
```

Avsnitt kan returneras som om de har en typ av dynamicContent. Mer information finns i avsnittet [Dynamiskt innehåll](dynamic-content.md).

## Fråga efter CC-fält

Du kan hämta de fält som är aktiverade för e-post-CC i målinstansen genom att anropa slutpunkten [Hämta e-post-CC-fält](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/getEmailCCFieldsUsingGET).

```
GET /rest/asset/v1/email/ccFields.json
```

```json
{
   "success":true,
   "errors":[ ],
   "requestId":"e54b#16796fdbd4e",
   "warnings":[ ],
   "result":[
      {
         "attributeId":"157",
         "objectName":"lead",
         "displayName":"Lead Owner Email Address",
         "apiName":"leadOwnerEmailAddress"
      },
      {
         "attributeId":"396",
         "objectName":"company",
         "displayName":"Account Owner Email Address",
         "apiName":"accountOwnderEmailAddress"
      }
   ]
}
```

## Skapa och uppdatera

[E-postmeddelanden skapas](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/createEmailUsingPOST) baserat på en källmall och har en lista med redigerbara avsnitt som härleds från varje separat HTML-element i den mallen med klassen&quot;mktEditable&quot; och en unik id-egenskap. Om du skapar ett e-postmeddelande med API:t skapas en post som baseras på mallen tillsammans med eventuella ytterligare metadata som skickas. Följande parametrar krävs för ett lyckat Create Email-anrop: name, template, folder.

Följande parametrar kan inte skapas: `subject`, `fromName`, `fromEmail`, `replyEmail`, `operational`, `isOpenTrackingDisabled`. Om den tas bort kommer `subject` att vara tom, `fromName`, `fromEmail` och `replyEmail` kommer att anges till standardvärden för instanser och `operational` och `isOpenTrackingDisabled` kommer att vara false. `isOpenTrackingDisabled` avgör om pixeln för öppen spårning inkluderas i ett e-postmeddelande när den skickas.

```
POST /rest/asset/v1/emails.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=My New Email 02 - deverly&folder={"id":1017,"type":"Program"}&template=24&description=This is a test email&subject=Hey There&fromName=SomeBody&fromEmail=somebody@marketo.com&replyEmail=somebody@marketo.com
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "f557#14e22db88d9",
    "result": [
        {
            "id": 2212,
            "name": "My New Email 02 - deverly",
            "description": "This is a test email",
            "createdAt": "2015-06-23T23:58:09Z+0000",
            "updatedAt": "2015-06-23T23:58:09Z+0000",
            "url": "https://app-abm.marketo.com/#EM2212A1LA1",
            "subject": {
                "type": "Text",
                "value": "Hey There"
            },
            "fromName": {
                "type": "Text",
                "value": "SomeBody"
            },
            "fromEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "replyEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "folder": {
                "type": "Program",
                "value": 1017,
                "folderName": "Landing Page - promotion"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": false,
            "status": "draft",
            "template": 24,
            "workspace": "Default",
            "isOpenTrackingDisabled": false,
            "version": 2,
            "autoCopyToText": false,
            "ccFields": null,
            "preHeader": null
        }
    ]
}
```

[Uppdatering av en post](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/updateEmailContentUsingPOST) kan göras av ID. Detta gör att du kan uppdatera beskrivningen eller namnet på e-postmeddelandet.

```
POST /rest/asset/v1/email/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
description=This is an Email&name=Updated Email
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "f557#14e22db88d9",
    "result": [
        {
            "id": 2212,
            "name": "Updated Email",
            "description": "This is an Email",
            "createdAt": "2015-06-23T23:58:09Z+0000",
            "updatedAt": "2015-06-23T23:58:09Z+0000",
            "url": "https://app-abm.marketo.com/#EM2212A1LA1",
            "subject": {
                "type": "Text",
                "value": "Hey There"
            },
            "fromName": {
                "type": "Text",
                "value": "SomeBody"
            },
            "fromEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "replyEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "folder": {
                "type": "Program",
                "value": 1017,
                "folderName": "Landing Page - promotion"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": false,
            "status": "draft",
            "template": 24,
            "workspace": "Default",
            "isOpenTrackingDisabled": false,
            "version": 2,
            "autoCopyToText": false,
            "ccFields": null,
            "preHeader": null
        }
    ]
}
```

### Innehållsavsnitt, typ och uppdatering

Innehållet för varje avsnitt i ett e-postmeddelande måste uppdateras individuellt, förutom ämnet fromName, fromEmail och replyEmail, som uppdateras med slutpunkten [Uppdatera e-postinnehåll](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/updateEmailContentUsingPOST). När du använder den här slutpunkten kan dessa värden även anges till att använda dynamiskt innehåll i stället för statiskt innehåll. Varje parameter är ett JSON-objekt av typen/värde, där typen är antingen &quot;Text&quot; eller &quot;DynamicContent&quot; och värdet antingen är rätt textvärde eller det ID för segmenteringen som ska användas för det dynamiska innehållet. Data skickas som POST x-www-form-urlencoded, inte som JSON.  isOpenTrackingDisabled kan anges med Uppdatera e-postinnehåll

```
POST /rest/asset/v1/email/{id}/content.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
subject={"type":"Text","value":"Gettysburg Address"}&fromEmail={"type":"Text","value":"abe@testmail.com"}&fromName={"type":"Text","value":"Abe Lincoln"}&replyTO={"type":"Text","value":"replies@testmail.com"}
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"c865#14a1832afac",
   "result":[
      {
         "id":1356
      }
   ]
}
```

Om du anger att ett avsnitt ska använda dynamiskt innehåll, måste avsnitts-ID hämtas via anropet Hämta e-postinnehåll.

### Uppdatera redigerbart avsnitt

Redigerbara avsnitt uppdateras av de enskilda htmlIds-objekten. Endast ID:t för avsnitts e-post och htmlId krävs som sökvägsparametrar, medan typ, värde och textValue är valfria. Typen kan vara antingen Text, DynamicContent eller Snippet och påverkar det som skickas i värdet. Om typen är Text är värdet en sträng som innehåller HTML-innehållet i avsnittet. Om det är DynamicContent är det ett JSON-block, med tre medlemmar, type, som blir &quot;DynamicContent&quot;, segmentering, som är det id för segmenteringen som ska användas för innehållet, och default, som är en sträng som innehåller standardinnehållet för HTML i avsnittet. Den valfria parametern textValue är en sträng som innehåller textversionen av avsnittet. Data skickas som POST x-www-form-urlencoded, inte som JSON.

```
POST /rest/asset/v1/email/{id}/content/{htmlId}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
type=Text&value=<h1>Hello World!</h1>&textValue=Hello World!
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "155ac#14d58dfa9ad",
    "result": [
        {
            "id": 2179
        }
    ]
}
```

Obs! Om automatisk kopiering till text är inaktiverat för ett utdrag som är inbäddat i ett e-postmeddelande, uppdateras utdragets HTML-värde och textversionen av ett annat avsnitt i e-postmeddelandet uppdateras. Textversionen av e-postmeddelandet har då text som visar det uppdaterade värdet för utdraget HTML, inte den tidigare versionen som skulle ha varit förväntat med automatisk kopiering inaktiverad.

## Moduler

I e-postredigeraren 1.0 är en modul en del av e-postmeddelandet som definieras i mallen. Moduler kan innehålla valfri kombination av element, variabler och annat HTML-innehåll enligt beskrivningen [här](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/general/email-editor-2/email-template-syntax#EmailTemplateSyntax-Modules). Marketo erbjuder en uppsättning API:er för hantering av moduler i ett e-postmeddelande. För modulrelaterade slutpunkter som kräver HTTP POST-metoden formateras brödtexten som&quot;application/x-www-form-urlencoded&quot; (inte som JSON).

De flesta modulrelaterade slutpunkter kräver ett moduleId som sökvägsparameter. Detta är en sträng som beskriver modulen. moduleIds returneras av [Get Email Content](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/getEmailContentByIdUsingGET) som htmlId-attribut (se [Query](#modules_query) nedan).

### Fråga

Om du vill arbeta med moduler måste du ange en moduleId-parameter som unikt identifierar modulen. Du kan också ange indexparametern för modulen, som är ett heltal som beskriver ordningen för modulen i e-postmeddelandet.

[Hämta moduleIds och deras index](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/getEmailContentByIdUsingGET) genom att ange e-post-ID som en sökvägsparameter.

I följande exempel efterfrågas ett 1.0-e-postmeddelande baserat på mallen Skeleton som finns i avsnittet Startmallar i mallväljarens användargränssnitt.

```
GET /rest/asset/v1/email/{moduleId}/content.json
```

```json
{
  "success": true,
  "warnings": [ ],
  "errors": [ ],
  "requestId": "3d79#158da6492bd",
  "result": [
    {
      "htmlId": "free-image",
      "contentType": "Module",
      "index": 1,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "single",
      "value": {
        "width": "600",
        "altText": "",
        "style": "-ms-interpolation-mode: bicubic; outline: none; border-right-width: 0; border-bottom-width: 0; border-left-width: 0; text-decoration: none; border-top-width: 0; display: block; max-width: 100%; line-height: 100%; height: auto; width: 600px"
      },
      "contentType": "Image",
      "parentHtmlId": "free-image",
      "isLocked": false
    },
    {
      "htmlId": "video",
      "contentType": "Module",
      "index": 2,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "video2",
      "value": {

      },
      "contentType": "Video",
      "parentHtmlId": "video",
      "isLocked": false
    },
    {
      "htmlId": "free-text",
      "contentType": "Module",
      "index": 3,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "text",
      "value": [
        {
          "type": "HTML",
          "value": "Lorem ipsum dolor sit amet, consectetur adipisicing elit. Harum officiis dolorum, nulla, mollitia ducimus iure modi perferendis tenetur ea illum veniam aut sapiente deserunt repellendus. Excepturi illo numquam sint harum."
        },
        {
          "type": "Text",
          "value": "Lorem ipsum dolor sit amet, consectetur adipisicing elit. Harum officiis dolorum, nulla, mollitia ducimus iure modi perferendis tenetur ea illum veniam aut sapiente deserunt repellendus. Excepturi illo numquam sint harum."
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "free-text",
      "isLocked": false
    },
    {
      "htmlId": "two-articles",
      "contentType": "Module",
      "index": 6,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "article3",
      "value": {
        "height": "auto",
        "width": "270",
        "style": "-ms-interpolation-mode: bicubic; outline: none; border-right-width: 0; border-bottom-width: 0; border-left-width: 0; text-decoration: none; border-top-width: 0; display: block; max-width: 100%; line-height: 100%; height: auto; width: 270px"
      },
      "contentType": "Image",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "articleTitle",
      "value": [
        {
          "type": "HTML",
          "value": "LOREM IPSUM DOLOR SIT AMET"
        },
        {
          "type": "Text",
          "value": "LOREM IPSUM DOLOR SIT AMET"
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "text2",
      "value": [
        {
          "type": "HTML",
          "value": "Gumbo beet greens corn soko endive gumbo gourd. shallot courgette tatsoi pea sprouts fava bean collard greens dandelion okra wakame tomato. Dandelion cucumber earthnut pea peanut soko zucchini."
        },
        {
          "type": "Text",
          "value": "Gumbo beet greens corn soko endive gumbo gourd. shallot courgette tatsoi pea sprouts fava bean collard greens dandelion okra wakame tomato. Dandelion cucumber earthnut pea peanut soko zucchini."
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "article4",
      "value": {
        "height": "auto",
        "width": "270",
        "style": "-ms-interpolation-mode: bicubic; outline: none; border-right-width: 0; border-bottom-width: 0; border-left-width: 0; text-decoration: none; border-top-width: 0; display: block; max-width: 100%; line-height: 100%; height: auto; width: 270px"
      },
      "contentType": "Image",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "articleTitle2",
      "value": [
        {
          "type": "HTML",
          "value": "LOREM IPSUM DOLOR SIT AMET"
        },
        {
          "type": "Text",
          "value": "LOREM IPSUM DOLOR SIT AMET"
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "text3",
      "value": [
        {
          "type": "HTML",
          "value": "Gumbo beet greens corn soko endive gumbo gourd. shallot courgette tatsoi pea sprouts fava bean collard greens dandelion okra wakame tomato. Dandelion cucumber earthnut pea peanut soko zucchini."
        },
        {
          "type": "Text",
          "value": "Gumbo beet greens corn soko endive gumbo gourd. shallot courgette tatsoi pea sprouts fava bean collard greens dandelion okra wakame tomato. Dandelion cucumber earthnut pea peanut soko zucchini."
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "two-articles",
      "isLocked": false
    },
    {
      "htmlId": "footer",
      "contentType": "Module",
      "index": 7,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "footerText",
      "value": [
        {
          "type": "HTML",
          "value": "<p style=\"text-align: center;\"><span style=\"color: #333333;\"><strong>Acme, Inc<\/strong><\/span><\/p> \n<div style=\"text-align: center;\">\n  You received this because you've subscribed to our newsletter. Click \n <a href=\"{{system.unsubscribeLink}}\" target=\"_blank\" class=\"mktNoTrack\">here<\/a> to unsubscribe. \n <br> \n<\/div>"
        },
        {
          "type": "Text",
          "value": "Acme, Inc \n You received this because you've subscribed to our newsletter. Click here <{{system.unsubscribeLink}}> to unsubscribe."
        }
      ],
      "contentType": "Text",
      "parentHtmlId": "footer",
      "isLocked": false
    },
    {
      "htmlId": "spacer",
      "contentType": "Module",
      "index": 0,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "CTA",
      "contentType": "Module",
      "index": 4,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    },
    {
      "htmlId": "hr",
      "contentType": "Module",
      "index": 5,
      "parentHtmlId": "template-wrapper",
      "isLocked": false
    }
  ]
}
```

Resultatarrayen innehåller element som beskriver både moduler och HTML-element som är blandade. Modulelement innehåller ett contentType-attribut: Module-attribut och ett index-attribut. moduleId lagras i attributet htmlId.

Följande tabell innehåller en sammanfattning av moduleIds och deras motsvarande index i e-postmeddelandet.

| moduleId (a.k.a. htmlId) | Index |
|---|---|
| spacer | 0 |
| fri bild | 1 |
| video | 2 |
| fri text | 3 |
| CTA | 4 |
| hr | 5 |
| två artiklar | 6 |
| sidfot | 7 |

#### Lägg till

[Lägg till en modul](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/addModuleUsingPOST) i ett e-postmeddelande genom att välja någon av de befintliga modulerna i e-postmallen som används. Det gör du genom att ange e-post-id och moduleId som sökvägsparametrar. Indexfrågeparametern är obligatorisk och avgör ordningen på modulen i e-postmeddelandet. Om indexvärdet överskrider det största befintliga indexvärdet läggs modulen till i e-postmeddelandet.

```
POST /rest/asset/v1/email/{id}/content/{moduleId}/add.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
index=10
```

```
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "1063e#158d6ad2c3f",
    "result": [
        {
            "id": 1028
        }
    ]
}
```

#### Ta bort

[Ta bort en modul](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/deleteModuleUsingPOST) genom att ange e-post-ID och moduleId som sökvägsparametrar.

```
POST /rest/asset/v1/email/{id}/content/{moduleId}/delete.json
```

```
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "2356#158d6f6104a",
    "result": [
        {
            "id":1028
        }
    ]
}
```

#### Duplicera

[Duplicera en modul](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/duplicateModuleUsingPOST) genom att ange e-post-ID och moduleId som sökvägsparametrar. Det här anropet duplicerar modulen, placerar den under den ursprungliga modulen och flyttar de andra modulerna nedåt.

```
POST /rest/asset/v1/email/{id}/content/{moduleId}/duplicate.json
```

```
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "e740#158d705d967",
    "result": [
        {
            "id":1028
        }
    ]
}
```

#### Ordna om

[Ordna om modulerna](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/rearrangeModulesUsingPOST)en matris som innehåller alla moduler och den önskade positionen i e-postmeddelandet för varje. Varje arrayelement innehåller ett JSON-objekt med följande format:  { &quot;index&quot;: &lt;_index_>, &quot;moduleId&quot;: &quot;&lt;_moduleId_>&quot; }, där &lt;_index_> är det nollbaserade modulordernumret och &lt;_moduleId_> är moduleId.

```
POST /rest/asset/v1/email/{id}/content/rearrange.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
positions=[ {"index": 0, "moduleId": "free-image"}, {"index": 1, "moduleId": "title"}, {"index": 2, "moduleId": "mkvideo"}, {"index": 3, moduleId": "free-text"}, {"index": 4, "moduleId": "blankSpace"}, {"index": 5, "moduleId": "Separator"}, {"index": 6, "moduleId": "callToAction"}, {"index": 7, "moduleId": "blankSpace2"}, {"index": 8, "moduleId": "blankSpace3"} ]
```

```
{
    "success": true,
    "warnings":[ ],
    "errors":[ ],
    "requestId": "e67a#158d72d1cde",
    "result":[
        {
            "id": 1030
        }
    ]
}
```

#### Byt namn

[Byt namn på en modul](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/renameUsingPOST) i ett e-postmeddelande genom att skicka det nya namnet via name-parametern. Ange e-post-ID och moduleId (befintligt namn) som sökvägsparametrar.

```
POST /rest/asset/v1/email/{id}/content/{moduleId}/rename.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=MarketoVideo
```

```
{
    "success": true,
    "warnings":[ ],
    "errors": [ ],
    "requestId":"11521#158d740abc0",
    "result": [
        {
            "id": 1030
        }
    ]
}
```

## Variabel

I e-postredigeraren 1.0 används variabler för att lagra värden för element i e-postmeddelandet. Varje variabel definieras genom att lägga till Marketo-specifik syntax i din HTML enligt beskrivningen [här](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/general/email-editor-2/email-template-syntax#EmailTemplateSyntax-Variables). Marketo erbjuder en uppsättning API:er för hantering av variabler i ett e-postmeddelande.

### Fråga

[Hämta variabler](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/getEmailVariablesUsingGET) för ett e-postmeddelande genom att ange e-post-ID som en sökvägsparameter.

I följande exempel efterfrågas ett 1.0-e-postmeddelande baserat på mallen Skeleton som finns i avsnittet Startmallar i mallväljarens användargränssnitt.

```
GET /rest/asset/v1/email/{id}/variables.json
```

```
{
  "success": true,
  "warnings": [ ],
  "errors": [  ],
  "requestId": "756#158dade55e8",
  "result": [
    {
      "name": "twoArticlesSpacer5",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer6",
      "value": "15",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "footerSpacer2",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer7",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "twoArticlesLinkText2",
      "value": "CALL TO ACTION",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer8",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "twoArticlesLinkText",
      "value": "CALL TO ACTION",
      "moduleScope": false
    },
    {
      "name": "freeTextSpacer",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "freeTextSpacer2",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "ctaSpacer2",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "hrBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "freeTextBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "spacerBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "twoArticlesLink2",
      "value": "http:\/\/mylink",
      "moduleScope": false
    },
    {
      "name": "hrBorderColor",
      "value": "#e6e6e6",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBorderSize",
      "value": "1",
      "moduleScope": false
    },
    {
      "name": "ctaLink",
      "value": "http:\/\/mylink",
      "moduleScope": false
    },
    {
      "name": "freeImageBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "spacerSpacer",
      "value": "40",
      "moduleScope": false
    },
    {
      "name": "footerSpacer",
      "value": "10",
      "moduleScope": false
    },
    {
      "name": "ctaLinkText",
      "value": "CALL TO ACTION",
      "moduleScope": false
    },
    {
      "name": "twoArticlesButtonBackgroundColor2",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "ctaBorderSize",
      "value": "1",
      "moduleScope": false
    },
    {
      "name": "ctaBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "footerBackgroundColor",
      "value": "#ffffff",
      "moduleScope": false
    },
    {
      "name": "twoArticlesLink",
      "value": "http:\/\/mylink",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBorderColor",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "ctaBorderColor",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBorderColor2",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "hrBorderSize",
      "value": "1",
      "moduleScope": false
    },
    {
      "name": "twoArticlesButtonBackgroundColor",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "twoArticlesBorderSize2",
      "value": "1",
      "moduleScope": false
    },
    {
      "name": "ctaButtonBackgroundColor",
      "value": "#333333",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer4",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer3",
      "value": "15",
      "moduleScope": false
    },
    {
      "name": "twoArticlesSpacer2",
      "value": "20",
      "moduleScope": false
    },
    {
      "name": "ctaSpacer",
      "value": "20",
      "moduleScope": false
    }
  ]
}
```

Resultatarrayen innehåller element som beskriver variabler, en variabel per element.

Variabler kan omfatta hela e-postmeddelandet eller lokalt till en viss modul. Variabler för båda omfånget innehåller attributen name, value och moduleScope. Attributet &quot;moduleScope&quot; är booleskt, där false anger global och true anger lokal. Lokala variabler innehåller ett extra &quot;moduleId&quot;-attribut som anger modulen som variabeln är kopplad till.

#### Uppdatering

[Uppdatera en variabel](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/updateVariableUsingPOST) i ett e-postmeddelande genom att skicka det nya önskade värdet via parametern value. Ange e-post-id och variabelnamn som sökvägsparametrar. Om du uppdaterar en modulvariabel måste du också skicka parametern moduleId för att ange modulen som variabeln är kopplad till.

I följande exempel uppdaterar vi en global variabel med namnet &quot;hrBorderSize&quot; till värdet 1.

```
POST /rest/asset/v1/email/{id}/variable/{name}.json
```

```
Content-Type: application/x-www-form-urlencoded; charset=utf-8
```

```
value=2
```

```
{
    "success":true,
    "warnings":[ ],
    "errors":[ ],
    "requestId":"feb5#158db4be57e",
    "result": [
        {
            "name":"hrBorderSize",
            "value":"2",
            "moduleScope":false
        }
    ]
}
```

I följande exempel uppdaterar vi en lokal variabel med namnet&quot;ctaLinkText&quot; till värdet&quot;Click this button!&quot; i moduleId &quot;CTA&quot;.

```
POST /rest/asset/v1/email/1032/variable/ctaLinkText.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
value=Click this button!&moduleId=CTA
```

```json
{
    "success": true,
    "warnings":[ ],
    "errors":[ ],
    "requestId": "7f34#158dc28d2f7",
    "result": [
        {
            "name":"ctaLinkText",
            "value":"Click this button!",
            "moduleScope":true,
            "moduleId":"CTA"
        }
    ]
}
```

## Godkännande

E-postmeddelanden följer standardmönstret för godkännande av tillgångsposter. Du kan godkänna ett utkast, avgodkänna en godkänd version och ignorera ett befintligt utkast av ett e-postmeddelande via sina egna slutpunkter.

### Godkänn

När du anropar slutpunkten för godkännandet valideras e-postmeddelandet mot reglerna för Marketo-e-postmeddelanden. `from name`, `from email`, `reply to email` och `subject` måste fyllas i innan e-postmeddelandet kan godkännas.

```
POST /rest/asset/v1/email/{id}/approveDraft.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"15dbf#14a1832ae86",
   "result":[
      {
         "id":1362
      }
   ]
}
```

#### Ogodkänd

Åtgärden `unapprove` kan bara utföras på godkända e-postmeddelanden.

```
POST /rest/asset/v1/email/{id}/unapprove.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"3514#14a1832b0fa",
   "result":[
      {
         "id":1364
      }
   ]
}
```

#### Ignorera

E-postmeddelandet måste ha utkaststatus för att tas bort. Ett godkänt e-postmeddelande kan inte ignoreras.

```
POST /rest/asset/v1/email/{id}/discardDraft.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"182c0#14a1832af4f",
   "result":[
      {
         "id":1362
      }
   ]
}
```

#### Ta bort

```
POST /rest/asset/v1/email/{id}/delete.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"169cd#14a1832adba",
   "result":[
      {
         "id":1361
      }
   ]
}
```

## Klona

Marketo erbjuder en enkel metod för kloning av e-postmeddelanden. Den här typen av begäran görs med en application/x-www-url-urlencoded POST och har två obligatoriska parametrar, name och folder, ett inbäddat JSON-objekt med id och type. description är också en valfri parameter. Om det inte finns någon godkänd version klonas utkastversionen.

```
POST /rest/asset/v1/email/{id}/clone.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Clone of Social Sharing in Email&folder={"id":239,"type":"Folder"}&description=This is a test of clone email
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "bd49#15706f43d96",
    "result": [
        {
            "id": 2250,
            "name": "Clone of Social Sharing in Email",
            "description": "This is a test of clone email",
            "createdAt": "2016-09-07T23:20:52Z+0000",
            "updatedAt": "2016-09-07T23:20:52Z+0000",
            "url": "https://app-abm.marketo.com/#EM2250B2",
            "subject": {
                "type": "Text",
                "value": "Hey There"
            },
            "fromName": {
                "type": "Text",
                "value": "SomeBody"
            },
            "fromEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "replyEmail": {
                "type": "Text",
                "value": "somebody@marketo.com"
            },
            "folder": {
                "type": "Folder",
                "value": 239,
                "folderName": "Tradeshows and Events"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": false,
            "status": "draft",
            "template": 24,
            "workspace": "Default",
            "isOpenTrackingDisabled": false
        }
    ]
}
```

## Skicka exempel

Du kan utlösa ett exempelmeddelande via api som ska skickas till frågeparametern emailAddress. Du kan också lägga till en leadId-parameter för att personifiera ett visst lead från databasen och en textOnly-parameter för att skicka endast textversionen av e-postmeddelandet.

```
POST /rest/asset/v1/email/{id}/sendSample.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
emailAddress=abe@testmail.com&textOnly=true
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "360b#14cce7d2708",
    "result": [
        {
            "id": 2179
        }
    ]
}
```

## Förhandsgranska e-post

Marketo tillhandahåller slutpunkten [Get Email Full Content](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/getEmailFullContentUsingGET) för att hämta en direktförhandsvisning av ett e-postmeddelande som det skulle skickas till en mottagare. Den här slutpunkten kan bara användas i e-postmeddelanden i version 1.0. Det finns en obligatorisk parameter, id path-parametern, som är ID:t för den e-postresurs som du vill förhandsgranska. Det finns ytterligare tre valfria frågeparametrar:

- status: Accepterar värdena &quot;Utkast&quot; eller &quot;Godkänd&quot; som standard till den godkända versionen, om Godkänd, Utkast om ej godkänt
- type: Accepterar &quot;Text&quot; eller &quot;HTML&quot; och standardvärdet är HTML
- leadId:. Accepterar heltals-ID för en lead. Förhandsgranskar e-postmeddelandet som om det hade tagits emot av det valda leadet

```
GET /rest/asset/v1/email/{id}/fullContent.json
```

```json
{
   "success": true,
   "warnings": [ ],
   "errors": [ ],
   "requestId": null,
   "result": [
      {
         "id": 339,
         "status": "draft",
         "content": "<!DOCTYPE HTML PUBLIC \"-//W3C//DTD HTML 1.01 Transitional//EN\" \"http://www.w1.org/TR/html4/loose.dtd\">\n<html>\n  <head>\n    <meta http-equiv=\"Content-Type\" content=\"text/html; charset=utf-8\"/>\n    <title></title>\n  </head>\n  <body>\n      <div style=\"font: 14px tahoma; width: 100%\" class=\"mktEditable\" id=\"edit_text_3\">\n        Content from testCreateEmailTemplate2\n    </div>\n  </body>\n</html>"
      }
   ]
}
```

## Ersätt HTML

Marketo tillhandahåller slutpunkten [Uppdatera e-post med fullständigt innehåll](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/createEmailFullContentUsingPOST) för att ersätta hela innehållet i en e-postresurs. Den här slutpunkten kan bara användas för e-postmeddelanden i version 1.0 som har haft funktionen Redigera kod i användargränssnittet aktiverad och som har haft relationen till den överordnade mallen bruten. Detta API är främst avsett för resurser som har klonats som en del av ett program och kan inte ändras med standardinnehållets slutpunkter. E-post med dynamiskt innehåll stöds inte. Om du försöker ersätta HTML i ett e-postmeddelande där relationen är intakt returneras ett fel.

Den här slutpunkten förväntar sig en Content-Type: multipart/form-data med id-parametern i sökvägen, id:t för e-postmeddelandet och en parameter i brödtexten, innehåll som ett fullständigt HTML-e-postdokument med Content-Type&quot;text/html&quot;. Ett felformaterat HTML-dokument genererar en varning, men kanske inte tillåter godkännande, medan om JavaScript- och/eller `<script>`taggar inkluderas i dokumentet misslyckas anropet och ett fel genereras.

```
POST /rest/asset/v1/email/{id}/fullContent.json
```

```
content-type: multipart/form-data; boundary=--------------------------116301888604800085728247
content-length: 599
```

```html
----------------------------116301888604800085728247
Content-Disposition: form-data; name="content"; filename="email_content.html"
Content-Type: text/html

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 1.01 Transitional//EN" "http://www.w1.org/TR/html4/loose.dtd">
 <html>
 <head>
 <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
 <title></title>
 </head>
 <body>
 <div style="font: 14px tahoma; width: 100%" class="mktEditable" id="edit_text_3">
 EMAIL TEST CONTENT
 </div>
 </body>
 </html>
----------------------------116301888604800085728247--
```

```json
{
   "success": true,
   "warnings": [ ],
   "errors": [ ],
   "requestId": "15dbf#14a1832ae86",
   "result": [
      {
         "id": 1001
      }
   ]
}
```
