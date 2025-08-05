---
title: Bulkladsextrahering
feature: REST API
description: Batchextrahering av lead-data.
exl-id: 42796e89-5468-463e-9b67-cce7e798677b
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '1173'
ht-degree: 0%

---

# Bulkladsextrahering

[Referens för extrahering av slutpunkt i grupplead](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads)

Uppsättningen REST API:er för Bulk Lead Extract utgör ett programmatiskt gränssnitt för att hämta stora uppsättningar lead-/personposter från Marketo. Den kan också användas för att hämta leads inkrementellt baserat på postens skapandedatum, senaste uppdatering, statiskt listmedlemskap eller smart listmedlemskap. Det rekommenderade gränssnittet för användningsfall som kräver kontinuerligt datautbyte mellan Marketo och ett eller flera externa system för ETL, datalagerhantering och arkivering.

## Behörigheter

API:erna för extrahering av grupplead kräver att den ägande API-användaren har en roll med en eller båda behörigheterna för skrivskyddad lead eller skrivskyddad lead.

## Filter

Leads har stöd för olika filteralternativ. Vissa filter, bland annat `updatedAt`, `smartListName` och `smartListId`, kräver ytterligare infrastrukturkomponenter som ännu inte har lanserats för alla prenumerationer. Endast en filtertyp kan anges per exportjobb.

| Filtertyp | Datatyp | Anteckningar |
|---|---|---|
| createdAt | Datumintervall | Accepterar ett JSON-objekt med medlemmarna `startAt` och `endAt`. `startAt` accepterar en datetime som representerar den låga vattenstämpeln och `endAt` accepterar en datetime som representerar den övre vattenstämpeln. Intervallet måste vara högst 31 dagar. Datumtider ska vara i ISO-8601-format, utan millisekunder. Jobb med den här filtertypen returnerar alla tillgängliga poster som skapades inom datumintervallet. |
| updatedAt* | Datumintervall | Accepterar ett JSON-objekt med medlemmarna `startAt` och `endAt`. `startAt` accepterar en datetime som representerar den låga vattenstämpeln och `endAt` accepterar en datetime som representerar den övre vattenstämpeln. Intervallet måste vara högst 31 dagar. Datumtider ska vara i ISO-8601-format, utan millisekunder. Obs! Det här filtret filtrerar inte på det synliga&quot;updatedAt&quot;-fältet som bara återger uppdateringar av standardfält. Den filtrerar baserat på när den senaste fältuppdateringen gjordes för en lead-postJobs med den här filtertypen returnerar alla tillgängliga poster som senast uppdaterades inom datumintervallet. |
| staticListName | Sträng | Accepterar namnet på en statisk lista. Jobb med den här filtertypen returnerar alla tillgängliga poster som är medlemmar i den statiska listan när jobbet börjar bearbetas. Hämta statiska listnamn med slutpunkten Hämta listor. |
| staticListId | Heltal | Accepterar ID:t för en statisk lista. Jobb med den här filtertypen returnerar alla tillgängliga poster som är medlemmar i den statiska listan när jobbet börjar bearbetas. Hämta statiska list-ID:n med slutpunkten Hämta listor. |
| smartListName* | Sträng | Accepterar namnet på en smart lista. Jobb med den här filtertypen returnerar alla tillgängliga poster som är medlemmar i de smarta listorna när jobbet börjar bearbetas. Hämta smarta listnamn med slutpunkten Hämta smarta listor. |
| smartListId* | Heltal | Accepterar ID:t för en smart lista. Jobb med den här filtertypen returnerar alla tillgängliga poster som är medlemmar i de smarta listorna när jobbet börjar bearbetas. Hämta smarta list-ID:n med slutpunkten Hämta smarta listor. |

Filtertypen är inte tillgänglig för vissa prenumerationer. Om du inte är tillgänglig för din prenumeration får du ett felmeddelande när du anropar slutpunkten Skapa Lead-jobb (&quot;1035, filtertypen stöds inte för målprenumerationen&quot;). Kunder kan kontakta Marketo Support för att aktivera den här funktionen i prenumerationen.

## Alternativ

Slutpunkten Skapa exportlead-jobb innehåller flera formateringsalternativ som gör att användaren kan ta med särskilda fält i den exporterade filen, ändra namn på kolumnrubriker i dessa fält och formatet för den exporterade filen.

| Parameter | Datatyp | Obligatoriskt | Anteckningar |
|---|---|---|---|
| fält | Array[String] | Ja | Parametern fields accepterar en JSON-array med strängar. Varje sträng måste vara REST API-namnet för ett Marketo lead-fält. De listade fälten inkluderas i den exporterade filen. Kolumnrubriken för varje fält blir REST API-namnet för varje fält, såvida det inte åsidosätts av columnHeader. Obs! När funktionen [!DNL Adobe Experience Cloud Audience Sharing] är aktiverad utförs en cookie-synkroniseringsprocess som associerar [!DNL Adobe Experience Cloud] ID (ECID) med Marketo leads. Du kan ange att fältet &quot;ecids&quot; ska innehålla ECID:n i exportfilen. |
| columnHeaderNames | Objekt | Nej | Ett JSON-objekt som innehåller nyckelvärdepar med fält- och kolumnrubriknamn. Nyckeln måste vara namnet på ett fält som ingår i exportjobbet. Detta är API-namnet för fältet som kan hämtas genom att anropa Beskriv lead. Värdet är namnet på den exporterade kolumnrubriken för det fältet. |
| format | Sträng | Nej | Accepterar något av följande: CSV, TSV, SSV. Den exporterade filen återges som en fil med kommaseparerade värden, tabbseparerade värden eller blankstegsavgränsade värden, om en sådan anges. Standardvärdet är CSV om den tas bort. |

