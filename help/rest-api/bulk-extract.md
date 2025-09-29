---
title: Massextrahering
feature: REST API
description: Lär dig hur du använder Marketo Bulk Extract REST API för att exportera leads, aktiviteter, programmedlemmar och anpassade objekt, med OAuth, jobbköer och 500 MB dagliga begränsningar.
exl-id: 6a15c8a9-fd85-4c7d-9f65-8b2e2cba22ff
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '1702'
ht-degree: 0%

---

# Massextrahering

Marketo har gränssnitt för att hämta stora mängder persondata och personrelaterade data, som kallas Bulk Extract. För närvarande finns gränssnitt för tre objekttyper:

- Leads (personer)
- Aktiviteter
- Programmedlemmar
- Anpassade objekt

Massextrahering utförs genom att ett jobb skapas, uppsättningen data som ska hämtas definieras, jobbet köas, jobbet väntar på att jobbet ska skriva en fil och sedan hämta filen via HTTP. Dessa jobb körs asynkront och kan avfrågas för att hämta exportens status.

`Note:` API-slutpunkter som inte är gruppbaserade har inte prefixet /rest som andra slutpunkter.

## Autentisering

Massextraherings-API:erna använder samma OAuth 2.0-autentiseringsmetod som andra Marketo REST-API:er. Detta kräver att en giltig åtkomsttoken skickas som HTTP-huvud `Authorization: Bearer {_AccessToken_}`.

>[!IMPORTANT]
>
>Stöd för autentisering med frågeparametern **access_token** tas bort den 30 juni 2025. Om ditt projekt använder en frågeparameter för att skicka åtkomsttoken bör den uppdateras så att rubriken **Authorization** används så snart som möjligt. Ny utveckling bör endast använda rubriken **Authorization**.

## Gränser

- Maximalt antal samtidiga exportjobb: 2
- Maximalt antal exportjobb i kö (inklusive aktuella exportjobb): 10
- Bevarandeperiod för filer: sju dagar
- Standardallokering för daglig export: 500 MB (som återställs dagligen till 12:00AM CST). Kan köpas upp.
- Maximal tidsrymd för datumintervallfilter (skapadVid eller uppdateradAt): 31 dagar

Filtren Bulkextrahera för UpdatedAt och Smart List är inte tillgängliga för vissa prenumerationstyper. Om det inte är tillgängligt returnerar ett anrop till slutpunkten Skapa exportlead-jobb felmeddelandet&quot;1035, filtertypen stöds inte för målprenumerationen&quot;. Kunder kan kontakta Marketo Support för att aktivera den här funktionen i prenumerationen.

### Kö

Massextraherings-API:erna använder en jobbkö (som delas mellan leads, aktiviteter, programmedlemmar och anpassade objekt). Extraheringsjobb måste först skapas och sedan köas genom att anropa Skapa exportlead/aktivitet/programmedlemsjobb och Köa exportlead/aktivitet/programmedlemsjobb som slutpunkter. När jobben står i kö hämtas de från kön och startas när datorresurser blir tillgängliga.

Det högsta antalet jobb i kön är 10. Om du försöker placera ett jobb i kö när kön är full returnerar slutpunkten för kön för exportjobb felet&quot;1029, för många jobb i kö&quot;. Högst två jobb kan köras samtidigt (statusen är &quot;Bearbetar&quot;).

### Filstorlek

Massextraherings-API:erna mäts baserat på storleken på disken för de data som hämtas av ett massextraheringsjobb. Den explicita storleken i byte för ett jobb kan fastställas genom att attributet `fileSize` läses från det slutförda statussvaret för ett exportjobb.

