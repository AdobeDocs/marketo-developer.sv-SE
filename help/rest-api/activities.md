---
title: Aktiviteter
feature: REST API
description: Använd Marketo Engage Activity REST API för att lista aktivitetstyper, hämta lead-aktiviteter med sidtoken och hantera anpassade ändringar och datavärdesändringar.
exl-id: 1e69af23-2b0c-467a-897c-1dcf81343e73
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '2046'
ht-degree: 0%

---

# Aktiviteter

Marketo tillåter ett stort antal aktivitetstyper som hör ihop med lead-register.  Nästan varje ändring, åtgärd eller flödessteg registreras mot en leads aktivitetsloggar och kan hämtas via API:t eller utnyttjas i Smart List och Smart Campaign-filter och -utlösare.  Aktiviteter är alltid relaterade tillbaka till lead-posten via leadId, som motsvarar postens ID-fält, och har också ett unikt ID.

Det finns ett mycket stort antal möjliga aktivitetstyper, som kan variera från prenumeration till prenumeration, och som har unika definitioner för varje. Alla aktiviteter har sina egna unika `id`-, `leadId`- och `activityDate`-värden, men värdena `primaryAttributeValueId` och `primaryAttributeValue` varierar i sin mening.

Marketo tillåter även att anpassade aktivitetstyper skapas via API:t för anpassade aktiviteter. Du lägger till anpassade aktiviteter via API:t Lägg till anpassade aktiviteter.

De flesta aktiviteter rensas efter en viss tid.

## Beskriv

Om du vill hämta en lista över tillgängliga typer och definitioner för en instans kan du använda slutpunkten [Hämta aktivitetstyper](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getAllActivityTypesUsingGET).

```
GET /rest/v1/activities/types.json
```

```json
  "requestId": "6e78#148ad3b76f1",
  "success": true,
  "result": [
    {
      "id": 2,
      "name": "Fill Out Form",
      "description": "User fills out and submits form on web page",
      "primaryAttribute": {
        "name": "Webform ID",
        "dataType": "integer"
      },
      "attributes": [
        {
          "name": "Client IP Address",
          "dataType": "string"
        },
        {
          "name": "Form Fields",
          "dataType": "text"
        },
        {
          "name": "Query Parameters",
          "dataType": "string"
        },
        {
          "name": "Referrer URL",
          "dataType": "string"
        },
        {
          "name": "User Agent",
          "dataType": "string"
        },
        {
          "name": "Webpage ID",
          "dataType": "integer"
        }
      ]
    }
  ]
}
```

Verkliga reaktioner innehåller mycket fler definitioner. I det här exemplet är typen som visas ett&quot;Fyll i formulär&quot;, som har det primära attributet&quot;Webform-ID&quot;, som refererar tillbaka till Marketo-id:t för det formulär som fylldes i, och som kan användas för att relatera tillbaka till just den resursen i Marketo. Dessutom finns det definitioner för vart och ett av de möjliga attributen för en viss aktivitetspost av den här typen och deras datatyper. Observera att om fältet är tomt utelämnas det attributet från en enskild aktivitetspost.

## Fråga

Om du vill hämta aktiviteter från Marketo anropar du slutpunkten [Hämta leadaktiviteter](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadActivitiesUsingGET). Du måste först hämta en växlingstoken för datetime som du vill börja hämta aktiviteter från. Du skickar sedan sidindelningstoken i frågeparametern `nextPageToken`. Dessutom skickar du upp till tio aktivitetstyp-ID:n i frågeparametern `activityTypeIds` som en kommaavgränsad lista.

Du kan antingen inkludera en listId-frågeparameter för att begränsa sökningen till enbart de poster som ingår i en viss statisk lista, eller en leadIds-frågeparameter och söka efter aktiviteter från endast en angiven uppsättning leads. Du kan skicka upp till 30 leadIds som en kommaseparerad lista.

```
GET /rest/v1/activities.json?activityTypeIds=1&nextPageToken=WQV2VQVPPCKHC6AQYVK7JDSA3I3LCWXH3Y6IIZ7YSGQLXHCPVE5Q====
```

