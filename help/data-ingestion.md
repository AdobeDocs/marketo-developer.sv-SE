---
title: "Datainmatning"
description: "API-översikt för datainmatning"
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '952'
ht-degree: 1%

---


# Inmatning av data

API:t för datainmatning är en tjänst med hög volym, låg latens och hög tillgänglighet som är utformad för att hantera stora mängder personrelaterade data effektivt och med minimal fördröjning. 

Data hämtas genom att begäranden som körs asynkront skickas. Status för begäran kan hämtas genom att prenumerera på händelser från [Marketo observability Data Stream](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-observability-data-stream-setup/). &#x200B;

Gränssnitt erbjuds för två objekttyper: Personer, Egna objekt. Poståtgärden är bara&quot;infoga eller uppdatera&quot;.

API:t för datainmatning finns i en privat beta. Inbjudna måste ha ett berättigande för [Marketo Engage Performance Tier-paket](https://nation.marketo.com/t5/product-documents/marketo-engage-performance-tiers/ta-p/328835).

## Autentisering

API:t för datainmatning använder samma OAuth 2.0-autentiseringsmetod som Marketo REST API för att generera en åtkomsttoken, men åtkomsttoken måste skickas via HTTP-huvudet `X-Mkto-User-Token`. Du kan inte skicka åtkomsttoken via en frågeparameter.

Exempel på åtkomsttoken via rubrik:

`X-Mkto-User-Token: 11606815-aa7a-405a-80a1-f9683efa528b:ab`

## Behörigheter

Datainmatning använder samma behörighetsmodell som Marketo REST API och kräver inga ytterligare specialbehörigheter att använda, men specifika behörigheter krävs för varje slutpunkt.

| Slutpunkt | Behörighet |
|---|---|
| Personer | Läs/skriv lead |
| Anpassade objekt | Anpassat objekt för läsning/skrivning |

## Sidhuvuden

Datainmatning använder följande anpassade HTTP-rubriker.

### Begäran

| Nyckel | Värde | Obligatoriskt | Beskrivning |
|---|---|---|---|
| X-Correlation-ID | Arbitrary string (maximum length 255 characters). | Nej | Kan användas för att spåra begäranden via systemet. Se Marketo observabilitetsdataström |
| X-Request-Source | Arbitrary string (max 50 characters). | Nej | Kan användas för att spåra källan för begäranden via systemet. Se Marketo observabilitetsdataström |

### Svar

| Nyckel | Värde | Obligatoriskt | Beskrivning |
|---|---|---|---|
| X-Request-ID | Unikt begärande-ID. | Ja | |

## Begäranden

Använd metoden HTTP-POST för att skicka data till servern.

Datarepresentationen är inkluderad i begärandetexten som application/json.

Domännamnet är: `mkto-ingestion-api.adobe.io`

Sökvägen börjar med `/subscriptions/_MunchkinId_` där `_MunchkinId_` är specifikt för din Marketo-instans. Du hittar ditt Munchkin-ID i användargränssnittet för Marketo Engage under **Administratör** >**Mitt konto** > **Supportinformation**. Resten av sökvägen används för att ange den givna resursen.

Exempel-URL för personer:

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/persons`

Exempel-URL för anpassade objekt:

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/customobjects/purchases`

## Svar

Alla svar returnerar ett unikt begärande-ID via `X-Request-Id` header.

Exempel på begärande-ID via rubrik:

`X-Request-Id: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### Lyckades

När ett anrop lyckas returneras status 202. Ingen svarstext returneras.

Exempel på lyckat svar:

`HTTP/1.1 202 Accepted` `X-Request-Id: e3d92152-0fb1-444a-8f8f-29d5a2338598` `Content-Length: 0` `Date: Wed, 18 Oct 2023 18:56:49 GMT`

### Fel

När ett anrop genererar ett fel returneras en status som inte är 202 tillsammans med ett svarstext med ytterligare felinformation. Svarstexten är application/json och innehåller ett enda objekt med medlemmar `error_code` och `message`.

Nedan visas återanvända felkoder från Adobe Developer Gateway.

| HTTP-statuskod | error_code | message |
|--- |--- |--- |
| 401 | 401013 | Oauth-token är ogiltig |
| 403 | 403010 | Oauth-token saknas |
| 404 | 404040 | Resursen hittades inte |
| 429 | 429001 | Tjänstanvändningsgränsen har nåtts |

Nedan visas felkoder som är unika för API:t för datainmatning och som består av tre segment. De första tre siffrorna är statusen (returneras av Adobe IO Gateway), följt av noll&quot;0&quot;, följt av tre siffror.

| HTTP-statuskod | error_code | message |
|--- |--- |--- |
| 400 | 4000801 | Felaktig begäran |
| 400 | 4000802 | Ogiltiga data |
| 403 | 4030801 | Obehörig |
| 429 | 4290801 | Kvot per dag har nåtts |
| 500 | 5000801 | Internt serverfel |

Exempel på felsvar:

`HTTP/1.1 403 Forbidden` `Content-Type: application/json` `Content-Length: 54` `Date: Wed, 18 Oct 2023 19:10:07 GMT` `X-Request-Id: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw` `{"error_code":"403010","message":"Oauth token is missing"}`

## Försök igen

När ett tillfälligt fel upptäcks försöker tjänsten utföra åtgärden igen tre gånger. Det första återförsöket sker efter en 5 minuter lång vänteperiod, det andra efter 30 minuter till och slutligen det tredje efter 30 minuter till. Försök görs på nytt av olika anledningar, i första hand när en beroende tjänst inte är tillgänglig eller inte är tillgänglig för tillfället.

## Slutpunkter

Slutpunkter för inmatning är tillgängliga för Personer och Anpassade objekt.

### Personer

Slutpunkt som används för att infoga personposter.

| Metod |
|---|
| POST |

| Bana |
|---|
| /subscriptions/{munchkinId}/personer |

| HeadersKey | Värde |
|---|---|
| Content-Type | application/json |
| X-MKTO-User-Token | {accessToken} |

Begärandetext

| Nyckel | Datatyp | Obligatoriskt | Värde | Standardvärde |
|---|---|---|---|---|
| prioritet | Sträng | Nej | Förfrågans prioritet:normal hög | normal |
| partitionName | Sträng | Nej | Namn på personpartition | Standard |
| dedupeFields | Objekt | Nej | Attribut som inte ska dupliceras. Ett eller två attributnamn tillåts. Två attribut används i en AND-åtgärd. Om till exempel båda `email` och `firstName` anges används båda för att leta upp en person med operationen AND. Attributen stöds:`idemail`, `sfdcAccountId`, `sfdcContactId`, `sfdcLeadId`, `sfdcLeadOwnerIdCustom` attribut (&quot;string&quot; och&quot;integer&quot;) | e-post |
| personer | Objektmatris | Ja | Lista över attributnamnvärdespar för personen | - |

| Behörighet |
|---|
| Läs/skriv lead |

#### Exempel på personer

```
POST /subscriptions/{munchkinId}/persons
```

```
Content-Type: application/json
X-Mkto-User-Token: {accessToken}
```

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
         "lastName": "Parker"
      },
      {
         "email": "johnny.neal@yvu30.com",
         "firstName": "Johnny",
         "lastName": "Neal"
      }
   ]
}
```

```
HTTP/1.1 202
X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw
```

### Anpassade objekt

Slutpunkt som används för att infoga anpassade objektposter.

| Metod |
|---|
| POST |

| Bana |
|---|
| /subscriptions/{munchkinId}/customobjects/{customObjectAPIName} |

Sidhuvuden

| Nyckel | Värde |
|---|---|
| Content-Type | application/json |
| X-MKTO-User-Token | {accessToken} |

Begärandetext | Nyckel | Datatyp | Obligatoriskt | Värde | Standardvärde | |—|—|—|—|—|—| | prioritet | Sträng | Nej | Förfrågans prioritet:normal hög | normal | | dedupeBy | Sträng | Nej | Attribut som ska dedupliceras på:deduplicpeFieldMarketoGUID | dedupeFields | | customObjects | Objektmatris | Ja | Lista över attributnamnvärdespar för objektet. | - |

| Behörighet |
|---|
| Anpassat objekt för läsning/skrivning |

#### Personen finns inte

Om ett länkfält till en person anges i begäran och den personen inte finns, görs flera försök. Om den personen läggs till under återförsöksfönstret (65 minuter) slutförs uppdateringen. Om länkfältet till exempel är `email` på Person, och personen finns inte, görs nya försök.

#### Exempel på anpassade objekt

```
POST /subscriptions/{munchkinId}/customobjects/{customObjectAPIName}
```

```
Content-Type: application/json
X-Mkto-User-Token: {accessToken}
```

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

```
HTTP/1.1 202
X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw
```

## Gränser

Här följer en lista över användning av skyddsräcken:

- Maximal storlek för begäran: 1 MB
- Maximalt antal objekt per begäran per objekttyp: 1 000
- Maximalt antal begäranden per sekund per klient-ID: 5 000
- Maximalt antal objekt per dag: 10 000 000

## API för datainmatning jämfört med REST API

Här är en lista över skillnader mellan API:t för datainmatning och andra Marketo REST-API:er:

- Det här är inte ett fullständigt CRUD-gränssnitt, det stöder endast upsert
- Du måste skicka åtkomsttoken med `X-Mkto-User-Token` header
- URL-domännamnet är `mkto-ingestion-api.adobe.io`
- URL-sökvägen börjar med `/subscriptions/_MunchkinId_`
- Det finns inga frågeparametrar
- Om anropet lyckas returneras status 202 och svarstexten är tom
- Om anropet misslyckas returneras en status som inte är 202 och svarstexten innehåller `{ "error_code" : "_Error Code_", "message" : "_Message_" }`
- Begärande-ID returneras via `X-Request-Id` header
