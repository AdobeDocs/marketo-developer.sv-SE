---
title: Lead-databas
feature: REST API, Database
description: Ändra huvuddatabasen för lead.
exl-id: e62e381f-916b-4d56-bc3d-0046219b68d3
source-git-commit: 981ed9b254f277d647a844803d05a1a2549cbaed
workflow-type: tm+mt
source-wordcount: '1342'
ht-degree: 0%

---

# Lead-databas

Marketo Lead Database-API:erna är de mest använda API:erna som Marketo tillhandahåller när de möjliggör datautbyte av person- och personrelaterade data från Marketo, t.ex. aktiviteter, affärsmöjligheter och företag.

## Objekt

Leaddatabasobjekten innehåller följande:

- Leads
- Företag/konton
- Namngivna konton
- Möjligheter
- AffärsmöjlighetRoller
- SalesPeople
- Anpassade objekt
- Aktiviteter
- List- och programmedlemskap

De flesta av dessa objekt omfattar åtminstone metoderna Skapa, Läs, Uppdatera och Ta bort. Dessutom ingår en&quot;Beskriv&quot;-metod som innehåller en lista med tillgängliga fält för varje typ, och en lista med fält som används för borttagning av dubbletter (för icke-lead-objekt) och vilka fält som är sökbara för hämtning av poster. Den bästa uppsättningen finns för leads eftersom de har de största möjligheterna inom Marketo program.

## API