```json
{
  "requestId": "24fd#15188a88d7f",
  "result": [
    {
      "id": 102988,
      "marketoGUID": "102988",
      "leadId": 1,
      "activityDate": "2023-01-16T23:32:19Z",
      "activityTypeId": 1,
      "primaryAttributeValueId": 71,
      "primaryAttributeValue": "localhost/munchkintest2.html",
      "attributes": [
        {
          "name": "Client IP Address",
          "value": "10.0.19.252"
        },
        {
          "name": "Query Parameters",
          "value": ""
        },
        {
          "name": "Referrer URL",
          "value": ""
        },
        {
          "name": "User Agent",
          "value": "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.95 Safari/537.36"
        },
        {
          "name": "Webpage URL",
          "value": "/munchkintest2.html"
        }
      ]
    }
  ],
  "success": true,
  "nextPageToken": "WQV2VQVPPCKHC6AQYVK7JDSA3J62DUSJ3EXJGDPTKPEBFW3SAVUA====",
  "moreResult": false
}
```

Använd API:t Get Paging Token för att hämta `nextPageToken` för det första anropet. Använd `nextPageToken returned` från svaret för efterföljande anrop till den här slutpunkten. Den här slutpunkten returnerar alltid `the nextPageToken`.

Om attributet `moreResult` är true innebär det att fler resultat är tillgängliga. Fortsätt anropa den här slutpunkten tills attributet `moreResult` returnerar false, vilket betyder att det inte finns några tillgängliga resultat. `nextPageToken` som returneras från detta API ska alltid återanvändas för nästa iteration av det här anropet.

I vissa fall kan denna API svara med färre än 300 aktivitetsobjekt, men har också attributet `moreResult` inställt på true.  Detta indikerar att det finns fler aktiviteter som kan returneras och att slutpunkten kan efterfrågas för senare aktiviteter genom att inkludera den returnerade `nextPageToken` i ett efterföljande anrop.

Observera att i varje resultatarrayobjekt ersätts heltalsattributet `id` av strängattributet `marketoGUID` som en unik identifierare.

### Ändringar av datavärde

För datavärdesändringsaktiviteter finns en specialversion av aktivitets-API:t. Slutpunkten [Hämta lead-ändringar](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadChangesUsingGET) returnerar bara aktiviteter för datavärdesändringsposter till huvudfält. Gränssnittet är detsamma som API:t Hämta leadaktiviteter med två skillnader:

* Det finns ingen `activityTypeIds`-parameter eftersom slutpunkten bara returnerar datavärdesändring och nya lead-aktiviteter.
* Frågeparametern `fields` krävs, där du kan skicka en kommaavgränsad lista med fält för att ange vilka fält du vill hämta ändringar för.

```
GET /rest/v1/activities/leadchanges.json?nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ&fields=firstName,lastName,department
```

```json
{
  "requestId": "a9ae#148add1e53d",
  "success": true,
  "nextPageToken": "GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBRGA3TQ===",
  "moreResult": true,
  "result": [
    {
      "id": 1078,
      "marketoGUID": "1078",
      "leadId": 775,
      "activityDate": "2014-09-17T22:31:49+0000",
      "activityTypeId": 13,
      "fields": [
        {
          "id": 48,
          "name": "firstName",
          "newValue": "FirstName_6176",
          "oldValue": "FirstName_4914"
        }
      ],
      "attributes": [
        {
          "name": "Reason",
          "value": "Web service API"
        },
        {
          "name": "Source",
          "value": "Web service API"
        },
        {
          "name": "Lead ID",
          "value": 775
        }
      ]
    }
  ]
}
```

Varje aktivitet i svaret har en fältarray, med en lista över ändringar i aktiviteten, som anger `id` och `name` för det ändrade fältet samt nya och gamla värden i förhållande till ändringen.

Observera att i varje resultatarrayobjekt ersätts heltalsattributet `id` av strängattributet `marketoGUID` som en unik identifierare.

### Borttagna leads

Det finns också en särskild slutpunkt, [Hämta borttagna leads](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getDeletedLeadsUsingGET), för att hämta borttagna aktiviteter från Marketo.

