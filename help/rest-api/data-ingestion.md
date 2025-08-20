---
title: Datainhämtning
feature: REST API, Dynamic Content
description: Förbruka data med Marketo API:er.
exl-id: 1d501916-53ac-42d8-a804-abb4ab01c7e8
source-git-commit: 4bac6968867f1fbee0846b1bcd75b5e34ad91dee
workflow-type: tm+mt
source-wordcount: '965'
ht-degree: 2%

---

# API för datainmatning

API:t för datainmatning är en tjänst med hög volym, låg latens och hög tillgänglighet som är utformad för att hantera stora mängder personrelaterade data effektivt och med minimal fördröjning.

Data hämtas genom att begäranden som körs asynkront skickas. Status för begäran kan hämtas genom att prenumerera på händelser från [Marketo-dataströmmen för observabilitet](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-observability-data-stream-setup). &#x200B;

Gränssnitt erbjuds för två objekttyper: Personer, Egna objekt. Poståtgärden är bara&quot;infoga eller uppdatera&quot;.

>[!NOTE]
>
>API:t för datainmatning finns för närvarande i en privat beta.  Inbjudna måste ha ett berättigande för [Marketo Engage Performance Tier](https://nation.marketo.com/t5/product-documents/marketo-engage-performance-tiers/ta-p/328835)-paketet.

## Autentisering

API:t för datainmatning använder samma OAuth 2.0-autentiseringsmetod som Marketo REST API för att generera en åtkomsttoken, men åtkomsttoken måste skickas via HTTP-huvudet `X-Mkto-User-Token`. Du kan inte skicka åtkomsttoken via en frågeparameter.

Exempel på åtkomsttoken via rubrik:

`X-Mkto-User-Token: 11606815-aa7a-405a-80a1-f9683efa528b:ab`

## Behörigheter

Datainmatning använder samma behörighetsmodell som Marketo REST API och kräver inga ytterligare specialbehörigheter att använda, men specifika behörigheter krävs för varje slutpunkt.

| Slutpunkt | Behörighet |
|-|-|
| Personer | Läs/skriv lead |
| Anpassade objekt | Anpassat objekt för läsning/skrivning |

## Sidhuvuden

Datainmatning använder följande anpassade HTTP-rubriker.

### Begäran

| Nyckel | Värde | Obligatoriskt | Beskrivning |
| - | - | - | - |
| X-Correlation-ID | Arbitrary string (maximum length 255 characters). | Nej | Kan användas för att spåra begäranden via systemet.  Se Marketo observabilitetsdataström |
| X-Request-Source | Arbitrary string (max 50 characters). | Nej | Kan användas för att spåra källan för begäranden via systemet.  Se Marketo observabilitetsdataström |

### Svar

| Nyckel | Värde | Obligatoriskt |
| - | - | - |
| X-Request-ID | Unikt begärande-ID. | Ja |

## Begäranden

Använd HTTP POST-metoden för att skicka data till servern.

Datarepresentationen är inkluderad i begärandetexten som application/json.

Domännamnet är: `mkto-ingestion-api.adobe.io`

Sökvägen börjar med `/subscriptions/MunchkinId` där MunchkinId är specifik för din Marketo-instans. Du hittar ditt Munchkin-ID i Marketo Engage-gränssnittet under **Admin** > **Mitt konto** > **Supportinformation**.  Resten av sökvägen används för att ange den givna resursen.

Exempel-URL för personer:

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/persons`

Exempel-URL för anpassade objekt:

`https://mkto-ingestion-api.adobe.io/subscriptions/556-RJS-213/customobjects/purchases`

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
| - | - | - |
| 401 | 401013 | Oauth-token är ogiltig |
| 403 | 403010 | Oauth-token saknas |
| 404 | 404040 | Resursen hittades inte |
| 429 | 429001 | Tjänstanvändningsgränsen har nåtts |

Nedan visas felkoder som är unika för API:t för datainmatning och som består av tre segment.  De första tre siffrorna är statusen (returneras av Adobe Developer Gateway), följt av noll&quot;0&quot;, följt av tre siffror.

| HTTP-statuskod | error_code | message |
| - | - | - |
| 400 | 4000801 | Felaktig begäran |
| 400 | 4000802 | Ogiltiga data |
| 403 | 4030801 | Obehörig |
| 429 | 4290801 | Kvot per dag har nåtts |
| 500 | 5000801 | Internt serverfel |

## Försök igen

När ett tillfälligt fel upptäcks försöker tjänsten utföra åtgärden igen tre gånger.  Det första återförsöket görs efter en 5 minuter lång vänteperiod, det andra efter 30 minuter till och slutligen det tredje efter 30 minuter till.  Försök görs på nytt av olika anledningar, i första hand när en beroende tjänst inte är tillgänglig eller inte är tillgänglig för tillfället.

## Slutpunkter

Slutpunkter för inmatning är tillgängliga för Personer och Anpassade objekt.

### Personer

Slutpunkt som används för att infoga personposter.

| Metod | Sökväg |
| - | - |
| POST | /subscriptions/{munchkinId}/personer |

#### Sidhuvuden

| Nyckel | Värde |
| - | - |
| Content-Type | application/json |
| X-MKTO-User-Token | {accessToken} |

#### Begärandetext

| Nyckel | Datatyp | Obligatoriskt | Värde | Standardvärde |
| - | - | - | - | - |
| prioritet | Sträng | Nej | Förfrågans prioritet: normal eller hög | normal |
| partitionName | Sträng | Nej | Namn på personpartition | Standard |
| dedupeFields | Objekt | Nej | Attribut som inte ska dupliceras. Ett eller två attributnamn tillåts. <br/> Två attribut används i en AND-åtgärd. Om till exempel både `email` och `firstName` anges används båda för att leta upp en person med AND-åtgärden. <br/>Attribut som stöds är: `id`, `email`, `sfdcAccountId`, `sfdcContactId`, `sfdcLeadId` `sfdcLeadOwnerId`, anpassade attribut (&quot;string&quot; och&quot;integer&quot;), `email` |
| personer | Objektmatris | Ja | Lista över attributnamnvärdespar för personen | - |

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

#### Svar

`HTTP/1.1 202`
`X-Request-ID: WOUBf3fHJNU6sTmJqLL281lOmAEpMZFw`

### Anpassade objekt

Slutpunkt som används för att skicka anpassade objektposter

| Metod | Sökväg |
| - | - |
| POST | `/subscriptions/{munchkinId}/customobjects/{customObjectAPIName}` |

#### Sidhuvuden

| Nyckel | Värde |
| - | - |
| Content-Type | application/json |
| X-MKTO-User-Token | {accessToken} |

#### Begärandetext

| Nyckel | Datatyp | Obligatoriskt | Värde | Standardvärde |
| - |- | - | - | - |
| prioritet | Sträng | Nej | Företräde: normal, hög | normal |
| dedupeBy | Sträng | Nej | Attribut som ska dedupliceras på: dedupeFields, marketoGUID | dedupeFields |
| customObjects | Objektmatris | Ja | Lista över attributnamnvärdespar för objektet. | - |

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

## Gränser

Här följer en lista över användning av skyddsräcken:

* Maximal storlek för begäran: 1 MB
* Maximalt antal objekt per begäran per objekttyp: 1 000
* Maximalt antal begäranden per sekund per klient-ID: 5 000
* Maximalt antal objekt per dag: 10 000 000

## API för datainmatning jämfört med REST API

Här är en lista över skillnader mellan API:t för datainmatning och andra Marketo REST-API:er:

* Det här är inte ett fullständigt CRUD-gränssnitt, det stöder bara upsert
* Om du vill autentisera måste du skicka åtkomsttoken med hjälp av huvudet `X-Mkto-User-Token`
* URL-domännamnet är `mkto-ingestion-api.adobe.io`
* URL-sökvägen börjar med `/subscriptions/MunchkinId`
* Det finns inga frågeparametrar
* Om anropet lyckas returneras status 202 och svarstexten är tom
* Om anropet misslyckas returneras en status som inte är 202 och svarstexten innehåller `{ "error_code" : "Error Code", "message" : "Message" }`
* Begärande-ID returneras via rubriken `X-Request-Id`
