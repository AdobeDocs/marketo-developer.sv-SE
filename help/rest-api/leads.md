---
title: Leads
feature: REST API
description: Utforska Marketo Leads REST API-funktioner som Beskriv, fråga efter ID eller filter, standardfält, begränsningar och hämtning av ECID.
exl-id: 0a2f7c38-02ae-4d97-acfe-9dd108a1f733
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '3351'
ht-degree: 0%

---

# Leads

[Referens för lead-slutpunkt](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads)

Marketo Leads API innehåller en stor uppsättning funktioner för enkla CRUD-program mot lead-poster, samt möjlighet att ändra ett leadmedlemskap i statiska listor och program och initiera Smart Campaign-bearbetning för leads.

## Beskriv

En av huvudfunktionerna i Leads-API:t är Beskriv-metoden. Använd Beskriv leads för att hämta en fullständig lista över de fält som är tillgängliga för interaktion via både REST API, samt metadata för varje:

* Datatyp
* REST API-namn
* Längd (om tillämpligt)
* Skrivskyddad
* Etikett

Beskrivning är den primära källan till sanning för om fält är tillgängliga och metadata om dessa fält.

### Begäran

```
GET /rest/v1/leads/describe.json
```

### Svar

```json
{
   "requestId":"37ca#1475b74e276",
   "success":true,
   "result":[
      {
         "id":2,
         "displayName":"Company Name",
         "dataType":"string",
         "length":255,
         "rest":{
            "name":"company",
            "readOnly":false
         },
         "soap":{
            "name":"Company",
            "readOnly":false
         }
      }
}
```

Vanligtvis innehåller svaren en mycket större uppsättning fält i resultatarrayen, men vi utelämnar dem i demonstrationssyfte. Varje objekt i resultatarrayen motsvarar ett fält som är tillgängligt på lead-posten och har minst ett id, ett displayName och en datatyp. Resten och de underordnade soap-objekten kan finnas med eller inte för ett visst fält, och dess närvaro visar om fältet är giltigt för användning i antingen REST- eller SOAP-API:erna. Egenskapen `readOnly` anger om fältet är skrivskyddat via motsvarande API (REST eller SOAP). Egenskapen length anger fältets maximala längd om den finns. Egenskapen dataType anger fältets datatyp.

## Fråga

Det finns två primära metoder för hämtning av leads: metoderna Hämta lead med ID och Hämta leads med filtertyp. Hämta lead med ID tar ett enda lead-ID som sökvägsparameter och returnerar en enda lead-post.

Du kan också skicka en fältparameter som innehåller en kommaavgränsad lista med fältnamn som ska returneras. Om fältparametern inte ingår i denna begäran returneras följande standardfält: `email`, `updatedAt`, `createdAt`, `lastName`, `firstName` och `id`. När du begär en lista med fält, om ett visst fält begärs men inte returneras, anges värdet som null.

### Begäran

```
GET /rest/v1/lead/{id}.json
```

### Svar

```json
{
   "requestId": "10226#14d3049e51b",
   "success": true,
   "result": [
      {
         "id": 318581,
         "updatedAt":"2015-05-07T11:47:30-08:00"
         "lastName": "Doe",
         "email": "jdoe@marketo.com",
         "createdAt": "2015-05-01T16:47:30-08:00",
         "firstName": "John"
      }
   ]
}
```

För den här metoden finns det alltid en enda post i den första positionen i resultatarrayen.

Hämta leads efter filtertyp returnerar samma typ av poster, men kan returnera upp till 300 per sida. Det kräver frågeparametrarna `filterType` och `filterValues`.

`filterType` accepterar alla anpassade fält eller de flesta vanliga fält. Anropa `Describe2`-slutpunkten för att få en omfattande lista över sökbara fält som är tillåtna för användning i `filterType`. Vid sökning efter anpassat fält stöds endast följande datatyper: `string`, `email`, `integer`. Du kan hämta fältdetaljer (beskrivning, typ osv.) med den ovannämnda beskrivningsmetoden.

`filterValues` accepterar upp till 300 värden i kommaavgränsat format. Samtalet söker efter poster där leadets fält matchar en av de inkluderade `filterValues`. Om antalet leads som matchar leadfiltret är större än 1 000 returneras ett fel: &quot;1 003, för många resultat matchar filtret&quot;.

Om den totala längden på din GET-begäran överstiger 8 kB returneras ett HTTP-fel: &quot;414, URI för lång&quot; (per RFC 7231). Du kan komma runt problemet genom att ändra din GET till POST, lägga till parametern _method=GET och placera en frågesträng i begärandetexten.

### Begäran

```
GET /rest/v1/leads.json?filterType=id&filterValues=318581,318592
```

### Svar

