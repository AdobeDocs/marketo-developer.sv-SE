---
title: Utskriftstoken
feature: REST API
description: Använd Marketo REST API-token för sidindelning för att hämta aktiviteter och leads, som omfattar datumbaserade och positionsbaserade token, ISO 8601 sinceDateTime och 414 fel.
exl-id: 63fbbf03-8daf-4add-85b0-a8546c825e5b
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '370'
ht-degree: 0%

---

# Utskriftstoken

Om du vill bläddra igenom resultaten, eller hämta data som har uppdaterats i förhållande till en viss data, tillhandahåller Marketo växlingstoken.

I vissa fall kan långa växlingstokensträngar returneras. Detta kan göra att du får en HTTP 414-felkod. Mer information om hur du hanterar dessa [fel](error-codes.md) finns.

Se dokumentationen för [Paging Token API](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getActivitiesPagingTokenUsingGET).

## Tokentyper

Det finns två relaterade, men distinkta, typer av växlingstoken som Marketo tillhandahåller:

- Datumbaserad
- Positionsbaserad

## Datumbaserad

Den första är en växlingstoken som representerar ett datum. Dessa används för att hämta aktiviteter, ändringar av datavärden och borttagna leads som har inträffat efter det datum som representeras av sidindelningstoken. Den här typen av växlingstoken genereras genom anrop av slutpunkten [Get Paging Token](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getActivitiesPagingTokenUsingGET) och en datetime.

```
GET /rest/v1/activities/pagingtoken.json?sinceDatetime=2014-10-06T13:22:17-08:00
```

```json
{
    "requestId": "1607c#14884f3e74e",
    "success": true,
    "nextPageToken": "GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ"
}
```

Formatet på parametern `sinceDateTime` måste följa standarddatumnotationen för [&#x200B; ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) . Bäst resultat får du om du använder en fullständig datetime som innehåller tidszonen. Tidszonen kan representeras som en förskjutning från GMT i följande format:

`yyyy-mm-ddThh:mm:ss+|-hh:mm`

Eller använda ett stort&quot;Z&quot; som kortskrift för att representera UTC så här:

`yyyy-mm-ddThh:mm:ssZ`

Exempel

`2016-09-15T15:53:00+05:00`

`2016-09-15T10:53:00Z`

Eftersom `sinceDateTime` är en frågeparameter måste den vara URL-kodad.

Strängen `nextPageToken` tillhandahålls sedan till ett [Get Lead Activities](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadActivitiesUsingGET), [Get Lead Changes](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadChangesUsingGET) eller [Get Deleted Leads](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getDeletedLeadsUsingGET) -anrop, och aktiviteter hämtas från efter det datum/tid som anges till API:t Get Paging Token.

```
GET /rest/v1/activities.json?nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ&activityTypeIds=1&activityTypeIds=12
```

## Positionsbaserad

Den andra typen av växlingstoken kan returneras av alla batchhämtningsanrop till en lead-databas-API. Den här typen av sidindelningstoken påminner om en databasmarkör, som gör det möjligt att gå igenom poster. Ett anrop av typen Hämta leads efter filtertyp kan till exempel representera en uppsättning som är större än den angivna gruppstorleken, vanligtvis maxvärdet och standardvärdet 300. När det finns fler resultat är fältet moreResult true i svaret och ett `nextPageToken` returneras. Om du vill hämta ytterligare poster i resultatuppsättningen, ett ytterligare anrop som innehåller `nextPageToken` med det mottagna värdet från det föregående svaret i det nya anropet. Svaret returnerar nästa sida i resultatuppsättningen.
