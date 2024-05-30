---
title: "Bulk Activity Extract"
feature: REST API
description: "Batchbearbetning av aktivitetsdata från Marketo."
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '1381'
ht-degree: 0%

---


# Extrahera massaktivitet

[Referens för extraheringsslutpunkt för gruppaktivitet](https://developer.adobe.com/marketo-apis/api/mapi/)

Uppsättningen REST API:er för Bulk Activity Extract utgör ett programmatiskt gränssnitt för att hämta stora mängder aktivitetsdata från Marketo.  I de fall där det inte krävs låg fördröjning och där det krävs att betydande volymer aktivitetsdata överförs från Marketo, t.ex. CRM-integrering, ETL, datalagerhantering och dataarkivering.

## Behörigheter

API:erna för extrahering av gruppaktivitet kräver att API-användaren har behörigheterna Skrivskyddad aktivitet eller Skrivskyddad aktivitet.

## Filter

<table>
  <tbody>
    <tr>
      <td>Filtertyp</td>
      <td>Datatyp</td>
      <td>Obligatoriskt</td>
      <td>Anteckningar</td>
    </tr>
    <tr>
      <td>createdAt</td>
      <td>Datumintervall</td>
      <td>Ja</td>
      <td>Accepterar ett JSON-objekt med medlemmarna startAt och endAt. startAt accepterar ett datetime-värde som representerar den låga vattenstämpeln, och endAt accepterar ett datetime-värde som representerar den övre vattenstämpeln. Intervallet måste vara högst 31 dagar. Jobb med den här filtertypen returnerar alla tillgängliga poster som skapades inom datumintervallet.Datumtider måste vara i ett ISO-8601-format, utan millisekunder.</td>
    </tr>
    <tr>
      <td>activityTypeIds</td>
      <td>Array[heltal]</td>
      <td>Nej</td>
      <td>Accepterar ett JSON-objekt med en medlem, activityTypeIds. Värdet måste vara en matris med heltal som motsvarar de önskade aktivitetstyperna. Aktiviteten Ta bort lead stöds inte (använd <a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getDeletedLeadsUsingGET">Hämta borttagna leads</a>slutpunkt i stället).Hämta aktivitetstyp-ID med<a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getActivitiesPagingTokenUsingGET">Hämta aktivitetstyper</a>slutpunkt.</td>
    </tr>
    <tr>
      <td>primaryAttributeValueIds</td>
      <td>Array[heltal]</td>
      <td>Nej</td>
      <td>Accepterar ett JSON-objekt med en medlem, primärAttributeValueIds. Värdet är en array med id:n som anger de primära attribut som ska filtreras. Högst 50 ID:n kan anges. ID:n är den unika identifieraren för antingen ett lead-fält eller en resurs och kan hämtas genom att anropa rätt REST API-slutpunkt. Om du till exempel vill filtrera ett visst formulär för aktiviteten Fyll i formulär skickar du formulärnamnet till <a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Forms/operation/getLpFormByNameUsingGET">Hämta formulär efter namn</a> slutpunkt för hämtning av formulär-ID. Här följer en lista över aktivitetstyper där filtrering av primära attribut stöds.
        <table>
          <tbody>
            <tr>
              <td>Typ av aktivitet</td>
              <td>ID för primärt attributvärde</td>
              <td>Hämtningsslutpunkt</td>
              <td>Resursgrupp</td>
            </tr>
            <tr>
              <td>Ändra datavärde</td>
              <td>Lead-fält-ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2">Beskriv lead</a></td>
              <td>Attributnamn</td>
            </tr>
            <tr>
              <td>Ändra poäng</td>
              <td>Lead-fält-ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2">Beskriv lead</a></td>
              <td>Attributnamn</td>
            </tr>
            <tr>
              <td>Ändra status i progression</td>
              <td>Program-ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByNameUsingGET">Hämta program efter namn</a></td>
              <td>Marketing Program</td>
            </tr>
            <tr>
              <td>Lägg till i listan</td>
              <td>Statiskt list-id</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET">Hämta statisk lista efter namn</a></td>
              <td>Statisk lista</td>
            </tr>
            <tr>
              <td>Ta bort från lista</td>
              <td>Statiskt list-id</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByNameUsingGET">Hämta statisk lista efter namn</a></td>
              <td>Statisk lista</td>
            </tr>
            <tr>
              <td>Fyll i formulär</td>
              <td>Formulär-ID</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Forms/operation/getLpFormByNameUsingGET">Hämta formulär efter namn</a></td>
              <td>Webbformulär</td>
            </tr>
          </tbody>
        </table>
        När primärAttributeValueIds används måste filtret activityTypeIds finnas och bara innehålla aktivitets-ID:n som matchar motsvarande resursgrupp.Exempel:Om du filtrerar på webbformulärresurser tillåts endast aktivitetstyp-ID:t "Fyll i formulär" i activityTypeIds.Exempel Innehåll i begäran:{"filter":{"createdAt":{"startAt": "202 1-07-01T23:59:59-00:00","endAt": "2021-07-02T23:59:59-00:00"},"activityTypeIds":[2],"primärAttributeValueIds" : [16,102,95,8]}}primärAttributeValueIds och primärAttributeValues kan inte användas tillsammans.</td>
    </tr>
    <tr>
      <td>primärAttributeValues</td>
      <td>Array[String]</td>
      <td>Nej</td>
      <td>Accepterar ett JSON-objekt med en medlem, primärAttributeValues. Värdet är en array med namn som anger de primära attribut som ska filtreras. Högst 50 namn kan anges. Namnen är den unika identifieraren för antingen ett lead-fält eller en resurs och kan hämtas genom att anropa rätt REST API-slutpunkt. Om du till exempel vill filtrera ett specifikt formulär för aktiviteten Fyll i formulär skickar du formulär-ID:t till <a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5">Hämta formulär efter ID</a> slutpunkt för att hämta formulärnamnet. Här följer en lista över aktivitetstyper där filtrering av primärt attribut stöds.
        <table>
          <tbody>
            <tr>
              <td>Typ av aktivitet</td>
              <td>Primärt attributvärde</td>
              <td>Hämtningsslutpunkt</td>
              <td>Resursgrupp</td>
            </tr>
            <tr>
              <td>Ändra datavärde</td>
              <td>Visningsnamn för leadfält</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2">Beskriv lead</a></td>
              <td>Attributnamn</td>
            </tr>
            <tr>
              <td>Ändra poäng</td>
              <td>Visningsnamn för leadfält</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/describeUsingGET_2">Beskriv lead</a></td>
              <td>Attributnamn</td>
            </tr>
            <tr>
              <td>Ändra status i progression</td>
              <td>Programnamn</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/getProgramByIdUsingGET">Hämta program efter ID</a></td>
              <td>Marketing Program</td>
            </tr>
            <tr>
              <td>Lägg till i listan</td>
              <td>Statiskt listnamn</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET">Hämta statisk lista efter ID</a></td>
              <td>Statisk lista</td>
            </tr>
            <tr>
              <td>Ta bort från lista</td>
              <td>Statiskt listnamn</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists/operation/getStaticListByIdUsingGET">Hämta statisk lista efter ID</a></td>
              <td>Statisk lista</td>
            </tr>
            <tr>
              <td>Fyll i formulär</td>
              <td>Formulärnamn</td>
              <td><a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Sales-Persons/operation/describeUsingGET_5">Hämta formulär efter ID</a></td>
              <td>Webbformulär</td>
            </tr>
          </tbody>
        </table>
        Observera att du måste använda "&lt;<em>program</em>&gt;.&lt;<em>resurs</em>&gt;" för att unikt ange namnet på följande resursgrupper: Marketing Program, Static List, Web Form.Example:Ett formulär med namnet "MPS Outbound" som finns under programmet med namnet "GL_OP_ALL_2021" skulle anges som "GL_OP_ALL_2021.MPS Outbound".Exempel Begär brödtext:{"filter":{"createdAt":{"startAt": "2021-07-01T23:59:59-00:00","endAt": "2021-07-02T23:59:59-00:00"},"activityTypeIds":[2],"primaryAttributeValues":["GL_OP_ALL_2021.MPS Outbound"]}}När primärAttributeValues används, måste activityTypeIds-filtret finnas och får endast innehålla aktivitets-ID:n som matchar motsvarande resursgrupp. Om du till exempel filtrerar resurser i webbformulär tillåts bara aktivitetstyp-ID:t "Fyll i formulär" i activityTypeIds.primaryAttributeValues och primaryAttributeValueIds att användas tillsammans.</td>
    </tr>
  </tbody>
</table>

## Alternativ

| Parameter | Datatyp | Obligatoriskt | Anteckningar |
|---|---|---|---|
| filter | Array[Objekt] | Ja | Accepterar en array med filter. Exakt ett createdAt-filter måste inkluderas i arrayen. Ett valfritt activityTypeIds-filter kan finnas med. Filtren tillämpas på den tillgängliga aktivitetsuppsättningen och den resulterande uppsättningen aktiviteter returneras av exportjobbet. |
| format | Sträng | Nej | Accepterar något av följande: CSV, TSV, SSV Den exporterade filen återges som ett kommaavgränsat värde, tabbavgränsade värden eller en blankstegsavgränsad värdefil om den anges. Standardvärdet är CSV om den avmarkeras. |
| columnHeaderNames | Objekt | Nej | Ett JSON-objekt som innehåller nyckelvärdepar med fält- och kolumnrubriknamn. Nyckeln måste vara namnet på ett fält som ingår i exportjobbet. Värdet är namnet på den exporterade kolumnrubriken för det fältet. |
| fält | Array[Sträng] | Nej | Valfri array med strängar som innehåller fältvärden. Fälten i listan inkluderas i den exporterade filen.Som standard returneras följande fält: `marketoGUIDleadId` `activityDate` `activityTypeId` `campaignId` `primaryAttributeValueId` `primaryAttributeValueattributes`,Den här parametern kan användas för att minska antalet fält som returneras genom att ange en delmängd i listan ovan.Exempel:&quot;fält&quot;: [&quot;leadId&quot;, &quot;activityDate&quot;, &quot;activityTypeId&quot;]Ett ytterligare fält, &quot;actionResult&quot;, kan anges för att inkludera aktivitetsåtgärden (&quot;success&quot;, &quot;Skipped&quot; eller &quot;failed&quot;). |


## Skapa ett jobb

Om du vill exportera poster måste du först definiera jobbet och den uppsättning poster som du vill hämta.  Skapa jobbet med [Skapa exportaktivitetsjobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/createExportActivitiesUsingPOST) slutpunkt.  När du exporterar aktiviteter finns det två primära filter: `createdAt`, som alltid är obligatoriskt, och `activityTypeIds`, vilket är valfritt.  Filtret createdAt används för att definiera ett datumintervall där aktiviteter skapades med hjälp av `startAt` och `endAt` parametrar, som båda är datetime-fält, och representerar det tidigaste tillåtna skapandedatumet och det senaste tillåtna skapandedatumet.  Du kan även filtrera på vissa typer av aktiviteter med `activityTypeIds` filter.  Detta är användbart när du vill ta bort resultat som inte är relevanta för ditt användningsfall.

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

Jobbet har nu statusen&quot;Skapat&quot;, men finns ännu inte i bearbetningskön.  Om du vill placera den i kön så att den kan börja bearbetas måste vi anropa [Köa exportaktivitetsjobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/enqueueExportActivitiesUsingPOST) slutpunkt som använder exportId från statussvaret för skapandet.

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

Nu rapporterar statusen att jobbet har placerats i kö.  När en arbetare blir tillgänglig för det här jobbet växlas statusen till &quot;Bearbetar&quot; och jobbet börjar samla poster från Marketo.

## Avsökningsjobbstatus

Jobbstatus kan bara hämtas för jobb som skapats av samma API-användare.

Marketo Bulk Activity Extract är en asynkron slutpunkt, så jobbstatusen måste avfrågas för att avgöra när jobbet är klart.  Rulla med [Hämta jobbstatus för exportaktivitet](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/getExportActivitiesStatusUsingGET) slutpunkt enligt följande:

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

När jobbet är klart hämtar du dina data med [Hämta exportaktivitetsfil](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/getExportActivitiesFileUsingGET) slutpunkt.

```
GET /bulk/v1/activities/export/{exportId}/file.json
```

Svaret innehåller en fil som är formaterad på det sätt som jobbet konfigurerades. Slutpunkten svarar med filens innehåll.

Om ett begärt lead-fält är tomt (innehåller inga data), `then null` placeras i motsvarande fält i exportfilen.  I exemplet nedan är fältet campaignId för den returnerade aktiviteten tomt.

```json
marketoGUID,leadId,activityDate,activityTypeId,campaignId,primaryAttributeValueId,primaryAttributeValue,attributes
783957693,5414087,2022-02-13T14:06:20Z,104,8497,1670,MembershipTest1,"{""Reason"":""Changed by Smart Campaign MembershipTestCampaignStepChoice.MembershipTestCampaignStepChoiceSetUp action Change Data Value"",""Program Member ID"":3240303,""Acquired By"":true,""Old Status"":""Not in Program"",""New Status ID"":21,""Success"":false,""New Status"":""On List"",""Old Status ID"":20}"
783958220,5414094,2022-02-13T14:08:50Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":6,""Success"":true,""New Status"":""Attended"",""Old Status ID"":1}"
783958306,5414094,2022-02-13T14:09:16Z,104,17240,3569,SuccessWebCPS,"{""Program Member ID"":3240305,""Acquired By"":false,""Old Status"":""Attended"",""New Status ID"":6,""Success"":false,""New Status"":""Attended"",""Old Status ID"":6}"
783961924,5316669,2022-02-13T14:27:21Z,104,11614,2333,Nurture Automation,"{""Program Member ID"":3240306,""Acquired By"":false,""Old Status"":""Not in Program"",""New Status ID"":27,""Success"":false,""New Status"":""Member"",""Old Status ID"":26}"
```

För att ge stöd för delvis och återinsättningsvänlig hämtning av extraherade data, har filslutpunkten valfritt stöd för HTTP-huvudet `Range` av typen `bytes`.  Om rubriken inte är inställd returneras hela innehållet.  Du kan läsa mer om hur du använder intervallhuvudet med Marketo [Massextrahering](bulk-extract.md).

## Avbryta ett jobb

Om ett jobb konfigurerades felaktigt eller blir onödigt kan det enkelt avbrytas med [Avbryt export av aktivitetsjobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities/operation/cancelExportActivitiesUsingPOST) slutpunkt:

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