## Skapa ett jobb

Parametrarna för jobbet definieras innan exporten avbryts med slutpunkten [Skapa exporthuvudjobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/createExportLeadsUsingPOST). Vi måste definiera de `fields` som behövs för export, parametertypen för `filter`, `format` för filen och kolumnrubriknamnen, om det finns några.

```
POST /bulk/v1/leads/export/create.json
```

```json
{
   "fields": [
      "firstName",
      "lastName",
      "id",
      "email"
   ],
   "format": "CSV",
   "columnHeaderNames": {
      "firstName": "First Name",
      "lastName": "Last Name",
      "id": "Marketo Id",
      "email": "Email Address"
   },
   "filter": {
      "createdAt": {
         "startAt": "2017-01-01T00:00:00Z",
         "`endAt`": "2017-01-31T00:00:00Z"
      }
   }
}
```

Denna begäran börjar exportera en uppsättning leads som skapats mellan 1 januari 2017 och 31 januari 2017, inklusive värdena från motsvarande `firstName`-, `lastName`-, `id`- och `email`-fält.

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

Detta returnerar ett statussvar som anger att jobbet har skapats. Jobbet har definierats och skapats, men har ännu inte startats. Om du vill göra det måste slutpunkten [Enqueue Export Lead Job](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/enqueueExportLeadsUsingPOST) anropas med exportId från svaret på status när projektet skapades:

```
POST /bulk/v1/leads/export/{exportId}/enqueue.json
```

```json
{
    "requestId": "147e4#16b24d9b913",
    "result": [
        {
            "exportId": "fad2cd1b-e822-4025-be1e-9caa9cf1d4b8",
            "format": "CSV",
            "status": "Queued",
            "createdAt": "2019-06-04T23:35:43Z",
            "queuedAt": "2019-06-04T23:36:17Z"
        }
    ],
    "success": true
}
```

Detta svarar med `status` i kö, varefter det ställs in på Bearbetning när det finns en tillgänglig exportplats.

## Avsökningsjobbstatus

Status för `Note:` kan bara hämtas för jobb som har skapats av samma API-användare.

Eftersom det här är en asynkron slutpunkt måste vi avfråga status när vi har skapat jobbet för att avgöra hur det förlöper. Avsök med slutpunkten [Hämta status för Lead-jobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/getExportLeadsStatusUsingGET). Statusen uppdateras endast en gång var 60:e sekund, så en lägre avsökningsfrekvens rekommenderas inte och är i nästan alla fall fortfarande för hög. Låt oss ta en snabb titt på enkäten.

```
GET /bulk/v1/leads/export/{exportId}/status.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Processing",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "format": "CSV"
      }
   ]
}
```

Statusslutpunkten svarar som anger att jobbet fortfarande bearbetas, så filen är inte tillgänglig för hämtning än. När jobbstatusen ändras till&quot;Slutfört&quot; förbereds den för hämtning.

Statusfältet kan svara med något av följande:

- Skapad
- Köad
- Bearbetar
- Avbruten
- Slutförd
- Misslyckades

## Hämtar data

Om du vill hämta filen för en slutförd leadexport anropar du [Get Export Lead File](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/getExportLeadsFileUsingGET) -slutpunkten med din `exportId`.

```
GET /bulk/v1/leads/export/{exportId}/file.json
```

Svaret innehåller en fil som är formaterad på det sätt som jobbet konfigurerades. Slutpunkten svarar med filens innehåll.

Om ett begärt lead-fält är tomt (innehåller inga data) placeras `null` i motsvarande fält i exportfilen. I exemplet nedan är e-postfältet för den returnerade leadet tomt.

```csv
firstName,lastName,email,cookies
Russell,Wilson,null,_mch-localhost-1536605780000-12105
```

Om du vill ha stöd för delvis och återinsättningsvänlig hämtning av extraherade data, kan filslutpunkten (om så önskas) ha stöd för HTTP-rubrikintervallet för bytetypen. Om rubriken inte är inställd returneras hela innehållet. Läs mer om hur du använder intervallhuvudet med Marketo [Massextrahering](bulk-extract.md).

## Avbryta ett jobb

Om ett jobb konfigurerades felaktigt eller blir onödigt kan det enkelt avbrytas med slutpunkten [Avbryt export av lead-jobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/cancelExportLeadsUsingPOST) :

```
POST /bulk/v1/leads/export/{exportId}/cancel.json
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

Detta svarar med en status som anger att jobbet har avbrutits.
