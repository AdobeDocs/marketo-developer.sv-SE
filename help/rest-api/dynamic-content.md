---
title: Dynamiskt innehåll
feature: REST API, Dynamic Content
description: Konfigurera Marketo dynamiska innehåll på avsnittsnivå via REST API:er med segmentering för att personalisera e-post, landningssidor och kodfragment med slutpunkter och exempel
exl-id: 8ab97624-5fb5-4a41-911f-ec8616dd43c9
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '441'
ht-degree: 0%

---

# Dynamiskt innehåll

Marketo underlättar användningen av dynamiskt innehåll genom ledande segmentering av flera tillgångstyper:

- E-post
- Landningssidor
- Fragment

## Översikt

Dynamiskt innehåll implementeras på avsnittsnivå genom att ange specifika varianter av ett avsnitt som ska användas som lead baserat på deras kvalificering i ett segment inom en vald segmentering. Om en del av innehållet är konfigurerad för dynamiskt innehåll baserat på en viss segmentering, kan en lead se att innehållet betjänas av innehållsvariationen som matchar segmentet som de tillhör, eller standardinnehållet, om de inte kvalificerar sig för ett segment.

## Exempel

Som exempel kan vi titta på ett exempel på ett e-postmeddelande där vi har en regionssegmentering (USA) och bara vill visa en eventkampanj för leads som hamnar i sydvästsegmentet, som omfattar leads i Kalifornien, Nevada, Utah, Colorado, Arizona och New Mexico. För att göra detta skapar vi ett redigerbart avsnitt i vårt e-postmeddelande med ID&quot;Q1-Promote-banner&quot; till ett DynamicContent-avsnitt. För att göra detta måste vi använda slutpunkten [Uppdatera e-postinnehåll](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/updateEmailComponentContentUsingPOST) för e-postmeddelandet. Parametern `value` används för att ange ID för segmenteringen.

Obs! Både e-post och landningssidor följer det här mönstret. Kodfragment har ett annat mönster, vilket beskrivs i API-dokumentationen för fragment.

I följande exempel anges att avsnittet ska vara ett avsnitt med dynamiskt innehåll, segmenterat med segmentering 1001.

```
POST /rest/asset/v1/email/{id}/content/Q1-promotion-banner.json
```

```
type=DynamicContent&value=1001
```

```json
{
  "success": true,
  "errors": [],
  "requestId": "891b#1729b34b9a5",
  "warnings": [],
  "result": [
    {
      "id": 1909
    }
  ]
}
```

Om du vill lägga till innehåll för enskilda segment måste vi anropa slutpunkten [Uppdatera avsnitt för dynamiskt e-postinnehåll](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails/operation/updateEmailDynamicContentUsingPOST) för det specifika avsnittet.

Följande exempel ställer in avsnittet så att det visar vår speciella banderollbild för leads i segmentet sydväst i stället för standardbilden. Om vi vill skapa fler variationer för fler segment anropar vi den här slutpunkten igen för varje segment och avsnitt.

```
POST /rest/asset/v1/email/{id}/dynamicContent/{dynamicContentId}.json
```

```
segment=Southwest&type=HTML&value=<img src='//www.example.com/SuperSpecialBannerForAmericanSouthwestLeads.jpg'/>
```

```json
{
  "success": true,
  "errors": [],
  "requestId": "891b#1729b34b9a5",
  "warnings": [],
  "result": [
    {
      "id": 1637
    }
  ]
}
```

## Segmentering

Segmentering är kärnan i Marketo dynamiska innehåll. En segmentering är en användardefinierad lista med enskilda regeluppsättningar som utvärderas uppifrån och ned mot hela lead-databasen. En lead får endast vara medlem av ett segment i varje segmentering och blir medlem av det första som kvalificeras för i varje segmentering. Om det inte är kvalificerat för ett segment blir det medlem i standardsegmentet och får standardinnehållet för alla delar av dynamiskt innehåll som använder den segmenteringen.

### Lista

Segmenteringar har en listslutpunkt som returnerar ett svar med en lista över tillgängliga segmenteringar.

```
GET /rest/asset/v1/segmentation.json
```

```json
{
  "success": true,
  "warnings": [ ],
  "errors": [ ],
  "requestId": "78eb#14e9de95868",
  "result": [
    {
      "id": 1001,
      "name": "My Industry Segmentation",
      "description": "",
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:10Z+0000",
      "url": "https://app-abm.marketo.com/#SG1001A1",
      "folder": {
        "type": "Program",
        "value": 396,
        "folderName": null
      },
      "status": "approved",
      "workspace": "Default"
    },
    {
      "id": 1002,
      "name": "My Country Segmentation",
      "description": "",
      "createdAt": "2015-04-06T18:28:23Z+0000",
      "updatedAt": "2015-04-06T18:37:18Z+0000",
      "url": "https://app-abm.marketo.com/#SG1002A1",
      "folder": {
        "type": "Program",
        "value": 396,
        "folderName": null
      },
      "status": "approved",
      "workspace": "Default"
    }
  ]
}
```

Segmenteringar har också en slutpunkt som returnerar ett svar med en lista över segment från en överordnad segmentering.

```
GET /rest/asset/v1/segmentation/1001/segments.json
```

```json
{
  "success": true,
  "warnings": [ ],
  "errors": [ ],
  "requestId": "2031#14e9df08796",
  "result": [
    {
      "id": 1001,
      "name": "Manufacturing",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "status": "approved",
      "segmentationId": 1001
    },
    {
      "id": 1002,
      "name": "Healthcare",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "url": "https://app-abm.marketo.com/#SL769688A1",
      "status": "approved",
      "segmentationId": 1001
    },
    {
      "id": 1003,
      "name": "Financial",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "url": "https://app-abm.marketo.com/#SL769690A1",
      "status": "approved",
      "segmentationId": 1001
    },
    {
      "id": 1004,
      "name": "Technology",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "url": "https://app-abm.marketo.com/#SL769692A1",
      "status": "approved",
      "segmentationId": 1001
    },
    {
      "id": 1005,
      "name": "Default",
      "description": null,
      "createdAt": "2015-04-06T18:23:32Z+0000",
      "updatedAt": "2015-04-06T18:37:09Z+0000",
      "url": "https://app-abm.marketo.com/#SL769694A1",
      "status": "approved",
      "segmentationId": 1001
    }
  ]
}
```
