---
title: Dataströmmar
description: Översikt över dataströmmar
exl-id: 5617b6a5-ebc8-4d97-a290-e3b87f83e360
source-git-commit: 9830572277db2709c6853bea56fc70c455fd5e54
workflow-type: tm+mt
source-wordcount: '1583'
ht-degree: 0%

---

# Dataströmmar

Våra kunders marknadsföringsorganisationer förlitar sig på aktuella och fokuserade marknadsföringskampanjer för att ligga steget före sina affärer och vara konkurrenskraftiga. För att stödja snabba beslut och möjliggöra strategiska förändringar snabbt är det viktigt att ha data som stöder och driver de viktiga beslut som levererar fokuserade och målinriktade kampanjer. Det finns också vissa kunder som arbetar med marknadsföring i sina kundsegment både inom och utanför Marketo Engage. För att stödja dessa olika satsningar har Marketo skapat möjligheten att inhämta stora datavolymer i nära realtid via dataströmmar.

Förutom fördelarna med nära realtidsdata finns det produktrelaterade fördelar:

- Hämtar flaskhalsen för API-gränser eftersom direktuppspelning används i stället.
- Minskar scenariot för API-gränser och genererar färre varningsmeddelanden.
- Inga massexporter måste utföras för att extrahera data på grund av dataströmningsfunktionen.