En fullständig lista över Lead Database API-slutpunkter, inklusive parametrar och modelleringsinformation finns i [API-slutpunktsreferens för lead-databas](https://developer.adobe.com/marketo-apis/api/mapi/).

För instanser där en intern CRM-integrering är aktiverad (antingen Microsoft Dynamics eller Salesforce.com) inaktiveras API:erna Company, Opportunity, Opportunity Role och Sales Person. Posterna hanteras via CRM när de är aktiverade och kan inte öppnas eller uppdateras via Marketo API:er.

- Maximal batchstorlek (standard): 300 poster
- Maximal batchstorlek (bulk): 10 MB fil
- Standardbatchstorlek: 300 poster
- Content-type header (standard): application/json
- Content-type header (bulk): multipart/form-data

## Beskriv

För Leads, Companies, Opportunity, Roles, SalesPeople och Custom Objects finns en beskrivning av API:t. Anrop till detta hämtar metadata för objektet och en lista över fält som är tillgängliga för uppdatering och fråga. Att beskriva är en viktig del i utformningen av en bra integrering med Marketo. Det innehåller omfattande metadata om hur objekt kan och inte kan interagera med dem samt om hur de kan skapas, uppdateras och frågas. Förutom Beskriv leads returnerar vart och ett av dessa en lista med nycklar som är tillgängliga för `deduplication` i svarsparametern `dedupeFields`. En lista med fält är tillgänglig som nycklar för frågor i svarsparametern `searchableFields`.

```
GET /rest/v1/opportunities/roles/describe.json
```

```json
{
   "requestId":"185d6#14b51985ff0",
   "success":true,
   "result":[
      {
         "name":"opportunityRole",
         "displayName":"Opportunity Role",
         "createdAt":"2015-02-03T22:36:23Z",
         "updatedAt":"2015-02-03T22:36:24Z",
         "idField":"marketoGUID",
         "dedupeFields":[
            "externalOpportunityId",
            "leadId",
            "role"
         ],
         "searchableFields":[
            [
               "externalOpportunityId",
               "leadId",
               "role"
            ],
            [
               "marketoGUID"
            ],
            [
               "leadId"
            ],
            [
               "externalOpportunityId"
            ]
         ],
         "fields":[
            {
               "name":"marketoGUID",
               "displayName":"Marketo GUID",
               "dataType":"string",
               "length":36,
               "updateable":false
            },
            {
               "name":"externalOpportunityId",
               "displayName":"External Opportunity Id",
               "dataType":"string",
               "length":50,
               "updateable":false
            },
            {
               "name":"leadId",
               "displayName":"Lead Id",
               "dataType":"integer",
               "updateable":false
            },
            {
               "name":"role",
               "displayName":"Role",
               "dataType":"string",
               "length":50,
               "updateable":false
            },
            {
               "name":"isPrimary",
               "displayName":"Is Primary",
               "dataType":"boolean",
               "updateable":true
            },
            {
               "name":"externalCreatedDate",
               "displayName":"External Created Date",
               "dataType":"datetime",
               "updateable":true
            }
         ]
      }
   ]
}
```

I det här exemplet är `dedupeFields` i själva verket en sammansatt nyckel. Det innebär att du i framtida uppdateringar och skapar, när du använder läget `dedupeFields`, måste inkludera alla tre av `externalOpportunityId`, `leadId` och `role` för varje roll. Arrayen `searchableFields` innehåller även en lista med fält som är tillgängliga för frågor om rollposter. Detta inkluderar även den sammansatta nyckeln för `externalOpportunityId`, `leadId` och `role`.

Det finns också en fältsvarsparameter som ger namnet på varje fält, `displayName` som det visas i Marketo-gränssnittet, fältets datatyp, om det kan uppdateras efter att det har skapats och fältets längd om tillämpligt.

## Fråga

Leaddatabasobjekt har alla gemensamma grundmönster för frågor mot enkla nycklar, där bara ett fält refereras.

```
GET /rest/v1/{type}.json?filterType={field to query}&filterValues={comma-separated list of possible values}
```

För alla objekt utom leads kan du välja {field to query} bland sökbara fält i motsvarande describe-anrop och skapa en kommaseparerad lista på upp till 300 värden. Det finns även följande valfria frågeparametrar:

- `batchSize` - Ett heltal av antalet resultat som ska returneras. Standard och Maximum är 300.
- `nextPageToken` - Token returnerades från ett tidigare anrop för sidindelning. Mer information finns i [Utskriftstoken](paging-tokens.md).
- `fields` - En kommaavgränsad lista med fältnamn som ska returneras för varje post. Se motsvarande beskrivning för en lista över giltiga fält. Om ett visst fält begärs, men inte returneras, anges värdet som null.
- `_method` - Används för att skicka frågor med HTTP-metoden POST. Se avsnittet _method=GET nedan för mer information.

Låt oss titta på möjligheterna:

```
GET /rest/v1/opportunities.json?filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fa&dff23271-f996-47d7-984f-f2676861b5fc,dff23271-f996-47d7-984f-f2676861b5fb
```

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fa ",
         "externalOpportunityId":"19UYA31581L000000",
         "name":"Chairs",
         "description":"Chairs",
         "amount":"1604.47",
         "source":"Inbound Sales Call/Email"
      },
      {
         "seq":1,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fc ",
         "externalOpportunityId":"29UYA31581L000000",
         "name":"Big Dog Day Care-Phase12",
         "description":"Big Dog Day Care-Phase12",
         "amount":"1604.47",
         "source":"Email"
      }
   ]
}
```

`filterType` som anges i det här anropet är&quot;idField&quot; och inte&quot;marketoGUID&quot;. Detta och&quot;dedupliceraFields&quot; är båda speciella fall, där fältet som motsvarar idField, eller dedupliceraFields kan kantutjämnas på det här sättet. MarketoGUID är fortfarande det resulterande sökfältet i anropet, men det anges inte explicit i anropet. De fält och/eller uppsättningar av fält som anges av `idField` och `dedupeFields` för en objektbeskrivning är alltid giltiga `filterTypes` för en fråga. Det här anropet söker efter poster som matchar de GUID som ingår i filterValues och returnerar alla matchande poster. Om inga poster hittas med den här metoden kommer svaret fortfarande att visa att åtgärden lyckades, men resultatarrayen kommer att vara tom eftersom sökningen lyckades, men det finns inga poster att returnera.

Om uppsättningen med poster i frågan överstiger 300 eller `batchSize` som har angetts, beroende på vilket som är minst, har svaret en medlem `moreResult` med värdet true och en `nextPageToken` som kan inkluderas i ett efterföljande anrop för att hämta mer av uppsättningen. Mer information finns i [Utskriftstoken](paging-tokens.md).

### Långa URI:er

Ibland, t.ex. vid sökning med GUID, kan din URI vara lång och överskrida 8 kB som tillåts av REST-tjänsten. I det här fallet måste du använda HTTP POST-metoden i stället för GET och lägga till en frågeparameter, `_method=GET`. Dessutom måste resten av frågeparametrarna skickas i POST-brödtexten som en &quot;application/x-www-form-urlencoded&quot;-sträng, och den associerade Content-type-rubriken skickas.

```
POST /rest/v1/opportunities.json?_method=GET
```

```
Content-Type: application/x-www-form-urlencoded
```

```
filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fa&dff23271-f996-47d7-984f-f2676861b5fc,dff23271-f996-47d7-984f-f2676861b5fb,544fb7f5-2ddf-4fca-ae32-7e6ef1415e9f,f1ba41a2-69d1-4a35-9807-0e159d66f2c9,f7521272-3331-4a89-a768-222baff2f894
```

Förutom långa URI:er krävs den här parametern även när sammansatta nycklar efterfrågas.

### Sammansatta tangenter

Mönstret för att fråga efter sammansatta nycklar skiljer sig från enkla nycklar, eftersom det kräver att en POST skickas med en JSON-brödtext. Detta är inte nödvändigt i alla fall, endast i de fall där ett `dedupeFields`-alternativ med flera fält används som `filterType`. För närvarande används sammansatta nycklar bara av säljprojektsroller och vissa anpassade objekt. Vi tittar på ett exempel på en fråga om säljprojektsroller med den sammansatta nyckeln från `dedupeFields`:

```
POST /rest/v1/opportunities/roles.json?_method=GET
```

```json
{
   "filterType":"dedupeFields",
   "fields":[
      "marketoGuid",
      "externalOpportunityId",
      "leadId",
      "role"
   ],
   "input":[
      {
        "externalOpportunityId":"Opportunity1",
        "leadId": 1,
        "role": "Captain"
      },
      {
        "externalOpportunityId":"Opportunity2",
        "leadId": 1872,
        "role": "Commander"
      },
      {
        "externalOpportunityId":"Opportunity3",
        "leadId": 273891,
        "role": "Lieutenant Commander"
      }
   ]
}
```

JSON-objektets struktur är oftast platt, och alla frågeparametrar för frågor med enkla nycklar är giltiga medlemmar, förutom `filterValues`. I stället för ett filtervärde finns det en inmatningsarray med JSON-objekt, som var och en måste ha en medlem för vart och ett av fälten i den sammansatta nyckeln. I det här fallet är de `externalOpportunityId`, `leadId` och `role`. Detta kör en fråga för `roles`, mot angivna indata och returnerar matchande resultat. Om svaret returnerar en parameter med `moreResult=true` och en `nextPageToken` måste du ta med alla ursprungliga indata och `nextPageToken` för att frågan ska köras korrekt.

## Skapa och uppdatera

Skapar och uppdaterar lead-databasposter, som alla utförs via POST med JSON-kroppar. Gränssnittet för säljprojekt, roller, anpassade objekt, företag och säljare är detsamma. Leadens gränssnitt är lite annorlunda och du kan läsa mer om det där specifikt.

Den enda obligatoriska parametern är en array med namnet `input` som innehåller upp till 300 objekt, var och en med de fält som du vill infoga/uppdatera som medlemmar. Du kan också inkludera en `action`-parameter som kan vara någon av: `createOnly`, `updateOnly` eller `createOrUpdate`. Om åtgärden utelämnas blir läget som standard `createOrUpdate`. `dedupeBy` är en annan valfri parameter som kan användas när åtgärden är inställd på createOnly eller `createOrUpdate`. ` dedupeBy` kan vara antingen `idField` eller `dedupeFields`. Om `idField` väljs används `idField` i beskrivningen för borttagning av dubbletter och måste inkluderas i varje post. Läget `idField` är inte kompatibelt med läget `createOnly`. Om `dedupeFields` väljs används `dedupeFields` i objektbeskrivningen och vart och ett måste inkluderas i varje post. Om parametern `dedupeBy` utelämnas blir läget som standard `dedupeFields`.

När du skickar en lista med fältvärden skrivs värdet `null`, eller en tom sträng, till databasen som `null`.

```
POST /rest/v1/opportunities.json
```

```json
{
   "action":"createOrUpdate",
   "dedupeBy":"dedupeFields",
   "input":[
      {
         "externalOpportunityId":"19UYA31581L000000",
         "name":"Chairs",
         "description":"Chairs",
         "amount":"1604.47",
         "source":"Inbound Sales Call/Email"
      },
      {
         "externalOpportunityId":"29UYA31581L000000",
         "name":"Big Dog Day Care-Phase12",
         "description":"Big Dog Day Care-Phase12",
         "amount":"1604.47",
         "source":"Email"
      }
   ]
}
```

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "status":"updated",
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb"
      },
      {
         "seq":1,
         "status":"created",
         "marketoGUID":"cff23271-f996-47d7-984f-f2676861b5fb"
      }
   ]
}
```

