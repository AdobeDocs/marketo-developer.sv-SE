---
title: Extract för programmedlem
feature: REST API
description: Batchbearbetning av extrahering av medlemsdata.
exl-id: 6e0a6bab-2807-429d-9c91-245076a34680
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '1142'
ht-degree: 0%

---

# Extract för programmedlem

[Referens för extrahering av slutpunkt för gruppprogrammedlem](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members)

Uppsättningen REST API:er för Bulk Program Member Extract är ett programmatiskt gränssnitt för att hämta stora uppsättningar programmedlemsposter från Marketo. Det här är det rekommenderade gränssnittet för användningsfall som kräver kontinuerligt datautbyte mellan Marketo och ett eller flera externa system för ETL, datalagerhantering och arkivering.

## Behörigheter

API:erna för extrahering av gruppprogrammedlemmar kräver att den ägande API-användaren har en roll med en eller båda behörigheterna Skrivskyddad lead eller Skrivskyddad lead.

## Beskriv

[Beskriv programmedlem](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Program-Members/operation/describeProgramMemberUsingGET2) är den primära källan till sanning om fält är tillgängliga och metadata om fälten. Attributet `name` innehåller REST API-namnet.

```
GET /rest/v1/programs/members/describe.json
```

```json
{
    "requestId": "f813#1791563c7cc",
    "result": [
        {
            "name": "API Program Membership",
            "description": "Map for API program membership fields",
            "createdAt": "2021-03-20T01:30:05Z",
            "updatedAt": "2021-03-20T01:30:05Z",
            "dedupeFields": [
                "leadId",
                "programId"
            ],
            "searchableFields": [
                [
                    "leadId"
                ],
                [
                    "myCustomField"
                ],
                [
                    "reachedSuccess"
                ],
                [
                    "statusName"
                ]
            ],
            "fields": [
                {
                    "name": "acquiredBy",
                    "displayName": "acquiredBy",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "attendanceLikelihood",
                    "displayName": "attendanceLikelihood",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "createdAt",
                    "displayName": "createdAt",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "isExhausted",
                    "displayName": "isExhausted",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "leadId",
                    "displayName": "leadId",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "membershipDate",
                    "displayName": "membershipDate",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "nurtureCadence",
                    "displayName": "nurtureCadence",
                    "dataType": "string",
                    "length": 4,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "program",
                    "displayName": "program",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "programId",
                    "displayName": "programId",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "reachedSuccess",
                    "displayName": "reachedSuccess",
                    "dataType": "boolean",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "reachedSuccessDate",
                    "displayName": "reachedSuccessDate",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "registrationLikelihood",
                    "displayName": "registrationLikelihood",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "statusName",
                    "displayName": "statusName",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "statusReason",
                    "displayName": "statusReason",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "trackName",
                    "displayName": "trackName",
                    "dataType": "string",
                    "length": 255,
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "updatedAt",
                    "displayName": "updatedAt",
                    "dataType": "datetime",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "waitlistPriority",
                    "displayName": "waitlistPriority",
                    "dataType": "integer",
                    "updateable": false,
                    "crmManaged": false
                },
                {
                    "name": "myCustomField",
                    "displayName": "myCustomField",
                    "dataType": "string",
                    "length": 255,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "registrationCode",
                    "displayName": "registrationCode",
                    "dataType": "string",
                    "length": 100,
                    "updateable": true,
                    "crmManaged": false
                },
                {
                    "name": "webinarUrl",
                    "displayName": "webinarUrl",
                    "dataType": "string",
                    "length": 2000,
                    "updateable": true,
                    "crmManaged": false
                }
            ]
        }
    ],
    "success": true
}
```

## Filter

Programmedlemmar har stöd för olika filteralternativ. Flera filtertyper kan anges för ett jobb. I så fall kombineras de med AND. Du måste ange antingen filtret `programId` eller `programIds`. Alla andra filter är valfria. Filtret `updatedAt` kräver ytterligare infrastrukturkomponenter som ännu inte har distribuerats till alla prenumerationer.