Dataströmmar är tillgängliga för dem som har köpt ett [Marketo Engage Performance Tier Package](https://nation.marketo.com/t5/product-documents/marketo-engage-performance-tiers/ta-p/328835).

## Översikt över dataströmmen för huvudaktivitet

Dataströmmen för huvudaktivitet ger nästan direktuppspelning i realtid av huvudaktiviteter för spårning av revision där stora volymer leadaktiviteter kan skickas till en kunds externa system. Med strömmar kan kunderna effektivt granska leadrelaterade händelser, användningsmönster, tillhandahålla vyer till leadförändringar och utlösa processer och arbetsflöden baserat på de olika typerna av leadhändelser. Det finns fler än 144 aktivitetstyper som kan prenumereras på och skickas genom strömmen.

Strömmade typer av lead-data:

1. Leadändringar - alla ändringar i alla fält och nya leads
1. Leadaktiviteter - alla leadaktivitetstyper som beskrivs i dokumentet
1. Borttagna leads
1. Alla anpassade objekt på leadet (om det efterfrågas). Det är allt eller ingenting just nu.

Genom att förmedla vyer om förändringar i leads kan kunderna fatta snabbare beslut om sina övergripande marknadsföringsstrategier och skapa mer fokuserade kampanjer. Några vanliga användningsområden är:

- Anpassad avisering: När vissa leads påträffas med inkonsekventa villkor kan de läggas till i listan. Aktivitetsströmmar kan hämta upp dessa och push-aktivera aktiviteten&quot;Lägg till i lista&quot; för kunder för att följa upp åtgärder.
- Powering ML-modeller: En del kunder planerar att bygga bedömningsmodeller som använder aktivitetsinsikter och matar tillbaka dem till Marketo eller använder dem i sina egna interna bedömningsmodeller efter behov. Genom att betygsätta en lead kan kunderna sedan informera Marketo om att lägga till kunder i kommande kampanjer och öka poängsättningen.

Lista över strömmade aktiviteter:

| AchieveGoalInReferral | ClickPredictiveContent | MottagenVidarebefordraTillFriendEmail |
|--- |--- |--- |
| LäggTillLista | ClickRTPCallToAction | ReceiveSalesEmail |
| LäggTillStruktur | ClickSalesEmail | ReferToSocialApp |
| LäggTillMöjlighet | ClickSharedLink | TaBortFrånLista |
| LäggTillFörsäljningskampanj | ConvertLead | TaBortFrånMöjlighet |
| RingWebkrok | DeleteLead | RequestCampaign |
| ChangeDataValue | DiseligibleSweepstakes | SalesEmailBounced |
| ChangeLeadPartition | EarnEntryInSocialApp | SendAlert |
| ChangeNurtureCadence | EmailBounced | SendEmail |
| ChangeNurtureTrack | EmailBouncedSoft | SendSalesEmail |
| ChangeOwner | E-postLevererat | SkickatVidarebefordraTillFriendEmail |
| ChangeProgramData | EnrichWithDataDotCom | SFDCActivity |
| ChangeProgramMemberData | EnterSweepstakes | ShareContent |
| ChangeRevenueStage | FillOutFacebookLeadAdsForm | SignUpForReferralOffer |
| ChangeRevenueStageManually | FillOutForm | SyncLeadToMicrosoft |
| ChangeScore | IntressantMoment | SyncLeadToSFDC |
| ChangeSegment | MergeLeads | UnsubscribeEmail |
| ÄndraStatusInProgression | NewLead | UpdateOpportunity |
| ChangeStatusInSalesCampaign | OpenEmail | BesökWebbsida |
| ClickEmail | OpenSalesEmail | OmröstningInomröstning |
| ClickLink | PushLeadToMarketo | WinSweepstakes |

Observera att om anpassade objekt ska direktuppspelas måste det vara alla Lead-relaterade anpassade objekt. Det finns för närvarande inget sätt att välja vilka du vill ha.

## Översikt över dataströmmen för användargranskning

Dataströmmen för användargranskning ger nästan direkt realtidsspårning av resursändringar &#x200B; användare. På så sätt kan kunden effektivt granska tillgångshändelser, få en översikt över användarändringar och aktivera processer eller arbetsflöden baserat på olika typer av granskningshändelser. Nära resursändringar i realtid skickas via Adobe I/O-händelser till en konfigurerbar slutpunkt. Granskningshändelser delas upp efter tillgångstyp och kan prenumerera på granskningshändelser som är viktiga för dem.

Ett bra sätt att prenumerera på den här strömmen är:

- Spåra ändringar när du använder flera marknadsföringssystem: Det finns vissa kunder som också utför vissa marknadsföringsaktiviteter i ett annat system som CRM som Salesforce och sedan skickar lead till Marketo. Lead uppdateras och synkroniseras ibland fram och tillbaka så det är viktigt att spåra vilket system som har gjort de senaste ändringarna.

Lista med strömmade användargranskningshändelser:

| KOMPONENT | HÄNDELSETYPLISTA |
|--- |--- |
| Standardprogram | klona, skapa, ta bort, redigera kanal, exportera, ändra programinställningar, ändra programtoken, byta namn |
| E-post | godkänna, klona, skapa, ta bort, redigera, flytta, byta namn, avgodkänna |
| E-postbatchprogram | godkänn, childUpdate, klona, skapa, ta bort, redigera kanal, ändra programschema, ändra programinställning, ändra programtoken, ändra namn, avgodkänn |
| E-postmall | godkänn, klona, skapa, ta bort, draftCreate, draftDiscard, redigera, byta namn, avgodkänn |
| Engagement Program | klona, skapa, ta bort, redigera kanal, ändra programinställning, ändra programström, ändra programtoken, byta namn |
| Event Program | klona, skapa, ta bort, redigera kanal, ändra programschema, ändra programinställning, ändra programtoken, byta namn |
| Mapp | skapa, ta bort, redigera, byta namn |
| Formulär | godkänna, klona, skapa, ta bort, utkastSkapa, redigera, flytta, byta namn |
| Formulär -> Landing Page Form | skapa, klona, redigera, ta bort, godkänna, byta namn |
| Landningssida | godkänn, klona, skapa, ta bort, draftDiscard, redigera, byta namn, avgodkänn |
| Landningssidmall | godkänn, klona, skapa, ta bort, draftCreate, draftDiscard, redigera, byta namn, avgodkänn |
| Smart List | klona, skapa, ta bort, redigera, exportera, ändra inställningar för smarta listor, byta namn |
| Marknadsföringsmapp | skapa, redigera, ta bort |
| Näringsprogram | klona, skapa, ta bort, redigera kanal, ändra programkonfiguration, ändra programström, ändra programtoken, byta namn |
| Segment | skapa, ta bort, redigera, byta namn |
| Segmentering | godkänn, skapa, ta bort, utkastSkapad, utkastIgnorerad, ändra namn, avgodkänn |
| Smart Campaign | avbryt, aktivera, klona, skapa, inaktivera, ta bort, redigera, ändra kampanjschema, ändra flödesstegåtgärd, ändra inställning för smart lista, flytta, byta namn |
| Fragment | godkänna, godkänna utan utkast, klona, skapa, ta bort, redigera, byta namn, avgodkänna |
| Administratörsgränssnitt -> Startpunkt -> Integrering | skapa, ta bort, redigera |
| Administratörsgränssnitt -> Användare | skapa, redigera, ta bort (samma för API-användare) |
| Administratörsinloggning -> Användare | inloggningen lyckades, inloggningsfel |
| Program -> E-postbatchprogram | redigera (för att ändra vald e-postadress) resurs-API |
| Program -> Marketing Program | skapa, klona |

Exempel på användargranskningshändelse:

```json
{
    "event_id": "a1b2c3d4-zyxw-9876-9z8y-a1b2c3d4e5f6",
    "event": {
        "specversion": "1.0",
        "id": "b77c743a-8e28-40f2-8aab-9541bbc85e68",
        "type": "com.adobe.platform.marketo.audit.user.email",
        "source": "https://www.marketo.com",
        "time": "2020-05-28T19:20:47.28Z",
        "datacontenttype": "application/json",
        "dataschema": "V1.0",
        "data": {
            "componentId": 232459,
            "componentType": "Email",
            "eventAction": "approve",
            "munchkinId": "123-ABC-456",
            "imsOrgId": "ADOBEORGID@AdobeOrg",
            "user": 253,
            "userId": "example@marketo.com"          
        }
    }
}
```

## Översikt över meddelandedataström

Meddelandedataströmmen är tillgänglig som en del av prestandanivåerna i Marketo Engage.

För närvarande kan meddelandecentret i Marketo konfigureras att skicka meddelanden till en e-postadress. Med Notification Data Stream kan meddelandena skickas direkt till en konfigurerbar slutpunkt via Adobe I/O-händelser. Meddelanden skickas via gränssnittet idag och kan refereras av den orangefärgade klockan i skärmens övre högra hörn. Den här strömmen tar dessa meddelanden och skickar dem till en ström.

Lista över meddelandehändelser:

| KOMPONENT | HÄNDELSETYPLISTA |
|--- |--- |
| Meddelande | kampanjavbrott, kampanjfel, näring (programmet är slut), synkfel för Salesforce, testgrupp (A/B-testresultat), webbtjänster (dagskvot) |

Exempel på meddelandehändelse:

```json
{
    "event_id": "a1b2c3d4-zyxw-9876-9z8y-a1b2c3d4e5f6",
    "event": {
        "specversion": "1.0",
        "type": "com.adobe.platform.marketo.notification.campaign_abort",
        "source": "https://www.marketo.com",
        "time": "2021-05-27T10:22:37.489-5:00",
        "datacontenttype": "application/json",
        "dataschema": "V1.0",
        "data": {
            "componentType": "campaign_abort",
            "subType": "user_campaign_abort",
            "eventAction": {
                "campaignId":1234,
                "userId":"example@marketo.com",
            }
            "imsOrgId":"ADOBEORGID@AdobeOrg",
            "munchkinId":"123-ABC-456"
        }
    }
}
```

## Teknisk information

Det här avsnittet innehåller riktlinjer för vad som behövs, hur du ansluter och tar emot strömmande data för varje ström. Det finns en viss nivå av kodning och konfiguration för varje.

### Dataström för huvudaktivitet

Lead Activity Stream ger i stort sett direktuppspelning i realtid av Marketo Lead Activity-händelser och skickar ändringar av den prenumererade aktivitetstypen med konfigurerbara attribut:

- Frekvensen för datapush varannan sekund som standard.
- Batchar från 100 till 500 per prenumeration.
- Tidsgränsen för kundens REST-tjänst är 20 sekunder med 3 återförsök var 3:e minut och autoaktiverad när åtgärden lyckades. I annat fall pausas de. När tjänsten har pausats försöker den återaktiveras var tredje minut om den inte tas bort manuellt.
- Datalagring i en kö i upp till 7 dagar.

Så här implementerar du dataströmmen för huvudaktiviteten:

1. Visa en HTTP-slutpunkt som kan ta emot POST-begäranden med en JSON-kropp från det offentliga Internet. Dataströmmen för överföring av aktivitet skickar begäranden till:
1. Tillhandahåll följande för Adobe:
   1. Marketo Munchkin ID för prenumerationer
   1. Slutpunktens URL i steg 1
   1. De aktivitetstyper de vill ta emot (fullständig lista ovan)
   1. Ett sätt att autentisera, så att kunden kan verifiera att förfrågningarna är berättigade. Antingen:
      1. En identitetsleverantörs-URL, klient-ID och klienthemlighet för OAuth [Autentisering av klientautentiseringsuppgifter](https://www.oauth.com/oauth2-servers/access-tokens/client-credentials/)
      1. En API-token, som kan inkluderas i begäranden som skickas av huvudaktivitetsdataströmmen i en http-rubrik för auktorisering

Adobe aktiverar sedan dataströmmen, då kunderna börjar ta emot data.

UML-diagram över ett typiskt dataströmanrop för lead-aktivitet:

![Dataströmsdiagram för huvudaktivitet](assets/lead-activity-data-stream.png)

Exempel på att skapa URL-slutpunkter:

```javascript
/*
Copyright 2022 Adobe
All Rights Reserved.

NOTICE: Adobe permits you to use, modify, and distribute this file in
accordance with the terms of the Adobe license agreement accompanying
it.
*/
constexpress=require('express')
constwinston=require('winston');
constport=3000

constapp=express().use(express.json())

constlogger=winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  defaultMeta: {service: 'activity-stream-consumer-example'},
  transports: [
    // - Write all logs with level `error` and below to `error.log`
    newwinston.transports.File({filename: 'error.log',level: 'error'}),
    // - Write all logs with level `info` and below to `combined.log`
    newwinston.transports.File({filename: 'combined.log'}),
    newwinston.transports.Console({format: winston.format.simple()})
  ],
});

app.get('/',(req,res)=>{
  logger.info(JSON.stringify(req.query))
  res.sendStatus(200)
})

app.post('/',(req,res)=>{
  logger.info(JSON.stringify(req.body))
  res.sendStatus(200)
})

app.listen(port,()=>{
  logger.info(`app listening on port ${port}`)
})
```

Ett kodexempel för ett program som använder Marketo Lead Activity Data Stream finns [här](https://github.com/ihgrant/activity-stream-consumer-example).

### Dataström för användargranskning och meddelandedata

Granskningshändelser skickas till Adobe IO och kan användas genom att logga in med en Adobe ID. Så här gör du:

1. Kunderna förser Adobe med följande:
   1. Adobe ID
   1. Marketo Munchkin ID för prenumerationer
1. Kunden visar en REST-slutpunkt för att förbruka händelser som normalt är i form av en webkrok.
1. När detta är klart aktiverar Adobe strömmen för kundens prenumeration.
1. Kunden ställer sedan in strömmen i Adobe IO (instruktioner ska anges)
   1. För det här steget krävs en Adobe-organisation
   1. Kräver att Adobe Org User har rollen Developer eller System Admin

Information om hur du konfigurerar Adobe IO finns i [Konfigurera Marketo användargranskningsdataströmmar med Adobe IO](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-user-audit-data-stream-setup/) i avsnittet Offentlig dokumentation.

### Konfigurera dataströmmen för användargranskning i Marketo

Dataströmmen för användargranskning är för närvarande tillgänglig som en del av prestandapaketen tillsammans med de tre andra dataströmmarna. Mer information om paketen finns på [produktbeskrivningssidan](https://helpx.adobe.com/legal/product-descriptions/adobe-marketo-engage---product-description.html) för produktbegränsningar och funktioner.

### Konfigurera Adobe I/O

[Se Komma igång med Adobe I/O Events](https://developer.adobe.com/runtime/docs/guides/getting-started/)

Grundläggande instruktioner för det här användningsfallet från [console.adobe.io](https://developer.adobe.com/console):

Välj antingen **[!UICONTROL Create New Project]** eller **[!UICONTROL Add Event]** när du uppmanas till detta.

### Kom igång med ditt nya projekt

Om du vill börja använda Adobe-tjänster lägger du till ett API, händelser eller en körningsversion i [dokumentationen](https://developer.adobe.com/runtime/docs/).

## Offentlig dokumentation

- [Marketo-dataströmmar](https://developer.adobe.com/events/docs/guides/using/marketo/marketo-data-streams/)
- [Introduktion till Adobe IO Events &amp; Webhooks](https://developer.adobe.com/events/docs/guides/)
- [Bloggen Dataströmmar](https://blog.developer.adobe.com/introducing-the-adobe-marketo-engage-data-streams-61198b567fbb)
