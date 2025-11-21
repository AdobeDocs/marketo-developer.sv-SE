---
title: Spårning av leads
description: Lär dig bädda in Marketo Munchkin JavaScript, spåra besök och klickningar, hantera kända eller anonyma leads, domänöverskridande cookies och avanmäla dig för smarta kampanjer.
feature: Munchkin Tracking Code, Javascript
exl-id: 7ece5133-9d32-4be3-a940-4ac0310c4d8b
source-git-commit: c1b9763835b25584f0c085274766b68ddf5c7ae2
workflow-type: tm+mt
source-wordcount: '785'
ht-degree: 0%

---

# API för lead-spårning

Marketo Munchkin JavaScript gör det möjligt att spåra besök och klickningar på Marketo landningssidor och externa webbsidor. Dessa spelas in i Marketo som aktiviteter av typen&quot;Besök webbsida&quot; och&quot;Länka på webbsida&quot; som du klickat på&quot;, som sedan kan användas i utlösare och filter för smarta kampanjer och smarta listor.

## Bädda in koden

Din Marketo-instans innehåller automatiskt förkonfigurerade spårningskod för att bädda in kod på dina externa sidor som spårar aktiviteten tillbaka till din Marketo-instans. Användning av inbäddningskoden regleras av det här [licensavtalet](../munchkin-license.pdf).

Det finns tre typer av spårningskod:

1. Enkel - läser in synkront
1. Asynkron - läser in asynkront
1. Asynkron jQuery - laddar asynkront och kräver att jQuery läses in i förväg

Vi rekommenderar starkt att den asynkrona spårningskoden används för inbäddning av Munchkin på externa sidor. För att säkerställa högsta möjliga lyckade hastighet för körning bäddar du in den asynkrona spårningskoden i `<head>` på varje sida.

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

## Munchkin Behavior

Standardbeteendet för Marketo Munchkin är att göra följande vid sidinläsning:

1. Kontrollera om webbläsaren har en Munchkin-cookie och skapa en om den inte finns där.
1. Skicka en&quot;Besök webbsida&quot;-händelse till den angivna Marketo-instansen med hjälp av informationen från den aktuella sidan och webbläsaren. Detta registrerar en aktivitet till motsvarande post i Marketo.
1. Skicka händelsen&quot;Klicka på Länk på webbsida&quot; för alla användarklick som inträffar på länkar.

Beteendet för Munchkin kan ändras genom att Munchkin [konfigurationsinställningar](configuration.md) används, till exempel om en cookie skapas för alla leads när de besöker sidan med inställningen `cookieAnon` eller om klickfördröjningen ändras med inställningen `clickTime` . Det går inte att skicka besöksaktiviteten genom att ange inställningen apiOnly till true. Från och med version 162 (augusti 2022) spåras både länkar för `tel` och `mailto` förutom länkar för `http/s`.

## Kända och anonyma leads

På en leads första besök på en sida på din domän skapas en ny anonym lead-post i Marketo. Primärnyckeln för den här posten är Munchkin-cookien (`_mkto_trk`) som skapas i användarens webbläsare. Alla efterföljande webbaktiviteter i den webbläsaren registreras mot den här anonyma posten. Om du vill associeras med en känd post i Marketo måste något av följande inträffa:

- Ledningen måste besöka en Munchkin-spårad sida med en `mkt_tok`-parameter i frågesträngen från en spårad Marketo-e-postlänk.
- Lead-blanketten måste fylla i ett Marketo-formulär.
- Ett REST [Associate Lead](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/associateLeadUsingPOST)-anrop måste skickas.

När ett av dessa villkor är uppfyllt kopplas cookien och all tillhörande webbaktivitet till det kända leadet.

En ny anonym webbaktivitetspost skapas för varje enskild webbläsare, så om en lead besöker din domän för första gången med en ny dator och/eller webbläsare måste den här associationen göras igen.

## Domäner

Munchkin skapar och spårar enskilda cookies per domän, så för att kända leads ska kunna spåras över domäner måste en lead-associationshändelse inträffa för varje domän. Om jag till exempel kontrollerar två domäner, `marketo.com`, och `example.com`, och en lead fyller i ett formulär på `marketo.com`, navigerar sedan till `example.com`, spåras deras aktivitet på `marketo.com` på en känd lead-post, men deras aktivitet på `example.com` är anonym. Kända leads finns dock över underdomäner, så ett känt lead på `www.example.com` är också ett känt lead på `info.example.com`.

Om din toppnivådomän är två delar, till exempel `.co.uk`, lägger du sedan till en domainLevel-parameter i ditt Munchkin-fragment för att koden ska kunna spåras korrekt. Mer information finns [här](configuration.md#domainlevel).

## Cookie

Munchkin cookie använder nyckeln `_mkto_trk` och har ett värde som följer det här mönstret:

`id:561-HYG-937&token:_mch-marketo.com-1374552656411-90718`

eller

`id:561-HYG-937&token:_mch-marketo.com-97bf4361ef4433921a6da262e8df45a`

Munchkin cookies är specifika för varje domännivå på andra nivån, det vill säga `example.com`. Kakans standardlivscykel är 2 år (730 dagar).

## Beta

Gå till menyn [Admin -> Treasure Chest](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/settings/enable-or-disable-treasure-chest-features) och aktivera inställningen &quot;Munchkin Beta on Landing Pages&quot; för att välja betakanalen för dina landningssidor från Munchkin. Detta innehåller nya kodfragment i **[!UICONTROL Admin]** ->  **[!UICONTROL Munchkin]** -menyn så att du kan använda betaversionen på externa platser.

## Avanmäl dig

Besökare kan välja bort Munchkin-spårning helt genom att lägga till parametern `querystring` &quot;marketo_opt_out=true&quot; i URL:en i webbläsaren. När den här inställningen identifieras av Munchkin JavaScript görs ett försök att ange en ny cookie med värdet `true`. Alla andra Marketo-spårningscookies tas bort, inga nya cookies anges och inga HTTP-begäranden görs av Munchkin när den här inställningen identifieras.