```json
{
    "requestId": "12951#15699db5c97",
    "result": [
        {
            "id": 318581,
            "updatedAt": "2016-05-17T22:11:45Z",
            "lastName": "Lincoln",
            "email": "abe@usa.gov",
            "createdAt": "2015-03-17T00:18:40Z",
            "firstName": "Abraham"
        },
        {
            "id": 318592,
            "updatedAt": "2016-05-17T22:20:51Z",
            "lastName": "Washington",
            "email": "george@usa.gov",
            "createdAt": "2015-04-06T16:29:21Z",
            "firstName": "George"
        }
    ],
    "success": true
}
```

Det här anropet söker efter poster som matchar ID:n som ingår i `filterValues` och returnerar matchande poster.

Om inga poster hittas indikerar svaret att åtgärden lyckades, men resultatarrayen är tom.

### Svar

```json
{
"requestId": "177a1#1578b643357",
"result": [],
"success": true
}
```

Både Hämta lead med ID och Hämta leads med filtertyp accepterar också en fältfrågeparameter som accepterar en kommaseparerad lista med API-fält. Om detta inkluderas, kommer varje post i svaret att inkludera de listade fälten.  Om den utelämnas returneras en standarduppsättning med fält: `id`, `email`, `updatedAt`, `createdAt`, `firstName` och `lastName`.

## ADOBE ECID

När funktionen Adobe Experience Cloud Audience Sharing (Målgruppsdelning) är aktiverad utförs en cookie-synkroniseringsprocess som associerar Adobe Experience Cloud-id (ECID) med Marketo leads.  De metoder för hämtning av leads som nämns ovan kan användas för att hämta associerade ECID-värden.  Gör detta genom att ange `ecids` i fältparametern. Exempel: `&fields=email,firstName,lastName,ecids`.

## Skapa och uppdatera

Förutom att hämta lead-data kan du skapa, uppdatera och ta bort lead-poster via API:t. När du skapar och uppdaterar leads delas samma slutpunkt med åtgärdstypen som definieras i begäran, och upp till 300 poster kan skapas eller uppdateras samtidigt.

>[!NOTE]
>
> Det går inte att uppdatera företagsfält med slutpunkten [Synkronisera leads](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/syncLeadUsingPOST). Använd slutpunkten [Synkronisera företag](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies/operation/syncCompaniesUsingPOST) i stället.

>[!NOTE]
>
> När du skapar eller uppdaterar e-postvärdet för en personpost stöds endast ASCII-tecken i e-postadressfältet.

### Begäran

```
POST /rest/v1/leads.json
```

### Brödtext

```json
{
   "action":"createOnly",
   "lookupField":"email",
   "input":[
      {
         "email":"kjashaedd-1@klooblept.com",
         "firstName":"Kataldar-1",
         "postalCode":"04828"
      },
      {
         "email":"kjashaedd-2@klooblept.com",
         "firstName":"Kataldar-2",
         "postalCode":"04828"
      },
      {
         "email":"kjashaedd-3@klooblept.com",
         "firstName":"Kataldar-3",
         "postalCode":"04828"
      }
   ]
}
```

