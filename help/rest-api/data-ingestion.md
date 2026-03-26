---
title: Inmatning av data
feature: REST API, Dynamic Content
description: Använd Marketo API för datainmatning för att få stora volymer, låg latens för personer, anpassade objekt, företag och programmedlemmar.
exl-id: 1d501916-53ac-42d8-a804-abb4ab01c7e8
source-git-commit: 6145067629ce78175af3b7464807a0fa100c7b57
workflow-type: tm+mt
source-wordcount: '1786'
ht-degree: 5%

---

# API för datainmatning

API:t för datainmatning är en tjänst med hög volym, låg latens och hög tillgänglighet som är utformad för att hantera stora mängder personrelaterade data effektivt och med minimal fördröjning.

Data hämtas genom att begäranden som körs asynkront skickas. Status för begäran kan hämtas genom att prenumerera på händelser från [Marketo-dataströmmen för observabilitet](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-observability-data-stream-setup).

Gränssnitt erbjuds för fyra objekttyper: Personer, Egna objekt, Företag och Program. Poståtgärden är bara&quot;infoga eller uppdatera&quot;, förutom för programmedlemmar som också stöder borttagning.

>[!NOTE]
>
>Åtkomst till API:t för datainmatning kräver tillstånd till [Marketo Engage Performance Tier](https://nation.marketo.com/t5/product-documents/marketo-engage-performance-tiers/ta-p/328835)-paketet.

## Autentisering

API:t för datainmatning använder samma OAuth 2.0-autentiseringsmetod som Marketo REST API för att generera en åtkomsttoken, men åtkomsttoken måste skickas via HTTP-huvudet `X-Mkto-User-Token`. Du kan inte skicka åtkomsttoken via en frågeparameter.

Exempel på åtkomsttoken via rubrik:

`X-Mkto-User-Token: 11606815-aa7a-405a-80a1-f9683efa528b:ab`

## Behörigheter

Datainmatning använder samma behörighetsmodell som Marketo REST API och kräver inga ytterligare specialbehörigheter att använda, men specifika behörigheter krävs för varje slutpunkt.

| Slutpunkt | Behörighet |
| --- | --- |
| Personer | Läs/skriv lead |
| Anpassade objekt | Anpassat objekt för läsning/skrivning |
| Företag | Läs/skriv företag |
| Programmedlemmar | Läs/skriv lead |

## Objekttyper som stöds

| Objekttyp | Åtgärder som stöds |
| --- | --- |
| Personer | Uppgradera (infoga eller uppdatera) |
| Anpassade objekt | Uppgradera (infoga eller uppdatera) |
| Företag | Synkronisera (`createOnly`, `updateOnly`, `createOrUpdate`) |
| Programmedlemmar | Synkronisera (status upsert), Ta bort (ta bort från program) |

## Sidhuvuden

Datainmatning använder följande anpassade HTTP-rubriker.

### Begäran

| Nyckel | Värde | Obligatoriskt | Beskrivning |
| --- | --- | --- | --- |
| `X-Correlation-Id` | Arbitrary string (maximum length 255 characters). | Nej | Kan användas för att spåra begäranden via systemet. Se Marketo observabilitetsdataström |
| `X-Request-Source` | Arbitrary string (max 50 characters). | Nej | Kan användas för att spåra källan för begäranden via systemet. Se Marketo observabilitetsdataström |

### Svar

| Nyckel | Värde | Obligatoriskt |
| --- | --- | --- |
| `X-Request-Id` | Unikt begärande-ID. | Ja |

## Begäranden

Använd HTTP POST-metoden för att skicka data till servern.

Datarepresentationen är inkluderad i begärandetexten som application/json.

Domännamnet är: `mkto-ingestion-api.adobe.io`

Sökvägen börjar med `/subscriptions/MunchkinId` där MunchkinId är specifik för din Marketo-instans. Du hittar ditt Munchkin-ID i Marketo Engage-gränssnittet under **Admin** > **Mitt konto** > **Supportinformation**.  Resten av sökvägen används för att ange den givna resursen.

Exempel-URL för personer:

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/persons`

Exempel-URL för anpassade objekt:

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/customobjects/purchases`

Exempel-URL för företag:

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/companies`

Exempel-URL för programmedlemmar:

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/programmembers`

### Svar

Alla svar returnerar ett unikt begärande-ID via huvudet `X-Request-Id`.

Exempel på begärande-ID via rubrik:

`X-Request-Id: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### Lyckades

När ett anrop lyckas returneras status 202.  Ingen svarstext returneras.

Exempel på lyckat svar:

```
HTTP/1.1 202 Accepted
X-Request-Id: e3d92152-0fb1-444a-8f8f-29d5a2338598
Content-Length: 0
Date: Wed, 18 Oct 2023 18:56:49 GMT
```

### Fel

När ett anrop genererar ett fel returneras en status som inte är 202 tillsammans med ett svarstext med ytterligare felinformation.  Svarstexten är application/json och innehåller ett enda objekt med medlemserror_code och message.

Nedan visas återanvända felkoder från Adobe Developer Gateway.

| HTTP-statuskod | error_code | message |
| --- | --- | --- |
| 401 | 401013 | Oauth-token är ogiltig |
| 403 | 403010 | Oauth-token saknas |
| 404 | 404040 | Resursen hittades inte |
| 429 | 429001 | Tjänstanvändningsgränsen har nåtts |

Nedan visas felkoder som är unika för API:t för datainmatning och som består av tre segment.  De första tre siffrorna är statusen (returneras av Adobe Developer Gateway), följt av noll&quot;0&quot;, följt av tre siffror.

| HTTP-statuskod | error_code | message |
| --- | --- | --- |
| 400 | 4000801 | Felaktig begäran |
| 400 | 4000802 | Ogiltiga data |
| 403 | 4030801 | Obehörig |
| 429 | 4290801 | Kvot per dag har nåtts |
| 500 | 5000801 | Internt serverfel |

## Försök igen

När ett tillfälligt fel upptäcks försöker tjänsten utföra åtgärden igen tre gånger.  Det första återförsöket görs efter en 5 minuter lång vänteperiod, det andra efter 30 minuter till och slutligen det tredje efter 30 minuter till.  Försök görs på nytt av olika anledningar, i första hand när en beroende tjänst inte är tillgänglig eller inte är tillgänglig för tillfället.

## Slutpunkter

Slutpunkter för inmatning är tillgängliga för personer, anpassade objekt, företag och programmedlemmar.

### Personer

Slutpunkt som används för att infoga personposter.

| Metod | Bana |
| --- | --- |
| POST | /subscriptions/{munchkinId}/personer |

#### Sidhuvuden

| Nyckel | Värde |
| --- | --- |
| `Content-Type` | application/json |
| `X-Mkto-User-Token` | {accessToken} |

#### Begärandetext

| Nyckel | Datatyp | Obligatoriskt | Värde | Standardvärde |
| --- | --- | --- | --- | --- |
| `priority` | Sträng | Nej | Förfrågans prioritet: normal eller hög | normal |
| `partitionName` | Sträng | Nej | Namn på personpartition | Standard |
| `dedupeFields` | Objekt | Nej | Attribut som inte ska dupliceras. Ett eller två attributnamn tillåts. <br/> Två attribut används i en AND-åtgärd. Om till exempel både `email` och `firstName` anges används båda för att leta upp en person med AND-åtgärden. <br/>Attribut som stöds är: `id`, `email`, `sfdcAccountId`, `sfdcContactId`, `sfdcLeadId` `sfdcLeadOwnerId`, anpassade attribut (&quot;string&quot; och&quot;integer&quot;), `email` |  |
| `persons` | Objektmatris | Ja | Lista över attributnamnvärdespar för personen | - |

Behörigheter som krävs är `Read-Write Lead`.

### Exempel på personer

#### Begäran

`POST /subscriptions/{munchkinId}/persons`

#### Sidhuvuden

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### Brödtext

```json
{
   "priority": "high",
   "partitionName": "EMEA",
   "dedupeFields": {
      "field1": "email",
      "field2": "firstName"
   },
   "persons":[
      {
         "email": "brooklyn.parker@karnv.com",
         "firstName": "Brooklyn",
         "lastName": "Parker",
         "company": "Karnv"
      },
      {
         "email": "johnny.neal@yvu30.com",
         "firstName": "Johnny",
         "lastName": "Neal",
         "company": "Acme Inc"
      }
   ]
}
```

#### Svar

`HTTP/1.1 202`
`X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### Anpassade objekt

Slutpunkt som används för att infoga anpassade objektposter.

| Metod | Bana |
| --- | --- |
| POST | `/subscriptions/{munchkinId}/customobjects/{customObjectAPIName}` |

#### Sidhuvuden

| Nyckel | Värde |
| --- | --- |
| `Content-Type` | application/json |
| `X-Mkto-User-Token` | {accessToken} |

#### Begärandetext

| Nyckel | Datatyp | Obligatoriskt | Värde | Standardvärde |
| --- | --- | --- | --- | --- |
| `priority` | Sträng | Nej | Företräde: normal, hög | normal |
| `dedupeBy` | Sträng | Nej | Attribut som ska dedupliceras på: dedupeFields, marketoGUID | dedupeFields |
| `customObjects` | Objektmatris | Ja | Lista över attributnamnvärdespar för objektet. | - |

Nödvändiga behörigheter är `Read-Write Custom Object`.

Om ett länkfält till en person anges i begäran och den personen inte finns, görs flera försök. Om den personen läggs till under återförsöksfönstret (65 minuter) slutförs uppdateringen. Om t.ex. länkfältet är e-post för person och personen inte finns, görs ett nytt försök.

### Exempel på anpassade objekt

#### Begäran

`POST /subscriptions/{munchkinId}/customobjects/{customObjectAPIName}`

#### Sidhuvuden

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### Brödtext

```json
{
   "dedupeBy": "dedupeFields",
   "priority": "high",
   "customObjects": [
      {
         "email": "brooklyn.parker@karnv.com",
         "vin": "20UYA31581L000000",
         "make": "BMW",
         "model": "3-Series 330i",
         "year": 2003
      },
      {
         "email": "johnny.neal@yvu30.com",
         "vin": "19UYA31581L000000",
         "make": "BMW",
         "model": "3-Series 325i",
         "year": 1989
      }
   ]
}
```

#### Svar

`HTTP/1.1 202`
`X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### Företag

Slutpunkt som används för att synkronisera företagsposter. Stöder skapande, uppdatering och upsert-åtgärder med borttagning av dubbletter via externt företags-ID eller Marketo internt ID.

| Metod | Bana |
| --- | --- |
| POST | `/subscriptions/{munchkinId}/companies` |

#### Sidhuvuden

| Nyckel | Värde | Obligatoriskt |
| --- | --- | --- |
| `Content-Type` | application/json | Ja |
| `X-Mkto-User-Token` | {accessToken} | Ja |
| `X-Correlation-Id` | Arbitrary string (max 255 characters) | Nej |
| `X-Request-Source` | Arbitrary string (max 50 characters) | Nej |

#### Begärandetext

| Nyckel | Datatyp | Obligatoriskt | Värde | Standardvärde |
| --- | --- | --- | --- | --- |
| `action` | Sträng | Nej | Synkroniseringsåtgärd: `createOnly`, `updateOnly` eller `createOrUpdate` | `createOrUpdate` |
| `dedupeBy` | Sträng | Nej | Fält som ska dedupliceras på: `dedupeFields` eller `idField` (skiftlägesokänslig). Endast `dedupeFields` tillåts för `createOnly` och `createOrUpdate`. För `updateOnly` tillåts båda. | `dedupeFields` |
| `input` | Objektmatris | Ja | Lista över namnvärdespar för företagsattribut. Accepterar JSON-nyckeln `input` eller `companies`. | - |

Varje företagsobjekt i arrayen `input` har stöd för följande fält:

| Nyckel | Datatyp | Obligatoriskt | Beskrivning |
| --- | --- | --- | --- |
| `externalCompanyId` | Sträng | Villkorlig | Identifierare för externt företag. Krävs när `dedupeBy` är `dedupeFields`. Inte tillåtet när `dedupeBy` är `idField`. |
| `id` | Lång | Villkorlig | Marketo internt företags-ID. Krävs när `dedupeBy` är `idField` och `action` är `updateOnly`. Inte tillåtet när `dedupeBy` är `dedupeFields`. |
| `company` | Sträng | Nej | Företag |
| (alla fält) | Alla | Nej | Ytterligare standardfält eller anpassade företagsfält som definierats av [Beskriv företag](companies.md). Fältnamn är inte skiftlägeskänsliga. |

Nödvändiga behörigheter är `Read-Write Company`.

### Exempel på företag

#### Begäran

`POST /subscriptions/{munchkinId}/companies`

#### Sidhuvuden

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### Brödtext

```json
{
   "action": "createOrUpdate",
   "dedupeBy": "dedupeFields",
   "input": [
      {
         "externalCompanyId": "ext-company-001",
         "company": "Acme Corporation",
         "industry": "Technology",
         "numberOfEmployees": 5000,
         "annualRevenue": 100000000
      },
      {
         "externalCompanyId": "ext-company-002",
         "company": "Globex Industries",
         "industry": "Manufacturing",
         "numberOfEmployees": 1200
      }
   ]
}
```

#### Svar

`HTTP/1.1 202`
`X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### Exempel på företag som uppdaterar efter ID

```json
{
   "action": "updateOnly",
   "dedupeBy": "idField",
   "input": [
      {
         "id": 12345,
         "company": "Acme Corporation (Renamed)",
         "numberOfEmployees": 5500
      }
   ]
}
```

### Valideringsregler för företag

| Regel | Detalj |
| --- | --- |
| åtgärd | Måste vara något av: `createOnly`, `updateOnly`, `createOrUpdate`. Skiftlägeskänslig. |
| dedupeBy | Måste vara `dedupeFields` eller `idField` (skiftlägesokänslig). Standardvärdet är `dedupeFields`. |
| dedupeBy + action | `createOnly` och `createOrUpdate` tillåter bara `dedupeFields`. `updateOnly` tillåter både `dedupeFields` och `idField`. |
| När `dedupeBy=dedupeFields` | Varje företag måste ha `externalCompanyId`. Fältet `id` får inte finnas. |
| När `dedupeBy=idField` | Varje företag måste ha `id`. Fältet `externalCompanyId` får inte finnas. |
| `input` / `companies` | Får inte vara null eller tom. |
| Max objekt per begäran | 1,000 |

### Programmedlemmar (synkronisera)

Slutpunkt som används för att synkronisera programmedlemmens status, lägga till leads till program eller uppdatera deras programstatus.

| Metod | Bana |
| --- | --- |
| POST | `/subscriptions/{munchkinId}/programmembers` |

#### Sidhuvuden

| Nyckel | Värde | Obligatoriskt |
| --- | --- | --- |
| Content-Type | application/json | Ja |
| X-MKTO-User-Token | {accessToken} | Ja |
| X-Correlation-ID | Arbitrary string (max 255 characters) | Nej |
| X-Request-Source | Arbitrary string (max 50 characters) | Nej |

#### Begärandetext

| Nyckel | Datatyp | Obligatoriskt | Värde | Standardvärde |
| --- | --- | --- | --- | --- |
| program | Objektmatris | Ja | Lista över programoperationer. Var och en anger ett program, en målstatus och de leads som ska synkroniseras. | - |

Varje objekt i arrayen `programs` innehåller:

| Nyckel | Datatyp | Obligatoriskt | Beskrivning |
| --- | --- | --- | --- |
| programId | Lång | Ja | Marketo program-ID. Måste vara ett positivt heltal. |
| status | Sträng | Ja | Status för programmedlem som ska anges, till exempel `"Member"` eller `"Influenced"`. Accepterar JSON-nyckeln `statusName` eller `status`. Värdet får inte vara `"Not in Program"`. Använd slutpunkten för borttagning i stället. |
| medlemmar | Objektmatris | Ja | Lista med lead-referenser som ska läggas till eller uppdateras i programmet. Accepterar JSON-nyckeln `input` eller `members`. |

Varje objekt i arrayen `members` innehåller:

| Nyckel | Datatyp | Obligatoriskt | Beskrivning |
| --- | --- | --- | --- |
| leadId | Lång | Ja | Marketo lead-ID. |
| (alla fält) | Alla | Nej | Ytterligare anpassade programmedlemsfält. Fältnamn är inte skiftlägeskänsliga. |

Nödvändiga behörigheter är `Read-Write Lead`.

### Exempel på synkronisering av programmedlemmar

#### Begäran

`POST /subscriptions/{munchkinId}/programmembers`

#### Sidhuvuden

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### Brödtext

```json
{
   "programs": [
      {
         "programId": 1001,
         "status": "Member",
         "members": [
            {
               "leadId": 10001
            },
            {
               "leadId": 10002
            }
         ]
      },
      {
         "programId": 1002,
         "status": "Influenced",
         "members": [
            {
               "leadId": 10003
            }
         ]
      }
   ]
}
```

#### Svar

`HTTP/1.1 202`
`X-Request-ID: e3d92152-0fb1-444a-8f8f-29d5a2338598`

### Synkroniseringsverifieringsregler för programmedlemmar

| Regel | Detalj |
| --- | --- |
| program | Får inte vara null eller tom. |
| programId | Obligatoriskt. Måste vara ett positivt heltal. |
| status | Obligatoriskt. Får inte vara tom. Får inte vara `"Not in Program"` (skiftlägesokänslig). Använd slutpunkten för borttagning i stället. |
| medlemmar | Får inte vara null eller tom. |
| leadId | Krävs för varje medlem i inmatningsarrayen. |
| Max antal leads per begäran | 1 000 medlemmar totalt i alla program. |

### Programmedlemmar (ta bort)

Slutpunkt som används för att ta bort leads från program. Detta ställer in leadets medlemskapsstatus till `"Not in Program"` och tar bort medlemmen från det programmet.

>[!NOTE]
>
>Den här slutpunkten använder POST i stället för DELETE eftersom begäran kräver en JSON-brödtext med strukturerade data.

| Metod | Bana |
| --- | --- |
| POST | `/subscriptions/{munchkinId}/programmembers/delete` |

#### Sidhuvuden

| Nyckel | Värde | Obligatoriskt |
| --- | --- | --- |
| Content-Type | application/json | Ja |
| X-MKTO-User-Token | {accessToken} | Ja |
| X-Correlation-ID | Arbitrary string (max 255 characters) | Nej |
| X-Request-Source | Arbitrary string (max 50 characters) | Nej |

#### Begärandetext

| Nyckel | Datatyp | Obligatoriskt | Värde | Standardvärde |
| --- | --- | --- | --- | --- |
| program | Objektmatris | Ja | Lista över programborttagningsåtgärder. Varje anger ett program och de leads som ska tas bort. | - |

Varje objekt i arrayen `programs` innehåller:

| Nyckel | Datatyp | Obligatoriskt | Beskrivning |
| --- | --- | --- | --- |
| programId | Lång | Ja | Marketo program-ID. Måste vara ett positivt heltal. |
| medlemmar | Objektmatris | Ja | Lista med lead-referenser som ska tas bort från programmet. Accepterar JSON-nyckeln `input` eller `members`. |

Varje objekt i arrayen `members` innehåller:

| Nyckel | Datatyp | Obligatoriskt | Beskrivning |
| --- | --- | --- | --- |
| leadId | Lång | Ja | Marketo lead-ID. |

Nödvändiga behörigheter är `Read-Write Lead`.

### Exempel på borttagning av programmedlemmar

#### Begäran

`POST /subscriptions/{munchkinId}/programmembers/delete`

#### Sidhuvuden

`Content-Type: application/json`
`X-Mkto-User-Token: {accessToken}`

#### Brödtext

```json
{
   "programs": [
      {
         "programId": 1001,
         "members": [
            {
               "leadId": 10001
            },
            {
               "leadId": 10002
            }
         ]
      },
      {
         "programId": 1002,
         "members": [
            {
               "leadId": 10003
            }
         ]
      }
   ]
}
```

#### Svar

`HTTP/1.1 202`
`X-Request-ID: a1b2c3d4-e5f6-7890-abcd-ef1234567890`

### Programmedlemmar tar bort valideringsregler

| Regel | Detalj |
| --- | --- |
| program | Får inte vara null eller tom. |
| programId | Obligatoriskt. Måste vara ett positivt heltal. |
| medlemmar | Får inte vara null eller tom. |
| leadId | Krävs för varje medlem i inmatningsarrayen. |
| Max antal leads per begäran | 1 000 medlemmar totalt i alla program. |

## Gränser

Här är en uppdaterad lista över skyddsräcken:

* Maximal storlek för begäran: 1 MB
* Maximalt antal objekt per begäran per objekttyp: 1 000
* Maximalt antal begäranden per sekund per klient-ID: 5 000
* Maximalt antal objekt per dag: 10 000 000

Dessa begränsningar gäller enhetligt för personer, anpassade objekt, företag och programmedlemmar. För programmedlemmar är&quot;objekt per begäran&quot; det totala antalet lead-referenser i alla program i en enda begäran.

## API för datainmatning jämfört med REST API

Här är en lista över skillnader mellan API:t för datainmatning och andra Marketo REST-API:er:

* Om du vill autentisera måste du skicka åtkomsttoken med hjälp av huvudet `X-Mkto-User-Token`
* URL-domännamnet är `mkto-ingestion-api.adobe.io`
* URL-sökvägen börjar med `/subscriptions/MunchkinId`
* Det finns inga frågeparametrar
* Om anropet lyckas returneras status 202 och svarstexten är tom
* Om ett anrop misslyckas returneras en status som inte är 202 och svarstexten innehåller `{ "error_code" : "Error Code", "message" : "Message" }`
* Begärande-ID returneras via rubriken `X-Request-Id`