```
GET /rest/v1/activities/deletedleads.json?nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ
```

```json
{
  "requestId": "a9ae#148add1e53d",
  "success": true,
  "nextPageToken": "GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBRGA3TQ===",
  "moreResult": true,
  "result": [
    {
      "id": 2,
      "marketoGUID": "2",
      "leadId": 6,
      "activityDate": "2013-09-26T06:56:35+0000",
      "activityTypeId": 37,
      "primaryAttributeValueId": 6,
      "primaryAttributeValue": "Owyliphys Iledil",
      "attributes": []
    },
    {
      "id": 3,
      "marketoGUID": "3",
      "leadId": 9,
      "activityDate": "2013-12-28T00:39:45+0000",
      "activityTypeId": 37,
      "primaryAttributeValueId": 4,
      "primaryAttributeValue": "First Last",
      "attributes": []
    }
  ]
}
```

Observera att i varje resultatarrayobjekt ersätts heltalsattributet `id` av strängattributet `marketoGUID` som en unik identifierare.

### Bläddra bland resultaten

Som standard returnerar slutpunkterna som nämns i det här avsnittet 300 aktivitetsobjekt i taget.  Om attributet `moreResult` är true är fler resultat tillgängliga. Anropa slutpunkten tills attributet `moreResult` returnerar false, vilket betyder att det inte finns fler tillgängliga resultat. `nextPageToken` som returneras från den här slutpunkten ska alltid återanvändas för nästa iteration av anropet.

I vissa fall kan den här slutpunkten svara med färre än 300 aktivitetsobjekt, men har också attributet `moreResult` inställt på true.  Detta indikerar att det finns ytterligare aktiviteter som kan returneras och att slutpunkten kan efterfrågas för senare aktiviteter genom att inkludera den returnerade `nextPageToken` i ett efterföljande anrop. Observera att `nextPageToken` måste vara URL-kodad i begäran.

## Anpassade aktivitetstyper

Anpassade aktiviteter fungerar på samma sätt som vanliga aktiviteter, förutom att schemat hanteras av tredje part, och inte av Marketo. Instanser av anpassade aktiviteter länkas till lead-poster via `leadId` på samma sätt som standardaktiviteter, men både primära och sekundära attribut definieras godtyckligt. När en anpassad aktivitetstyp godkänns skapas en motsvarande Smart List-utlösare och -filter så att leads kan bearbetas baserat på aktuella eller tidigare anpassade aktivitetsdata.

* Maximalt antal anpassade aktiviteter: 10
* Maximalt antal attribut per anpassad aktivitet: 20

Hämtning av anpassade aktivitetsdata görs på samma sätt som standardaktiviteter via API:t [Hämta lead-aktiviteter](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getLeadActivitiesUsingGET).

## Frågetyper

Förutom standardslutpunkten för Hämta aktivitetstyper returnerar slutpunkterna [Hämta anpassade aktivitetstyper](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getCustomActivityTypeUsingGET) och [Beskriv anpassad aktivitetstyp](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/describeCustomActivityTypeUsingGET) information om aktivitetstyperna som har etablerats i Marketo-instansen samt metadata om attributen för en viss typ. De normala [Get Activity Types](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getAllActivityTypesUsingGET) returnerar fortfarande metadata för anpassade aktiviteter, men anger inte om en viss typ är anpassad.

### Hämta typer

```
GET /rest/v1/activities/external/types.json
```

```json
{
  "requestId": "185d6#14b51985ff0",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attends Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved"
    }
  ]
}
```

### Beskriv typer

För typbeskrivningar måste du skicka `apiName` som en sökvägsparameter. Som standard får du den godkända versionen av aktiviteten. Du kan också skicka parametern `draft=true` för att hämta aktivitetens utkastversion.

```
GET /rest/v1/activities/external/type/{apiName}/describe.json
```

```json
{
  "requestId": "185d6#14b51985ff0",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attends Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      },
      "attributes": [
        {
          "apiName": "conferenceDate",
          "name": "Conference Date",
          "description": "Date of the conference",
          "dataType": "datetime"
        },
        {
          "apiName": "numberOfAttendees",
          "name": "Number of Attendees",
          "description": "Number of people attending conference",
          "dataType": "integer"
        }
      ]
    }
  ]
}
```