### Svar

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "id":50,
         "status":"created"
      },
      {
         "id":51,
         "status":"created"
      },
      {
         "id":52,
         "status":"created"
      }
   ]
}
```

I den här begäran visas två viktiga fält, `action` och `lookupField`.  `action` anger åtgärdstypen för begäran och kan vara `createOrUpdate`, `createOnly`, `updateOnly` eller `createDuplicate`. Om det utelämnas blir åtgärden som standard `createOrUpdate`.  Parametern `lookupField` anger nyckeln som ska användas när åtgärden är antingen `createOrUpdate` eller `updateOnly`. Om `lookupField` utelämnas är standardnyckeln `email`.

Standardpartitionen används som standard. Du kan också ange parametern `partitionName`, som bara fungerar om åtgärden är `createOnly` eller `createOrUpdate`. För att `partitionName` ska fungera som ytterligare dedupliceringsvillkor måste det ingå i källtypen i anpassade dedupliceringsregler. Om det inte finns något lead i den angivna partitionen under en uppdateringsåtgärd returneras ett fel. Om användaren som bara har API inte har behörighet att komma åt den angivna partitionen returneras ett fel.

Fältet `id` kan bara inkluderas som parameter när åtgärden `updateOnly` används, eftersom `id` är en systemhanterad unik nyckel.

Begäran måste också ha en `input`-parameter, som är en matris med lead-poster. Varje lead-post är ett JSON-objekt med valfritt antal lead-fält. Nycklarna i en post ska vara unika för den posten och alla JSON-strängar ska vara UTF-8-kodade. Fältet `externalCompanyId` kan användas för att länka lead-posten till en företagspost. Fältet `externalSalesPersonId` kan användas för att länka lead-posten till en säljpersonspost.

Obs! När du gör en begäran om att skicka fler leads samtidigt eller i snabb följd, kan dubblettposter resultera i att flera begäranden görs med samma nyckelvärde om ett efterföljande anrop med samma värde görs innan den första returneras. Detta kan undvikas antingen genom att använda `createOnly` eller `updateOnly`, eller genom att köa samtal och vänta på att ditt samtal ska returneras innan efterföljande upsert-anrop görs med samma nyckel.

## Fält

Leadobjektet innehåller standardfält och eventuellt anpassade fält. Standardfält finns i alla Marketo Engage-prenumerationer medan anpassade fält skapas av användaren efter behov. Varje fältdefinition består av en uppsättning attribut som beskriver fältet. Exempel på attribut är visningsnamn, API-namn och dataType. Dessa attribut kallas tillsammans för metadata.

Med följande slutpunkter kan du fråga, skapa och uppdatera fält på lead-objektet. Dessa API:er kräver att den ägande API-användaren har en roll med ett av eller båda behörigheterna för Läs-skriv-schemastandarden eller Anpassat fält för läs- och skrivschema.

## Frågefält

Det är enkelt att fråga om lead-fält. Du kan fråga ett enskilt lead-fält efter API-namn eller fråga uppsättningen med alla lead-fält. Både standardfält och anpassade fält kan hämtas, beroende på vilka rollbehörigheter som används. Dolda fält hämtas också.

## Efter namn

Slutpunkten Hämta lead-fält efter namn hämtar metadata för ett enskilt fält i lead-objektet. Den obligatoriska parametern fieldApiName path anger fältets API-namn. Svaret är som Beskriv lead-slutpunkten men innehåller ytterligare metadata som attributet isCustom, som anger om fältet är ett anpassat fält.

### Begäran

```
GET /rest/v1/leads/schema/fields/{fieldApiName}.json
```

### Svar

```json
{
    "requestId": "cd97#1793ee0fec4",
    "result": [
        {
            "displayName": "Email Address",
            "name": "email",
            "description": null,
            "dataType": "email",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        }
    ],
    "success": true
}
```

## Bläddra

Slutpunkten Hämta lead-fält hämtar metadata för alla fält i lead-objektet inklusive. Som standard returneras högst 300 poster. Du kan använda frågeparametern `batchSize` för att minska det här talet. Om attributet `moreResult` är true innebär det att fler resultat är tillgängliga. Fortsätt anropa den här slutpunkten tills attributet `moreResult` returnerar false, vilket betyder att det inte finns några tillgängliga resultat. `nextPageToken` som returneras från detta API ska alltid återanvändas för nästa iteration av det här anropet.

### Begäran

```
GET /rest/v1/leads/schema/fields.json
```

### Svar (trunkerat)

```json
{
    "requestId": "142c3#1793eb976d8",
    "result": [
        {
            "displayName": "Salutation",
            "name": "salutation",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "First Name",
            "name": "firstName",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Middle Name",
            "name": "middleName",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Last Name",
            "name": "lastName",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Date of Birth",
            "name": "dateOfBirth",
            "description": null,
            "dataType": "date",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Email Address",
            "name": "email",
            "description": null,
            "dataType": "email",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Phone Number",
            "name": "phone",
            "description": null,
            "dataType": "phone",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Mobile Phone Number",
            "name": "mobilePhone",
            "description": null,
            "dataType": "phone",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Fax Number",
            "name": "fax",
            "description": null,
            "dataType": "phone",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Job Title",
            "name": "title",
            "description": null,
            "dataType": "string",
            "length": 255,
            "isHidden": false,
            "isHtmlEncodingInEmail": true,
            "isSensitive": true,
            "isCustom": false
        },
        {
            "displayName": "Unsubscribed",
            "name": "unsubscribed",
            "description": null,
            "dataType": "boolean",
            "isHidden": false,
            "isHtmlEncodingInEmail": false,
            "isSensitive": true,
            "isCustom": false
        },
        ...
    ],
    "success": true,
    "moreResult": false
}
```

## Skapa fält

Slutpunkten Skapa lead-fält skapar ett eller flera anpassade fält på lead-objektet. Den här slutpunkten har funktioner som är jämförbara med de som finns i Marketo Engage användargränssnitt. Du kan skapa upp till 100 anpassade fält med den här slutpunkten.
Tänk noga igenom varje fält som du skapar i din produktionsinstans av Marketo Engage med API:t.  När ett fält har skapats kan du inte ta bort det (du kan bara dölja det). Oanvända fält sprids ofta på ett dåligt sätt, vilket gör att instansen blir rörig.

Den obligatoriska indataparametern är en array med lead-fältobjekt. Varje objekt innehåller ett eller flera attribut. Attribut som krävs är `displayName`, `name` och `dataType` som motsvarar fältets gränssnittsvisningsnamn, fältets API-namn och fälttypen.  Du kan också ange `description`, `isHidden`, `isHtmlEncodingInEmail` och `isSensitive`.

Det finns några regler associerade med namn och `displayName`-namn. Namnattributet måste vara unikt, börja med en bokstav och bara innehålla bokstäver, siffror eller understreck. `displayName` måste vara unik och får inte innehålla specialtecken.  En vanlig namngivningskonvention är att använda kamelskiftläge på `displayName` för att skapa ett namn. Ett `displayName` av &quot;Mitt anpassade fält&quot; skulle till exempel ge namnet &quot;myCustomField&quot;.

### Begäran

```
POST /rest/v1/leads/schema/fields.json
```

### Brödtext

```json
{
  "input": [
      {
        "displayName": "Acme Access Code",
        "name": "acmeAccessCode",
        "description": "Acme Direct Mail Integration",
        "dataType": "string"
      },
      {
        "displayName": "Acme Mail Date",
        "name": "acmeMailDate",
        "description": "Acme Direct Mail Integration",
        "dataType": "string"
      }
  ]
}
```

### Svar

```json
{
    "requestId": "d9f1#17943666811",
    "result": [
        {
            "name": "acmeAccessCode",
            "status": "created"
        },
        {
            "name": "acmeMailDate",
            "status": "created"
        }
    ],
    "success": true
}
```

## Uppdatera fält

Slutpunkten Uppdatera lead-fält uppdaterar ett enskilt anpassat fält på lead-objektet. För det mesta går det att utföra fältuppdateringsåtgärder som utförs med Marketo Engage-gränssnittet med API:t. I tabellen nedan sammanfattas några skillnader.

<table>
<tbody>
<tr>
<td style="width: 26.5306%;" rowspan="2"><strong>Attribut</strong></td>
<td style="width: 35%;" colspan="2"><strong>Standardfält</strong></td>
<td style="width: 38.2654%;" colspan="2"><strong>Anpassat fält</strong></td>
</tr>
<tr>
<td style="width: 17.449%;"><strong>Kan uppdateras av API?</strong></td>
<td style="width: 17.551%;"><strong>Kan uppdateras av användargränssnittet?</strong></td>
<td style="width: 19.3878%;"><strong>Kan uppdateras av API?</strong></td>
<td style="width: 18.8776%;"><strong>Kan uppdateras av användargränssnittet?</strong></td>
</tr>
<tr>
<td style="width: 26.5306%;">dataType</td>
<td style="width: 17.449%;">no</td>
<td style="width: 17.551%;">no</td>
<td style="width: 19.3878%;">no</td>
<td style="width: 18.8776%;">ja</td>
</tr>
<tr>
<td style="width: 26.5306%;">description</td>
<td style="width: 17.449%;">ja</td>
<td style="width: 17.551%;">ja</td>
<td style="width: 19.3878%;">ja</td>
<td style="width: 18.8776%;">ja</td>
</tr>
<tr>
<td style="width: 26.5306%;">displayName</td>
<td style="width: 17.449%;">no</td>
<td style="width: 17.551%;">no</td>
<td style="width: 19.3878%;">ja</td>
<td style="width: 18.8776%;">ja</td>
</tr>
<tr>
<td style="width: 26.5306%;">isCustom</td>
<td style="width: 17.449%;">no</td>
<td style="width: 17.551%;">no</td>
<td style="width: 19.3878%;">no</td>
<td style="width: 18.8776%;">no</td>
</tr>
<tr>
<td style="width: 26.5306%;">isHidden</td>
<td style="width: 17.449%;">no</td>
<td style="width: 17.551%;">ja</td>
<td style="width: 19.3878%;">ja (om den har skapats av API)</td>
<td style="width: 18.8776%;">ja</td>
</tr>
<tr>
<td style="width: 26.5306%;">isHtmlEncodingInEmail</td>
<td style="width: 17.449%;">ja</td>
<td style="width: 17.551%;">ja</td>
<td style="width: 19.3878%;">ja</td>
<td style="width: 18.8776%;">ja</td>
</tr>
<tr>
<td style="width: 26.5306%;">isSensitive</td>
<td style="width: 17.449%;">ja</td>
<td style="width: 17.551%;">ja</td>
<td style="width: 19.3878%;">ja</td>
<td style="width: 18.8776%;">ja</td>
</tr>
<tr>
<td style="width: 26.5306%;">length</td>
<td style="width: 17.449%;">no</td>
<td style="width: 17.551%;">no</td>
<td style="width: 19.3878%;">no</td>
<td style="width: 18.8776%;">no</td>
</tr>
<tr>
<td style="width: 26.5306%;">name</td>
<td style="width: 17.449%;">no</td>
<td style="width: 17.551%;">no</td>
<td style="width: 19.3878%;">no</td>
<td style="width: 18.8776%;">no</td>
</tr>
</tbody>
</table>

Sökvägsparametern `fieldApiName` som krävs anger API-namnet för det fält som ska uppdateras. Den obligatoriska indataparametern är en array som innehåller ett enda lead-fältobjekt.  Fältobjektet innehåller ett eller flera attribut.

### Begäran

```
POST /rest/v1/leads/schema/fields/{fieldApiName}.json
```

### Brödtext

```json
{
  "input": [
      {
        "displayName": "Acme Access Code",
        "description": "Acme Direct Mail Integration",
        "isHtmlEncodingInEmail": true
      }
  ]
}
```

### Svar

```json
{
    "requestId": "9f57#1794324f44c",
    "result": [
        {
            "name": "acmeAccessCode",
            "status": "updated"
        }
    ],
    "success": true
}
```

## Push Lead to Marketo

Push Lead är ett alternativ för synkning av leads till Marketo, som främst är utformat för att ge större utlösarmöjligheter än vanliga Sync Leads (liknande användning som ett Marketo-formulär). Förutom synkronisering av lead-fält tillåter den här slutpunkten även lead-associationer baserat på cookie-värden som skickas till slutpunkten. Detta gör du genom att skicka värdet `mkt_tok` som genererats genom att klicka dig igenom ett Marketo-e-postmeddelande eller genom att skicka ett programnamn i anropet. Den här slutpunkten skapar också en enda aktiveringsbar aktivitet som är kopplad till ett program och/eller en kampanj i Marketo. På så sätt kan du aktivera leadinspelningshändelser som är kopplade till en viss kampanj eller ett visst program för att starta associerade arbetsflöden inifrån Marketo.

Gränssnittet Push Lead påminner mycket om Sync Leads. Alla samma primärnycklar är giltiga, och samma API-namn används för fält (det finns ingen åtgärdsparameter eftersom detta alltid är en upsert-åtgärd). Parametrarna `programName` och indata krävs, och parametrarna `lookupField`, `source` och `reason` är valfria. Indataparametern är en array med lead-objekt. Den resulterande aktiviteten tillskrivs motsvarande namngivna program. Parametrarna `source` och `reason` är godtyckliga strängfält som kan läggas till i begäran för att bädda in dessa värden i de resulterande aktiviteterna. Dessa kan användas som begränsningar i motsvarande utlösare (Lead skickas till Marketo) och filter (Lead har skickats till Marketo).

Anmärkning om anonyma aktiviteter. Om du vill koppla tidigare anonyma aktiviteter till den nya lead som skapas ska du inte ange attributet cookies i lead-objektet och anropa Associate Lead efter Push Lead (Push Lead). Om du vill skapa ett nytt lead utan aktivitetshistorik anger du bara attributet cookies i lead-objektet.

### Begäran

```
POST /rest/v1/leads/push.json
```

### Brödtext

```json
{
    "programName": "Big Blue Thing Product Launch",
    "source": "Cool Sales Site",
    "reason": "Downloaded pricing sheet",
    "lookupField": "email",
    "input": [
        {
             "email": "Theresa.May@westminister.gov.uk",
             "country": "united kingdom",
             "firstName": "Theresa",
             "website": "www.brexit.com",
             "leadScore": 45,
             "marketoSocialFacebookProfileURL": "http://www.facebook.com/id/23434456",
             "jobTitle": "Prime Minister"
         },
         {
             "email": "Justin.Trudeau@ottowa.gov.ca",
             "country": "canada",
             "firstName": "Justin",
             "website": "www.take-off-eh.com",
             "leadScore": 92,
             "marketoSocialFacebookProfileURL": "http://www.facebook.com/id/42434",
             "jobTitle": "Sonny"
         }
     ]
}
```

### Svar

```json
{
    "requestId": "939079529805",
    "success": true,
    "warnings": [],
    "result": [
       {
           "id": 483894,
           "status": "created"
       },
       {
           "id": 1087425,
           "status": "updated"
       },
       {
           "id": 3525,
           "reasons": [
                    {
                        "code": "501",
                        "message": "Bad stuff happened"
                    }
           ]
       }
    ]
}
```

Om du vill skicka parametern `mkt_tok` tilldelar du värdet till mktToken-medlemmen i en lead-post i indataparametern enligt följande.

### Brödtext

```json
{
  "programName": "Big Blue Thing Product Launch",
  "source": "Cool Sales Site",
  "reason": "Downloaded pricing sheet",
  "lookupField": "mktToken",
  "input" : [
     {
       "mktToken" : "<tokenValue>",
       "firstName" : "Thelma"
     },
     {
       "mktToken" : "<tokenValue>",
       "firstName" : "Louise"
     }
   ]
}
```

## Skicka formulär

Skicka formulär är ett alternativ för synkronisering av leads till Marketo och är utformat för att ge funktionalitet som motsvarar inskickning av Marketo-formulär. På så sätt kan du aktivera leadinspelningshändelser som är kopplade till en viss kampanj eller ett visst program för att starta associerade arbetsflöden inifrån Marketo.

Slutpunkten Skicka formulär har stöd för följande funktioner:

* Infogar en lead-post med e-postfältet som primärnyckel
* Skapar aktiviteten Fyll i formulär som är kopplad till ett program och/eller en kampanj
* Tillåter lead-association baserat på cookie-värde
* Utför validering av formulärfält

När du skickar ett formulär följer du standarddatabasmönstret för lead. En enda objektpost skickas i den obligatoriska indatamedlemmen i JSON-brödtexten för en POST-begäran. Den obligatoriska `formId`-medlemmen innehåller Marketo-målformulär-ID:t.

Det valfria `programId` kan användas för att ange vilket program som leadet ska läggas till i och/eller ange vilket program som anpassade fält för programmedlemmar ska läggas till i. Om `programId` anges läggs leadet till i programmet och alla programmedlemsfält som finns i formuläret läggs också till. Observera att det angivna programmet måste finnas på samma arbetsyta som formuläret. Om formuläret inte innehåller anpassade fält för programmedlemmar och `programId` inte har angetts läggs lead inte till i ett program. Om formuläret finns i ett program och `programId` inte tillhandahålls, används det programmet när det finns ett eller flera anpassade fält för programmedlemmar i formuläret.

I indataposten krävs objektet `leadFormFields`. Det här objektet innehåller ett eller flera namn/värde-par som motsvarar de formulärfält som ska fyllas i.  Alla angivna fält måste definieras i det angivna formuläret. Namnet är REST API-namnet för fältet. Observera att fältet `email` är obligatoriskt.

Medlemsobjektet `visitorData` är valfritt och innehåller namn/värde-par som motsvarar sidbesöksdata som `pageURL`, `queryString`, `leadClientIpAddress` och `userAgentString`. Kan användas för att fylla i ytterligare aktivitetsfält för filtrering och utlösande syften.

Cookie-medlemssträngen är valfri och gör att du kan associera en Munchkin-cookie med en personpost i Marketo. När en ny lead skapas kopplas alla tidigare anonyma aktiviteter till denna lead, såvida inte cookie-värdet tidigare har associerats med en annan känd post. Om cookie-värdet tidigare var associerat spåras nya aktiviteter mot posten, men gamla aktiviteter migreras inte från den befintliga kända posten. Om du vill skapa ett nytt lead utan aktivitetshistorik utelämnar du bara cookie-medlemmen.

Nya leads skapas i den primära partitionen för den arbetsyta där formuläret finns.

### Begäran

```
POST /rest/v1/leads/submitForm.json
```

### Sidhuvud

```
Content-Type: application/json
```

### Brödtext

```json
{
  "formId": 1029,
  "input": [
    {
      "leadFormFields": {
        "firstName": "Marge",
        "lastName": "Simpson",
        "email": "marge.simpson@fox.com",
        "pMCFField": "PMCF value"
      },
      "visitorData": {
        "pageURL": "https://na-sjst.marketo.com/lp/063-GJP-217/UnsubscribePage.html",
        "queryString": "Unsubscribed=yes",
        "leadClientIpAddress": "192.150.22.5",
        "userAgentString": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.89 Safari/537.36"
      },
      "cookie": "id:063-GJP-217&token:_mch-marketo.com-1594662481190-60776"
    }
  ]
}
```

### Svar

```json
{
  "requestId": "10667#173bc585ca5",
  "result": [
    {
      "id": 319174,
      "status": "updated"
    }
  ],
  "success": true
}
```

Här ser vi motsvarande &quot;Fyll i formulär&quot;-aktivitetsinformation inifrån Marketo Engage-gränssnittet:

![Gränssnitt för att fylla i formulär](assets/fill_out_form_activity_details.png)

## Sammanfoga

Ibland är det nödvändigt att sammanfoga dubblettposter och Marketo underlättar detta med API:t för sammanslagna leads. När du sammanfogar leads kombineras deras aktivitetsloggar, program-, kampanj- och listmedlemskap och CRM-information, och alla deras fältvärden sammanfogas till en enda post. Sammanfogningsleads tar ett lead-ID som en sökvägsparameter och antingen en `leadId` som en frågeparameter, eller en lista med kommaseparerade ID:n i parametern `leadIds`.

### Begäran

```
POST /rest/v1/leads/{id}/merge.json?leadId=1324
```

### Svar

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true
}
```

