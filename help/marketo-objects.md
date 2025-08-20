---
title: Marketo Objects
feature: Email Programs
description: Översikt över hur du använder Marketo Objects med Velocity-skript
source-git-commit: 3ccb27a0d184e0c1314979d404022bc4e0794f7b
workflow-type: tm+mt
source-wordcount: '518'
ht-degree: 0%

---

# Marketo Objects

Implementeringen av Marketo snabbhet kan fungera på data från flera olika källor i Marketo: Leads, Opportunity, Custom Objects, Mobile App, Mobile App Installation.

## Läser in fält

Om du vill läsa in ett fält som ska användas i ett skript måste fältet kontrolleras under motsvarande lista i skripttokenredigeraren.

Om du inte läser in ett fält och det refereras i skriptet, misslyckas skriptkörningen vid körning. Du kan dra och släppa fält från fältmenyn till skriptet. Detta aktiverar dem för inläsning och lägger till en referens till fältet vid markören.

## Affärsmöjlighet och anpassade objektlistor

När du hämtar från säljprojekt eller anpassade objekt läses endast de 10 senast uppdaterade objekten av en typ in. Antalet tillgängliga anpassade objekt kan ökas genom att följa stegen som beskrivs här. Dessa anges som en lista med namnet `<objectName>List` och ordnas från den senast uppdaterade posten. Så om du vill komma åt beloppsfältet från den affärsmöjlighet som uppdaterades senast använder du följande:

`${OpportunityList.get(0).Amount}`

I det här exemplet refererar du till objektet OpportunityList, använder metoden get för att få åtkomst till posten som är indexerad till 0 och hämtar sedan egenskapen Amount från det returnerade objektet. Om du drar ett fält från ett säljprojekt eller anpassat objekt till redigeraren hämtas fältet automatiskt från posten som är indexerad till 0.

## SFDC anpassade objektrelationer

För att vara tillgängligt för användning får ett anpassat SFDC-objekt bara ha en relation till Marketo lead. Objekt länkas ofta via både kontakt och konto, så det är viktigt att bara synkronisera objekt till Marketo när lead-/kontaktrelationen är aktiverad.

## Utlösarobjekt

När en kampanj aktiveras via utlösarna för Lägg till i säljprojekt, Affärsmöjlighet uppdateras eller läggs till i `<Custom Object Name>` blir en särskild variabel tillgänglig i skripttoken som körs i kontexten för utlösarkampanjen: `$TriggerObject `(stöds inte för `<Custom Object Name>` är Updated trigger).  Om en token som använder en `$TriggerObject`-referens används i en batchkampanj misslyckas e-postsändningen eftersom det här objektet inte är tillgängligt i batchkampanjer av något slag.  Detta är en referens till objektet som utlöste kampanjen. Objektet innehåller alla data som posten har när den används via ett annat variabelnamn.

Om en kampanj till exempel utlöstes via ett anpassat objekt för en produktorder visas ordningen som leadet lades till i variabeln `$TriggerObject`.

Här följer ett exempelskript för ett e-postmeddelande med uppföljning av beställning:

```html
<div>
<strong>Your order information:</strong>
##pull information from the Triggering Order and format it in a list
<ul>
<li>Product Ordered: $!{TriggerObject.ProductName}</li>
<li>Product Quantity: $!{TriggerObject.Quanitity}</li>
<li>Shipping Address: $!{TriggerObject.ShippingAddress}</li>
<li>Billing Address: $!{TriggerObject.BillingAddress}</li>
<li>Order Total: $!{TriggerObject.Amount}</li>
</ul>
<p><a href="$!{TriggerObject.OrderURL}">View Your Order Online</a></p>
</div>
```

Fördelen med att använda variabeln `$TriggerObject` är att du inte behöver avsätta någon kod för att avgöra vilket av de tillgängliga objekten som du vill hämta lokala data från.  Objektet bestäms av den utlösande åtgärden. Detta är det mest uttryckliga sättet att välja ett objekt att referera till och bör användas när det är tillgängligt och lämpligt.

Obs! När du använder `$TriggerObject` måste du kontrollera fälten i redigeringsrutan för att objektet ska bli tillgängligt för skriptet.

Obs 2: `$TriggerObject` fungerar bara för utlösare som har lagts till och inte för utlösare som har uppdaterats.