## Skapa typ

Varje anpassad aktivitetstyp kräver ett visningsnamn, API-namn, utlösarnamn, filternamn och primärattribut.

För att dina typer ska vara konsekventa med Marketo konventioner och för att undvika kollisioner är det viktigt att du följer några riktlinjer när du skapar dina typer:

**Visningsnamn:** Visningsnamnet för aktivitetstypen ska kortfattat beskriva vad en aktivitetspost representerar, till exempel&quot;Skicka e-post&quot; eller&quot;Ändra datavärde&quot;. Dessa namn bör vanligtvis vara i den oändliga formen, det vill säga &quot;Attend Event&quot;.  Visningsnamn kan innehålla alfanumeriska tecken, blanksteg och understreck. Visningsnamn måste innehålla minst en bokstav.

**API-namn:** API-namnet består av alfanumeriska tecken (högst 255). Om du är en LaunchPoint-partner bör du lägga till ett representativt namnutrymme till dina API-namn för aktivitetstypen. Detta för att undvika kollisioner med kundtilldelade typer.  Konventionen är att använda enbart gemener eller camelCase för att skilja mellan andra textsträngar.

**Beskrivning:** För aktiviteter som kan ha ett icke-uppenbart beteende bör du inkludera en beskrivning av vad aktivitetstypen representerar i relation till leadet.

**Utlösarnamn:** Varje aktivitetstyp måste ha ett unikt, läsbart utlösarnamn. Utlösarnamn ska vara i den tredje person som är närvarande, t.ex. &quot;Attends an Event&quot;. LaunchPoint-partners ska inkludera sitt företagsnamn i aktiviteten, till exempel&quot;Attends Webinar - Acme Company&quot;.

**Filternamn:**  Varje aktivitetstyp måste ha ett unikt, läsbart filternamn. Filternamn ska vara i den tredje personens förflutna tendens, t.ex. &quot;Attended an Event&quot;. LaunchPoint-partners ska inkludera sitt företagsnamn i aktiviteten, det vill säga&quot;Attended Webinar - Acme Company&quot;.

**Primärt attribut:** Det primära attributet för en anpassad aktivitet bör vara det viktigaste fältet för aktivitetstypen. För en &quot;Attended Event&quot;-aktivitet är det här till exempel namnet på händelsen. Primära attribut inkluderas som parametrar som standard i alla instanser av en utlösare eller ett filter för den aktivitetstypen, och värdet visas i aktivitetsloggen för en personpost utan att någon fördjupning behövs i aktiviteten.

När en anpassad aktivitet skapas skapas den som ett utkast och måste godkännas innan den kan användas för att lägga till aktivitetsposter av den typen. Alla uppdateringar tillämpas implicit på utkastversionen av typen. För att återspegla ändringarna i den aktiva versionen av typen måste den godkännas. När en anpassad aktivitetstyp har godkänts och används kan inga ändringar göras i de ovanstående fälten.

När du skapar en typ är description-parametern valfri, medan alla följande parametrar krävs: `apiName`, `name`, `triggerName`, `filterName`, `primaryAttribute`.

```
POST /rest/v1/activities/external/type.json
```

```json
{
  "apiName": "attendConference",
  "name": "Attend Conference",
  "description": "Attend the conference",
  "triggerName": "Attends Conference",
  "filterName": "Attended Conference",
  "primaryAttribute": {
    "apiName": "conferenceName",
    "name": "Conference Name",
    "description": "Name of the conference"
  }
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attends Conference",
      "filterName": "Attended Conference",
      "status": "draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      }
    }
  ]
}
```

## Uppdateringstyp

Uppdateringen av en typ är mycket lik, förutom att apiName är den enda obligatoriska parametern som en path-parameter.

```
POST /rest/v1/activities/external/type/{apiName}.json
```

