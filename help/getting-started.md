---
title: Komma igång
description: Kom igång med Marketo Engage API:er och datamodell som leads, aktiviteter, program, taggar, listor, REST-vägledning och SOAP-meddelande om borttagning.
exl-id: 78c44c32-4e59-4d55-a45c-ef0d7dac814d
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '1352'
ht-degree: 0%

---

# Komma igång

Marketo Engage är en automatiserad marknadsföringsplattform som gör det möjligt för marknadsförare att hantera personaliserade flerkanalsprogram och kampanjer för potentiella kunder och kunder. Marketo Engage-plattformen kan utökas med integreringspunkter. Här nedan hittar du kärnenheterna och deras relationer.

>[!NOTE]
>SOAP-API:t har tagits bort och kommer inte längre att vara tillgängligt efter den 31 oktober 2025. All ny utveckling ska utföras med Marketo [REST API](./rest-api/rest-api.md) och befintliga tjänster ska migreras före detta datum för att undvika avbrott i tjänsten. Om du har en tjänst som använder SOAP API kan du läsa SOAP API [Migreringshandbok](./soap-api/migration.md) för information om hur du migrerar.
>

När antingen den inbyggda SFDC- eller MS Dynamics CRM-anslutningen är aktiverad för en Marketo Engage-instans är följande objekt skrivskyddade: Företag, Möjligheter, Affärsmöjlighetsroll, Försäljningsperson

![Datamodell](assets/data_model.png)

## Person (leads)

Människor är grunden för alla automatiserade marknadsföringsplattformar. Inom Marketo kallas alla icke-säljande personposter leads, oavsett om de har angetts som leads, prospects, misstänkta, kontakter osv. ur ett säljperspektiv. Leadobjektet innehåller en uppsättning standardfält, t.ex. e-post, förnamn och efternamn. Ytterligare fält kan läggas till i lead-objekttypen för att utöka informationstyperna som är kopplade till poster i systemet. Anpassade attribut kan läsas och skrivas till precis som standardfälten. En fullständig lista med fält finns på Marketo-menyn **[!UICONTROL Admin]** > **[!UICONTROL Field Management]**. Leads identifieras unikt i Marketo av ID-fältet. Andra unika nycklar måste verkställas externt från systemet.