<table>
  <tbody>
    <tr>
      <td>Filtertyp</td>
      <td>Datatyp</td>
      <td>Anteckningar</td>
    </tr>
    <tr>
      <td>programId</td>
      <td>Heltal</td>
      <td>Accepterar ID:t för ett program. Jobb returnerar alla tillgängliga poster som är medlemmar i programmet när jobbet börjar bearbetas.Hämta program-ID med slutpunkten <a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs">Hämta program</a>.Kan inte användas med filtret Program-ID.</td>
    </tr>
    <tr>
      <td>programIds</td>
      <td>Array[heltal]</td>
      <td>Accepterar en matris med upp till 10 program-ID. Jobb returnerar alla tillgängliga poster som är medlemmar i programmen när jobbet börjar bearbetas. Ett extra fält, "programId", läggs till i exportfilen som det första fältet. Det här fältet identifierar det program som en programmedlemspost har extraherats från. Hämta program-ID med slutpunkten <a href="https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs">Hämta program</a>. Det går inte att använda med filtret program-ID.</td>
    </tr>
    <tr>
      <td>isExforsted</td>
      <td>Boolean</td>
      <td>Accepterar ett booleskt värde som används för att filtrera programmedlemsposter för <a href="https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/email-marketing/drip-nurturing/using-engagement-programs/people-who-have-exhausted-content">personer som har slut på innehåll</a>.</td>
    </tr>
    <tr>
      <td>planttureCadence</td>
      <td>Sträng</td>
      <td>Accepterar en sträng som används för att filtrera programmedlemsposter för en given vårdnadstest. Tillåtna värden är:
        <ul>
          <li>pause - cadence is paused</li>
          <li>normal - cadence är normal</li>
        </ul></td>
    </tr>
    <tr>
      <td>statusNames</td>
      <td>Array[String]</td>
      <td>Accepterar en array med statusnamn för programmedlemmar. Flera statusnamn är ORed ihop.Jobb med den här filtertypen returnerar alla tillgängliga poster vars programmedlemmsstatus matchar något av de angivna statusnamnen. Både standard- och användardefinierade statusnamn kan användas.Om filtret statusNames används med filtret "programIds" söker varje program efter medlemsposter vars status matchar något av statusnamnen. Om inget statusnamn hittas i något av programmen returneras felet "1003, Invalid Data".
        <table>
          <tbody>
            <tr>
              <td>Anmäld</td>
              <td>Angivet på begäran</td>
              <td>Studsade</td>
            </tr>
            <tr>
              <td>Klickat</td>
              <td>Kontaktade</td>
              <td>Konverterad</td>
            </tr>
            <tr>
              <td>Engagerad</td>
              <td>Ifyllt formulär</td>
              <td>Påverkad</td>
            </tr>
            <tr>
              <td>Inbjuden</td>
              <td>medlem</td>
              <td>Ingen visning</td>
            </tr>
            <tr>
              <td>Inte i programmet</td>
              <td>På listan</td>
              <td>Öppnad</td>
            </tr>
            <tr>
              <td>Registrerad</td>
              <td>Registrerar</td>
              <td>Registreringsfel</td>
            </tr>
            <tr>
              <td>Skickat</td>
              <td>Prenumererat</td>
              <td>Avprenumererad</td>
            </tr>
            <tr>
              <td>Visad</td>
              <td>Besökta</td>
              <td>Besökta monter</td>
            </tr>
            <tr>
              <td>Väntande</td>
              <td>Webbinnehåll</td>
              <td></td>
            </tr>
          </tbody>
        </table></td>
    </tr>
    <tr>
      <td>updatedAt*</td>
      <td>Datumintervall</td>
      <td>Accepterar ett JSON-objekt med medlemmarna startAt och endAt. startAt accepterar ett datetime-värde som representerar den låga vattenstämpeln, och endAt accepterar ett datetime-värde som representerar den övre vattenstämpeln. Intervallet måste vara högst 31 dagar. Datumtider ska vara i ISO-8601-format, utan millisekunder. Jobb med den här filtertypen returnerar alla tillgängliga poster som senast uppdaterades inom datumintervallet.</td>
    </tr>
  </tbody>
</table>

Filtertypen är inte tillgänglig för vissa prenumerationer. Om den inte är tillgänglig för din prenumeration visas ett felmeddelande när du anropar slutpunkten Skapa exportprogrammedlemjobb (&quot;1035, filtertypen stöds inte för målprenumerationen&quot;). Kunder kan kontakta Marketo Support för att aktivera den här funktionen i prenumerationen.