```json
{
  "name": "Attend Conference",
  "description": "Attend the conference",
  "triggerName": "Attend Conference",
  "filterName": "Attended Conference",
  "primaryAttribute": {
    "apiName": "conferenceName",
    "name": "Conference Name",
    "description": "Name of the conference"
  }
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attend Conference",
      "filterName": "Attended Conference",
      "status": "draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      }
    }
  ]
}
```

## Godkänn typ

Typer kan hanteras med Godkänn anpassad aktivitetstyp, Ignorera anpassad aktivitetstyp utkast och Ta bort anpassad aktivitetstyp, på samma sätt som vanliga Marketo-resurser.

## Anpassade aktivitetstypattribut

Varje anpassad aktivitetstyp kan ha mellan 0 och 20 sekundära attribut. Sekundära attribut kan ha vilken giltig fälttyp som helst för ett Marketo-fält. De läggs till, uppdateras och tas bort separat från den överordnade typen, men kan redigeras medan en aktivitetstyp används och sedan godkänns. När fält redigeras på en live-typ har alla aktiviteter av den typen som skapas efter godkännandet den nya sekundära attributuppsättningen. Ändringar tillämpas inte retroaktivt på befintliga aktiviteter som delar den typen.

Var försiktig med att ta bort attribut eftersom detta påverkar deras tillgänglighet för användning i motsvarande filter.

Uppdateringar som görs i den sekundära attributlistan använder API-namnet för varje attribut som en primärnyckel. API-namnet för ett attribut får inte ändras. Det måste tas bort och läggas till igen med det önskade API-namnet.

Giltiga datatyper för attribut är: string, boolean, integer, float, link, email, currency, date, datetime, phone, text.

När du ändrar det primära attributet för en aktivitetstyp ska alla befintliga primära attribut sänkas genom att `isPrimary` anges till false först.

### Skapa attribut

När du skapar ett attribut krävs en `apiName`-sökvägsparameter. Parametrarna `name` och `dataType` krävs också.`The description and` `isPrimary` parametrar är valfria.

```
POST /rest/v1/activities/external/type/{apiName}/attributes/create.json
```

```json
{
  "attributes": [
    {
      "apiName": "conferenceDate",
      "name": "Conference Date",
      "description": "Date of the conference",
      "dataType": "datetime"
    },
    {
      "apiName": "numberOfAttendees",
      "name": "Number of Attendees",
      "description": "Number of people attending conference",
      "dataType": "integer"
    }
  ]
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attend Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved with draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      },
      "attributes": [
        {
          "apiName": "conferenceDate",
          "name": "Conference Date",
          "description": "Date of the conference",
          "dataType": "datetime"
        },
        {
          "apiName": "numberOfAttendees",
          "name": "Number of Attendees",
          "description": "Number of people attending conference",
          "dataType": "integer"
        }
      ]
    }
  ]
}
```

### Uppdatera attribut

När du uppdaterar attribut är `apiName` för attributet primärnyckel. Parametern `apiName` måste finnas för att uppdateringen ska lyckas (det vill säga du kan inte ändra parametern `apiName` med hjälp av uppdatering).

```
POST /rest/v1/activities/external/type/{apiName}/attributes/update.json
```

```json
{
  "attributes": [
    {
      "apiName": "conferenceDate",
      "name": "Conference Date",
      "description": "Date of the conference",
      "dataType": "datetime"
    },
    {
      "apiName": "numberOfAttendee",
      "name": "Number of Attendee",
      "description": "Number of people attending conference",
      "dataType": "integer"
    }
  ]
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attend Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved with draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      },
      "attributes": [
        {
          "apiName": "conferenceDate",
          "name": "Conference Date",
          "description": "Date of the conference",
          "dataType": "datetime"
        },
        {
          "apiName": "numberOfAttendee",
          "name": "Number of Attendee",
          "description": "Number of people attending conference",
          "dataType": "integer"
        }
      ]
    }
  ]
}
```

### Ta bort attribut

Om du tar bort ett attribut krävs en `apiName`-sökvägsparameter som är det anpassade aktivitets-API-namnet.  Obligatoriskt är också en attributparameter som är en array med attributobjekt.  Varje objekt måste innehålla en `apiName`-parameter som är det anpassade API-namnet för aktivitetstypen.