Relaterade API:er: [REST](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads), [JavaScript](javascript-api/lead-tracking.md#lead-tracking-api)

## Aktiviteter

Leads interagerar med organisationen på några sätt. En lead kan besöka en sida på företagets webbplats, delta i ett mässor eller ladda ned ett whitepaper. Var och en av dessa åtgärder kan samlas in inom Marketo för att hjälpa en marknadsförare att bättre förstå vilka aktiviteter en lead gjorde och när så att de kan samordna aktuell och relevant kommunikation. Aktiviteter är alltid relaterade tillbaka till leads av leadId.

Du kan definiera egna aktiviteter. När du har skapat och publicerat en anpassad aktivitet kan du lägga till anpassade aktiviteter via Marketo API. Mer information om anpassade aktiviteter finns [här](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/administration/marketo-custom-activities/understanding-custom-activities).

Relaterade API:er: [REST](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities), [JavaScript](javascript-api/lead-tracking.md#munchkin-behavior)

## Program och kampanjer

Ett program är den mekanism genom vilken en marknadsförare organiserar alla sina olika typer av marknadsföringssatsningar från en central plats. Ett exempel på ett program är ett e-postutslag. En lead kan vidta flera åtgärder/aktiviteter som rör ett visst program och som blir associerade med programmet. Detta kallas leadprogression. Ett exempel på hur ett e-postsnabbprogram fortskrider spelas in när en lead skickas ett e-postmeddelande, när personen öppnade e-postmeddelandet eller om de klickade via en länk i e-postmeddelandet.

Kampanjer skapas för ett specifikt syfte och mål inom ett program. Ett exempel på en kampanj kan vara att begränsa en grupp leads och skicka e-postmeddelanden till dem eller att meddela en säljare om en lead klickar via en länk i e-postsnabbprogrammet.

Relaterade API:er: [REST](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Campaigns)

## Taggar

Taggar är ett sätt att gruppera data för rapportering. Dessa identifierare gör det möjligt att kategorisera data och definiera hur du vill rapportera om programmet för att förstå hur effektivt det är och avkastningen på investeringen är.

Som Marketo-administratör kan du skapa obligatoriska och valfria taggtyper som kan väljas när en Marketo-användare skapar ett program. Möjliga värden för var och en av taggtyperna definieras av dig och visar hur ditt företag vill använda anpassade taggar för rapportering.

Du kan till exempel skapa en anpassad&quot;region&quot;-taggtyp med flera taggvärden (till exempel nordost, Sydost) som gör att du kan analysera vilket område som genererar mest leads. Eller så kan du till exempel skapa taggtypen&quot;Ägare&quot;, som gör att du kan bedöma och förstå vilka programägare (till exempel Maria, David eller John) som har störst effekt på att skapa leads och affärsmöjligheter. Mer information om taggar finns [här](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/core-marketo-concepts/programs/working-with-programs/understanding-tags).

Relaterade API:er: [REST](https://developer.adobe.com/marketo-apis/api/asset/)

## Listor

Med listor kan en marknadsförare ordna en samling leads. Det finns två typer av listor inom Marketo, statiska och smarta. En statisk lista är en fast lista med leads som en marknadsförare kan lägga till eller ta bort efter behov. En smart lista är en dynamisk samling leads som baseras på en uppsättning angivna egenskaper. Ett exempel på en smart lista är&quot;Alla leads som har besökt prissidan på vår webbplats&quot;. Den smarta listan fortsätter att växa när fler leads besöker prissidan. Mer information om listor finns [här](https://experienceleague.adobe.com/sv/docs/marketo/using/home).

Relaterade API:er: [REST](https://developer.adobe.com/marketo-apis/api/asset/#tag/Static-Lists)

## Möjligheter

Marknadsförarna levererar leads till försäljning i form av en affärsmöjlighet. En affärsmöjlighet representerar en potentiell försäljningsaffär och är kopplad till en lead eller kontakt och en organisation i Marketo. En affärsmöjlighetsroll är skärningspunkten mellan en viss lead och en organisation. Affärsmöjlighetsrollen gäller en lead-funktion inom organisationen.

Relaterade API:er: [REST](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities)

## Företag

En organisation, som ibland kallas ett konto i Marketo, avser den organisation som en person tillhör. När man använder avkastningsbaserad rapportering i Marketo eller RCA (Revenue Cycle Analytics) är det viktigt att man kopplar samman människor med deras organisation och affärsmöjligheter så att man kan fastställa rätt avkastningsgrad.

Relaterade API:er: [REST](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies)

## Assets

Assets avser landningssidor, e-post, formulär och bilder som används i ett program. Assets kan antingen vara lokalt eller globalt. Globala resurser är tillgängliga i alla program.

Relaterade API:er: [REST](https://developer.adobe.com/marketo-apis/api/asset/)

## Tokens

Med variabler kan en marknadsförare personalisera meddelanden med resurser och lägga till logik i flödesåtgärder. Det finns variabler för det övergripande systemet, program, leads och företag. Ett exempel på en lead-token är {{lead.First Name}}. Denna token kan placeras i ett e-postmeddelande för att visa leadets förnamn.

Tokens som definieras på Program- eller mappnivå kallas för&quot;Mina token&quot; inom Marketo. Mina token kan vara en av tre typer: lokal, ärvd eller åsidosatt.

Mina token som har skapats lokalt i en viss kampanjmapp eller program är tillgängliga för just det programmet eller kampanjmappen (lokal). Mina token som har skapats på kampanjmappsnivå är tillgängliga för användning i alla program i kampanjmappen (ärvda). Mina token som har ändrats på programnivå med anpassade värden ändrar inte det överordnade My Token-värdet för token på programmappsnivå (åsidosatt).

Mina token använder namnkonventionen {{my.My Token}}, med ordet&quot;my&quot; i början av tokennamnet. Om du till exempel skapar en datumtyp, Min token, med namnet EventDate, är namnet på token {{my.EventDate}}. Mer information om Mina token finns [här](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/core-marketo-concepts/programs/tokens/understanding-my-tokens-in-a-program).

Relaterade API:er: [REST](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens)

## Anpassade objekt

Med ett anpassat Marketo-objekt kan du skapa en 1:N- eller många-till-många-relation (Edge-Bridge-Edge) mellan dina Marketo Leads och de anpassade objektposterna. När du har skapat och publicerat ett anpassat Marketo-objekt kan du utföra CRUD-åtgärder på det anpassade objektet via Marketo API. Mer information om hur du skapar anpassade objekt finns [här](https://experienceleague.adobe.com/sv/docs/marketo/using/home). När nya poster läggs till i det anpassade objektet kan du använda en smart listutlösare för att svara. Du kan också använda anpassade objektdata som ett filter i smarta listor (segmentering) eller i e-postmeddelanden med [e-postskript](email-scripting.md).

Relaterade API:er: [REST](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects)

## Säljare

Försäljningspersonsposter och lead-relationer kan hanteras i Marketo när ingen intern CRM-integrering är aktiverad. Posterna innehåller grundläggande information om säljaren, till exempel Namn, E-post och Jobbtitel, som kan användas för filtrering och tokens i Marketo när ett lead ägs av en. Relationen till en säljare hanteras på lead-nivå via fältet externalSalesPersonId, som måste uppdateras via API:t [Synkronisera leads](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/syncLeadUsingPOST).

Relaterade API:er: [REST](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Sales-Persons)