Anrop om att skapa eller uppdatera lead-databasobjekt returnerar, förutom lead-API:t, ett `seq`-fält i varje objekt i `result`-arrayen. Numret som visas motsvarar ordningen för den uppdaterade posten i begäran. Varje objekt returnerar värdet för `idField` för objekttypen och ett `status`. Statusfältet anger något av alternativen &quot;skapat&quot;, &quot;uppdaterat&quot; eller &quot;Överhoppat&quot;.  Om statusen hoppas över finns det också en motsvarande &quot;reasons&quot;-array med ett eller flera orsaksobjekt som innehåller en kod och ett meddelande som anger varför en post hoppades över. Mer information finns i [felkoder](error-codes.md).

### Ta bort

Gränssnittet för borttagningar är standard för Lead Database-objekt, förutom leads. Förutom indata finns det bara en obligatorisk parameter, `deleteBy,`, som kan ha värdet idField eller dedupeFields. Låt oss titta på hur du tar bort några anpassade objekt.

```
POST /rest/v1/customobjects/{name}/delete.json
```

```json
{
   "deleteBy":"dedupeFields",
   "input":[
      {
         "vin":"19UYA31581L000000"
      },
      {
         "vin":"29UYA31581L000000"
      },
      {
         "vin":"39UYA31581L000000"
      }
   ]
}
```

```json
{
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[
      {
         "seq":0,
         "marketoGUID":"dff23271-f996-47d7-984f-f2676861b5fb",
         "status": "deleted"
      },
      {
         "seq":1,
         "marketoGUID":"da42707c-4dc4-4fc1-9fef-f30a3017240a",
         "status": "deleted"
      },
      {
         "seq":2,
         "status": "skipped"
         "reasons":[
            {
               "code":"1013",
               "message":"Object not found"
            }
         ]
      }
   ]
}
```

`seq`, `status`, `marketoGUID` och `reasons` bör vara bekanta för dig vid det här laget.

Mer information om hur du arbetar med CRUD-åtgärder för varje enskild objekttyp finns på respektive sidor.