```
POST /rest/v1/activities/external/type/{apiName}/attributes/delete.json
```

```json
{ "attributes":[ { "apiName":"conferenceDate" }, { "apiName":"numberOfAttendees" } ] }
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "id": 100001,
      "apiName": "attendConference",
      "name": "Attend Conference",
      "description": "Attend the conference",
      "triggerName": "Attend Conference",
      "filterName": "Attended Conference",
      "createdAt": "2016-02-03T22:36:23Z",
      "updatedAt": "2016-02-03T22:36:23Z",
      "status": "approved with draft",
      "primaryAttribute": {
        "apiName": "conferenceName",
        "name": "Conference Name",
        "description": "Name of the conference",
        "dataType": "string"
      }
    }
  ]
}
```

## Lägg till anpassade aktiviteter

Anpassade aktiviteter är register över historiska aktiviteter som är kopplade till enskilda personposter i Marketo. De här aktiviteterna har ett schema som hanteras av Marketo-administratörer eller via fjärråtkomst via en API-integrering. Anpassade aktiviteter läggs till i lead-poster via slutpunkten [Lägg till anpassade aktiviteter](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/addCustomActivityUsingPOST) och relateras till varje lead-post via fältet `leadId`. Anpassade aktiviteter kan visas i användargränssnittet via leadets aktivitetslogg eller hämtas via slutpunkten Hämta leadaktiviteter genom att ange den anpassade aktivitetens typ-ID.

Anpassade aktiviteter är lämpliga för att registrera data som är relaterade till en enskild personpost och som inte behöver uppdateras eller skrivas över. Ett exempel är att spela in en person som deltar i en händelse som en &quot;Attended Event&quot;-aktivitet. För poster som rör en person som kan ändras, t.ex. studentregistrering, bör anpassade objekt användas i stället, eftersom de kan uppdateras, där anpassade aktiviteter kanske inte gör det.

Indatamedlemmen är en array med activity-objekt. Högst 300 aktivitetsposter kan skickas i taget.

Attributmedlemmarna `leadId`, `activityDate`, `activityTypeId`, `primaryAttributeValue` och är obligatoriska. Attributarrayen måste innehålla det icke-primära attributet. Detta kan anges antingen med namn (fältnamn) eller apiName (API-namn) och värde som motsvarar det värde du anger.

```
POST /rest/v1/activities/external.json
```

```json
{
  "input": [
    {
      "leadId": 1001,
      "activityDate": "2016-09-26T06:56:35+07:00",
      "activityTypeId": 1001,
      "primaryAttributeValue": "Game Giveaway",
      "attributes": [
        {
          "apiName": "uRL",
          "value": "http://www.nvidia.com/game-giveaway"
        }
      ]
    },
    {
      "leadId": 1200,
      "activityDate": "2016-09-26T06:56:35+07:00",
      "activityTypeId": 1001,
      "primaryAttributeValue": "Game Giveaway",
      "attributes": [
        {
          "apiName": "uRL",
          "value": "http://www.nvidia.com/game-giveaway"
        }
      ]
    },
    {
      "leadId": 3000,
      "activityDate": "2016-09-26T06:56:35+07:00",
      "activityTypeId": 1001,
      "primaryAttributeValue": "Contest Form",
      "attributes": [
        {
          "apiName": "uRL",
          "value": "http://www.nvidia.com/game-giveaway"
        }
      ]
    }
  ]
}
```

```json
{
  "requestId": "e42b#14272d07d78",
  "success": true,
  "result": [
    {
      "id": 50,
      "marketoGUID": "50",
      "status": "added"
    },
    {
      "id": 51,
      "marketoGUID": "51",
      "status": "added"
    },
    {
      "status": "skipped",
      "errors": [
        {
          "code": "1004",
          "message": "Lead not found"
        }
      ]
    }
  ]
}
```

## Timeout

Slutpunkter för aktiviteter har en timeout på 30-tal om inget annat anges nedan.

* Hämta sidtoken: 300s
* Lägg till anpassad aktivitet: 90s
