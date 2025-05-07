---
title: Extrahera massaktivitet
feature: REST API
description: Gruppbearbetning av aktivitetsdata från Marketo.
exl-id: 6bdfa78e-bc5b-4eea-bcb0-e26e36cf6e19
source-git-commit: a5b855691e7fb9e628e2d68fd14a8a6c689d6750
workflow-type: tm+mt
source-wordcount: '1332'
ht-degree: 0%

---

# Extrahera massaktivitet

[Slutpunktsreferens för extrahering av gruppaktivitet](https://developer.adobe.com/marketo-apis/api/mapi/)

Uppsättningen REST API:er för Bulk Activity Extract utgör ett programmatiskt gränssnitt för att hämta stora mängder aktivitetsdata från Marketo.  I de fall där det inte krävs låg fördröjning och där man måste överföra betydande volymer aktivitetsdata från Marketo, t.ex. CRM-integrering, ETL, datalagerhantering och dataarkivering.

## Behörigheter

API:erna för extrahering av gruppaktivitet kräver att API-användaren har behörigheterna Skrivskyddad aktivitet eller Skrivskyddad aktivitet.

## Filter

| Filtertyp | Datatyp | Obligatoriskt | Anteckningar |
| --- | --- | --- | --- |
| createdAt | Datumintervall | Ja | Accepterar ett JSON-objekt med medlemmarna `startAt` och `endAt`. `startAt` accepterar en datetime som representerar den låga vattenstämpeln och `endAt` accepterar en datetime som representerar den övre vattenstämpeln. Intervallet måste vara högst 31 dagar. Jobb med den här filtertypen returnerar alla tillgängliga poster som har skapats inom datumintervallet. Datumtider ska vara i ISO-8601-format, utan millisekunder. |
| activityTypeIds | Array\[heltal\] | Nej | Accepterar ett JSON-objekt med en medlem, `activityTypeIds`. Värdet måste vara en array med heltal som motsvarar de önskade aktivitetstyperna. Aktiviteten Ta bort lead stöds inte (använd slutpunkten [Hämta borttagna leads](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getDeletedLeadsUsingGET) i stället). Hämta aktivitetstyp-ID:n med [slutpunkten Hämta aktivitetstyper](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getAllActivityTypesUsingGET). |
| [primärAttributeValueIds](#primaryattributevalueids-options) | Array\[heltal\] | Nej | Accepterar ett JSON-objekt med en medlem, `primaryAttributeValueIds`. Värdet är en array med id:n som anger de primära attribut som ska filtreras. Högst 50 ID:n får anges. ID:n är den unika identifieraren för antingen ett lead-fält eller en resurs, och kan hämtas genom att anropa rätt REST API-slutpunkt. Om du till exempel vill filtrera ett specifikt formulär för aktiviteten Fyll i formulär skickar du formulärnamnet till slutpunkten [Hämta formulär efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Forms/operation/getLpFormByNameUsingGET) för att hämta formulär-ID:t. Här följer en lista över aktivitetstyper där filtrering av primära attribut stöds. |
| [primärAttributeValues](#primaryattributevalues-options) | Array\[String\] | Nej | Accepterar ett JSON-objekt med en medlem, `primaryAttributeValues`. Värdet är en array med namn som anger de primära attribut som ska filtreras. Högst 50 namn får anges. Namnen är den unika identifieraren för antingen ett lead-fält eller en resurs och kan hämtas genom att anropa rätt REST API-slutpunkt. Om du till exempel vill filtrera ett specifikt formulär för aktiviteten Fyll i formulär skickar du formulär-ID:t till [Hämta formulär med ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5)-slutpunkten för att hämta formulärnamnet. Här följer en lista över aktivitetstyper där filtrering av primära attribut stöds. |

### Alternativ för primärAttributeValueIds {#primaryattributevalueids-options}

| Typ av aktivitet | ID för primärt attributvärde | Hämtningsslutpunkt | Resursgrupp |
| --- | --- | --- | --- |
| Ändra datavärde | Lead-fält-ID | [Beskriv lead](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2) | Attributnamn |
| Ändra poäng | Lead-fält-ID | [Beskriv lead](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2) | Attributnamn |
| Ändra status i progression | Program-ID | [Hämta program efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByNameUsingGET) | Marketing Program |
| Lägg till i listan | Statiskt list-id | [Hämta statisk lista efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET) | Statisk lista |
| Ta bort från lista | Statiskt list-id | [Hämta statisk lista efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET) | Statisk lista |
| Fyll i formulär | Formulär-ID | [Hämta formulär efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Forms/operation/getLpFormByNameUsingGET) | Webbformulär |

När du använder `primaryAttributeValueIds` måste filtret `activityTypeIds` finnas och bara innehålla aktivitets-ID:n som matchar motsvarande resursgrupp. Om du till exempel filtrerar resurser i webbformulär tillåts bara aktivitetstypen &quot;Fyll i formulär&quot; i `activityTypeIds`.

Exempeltext för begäran:

```json
{
  "filter": {
    "createdAt": {
      "startAt": "2021-07-01T23:59:59-00:00",
      "endAt": "2021-07-02T23:59:59-00:00"
    },
    "activityTypeIds": [
      2
    ],
    "primaryAttributeValueIds": [
      16,102,95,8
    ]
  }
}
```

`primaryAttributeValueIds` och `primaryAttributeValues` kan inte användas tillsammans.

### Alternativ för primärAttributvärden {#primaryattributevalues-options}

| Typ av aktivitet | Primärt attributvärde | Hämtningsslutpunkt | Resursgrupp |
| --- | --- | --- | --- |
| Ändra datavärde | Visningsnamn för leadfält | [Beskriv lead](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2) | Attributnamn |
| Ändra poäng | Visningsnamn för leadfält | [Beskriv lead](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2) | Attributnamn |
| Ändra status i progression | Programnamn | [Hämta program med ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByIdUsingGET) | Marketing Program |
| Lägg till i listan | Statiskt listnamn | [Hämta statisk lista med ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET) | Statisk lista |
| Ta bort från lista | Statiskt listnamn | [Hämta statisk lista med ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET) | Statisk lista |
| Fyll i formulär | Formulärnamn | [Hämta formulär med ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5) | Webbformulär |

Observera att du måste använda &quot;&lt;<em>program</em>>.&lt;<em>asset</em>>&quot; notation to specify the name for the following asset groups: Marketing Program, Static List, Web Form. Ett formulär med namnet&quot;MPS Outbound&quot; som finns under ett program med namnet&quot;GL_OP_ALL_2021&quot; skulle till exempel anges som&quot;GL_OP_ALL_2021.MPS Outbound&quot;.

Exempeltext för begäran:

```json
{
  "filter": {
    "createdAt": {
      "startAt": "2021-07-01T23:59:59-00:00",
      "endAt": "2021-07-02T23:59:59-00:00"
    },
    "activityTypeIds": [
      2
    ],
    "primaryAttributeValues": [
      "GL_OP_ALL_2021.MPS Outbound"
    ]
  }
}
```

När du använder `primaryAttributeValues` måste filtret `activityTypeIds` finnas och bara innehålla aktivitets-ID:n som matchar motsvarande resursgrupp. Om du till exempel filtrerar resurser i webbformulär tillåts bara aktivitetstypen &quot;Fyll i formulär&quot; i `activityTypeIds`. `primaryAttributeValues` och `primaryAttributeValueIds` kan inte användas tillsammans.

## Alternativ

| Parameter | Datatyp | Obligatoriskt | Anteckningar |
|---|---|---|---|
| filter | Array[Object] | Ja | Accepterar en array med filter. Exakt ett `createdAt`-filter måste inkluderas i arrayen. Ett valfritt `activityTypeIds`-filter kan inkluderas. Filtren tillämpas på den tillgängliga aktivitetsuppsättningen och den resulterande uppsättningen aktiviteter returneras av exportjobbet. |
| format | Sträng | Nej | Accepterar något av följande: CSV, TSV, SSV Den exporterade filen återges som ett kommaavgränsat värde, tabbavgränsade värden eller en blankstegsavgränsad värdefil, om en sådan anges. Standardvärdet är CSV om den tas bort. |
| columnHeaderNames | Objekt | Nej | Ett JSON-objekt som innehåller nyckelvärdepar med fält- och kolumnrubriknamn. Nyckeln måste vara namnet på ett fält som ingår i exportjobbet. Värdet är namnet på den exporterade kolumnrubriken för det fältet. |
| fält | Array[String] | Nej | Valfri array med strängar som innehåller fältvärden. De listade fälten inkluderas i den exporterade filen. Som standard returneras följande fält: <ul><li>`marketoGUIDleadId`</li><li> `activityDate` </li><li>`activityTypeId` </li><li>`campaignId`</li><li> `primaryAttributeValueId` </li><li>`primaryAttributeValue`</li><li> `attributes`</li></ul>. Den här parametern kan användas för att minska antalet fält som returneras genom att ange en delmängd från listan ovan:`"fields": ["leadId", "activityDate", "activityTypeId"]`. Ett ytterligare fält `actionResult` kan anges för att inkludera aktivitetsåtgärden: `("succeeded", "skipped", or "failed")`. |


## Skapa ett jobb

Om du vill exportera poster måste du först definiera jobbet och den uppsättning poster som du vill hämta.  Skapa jobbet med slutpunkten [Skapa exportaktivitetsjobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/createExportActivitiesUsingPOST).  När du exporterar aktiviteter finns det två primära filter som kan användas: `createdAt`, som alltid krävs, och `activityTypeIds`, som är valfritt.  Filtret `createdAt` används för att definiera ett datumintervall i vilket aktiviteter skapades med parametrarna `startAt` och `endAt` som båda är datetime-fält och representerar det tidigaste tillåtna skapandedatumet respektive det senaste tillåtna skapandedatumet.  Du kan även filtrera på vissa typer av aktiviteter med hjälp av filtret `activityTypeIds`.  Detta är användbart när du vill ta bort resultat som inte är relevanta för ditt användningsfall.

```
POST /bulk/v1/activities/export/create.json
```

```json
{ 
   "format": "CSV",
   "filter": { 
      "createdAt": { 
         "startAt": "2017-07-01T23:59:59-00:00",
         "endAt": "2017-07-31T23:59:59-00:00"
      },
      "activityTypeIds": [ 
         1,
         12,
         13
      ]
   }
}
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Created",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "format": "CSV"
      }
   ]
}
```

Jobbet har nu statusen&quot;Skapat&quot;, men finns ännu inte i bearbetningskön.  Om du vill placera den i kön så att den kan påbörja bearbetningen anropar du slutpunkten för [Enqueue-exportaktivitetsjobbet](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/enqueueExportActivitiesUsingPOST) med exportId från svaret på statusen när den skapades.

```
POST /bulk/v1/activities/export/{exportId}/enqueue.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Queued",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "format": "CSV"
      }
   ]
}
```

Nu rapporterar statusen att jobbet har placerats i kö.  När en arbetare blir tillgänglig för det här jobbet ändras statusen till &quot;Bearbetar&quot; och jobbet börjar samla poster från Marketo.

## Avsökningsjobbstatus

Jobbstatus kan bara hämtas för jobb som skapats av samma API-användare.

Marketo Bulk Activity Extract är en asynkron slutpunkt, så jobbstatusen måste avfrågas för att avgöra när jobbet är klart.  Avsök med slutpunkten [Get Export Activity Job Status](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/getExportActivitiesStatusUsingGET) enligt följande:

```
GET /bulk/v1/activities/export/{exportId}/status.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Completed",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "startedAt": "2017-01-21T11:51:30-08:00",
         "finishedAt": "2017-01-21T12:59:30-08:00",
         "format": "CSV",
         "numberOfRecords": 15423,
         "fileSize": 12342,
         "fileChecksum": "sha256:c16514c7e80fcac5ea055dacae9617fc3c29aff5365e3743071313ce0ed2a815"
      }
   ]
}
```

Statusfältet kan svara med ett av följande värden:

- Skapad
- Köad
- Bearbetar
- Avbruten
- Slutförd
- Misslyckades

## Hämtar data

När jobbet är klart hämtar du dina data med slutpunkten [Hämta exportaktivitetsfil](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/getExportActivitiesFileUsingGET).

```
GET /bulk/v1/activities/export/{exportId}/file.json
```

Svaret innehåller en fil som är formaterad på det sätt som jobbet konfigurerades. Slutpunkten svarar med filens innehåll.

Om ett begärt lead-fält är tomt (innehåller inga data) placeras `then null` i motsvarande fält i exportfilen.  I exemplet nedan är fältet `campaignId` för den returnerade aktiviteten tomt.

```json
marketoGUID,leadId,activityDate,activityTypeId,campaignId,primaryAttributeValueId,primaryAttributeValue,attributes
783957693,5414087,2022-02-13T14:06:20Z,104,8497,1670,MembershipTest1,"{""Reason"":""Changed by Smart Campaign MembershipTestCampaignStepChoice.MembershipTestCampaignStepChoiceSetUp action Change Data Value"",""Program Member ID"":3240303,""Acquired By"":true,""Old Status"":""Not in Program"",""New Status ID"":21,""Success"":false,""New Status"":""On List"",""Old Status ID"":20}"
783958220,5414094,2022-02-13T14:08:50Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":6,""Success"":true,""New Status"":""Attended"",""Old Status ID"":1}"
783958306,5414094,2022-02-13T14:09:16Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Attended"",""New Status ID"":6,""Success"":false,""New Status"":""Attended"",""Old Status ID"":6}"
783961924,5316669,2022-02-13T14:27:21Z,104,11614,2333,Nurture Automation,"{""Program Member ID"":3240306,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":27,""Success"":false,""New Status"":""Member"",""Old Status ID"":26}"
```

Om du vill ha stöd för delvis och återanvändningsvänlig hämtning av extraherade data, kan filslutpunkten (om det behövs) ha stöd för HTTP-huvudet `Range` av typen `bytes`.  Om rubriken inte är inställd returneras hela innehållet.  Du kan läsa mer om hur du använder intervallhuvudet med Marketo [Massextrahering](bulk-extract.md).

## Avbryta ett jobb

Om ett jobb konfigurerades felaktigt eller blir onödigt kan det enkelt avbrytas med slutpunkten [Avbryt exportaktivitetsjobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/cancelExportActivitiesUsingPOST) :

```
POST /bulk/v1/activities/export/{exportId}/cancel.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Cancelled",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "format": "CSV"
      }
   ]
}
```

Svaret har en status som anger att jobbet har avbrutits.
