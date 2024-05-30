---
title: "Spårning av leads"
description: "API för uppföljning av leads"
feature: Munchkin Tracking Code, Javascript
source-git-commit: 2185972a272b64908d6aac8818641af07c807ac2
workflow-type: tm+mt
source-wordcount: '766'
ht-degree: 0%

---


# API för lead-spårning

Marketo Munchkin JavaScript gör det möjligt att följa upp besök och klickningar på Marketo landningssidor och externa webbsidor. Dessa spelas in i Marketo som aktiviteter av typen&quot;Besök webbsida&quot; och&quot;Länka på webbsida&quot; som du klickat på&quot;, som sedan kan användas i utlösare och filter för smarta kampanjer och smarta listor.

## Bädda in koden

Din Marketo-instans innehåller automatiskt förkonfigurerade spårningskod för att bädda in kod på dina externa sidor som spårar aktiviteten tillbaka till din Marketo-instans. Användning av inbäddningskoden regleras av detta [licensavtal](../munchkin-license.pdf).

Det finns tre typer av spårningskod:

1. Enkel - läser in synkront
1. Asynkron - läser in asynkront
1. Asynkron jQuery - laddar asynkront och kräver att jQuery läses in i förväg

Vi rekommenderar starkt att den asynkrona spårningskoden används för att bädda in Munchkin på externa sidor. För att säkerställa högsta möjliga lyckade hastighet för körning bäddar du in den asynkrona spårningskoden i `<head>` av varje sida.

Vissa innehållshanteringssystem kan ha specifika metoder eller begränsningar när du bäddar in godtyckliga skript.

Som referens bör den sista sidan innehålla kod som liknar den här i `<head>` av ditt HTML-dokument:

```html
<head>
    <script type="text/javascript">
    (function() {
        var didInit = false;
        function initMunchkin() {
            if(didInit === false) {
                didInit = true;
                Munchkin.init('CHANGE-ME');
            }
        }
        var s = document.createElement('script');
        s.type = 'text/javascript';
        s.async = true;
        s.src = '//munchkin.marketo.net/munchkin.js';
        s.onreadystatechange = function() {
            if (this.readyState == 'complete' || this.readyState == 'loaded') {
                initMunchkin();
            }
        };
        s.onload = initMunchkin;
        document.getElementsByTagName('head')[0].appendChild(s);
        })();
    </script>
    ...
</head>
```

## Munchkins beteende

Marketo Munchkins standardbeteende är att göra följande vid sidinläsning:

1. Kontrollera om webbläsaren har en Munchkin-cookie och skapa en om den inte finns där.
1. Skicka en&quot;Besök webbsida&quot;-händelse till den angivna Marketo-instansen med hjälp av informationen från den aktuella sidan och webbläsaren. Detta registrerar en aktivitet till motsvarande post i Marketo.
1. Skicka händelsen&quot;Klicka på Länk på webbsida&quot; för alla användarklick som inträffar på länkar.

Munchkins beteende kan ändras genom användningen av Munchkin [Konfigurationsinställningar](lead-tracking.md#lead-tracking-api), till exempel om en cookie skapas för alla leads när du besöker sidan med `cookieAnon` ställa in eller ändra klickfördröjningen med `clickTime` inställning. Det går inte att skicka besöksaktiviteten genom att ange inställningen apiOnly till true. Från och med version 162 (augusti 2022) klickar du på `tel` och `mailto` länkar spåras utöver `http/s` länkar.

## Kända och anonyma leads

På en leads första besök på en sida på din domän skapas en ny anonym lead-post i Marketo. Primärnyckeln för den här posten är Munchkins cookie (`_mkto_trk`) som skapas i användarens webbläsare. Alla efterföljande webbaktiviteter i den webbläsaren registreras mot den här anonyma posten. För att kunna kopplas till en känd post i Marketo måste något av följande inträffa:

- Ledaren måste besöka en Munchkin-spårad sida med en `mkt_tok` i frågesträngen från en spårad e-postlänk från Marketo.
- Lead-blanketten måste fylla i ett Marketo-formulär.
- En SOAP [syncLead](../soap-api/leads.md) eller REST [Associera lead](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/associateLeadUsingPOST) samtal måste skickas.

När ett av dessa villkor är uppfyllt kopplas cookien och all tillhörande webbaktivitet till det kända leadet.

En ny anonym webbaktivitetspost skapas för varje enskild webbläsare, så om en lead besöker din domän för första gången med en ny dator och/eller webbläsare måste den här associationen göras igen.

## Domäner

Munchkin skapar och spårar enskilda cookies per domän, så för att kända leads ska kunna spåras över domäner måste en lead-associationshändelse inträffa för varje domän. Om jag till exempel kontrollerar två domäner, `marketo.com`och `example.com`och ett lead fyller i ett formulär på `marketo.com`navigerar sedan till `example.com` senare, sedan deras aktivitet på `marketo.com` spåras på en känd lead-post, men deras aktivitet på `example.com` är anonym. Kända leads kvarstår dock över underdomäner, så en känd lead på `www.example.com` är också ett känt lead på `info.example.com`.

Om din toppnivådomän är två delar, till exempel `.co.uk`lägger du sedan till en domainLevel-parameter i Munchkin-kodavsnittet så att koden kan spåras korrekt. Se [här](lead-tracking.md#domains) för mer information.

## Cookie

Munchkins cookie använder nyckeln `_mkto_trk`och har ett värde som följer detta mönster:

`id:561\-HYG\-937&token:_mch\-marketo.com\-1374552656411\-90718`

Munchkin-cookies är specifika för varje domännivå, det vill säga, `example.com`. Kakans standardlivscykel är 2 år (730 dagar).

## Beta

Om du vill gå med i Munchkins beta channel för dina landningssidor går du till [Admin -> Skatteklass](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/settings/enable-or-disable-treasure-chest-features) och aktivera inställningen &quot;Munchkin Beta on Landing Pages&quot;. Detta innehåller nya kodfragment i **[!UICONTROL Admin]** ->  **[!UICONTROL Munchkin]** så att du kan använda betaversionen på externa webbplatser.

## Avanmäl dig

Besökarna kan avanmäla sig helt från Munchkins spårning genom att lägga till `querystring` parametern&quot;marketo_opt_out=true&quot; till URL:en i webbläsaren. När Munchkin JavaScript identifierar den här inställningen försöker den ange en ny cookie &quot;mkto_opt_out&quot; med värdet `true`. Alla andra Marketo-spårningscookies tas bort, inga nya cookies anges och inga HTTP-begäranden görs av Munchkin när den här inställningen identifieras.