Den dagliga kvoten är högst 500 MB per dag, vilket delas mellan leads, aktiviteter, programmedlemmar och anpassade objekt. När kvoten överskrids kan du inte skapa eller köa ett annat jobb förrän den dagliga kvoten återställs vid [centraltid](https://en.wikipedia.org/wiki/Central_Time_Zone) kl. midnatt. Till dess returneras felmeddelandet&quot;1029, Export day quota  . Förutom den dagliga kvoten finns ingen maximal filstorlek.

När ett jobb står i kö eller bearbetas slutförs det (utan att ett fel eller jobb avbryts). Om ett jobb misslyckas av någon anledning måste du återskapa det. Filerna skrivs bara helt när ett jobb når det slutförda läget (delar av filer skrivs aldrig). Du kan verifiera att en fil skrivits helt genom att beräkna dess SHA-256-hash och jämföra den med kontrollsumman som returneras av jobbstatusslutpunkterna.

Du kan fastställa den totala mängden disk som används för den aktuella dagen genom att anropa Hämta lead-/aktivitets-/programmedlemsjobb för export. Dessa slutpunkter returnerar en lista över alla jobb de senaste sju dagarna. Du kan filtrera listan ned till endast de jobb som har slutförts under den aktuella dagen (med attributen `status` och `finishedAt`). Sedan summerar du filstorlekarna för dessa jobb för att få fram det totala beloppet som används. Det går inte att ta bort en fil för att frigöra diskutrymme.

## Behörigheter

Extrahera gruppvis använder samma behörighetsmodell som Marketo REST API och kräver inga ytterligare specialbehörigheter att använda, men specifika behörigheter krävs för varje uppsättning slutpunkter.

Extraheringsjobb i grupp är bara tillgängliga för den API-användare som skapade dem, inklusive sökning efter status och hämtning av filinnehåll.

Slutpunkter för gruppextrahering känner inte till Marketo arbetsytor. Extraheringsbegäranden inkluderar alltid data över alla arbetsytor, oavsett hur du definierar API Only-användaren för din anpassade tjänst.

## Skapa ett jobb

Marketo API:er för bulkextrahering använder begreppet jobb för att initiera och köra dataextrahering. Låt oss titta på hur du skapar ett enkelt exportjobb för leads.

```
POST /bulk/v1/leads/export/create.json
```

```json
{
   "fields": [
      "firstName",
      "lastName"
   ],
   "format": "CSV",
   "columnHeaderNames": {
      "firstName": "First Name",
      "lastName": "Last Name"
   },
   "filter": {
      "createdAt": {
         "startAt": "2023-01-01T00:00:00Z",
         "endAt": "2023-01-31T00:00:00Z"
      }
   }
}
```

Denna enkla begäran kommer att skapa ett jobb som returnerar värdena i fälten&quot;firstName&quot; och&quot;lastName&quot;, med kolumnrubrikerna&quot;First Name&quot; och&quot;Last Name&quot; som en CSV-fil, som innehåller varje lead som skapats mellan 1 januari 2023 och 31 januari 2023.

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "exportId": "ce45a7a1-f19d-4ce2-882c-a3c795940a7d",
         "status": "Created",
         "createdAt": "2023-01-21T11:47:30-08:00",
         "queuedAt": "2023-01-21T11:48:30-08:00",
         "format": "CSV",
      }
   ]
}
```

När vi skapar jobbet returneras ett jobb-ID i attributet `exportId`. Vi kan sedan använda detta jobb-ID för att placera jobbet i kö, avbryta det, kontrollera dess status eller hämta den färdiga filen.

### Gemensamma parametrar

Varje jobbskapandeslutpunkt delar några vanliga parametrar för konfiguration av filformat, fältnamn och filter för ett bulkextraheringsjobb. Varje deltyp av extraheringsjobb kan ha ytterligare parametrar:

| Parameter | Datatyp | Anteckningar |
|---|---|---|
| format | Sträng | Bestämmer filformatet för extraherade data med alternativ för kommaavgränsade värden, tabbavgränsade värden och semikolonavgränsade värden. Accepterar något av följande: CSV, SSV, TSV. Standardformatet är CSV. |
| columnHeaderNames | Objekt | Gör att du kan ange namn på kolumnrubriker i den returnerade filen. Varje medlemsnyckel är namnet på kolumnrubriken som ska bytas och värdet är det nya namnet på kolumnrubriken. Till exempel &quot;columnHeaderNames&quot;: { &quot;firstName&quot;: &quot;First Name&quot;, &quot;lastName&quot;: &quot;Last Name&quot; }, |
| filter | Objekt | Filter som används på extraheringsjobbet. Typer och alternativ varierar mellan olika jobbtyper. |

## Hämtar jobb

Ibland kan du behöva hämta dina senaste jobb. Detta är enkelt med Hämta exportjobb för motsvarande objekttyp. Varje Get Export Jobs-slutpunkt har stöd för ett `status`-filterfält, en  `batchSize` för att begränsa antalet returnerade jobb och `nextPageToken` för växling mellan stora resultatuppsättningar. Statusfiltret har stöd för varje giltig status för ett exportjobb: Skapat, Köat, Bearbetning, Avbrutet, Slutfört och Misslyckat. batchSize har ett maximum och standard på 300. Vi hämtar listan med leadexportjobb:

```
GET /bulk/v1/leads/export.json?status=Completed,Failed
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
         "numberOfRecords": 122323,
         "fileSize": 123424,
         "fileChecksum": "sha256:c16514c7e80fcac5ea055dacae9617fc3c29aff5365e3743071313ce0ed2a815"
      }
      ...
   ]
}
```

Slutpunkten svarar med `status`-svar för varje jobb som skapats under de senaste sju dagarna för den objekttypen i resultatarrayen. Svaret innehåller endast resultat för jobb som ägs av API-användaren som gör anropet.

## Starta ett jobb

Låt oss påbörja jobbet med vårt jobb:

```
POST /bulk/v1/leads/export/{exportId}/enqueue.json
```

Detta avbryter körningen av jobbet och returnerar ett statussvar. Eftersom exporten alltid görs asynkront måste vi undersöka jobbets status för att avgöra om det har slutförts. Statusen för ett givet jobb uppdateras inte oftare än en gång var 60:e sekund, så statusen bör aldrig avfrågas oftare än så. Tänk dock på att de flesta fall aldrig ska kräva avsökning oftare än en gång var femte minut. Data från varje lyckad export sparas i 10 dagar.

## Avsökningsjobbstatus

Det är enkelt att avgöra jobbets status.

Status kan bara avfrågas för jobb som skapats av samma API-användare som skapade dem.

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
         "status": "Completed",
         "createdAt": "2017-01-21T11:47:30-08:00",
         "queuedAt": "2017-01-21T11:48:30-08:00",
         "startedAt": "2017-01-21T11:51:30-08:00",
         "finishedAt": "2017-01-21T12:59:30-08:00",
         "format": "CSV",
         "numberOfRecords": 122323,
         "fileSize": 123424,
         "fileChecksum": "sha256:d9c73f0b6960c71623c8bafe29603b3e8e20fd0e4eeaefd119c0227506ea9be4"
      }
   ]
}
```