Det lead som anges i parametern path är det vinnande leadet, så om det finns fält som står i konflikt mellan posterna som sammanfogas, kommer värdet från vinnaren att hämtas, förutom om fältet i den vinnande posten är tomt och motsvarande fält i den förlorande posten inte är det. De leads som anges i parametern `leadId` eller `leadIds` är leads som går förlorade.

Om du har en prenumeration aktiverad för SFDC-synkronisering kan du även använda parametern `mergeInCRM` i din begäran. Om värdet är true utförs även motsvarande sammanslagning i CRM. Om båda leads finns i SFDC och den ena är en CRM-chef och den andra är en CRM-kontakt, är vinnaren CRM-kontakten (oavsett vilket lead som anges som vinnare). Om ett av huvudrollerna är i SFDC och det andra endast är Marketo, är vinnaren SFDC lead (oavsett vilket lead som anges som vinnare).

## Associera webbaktivitet

Genom Lead Tracking (Munchkin) registrerar Marketo webbaktivitet för besökare på er webbplats och Marketo Landing Pages. Dessa aktiviteter, besök och klick, spelas in med en nyckel som motsvarar en cookie av typen &quot;_mkto_trk&quot; i leadets webbläsare, och Marketo använder den för att hålla reda på samma persons aktiviteter. Normalt sker en association till lead-poster när en lead klickas igenom från ett Marketo-e-postmeddelande eller fyller i ett Marketo-formulär, men ibland kan en association aktiveras av en annan typ av händelse, och du kan använda Associate Lead-slutpunkten för att göra det. Slutpunkten tar den kända lead-postens id som en sökvägsparameter och cookie-värdet &quot;_mkto_trk&quot; i cookie-frågeparametern.