## Alternativ

Slutpunkten Skapa medlemsjobb för exportprogram innehåller flera formateringsalternativ. Dessa alternativ ger användaren möjlighet att:

- Ange vilka fält som ska inkluderas i den exporterade filen
- Byt namn på kolumnrubriker i dessa fält
- Ange format för den exporterade filen

| Parameter | Datatyp | Obligatoriskt | Anteckningar |
|---|---|---|---|
| fält | Array[String] | Ja | Parametern fields accepterar en JSON-array med strängar. De listade fälten inkluderas i den exporterade filen. Följande fälttyper kan exporteras: `LeadCustom` `LeadProgram` MemberCustom `ProgramMember`. Ange ett fält med hjälp av dess REST API-namn som kan hämtas med Beskriv lead2 och/eller Beskriv programmedlemmens slutpunkter. |
| columnHeaderNames | Objekt | Nej | Ett JSON-objekt som innehåller nyckelvärdepar med fält- och kolumnrubriknamn. Nyckeln måste vara namnet på ett fält som ingår i exportjobbet. Värdet är namnet på den exporterade kolumnrubriken för det fältet. |
| format | Sträng | Nej | Accepterar något av följande: CSV, TSV, SSV. Den exporterade filen återges som en fil med kommaseparerade värden, tabbseparerade värden eller blankstegsavgränsade värden, om en sådan anges. Standardvärdet är CSV om den tas bort. |


## Skapa ett jobb

Parametrarna för jobbet definieras innan exporten avbryts med slutpunkten [Skapa exportprogrammedlemjobb](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members/operation/createExportProgramMembersUsingPOST). Vi måste definiera `filter` som innehåller program-ID:t och `fields` som behövs för exporten. Vi kan också definiera `format` för filen och `columnHeaderNames`.

```
POST /bulk/v1/program/members/export/create.json
```

```json
{ 
   "format": "CSV",
   "fields": [ 
        "firstName",
        "lastName",
        "email",
        "membershipDate",
        "program",
        "statusName",
        "leadId",
        "reachedSuccess",
        "leadCustomField01",
        "leadCustomField02",
        "pMCustomField01",
        "pMCustomField02"
   ],
   "filter": { 
      "programId":1044
   }
}
```

```json
{
    "requestId": "4d44#16f92734f6e",
    "result": [
        {
            "exportId": "b5ca52a9-5ecb-4966-b5a9-11659a8b4c2b",
            "format": "CSV",
            "status": "Created",
            "createdAt": "2020-01-11T02:33:48Z"
        }
    ],
    "success": true
}
```

Detta returnerar ett statussvar som anger att jobbet har skapats. Jobbet har definierats och skapats, men har ännu inte startats. Det gör du genom att anropa slutpunkten [Enqueue Export Program Member Job](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members/operation/enqueueExportProgramMembersUsingPOST) med hjälp av `exportId` från statussvaret för skapandet:

```
POST /bulk/v1/program/members/export/{exportId}/enqueue.json
```

```json
{
    "requestId": "d70b#16f9273ae32",
    "result": [
        {
            "exportId": "b5ca52a9-5ecb-4966-b5a9-11659a8b4c2b",
            "format": "CSV",
            "status": "Queued",
            "createdAt": "2020-01-11T02:33:48Z",
            "queuedAt": "2020-01-11T02:34:13Z"
        }
    ],
    "success": true
}
```

Detta kommer att svara med en inledande `status` av &quot;Köad&quot;, varefter den ställs in på &quot;Bearbetning&quot; när det finns en tillgänglig exportplats.

## Avsökningsjobbstatus

Obs! Status kan bara hämtas för jobb som har skapats av samma API-användare.

Eftersom detta är en asynkron slutpunkt måste vi, när vi har skapat jobbet, undersöka dess status för att avgöra dess förlopp. Avsök med slutpunkten [Hämta jobbstatus för medlem i exportprogrammet](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads/operation/getExportLeadsStatusUsingGET). Statusen uppdateras endast en gång var 60:e sekund, så en lägre avsökningsfrekvens rekommenderas inte och är i nästan alla fall fortfarande för hög. Statusfältet kan svara med något av följande: Skapat, Köat, Bearbetning, Avbrutet, Slutfört, Misslyckat.

