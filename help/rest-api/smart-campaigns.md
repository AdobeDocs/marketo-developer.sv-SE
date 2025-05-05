---
title: Smarta kampanjer
feature: REST API, Smart Campaigns
description: Översikt över smarta kampanjer
exl-id: 540bdf59-b102-4081-a3d7-225494a19fdd
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '989'
ht-degree: 0%

---

# Smarta kampanjer

[Slutpunktsreferens för smarta kampanjer (resurs)](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns)

[Referens för kampanjslutpunkt (leads)](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Campaigns)

Marketo erbjuder en uppsättning REST API:er för att utföra åtgärder på smarta kampanjer. Dessa API:er följer standardgränssnittsmönstret för resurs-API:er med alternativ för att fråga, skapa, klona och ta bort. Dessutom kan ni hantera körning av smarta kampanjer genom att schemalägga batchkampanjer eller begära utlösarkampanjer.

## Fråga

När du frågar efter smarta kampanjer används standardfrågetyperna för resurserna [via ID](#by_id), [efter namn](#by_name) och [bläddring](#browse).

### Efter ID

Slutpunkten [Get Smart Campaign by ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/getSmartCampaignByIdUsingGET) tar en smart kampanj `id` som sökvägsparameter och returnerar en enda smart kampanjpost.

```
GET /rest/asset/v1/smartCampaign/{id}.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "7883#169838a32f0",
    "warnings": [],
    "result": [
        {
            "id": 1001,
            "name": "Process Bounced Emails",
            "description": "System smart campaign for processing bounced email events",
            "createdAt": "2016-09-10T23:16:19Z+0000",
            "updatedAt": "2016-09-10T23:16:19Z+0000",
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 1001,
            "flowId": 1001,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1001A1"
        }
    ]
}
```

Med den här slutpunkten kommer det alltid att finnas en enda post i den första positionen för `result`-arrayen.

### Efter namn

Slutpunkten [Get Smart Campaign by Name](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/getSmartCampaignByNameUsingGET) tar en smart kampanj `name` som parameter och returnerar en smart kampanjpost.

```
GET /rest/asset/v1/smartCampaign/byName.json?name=Test Trigger Campaign
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "14494#16c886ffa44",
    "warnings": [],
    "result": [
        {
            "id": 1069,
            "name": "Test Trigger Campaign",
            "description": "",
            "createdAt": "2018-02-16T01:34:39Z+0000",
            "updatedAt": "2019-08-13T00:45:21Z+0000",
            "folder": {
                "id": 327,
                "type": "Folder"
            },
            "status": "Inactive",
            "type": "trigger",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 2747,
            "flowId": 1088,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1069A1"
        }
    ]
}
```

Med den här slutpunkten kommer det alltid att finnas en enda post i den första positionen för `result`-arrayen.

### Bläddra

Slutpunkten [Hämta smarta kampanjer](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/getAllSmartCampaignsGET) fungerar på samma sätt som andra bläddringsslutpunkter för resurs-API och tillåter flera valfria frågeparametrar för att ange filtervillkor.

Parametrarna `earliestUpdatedAt` och `latestUpdatedAt` accepterar `datetimes` i formatet ISO-8601 (utan millisekunder). Om båda anges måste firstUpdatedAt föregå latestUpdatedAt.

Parametern `folder` anger den överordnade mappen som ska bläddras under. Formatet är JSON-block som innehåller attributen `id` och `type`.

Parametern `maxReturn` är ett heltal som anger det maximala antalet poster som ska returneras. Standardvärdet är 20. Max är 200.

Parametern `offset` är ett heltal som anger var poster ska hämtas. Kan användas tillsammans med `maxReturn`. Standardvärdet är 0.

Parametern `isActive` är en boolesk parameter som endast returnerar aktiva utlösarkampanjer.

```
GET /rest/asset/v1/smartCampaigns.json?earliestUpdatedAt=2016-09-10T23:15:00-00:00&latestUpdatedAt=2016-09-10T23:17:00-00:00
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "626#16983a92965",
    "warnings": [],
    "result": [
        {
            "id": 1001,
            "name": "Process Bounced Emails",
            "description": "System smart campaign for processing bounced email events",
            "createdAt": "2016-09-10T23:16:19Z+0000",
            "updatedAt": "2016-09-10T23:16:19Z+0000",
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 1001,
            "flowId": 1001,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1001A1"
        },
        {
            "id": 1002,
            "name": "Process Unsubscribes",
            "description": "System smart campaign for processing unsubscribe events",
            "createdAt": "2016-09-10T23:16:19Z+0000",
            "updatedAt": "2016-09-10T23:16:19Z+0000",
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 1002,
            "flowId": 1002,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1002A1"
        }
    ]
}
```

Med den här slutpunkten kommer det att finnas en eller flera poster i arrayen `result`.

## Skapa

Slutpunkten [Skapa smart kampanj](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/createSmartCampaignUsingPOST) körs med en application/x-www-form-urlencoded-POST med två obligatoriska parametrar. Parametern `name` anger namnet på den smarta kampanj som ska skapas. Parametern `folder` anger den överordnade mappen där den smarta kampanjen skapas. Formatet är JSON-block som innehåller attributen `id` och `type`.

Du kan också beskriva den smarta kampanjen med parametern `description` (högst 2 000 tecken).

```
POST /rest/asset/v1/smartCampaigns.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Smart Campaign 02&folder={"type": "folder","id": 640}&description=This is a smart campaign creation test.
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "25bc#16c9138f148",
    "warnings": [],
    "result": [
        {
            "id": 1076,
            "name": "Smart Campaign 02",
            "description": "This is a smart campaign creation test.",
            "createdAt": "2019-08-14T17:42:04Z+0000",
            "updatedAt": "2019-08-14T17:42:04Z+0000",
            "folder": {
                "id": 640,
                "type": "Folder"
            },
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": true,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 5132,
            "flowId": 1095,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1076A1"
        }
    ]
}
```

## Uppdatera

Slutpunkten [Update Smart Campaign](https://developer.adobe.com/marketo-apis/api/asset/) körs med en application/x-www-form-urlencoded-POST. Det krävs en smart kampanj `id` som sökvägsparameter. Du kan använda parametern `name` för att uppdatera namnet på den smarta kampanjen, eller parametern `description` för att uppdatera beskrivningen av den smarta kampanjen.

```
POST /rest/asset/v1/smartCampaign/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Smart Campaign 02 Update&description=This is a smart campaign update test.
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "14b6a#16c924b992f",
    "warnings": [],
    "result": [
        {
            "id": 1076,
            "name": "Smart Campaign 02 Update",
            "description": "This is a smart campaign update test.",
            "createdAt": "2019-08-14T17:42:04Z+0000",
            "updatedAt": "2019-08-14T22:42:04Z+0000",
            "folder": {
                "id": 640,
                "type": "Folder"
            },
            "status": "Never Run",
            "type": "batch",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": true,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 5132,
            "flowId": 1095,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1076A1"
        }
    ]
}
```

## Klona

Slutpunkten [Klona smart kampanj](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5) körs med en program/x-www-form-urlencoded-POST med tre obligatoriska parametrar. Det krävs en `id`-parameter som anger vilken smart kampanj som ska klonas, en `name`-parameter som anger namnet på den nya smarta kampanjen och en `folder`-parameter som anger den överordnade mappen där den nya smarta kampanjen skapas. Formatet är JSON-block som innehåller attributen `id` och `type`.

Du kan också beskriva den smarta kampanjen med parametern `description` (högst 2 000 tecken).

```
POST /rest/asset/v1/smartCampaign/{id}/clone.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Test Trigger Campaign Clone&folder={"type": "folder","id": 640}&description=This is a smart campaign clone test.
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "681d#16c9339499b",
    "warnings": [],
    "result": [
        {
            "id": 1077,
            "name": "Test Trigger Campaign Clone",
            "description": "This is a smart campaign clone test.",
            "createdAt": "2019-08-15T03:01:41Z+0000",
            "updatedAt": "2019-08-15T03:01:41Z+0000",
            "folder": {
                "id": 640,
                "type": "Folder"
            },
            "status": "Inactive",
            "type": "trigger",
            "isSystem": false,
            "isActive": false,
            "isRequestable": false,
            "isCommunicationLimitEnabled": false,
            "recurrence": {
                "weekdayOnly": false
            },
            "qualificationRuleType": "once",
            "workspace": "Default",
            "smartListId": 5135,
            "flowId": 1096,
            "computedUrl": "https://app-sjqe.marketo.com/#SC1077A1"
        }
    ]
}
```

## Ta bort

Slutpunkten [Ta bort smart kampanj](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/deleteSmartCampaignUsingPOST) tar en smart kampanj `id` som sökvägsparameter.

```
POST /rest/asset/v1/smartCampaign/{id}/delete.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "d757#16c934216ac",
    "warnings": [],
    "result": [
        {
            "id": 1077
        }
    ]
}
```

## Grupp

Starta smarta batchkampanjer vid en viss tidpunkt och påverka en viss uppsättning leads alla samtidigt.

## Schema

Använd slutpunkten för [Schemalägg kampanj](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Campaigns/operation/scheduleCampaignUsingPOST) för att schemalägga en batchkampanj som ska köras antingen direkt eller vid ett framtida datum. Kampanjen `id` är en obligatorisk sökvägsparameter. Valfria parametrar är `tokens`, `runAt` och `cloneToProgram` som skickas i begärandetexten som application/json.

Parametern för tokens-arrayen är en array med Mina token som åsidosätter befintliga programtoken. När kampanjen har körts ignoreras token.  Varje tokenmatrisobjekt innehåller namn/värde-par. Namnet på token måste vara formaterat som {{my.name}}.

Parametern runAt datetime anger när kampanjen ska köras. Om inget anges körs kampanjen 5 minuter efter att slutpunkten har anropats. Datetime-värdet får inte vara längre än två år framåt.

Kampanjer som schemaläggs via denna API väntar alltid minst fem minuter innan de körs.

Strängparametern `cloneToProgram` innehåller namnet på ett resulterande program.  När detta anges skapas kampanjen, det överordnade programmet och alla dess resurser med det nya namnet. Det överordnade programmet klonas och den nyligen skapade kampanjen schemaläggs. Det resulterande programmet skapas under det överordnade. Program med fragment, push-meddelanden, meddelanden i appen, statiska listor, rapporter och sociala resurser kanske inte klonas på det här sättet. När den används är slutpunkten begränsad till 20 anrop per dag. Slutpunkten för [klonprogrammet](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5) är det rekommenderade alternativet.

```
POST /rest/v1/campaigns/{id}/schedule.json
```

```json
{
   "input":
      {
         "runAt": "2018-03-28T18:05:00+0000",
         "tokens": [
            {
               "name": "{{my.message}}",
               "value": "Updated message"
            },
            {
               "name": "{{my.other token}}",
               "value": "Value for other token"
            }
          ]
      }
}
```

```json
{
    "requestId": "52b#161d90e1743",
    "result": [
        {
            "id": 3713
        }
    ],
    "success": true
}
```

## Utlösare

Utlösare smarta kampanjer påverkar en person i taget baserat på en utlöst händelse.

### Begäran

Använd slutpunkten [Begär kampanj](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Campaigns/operation/triggerCampaignUsingPOST) för att skicka en uppsättning leads till en utlösarkampanj som körs genom kampanjflödet. Kampanjen måste ha utlösaren&quot;Kampanj är begärd&quot; med&quot;Webbtjänst-API&quot; som källa.

Den här slutpunkten kräver en kampanj `id` som sökvägsparameter och en `leads` heltalsarrayparameter som innehåller lead-ID. Högst 100 leads tillåts per samtal.

Arrayparametern `tokens` kan också användas för att åsidosätta Mina token lokalt till kampanjens överordnade program. `tokens` accepterar högst 100 token. Varje `tokens`-matrisobjekt innehåller ett namn/värde-par. Namnet på token måste vara formaterat som {{my.name}}. Om du använder [Lägg till en systemtoken som en länk i en e-postadress](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/general/using-tokens/add-a-system-token-as-a-link-in-an-email) för att lägga till systemtoken viewAsWebpageLink kan du inte åsidosätta den med `tokens`. Använd i stället [Lägg till en vy som webbsideslänk i ett e-postmeddelande](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/add-a-view-as-web-page-link-to-an-email) som gör att du kan åsidosätta viewAsWebPageLink med `tokens`.

Parametrarna `leads` och `tokens` skickas i begärandetexten som application/json.

```
POST /rest/v1/campaigns/{id}/trigger.json
```

```json
{
   "input":
      {
         "leads" : [
            {
               "id" : 318592
            },
            {
               "id" : 318593
            }
         ],
         "tokens" : [
            {
               "name": "{{my.message}}",
               "value": "Updated message"
            },
            {
               "name": "{{my.other token}}",
               "value": "Value for other token"
            }
         ]
      }
}
```

```json
{
    "requestId": "9e01#161d922f1aa",
    "result": [
        {
            "id": 3712
        }
    ],
    "success": true
}
```

### Aktivera

Slutpunkten [Aktivera smart kampanj](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/activateSmartCampaignUsingPOST) är enkel. En `id`-sökvägsparameter krävs. För att aktiveringen ska lyckas måste följande gälla:

- Måste inaktiveras
- Måste ha minst en utlösare och ett flödessteg
- Måste ha felfria utlösare, filter och flödessteg

```
POST /rest/asset/v1/smartCampaign/{id}/activate.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "a33a#161d9c0dcf3",
    "result": [
        {
            "id": 1069
        }
    ]
}
```

### Inaktivera

[Inaktivera smart kampanj](https://developer.adobe.com/marketo-apis/api/asset/#tag/Smart-Campaigns/operation/deactivateSmartCampaignUsingPOST) är enkelt. En `id`-sökvägsparameter krävs. För att inaktiveringen ska lyckas måste kampanjen aktiveras.

```
POST /rest/asset/v1/smartCampaign/{id}/deactivate.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "6228#161d9c29fbf",
    "result": [
        {
            "id": 1069
        }
    ]
}
```