### Begäran

```
POST /rest/v1/leads/{id}/associate.json?cookie=id:287-GTJ-838%26token:_mch-marketo.com-1396310362214-46169
```

### Svar

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true
}
```

Om en cookie redan är associerad med en känd lead-post, kommer denna API på en annan lead-post att leda till att ny webbaktivitet spelas in mot den posten, men ingen befintlig webbaktivitet flyttas till den nya posten.
medlemskap

Leadposter kan också hämtas baserat på medlemskap i en statisk lista eller ett program. Dessutom kan ni hämta alla statiska listor, program eller smarta kampanjer som en lead är medlem i.

Svarsstrukturen och valfria parametrar är identiska med parametrarna för Get Leads by Filter Type, men filterType och filterValues kan inte användas med denna API.
Navigera till listan för att få åtkomst till list-ID via Marketo-gränssnittet. Listan `id` finns i URL:en för den statiska listan, `https://app-**&#x200B;**.marketo.com/#ST1001A1`. I det här exemplet är 1001 `id` för listan.

### Begäran

```
GET /rest/v1/list/{listId}/leads.json?batchSize=3
```

### Svar

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "nextPageToken":
"PS5VL5WD4UOWGOUCJR6VY7JQO2KUXL7BGBYXL4XH4BYZVPYSFBAONP4V4KQKN4SSBS55U4LEMAKE6===",
    "result":[
       {
            "id":50,
            "email":"kjashaedd@klooblept.com",
            "firstName":"Kataldar",
             "postalCode":"04828"
       },
       {
           "id":2343,
           "email":"kjashaedd@klooblept.com",
           "firstName":"Kataldar",
           "postalCode":"04828"
       },
      {
           "id":88498,
           "email":"kjashaedd@klooblept.com",
           "firstName":"Kataldar",
         "postalCode":"04828"
         }
    ]
}
```

Slutpunkten Hämta listor efter lead-ID tar en sökvägsparameter för lead-posten `id` och returnerar alla statiska listposter som leadet är medlem i.

### Begäran

```
GET /rest/v1/leads/{id}/listMembership.json?batchSize=3
```

### Svar

```json
{
    "requestId": "1184b#1706f0ec23f",
    "result": [
        {
            "listId": 3379,
            "createdAt": "2016-05-17T19:32:44Z",
            "updatedAt": "2016-05-17T19:32:44Z"
        },
        {
            "listId": 2792,
            "createdAt": "2009-05-19T18:29:15Z",
            "updatedAt": "2009-05-19T18:29:15Z"
        },
        {
            "listId": 42,
            "createdAt": "2009-04-22T19:24:22Z",
            "updatedAt": "2009-04-22T19:24:22Z"
        }
    ],
    "success": true,
    "nextPageToken": "BFRV7OMVSNJWDVKVTUFS3XHT4E======",
    "moreResult": true
}
```

## Program

Programmedlemskapet kan hämtas på liknande sätt som listor. Samma valfria frågeparametrar är tillgängliga när du anropar Get Leads by Program Id-slutpunkten och skickar sökvägsparametern `programId`.

Du kan också skicka en fältparameter som innehåller en kommaavgränsad lista med fältnamn som ska returneras. Om fältparametern inte ingår i denna begäran returneras följande standardfält: `email`, `updatedAt`, `createdAt`, `lastName`, `firstName`, `membership` och `id`. När du begär en lista med fält, om ett visst fält begärs men inte returneras, anges värdet som null.

Svarsstrukturen är mycket lik, eftersom varje objekt i resultatarrayen är en lead, förutom att varje post också har ett underordnat objekt som kallas &quot;membership&quot;. Det här medlemsobjektet innehåller data om leadets relation till programmet som anges i anropet, och visar alltid dess `progressionStatus`, `acquiredBy`, `reachedSuccess` och `membershipDate`. Om det överordnade programmet också är ett engagemangsprogram kommer medlemskapet att ha medlemmarna `stream`, `nurtureCadence` och `isExhausted` för att ange sin position och aktivitet i engagemangsprogrammet.

### Begäran

```
GET /rest/v1/leads/programs/{programId}.json?batchSize=3
```

### Svar

```json
{
    "requestId": "13ad4#1727b748a17",
    "result": [
        {
            "id": 319141,
            "firstName": "Meera",
            "lastName": "Reed",
            "email": "mree@housestark.com",
            "updatedAt": "2020-04-21T16:27:14Z",
            "createdAt": "2020-04-21T16:27:14Z",
            "membership": {
                "id": 1127,
                "progressionStatus": "Visited",
                "progressionStatusType": "Visited",
                "isExhausted": false,
                "acquiredBy": true,
                "reachedSuccess": false,
                "membershipDate": "2020-04-21T16:27:16Z",
                "updatedAt": "2020-04-21T16:27:16Z"
            }
        },
        {
            "id": 319142,
            "firstName": "Jon",
            "lastName": "Umber",
            "email": "jumb@housestark.com",
            "updatedAt": "2020-04-21T16:27:14Z",
            "createdAt": "2020-04-21T16:27:14Z",
            "membership": {
                "id": 1127,
                "progressionStatus": "Visited",
                "progressionStatusType": "Visited",
                "isExhausted": false,
                "acquiredBy": true,
                "reachedSuccess": false,
                "membershipDate": "2020-04-21T16:27:16Z",
                "updatedAt": "2020-04-21T16:27:16Z"
            }
        },
        {
            "id": 319143,
            "firstName": "Lyanna",
            "lastName": "Mormont",
            "email": "lmor@housestark.com",
            "updatedAt": "2020-04-21T16:27:14Z",
            "createdAt": "2020-04-21T16:27:14Z",
            "membership": {
                "id": 1127,
                "progressionStatus": "Visited",
                "progressionStatusType": "Visited",
                "isExhausted": false,
                "acquiredBy": true,
                "reachedSuccess": false,
                "membershipDate": "2020-04-21T16:27:16Z",
                "updatedAt": "2020-04-21T16:27:16Z"
            }
        }
    ],
    "success": true,
    "nextPageToken": "SW3PTMBVFCNHSHJGZ7LQH3ZWNUOHKADJZ3MOQ2LOZZVNO3WEIUPDKPRTTHBSMW756KOCWURTOF2XS==="
}
```

Slutpunkten Hämta program efter lead-ID tar en ID-sökvägsparameter för lead-post och returnerar alla programposter som leadet är medlem i. Med de valfria parametrarna `filterType` och `filterValues` kan du filtrera på program-ID.

### Begäran

```
GET /rest/v1/leads/{id}/programMembership.json
```

### Svar

```json
{
    "requestId": "12e84#1706f13a379",
    "result": [
        {
            "id": 1044,
            "progressionStatus": "Sent",
            "isExhausted": false,
            "acquiredBy": false,
            "reachedSuccess": false,
            "membershipDate": "2016-05-27T19:50:29Z",
            "updatedAt": "2016-05-27T19:50:29Z"
        }
    ],
    "success": true,
    "moreResult": false
}
```

## Smarta kampanjer

Slutpunkten Hämta smarta kampanjer med lead-ID tar en ID-sökvägsparameter för lead-post och returnerar alla smarta kampanjposter som leadet är medlem i.

### Begäran

```
GET /rest/v1/leads/{id}/smartCampaignMembership.json?batchSize=3
```

### Svar

```json
{
    "requestId": "e7b0#1706f163632",
    "result": [
        {
            "smartCampaignId": 3746,
            "createdAt": "2018-06-01T18:00:04Z",
            "updatedAt": "2018-06-01T18:00:06Z"
        },
        {
            "smartCampaignId": 3678,
            "createdAt": "2015-04-06T18:37:30Z",
            "updatedAt": "2015-04-06T18:37:41Z"
        },
        {
            "smartCampaignId": 3680,
            "createdAt": "2015-04-06T18:37:30Z",
            "updatedAt": "2015-04-06T18:37:40Z"
        }
    ],
    "success": true,
    "nextPageToken": "TNGAH3NKDUFDHNXUVGTNBXJCQM======",
    "moreResult": true
}
```

## Ta bort

Det är enkelt att ta bort leads med slutpunkten Ta bort leads.  Ange lead-ID:n som ska tas bort med id-attributen i brödtexten.  Det högsta antalet är 300 leads per begäran.  Använd Content-Type: application/json header.

### Begäran

```
POST /rest/v1/leads/delete.json
```

### Brödtext

```json
{
   "input":[
      {
         "id": 235
      },
      {
         "id":766
      }
   ]
}
```

### Svar

```json
{
  "requestId":"3608#16664333670",
  "result":[
    {
      "id":235,
      "status":"deleted"
    },
    {
      "id":766,
      "status":"deleted"
    }
  ],
  "success":true
}
```

## Relationer

* Företag via fältet externalCompanyId i lead-posten
* SalesPeople via externalSalesPersonId-fält på lead-post
* Program genom programmedlemskap
* Listor via listmedlemskap
* Aktiviteter via fältet leadId i aktiviteten
* Segmentering genom enskilda segmentfält på lead-post
* Partitioner via leadPartitionId på lead-post

## Timeout

Leads-slutpunkter har en 30-talstimeout om inget annat anges nedan:

* Synkronisera leads: 90s
* Associera lead: 60s
* Sammanfoga leads: 180s
* Uppdatera Lead-partition: 60s
* Push Lead to Marketo: 90s
* Hämta leads efter filtertyp: 60s
* Hämta leads efter list-ID: 60s