```
GET /bulk/v1/program/members/export/{exportId}/status.json
```

```json
{
    "requestId": "9a40#16f9274d250",
    "result": [
        {
            "exportId": "b5ca52a9-5ecb-4966-b5a9-11659a8b4c2b",
            "format": "CSV",
            "status": "Processing",
            "createdAt": "2020-01-11T02:33:48Z",
            "queuedAt": "2020-01-11T02:34:13Z",
            "startedAt": "2020-01-11T02:35:19Z"
        }
    ],
    "success": true
}
```

Statusslutpunkten svarar som anger att jobbet fortfarande bearbetas, så filen är inte tillgänglig för hämtning än. När jobbet `status` ändras till Slutfört är det tillgängligt för hämtning.

```json
{
    "requestId": "11ad1#16f9ff6da23",
    "result": [
        {
            "exportId": "1118dc83-273b-4d44-becb-4d212fece550",
            "format": "CSV",
            "status": "Completed",
            "createdAt": "2020-01-11T02:33:48Z",
            "queuedAt": "2020-01-11T02:34:13Z",
            "startedAt": "2020-01-11T02:35:19Z"
            "finishedAt": "2020-01-11T02:36:12Z",
            "numberOfRecords": 13,
            "fileSize": 1752,
            "fileChecksum": "sha256:b3c8e70e6e501cf1025e345a66b409d4fd07364c7da773cfa68a2b68ce1a7212"
        }
    ],
    "success": true
}
```

## Hämtar data

Om du vill hämta filen för en slutförd programmedlemsexport anropar du [Get Export Program Member File](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members/operation/getExportProgramMembersFileUsingGET) -slutpunkten med din `exportId`.

Svaret innehåller en fil som är formaterad på det sätt som jobbet konfigurerades. Slutpunkten svarar med filens innehåll. Om ett begärt programmedlemsfält är tomt (innehåller inga data) placeras `null` i motsvarande fält i exportfilen.

```
GET /bulk/v1/program/members/export/{exportId}/file.json
```

```
firstName,lastName,email,Member Date,Program,Status,Lead Id,Success,leadCustomField01,leadCustomField02,pMCustomField01,pMCustomField02
Meera,Reed,mree@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1789,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Jon,Umber,jumb@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1790,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Lyanna,Mormont,lmor@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1791,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Rickon,Stark,rsta@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1792,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Hodor,null,hodor@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1793,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Osha,null,osha@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1794,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Jojen,Reed,Jree@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1795,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Rickard,Karstark,rkar@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1796,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Maester,Luwin,mluw@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1797,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Rodrik,Cassel,rcas@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1798,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Jory,Cassel,jcas@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1799,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
Septa,Mordane,smor@housestark.com,2020-01-08T18:10:26Z,PMCF Program,On List,1800,false,Lead01_Value,Lead02_Value,PM01_Value,PM02_Value
```

Om du vill ha stöd för delvis och återinsättningsvänlig hämtning av extraherade data, kan filslutpunkten (om så önskas) ha stöd för HTTP-rubrikintervallet för bytetypen. Om rubriken inte är inställd returneras hela innehållet. Du kan läsa mer om hur du använder intervallhuvudet med Marketo [Massextrahering](bulk-extract.md).

## Avbryta ett jobb

Om ett jobb konfigurerades felaktigt eller blir onödigt kan det enkelt avbrytas med slutpunkten [Avbryt export av programmedlemmen](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Program-Members/operation/cancelExportProgramMembersUsingPOST) :

```
POST /bulk/v1/program/members/export/{exportId}/cancel.json
```

```json
{
    "requestId": "bb4f#16f86727f89",
    "result": [
        {
            "exportId": "f0d3520c-3a60-4568-9e71-2e619d3805a4",
            "format": "CSV",
            "status": "Cancelled",
            "createdAt": "2020-01-07T21:47:35Z"
        }
    ],
    "success": true
}
```

Detta svarar med en `status` som anger att jobbet har avbrutits.