Den inre `status`-medlemmen anger förloppet för jobbet och kan vara något av följande värden: Skapad, Köad, Bearbetning, Avbruten, Slutförd, Misslyckad. I det här fallet har vårt jobb slutförts, så vi kan avbryta avsökningen och fortsätta att hämta filen. När medlemmen `fileSize` är klar anger den totala längden på filen i byte, och medlemmen `fileChecksum` innehåller filens SHA-256-hash. Jobbstatus är tillgänglig i 30 dagar efter att statusen Slutförd eller Misslyckad har uppnåtts.

## Hämtar data

När jobbet är klart kan du enkelt hämta filen.

```
GET /bulk/v1/leads/export/{exportId}/file.json
```

Svaret innehåller en fil som är formaterad på det sätt som jobbet konfigurerades. Slutpunkten svarar med filens innehåll. Om ett jobb inte har slutförts, eller om ett felaktigt jobb-ID har skickats, svarar filslutpunkterna med statusen 404 Hittades inte och ett vanligt felmeddelande som nyttolast, till skillnad från de flesta andra Marketo REST-slutpunkter.

Om du vill ha stöd för delvis och återanvändningsvänlig hämtning av extraherade data, kan filslutpunkten (valfritt) ha stöd för HTTP-huvudet `Range` av typen `bytes` (per [RFC 7233](https://datatracker.ietf.org/doc/html/rfc7233)). Om rubriken inte är inställd returneras hela innehållet. Om du vill hämta de första 10 000 byten i en fil skickar du följande rubrik som en del av din GET-begäran till slutpunkten, med början från byte 0:

```
Range: bytes=0-9999
```

När den partiella filen hämtas svarar slutpunkten med statuskoden 206 och returnerar rubrikerna Accept-range, Content-Length och Content-Range:

```
Accept-Ranges: bytes
Content-Length: 1000
Content-Range: bytes 0-9999/123424
```

### Delvis hämtning och återupptagning

Filer kan hämtas delvis eller återupptas senare med huvudet `Range`. Intervallet för en fil börjar med byte 0 och slutar med värdet `fileSize` minus 1. Längden på filen rapporteras också som nämnare i värdet för `Content-Range`-svarshuvudet när en Get Export File-slutpunkt anropas. Om en hämtning misslyckas delvis kan den återupptas senare. Om du till exempel försöker hämta en fil som är 1000 byte lång, men bara de första 725 byten togs emot, kan du försöka hämta igen från felpunkten genom att anropa slutpunkten igen och skicka ett nytt intervall:

```
Range: bytes 724-999
```

Detta returnerar de återstående 275 byten i filen.

#### Verifiering av filintegritet

Jobbstatusslutpunkterna returnerar en kontrollsumma i attributet `fileChecksum` när `status` är Slutförd. Kontrollsumman är en SHA-256-hash av den exporterade filen. Du kan jämföra kontrollsumman med SHA-256-hash-värdet för den hämtade filen för att verifiera att den är fullständig.

Här är ett exempelsvar som innehåller kontrollsumman:

```json
{
    "exportId": "45547609-6732-418a-bb7b-17b0160b2317",
    "format": "CSV",
    "status": "Completed",
    "createdAt": "2019-06-04T23:13:12Z",
    "queuedAt": "2019-06-04T23:14:02Z",
    "startedAt": "2019-06-04T23:15:19Z",
    "finishedAt": "2019-06-04T23:36:40Z",
    "numberOfRecords": 1776,
    "fileSize": 400785,
    "fileChecksum": "sha256:83aca1351c9398d2770330e21a9e278880fd2f1eeaf8c8238bf7676d5c21d1c6"
}
```

Här är ett exempel på hur du skapar SHA-256-hash för en hämtad fil med namnet&quot;bulk_lead_export.csv&quot; med kommandoradsverktyget sha256sum:

```
$ sha256sum bulk_lead_export.csv
83aca1351c9398d2770330e21a9e278880fd2f1eeaf8c8238bf7676d5c21d1c6 *bulk_lead_export.csv
```

## Avbryta ett jobb

Om ett jobb konfigurerades felaktigt eller blir onödigt kan det enkelt avbrytas:

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
         "format": "CSV",
      }
   ]
}
```

Detta svarar med en status som anger att jobbet har avbrutits.
