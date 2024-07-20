---
title: Anpassade åtgärder
feature: Mobile Marketing
description: Översikt över anpassade åtgärder
exl-id: 8c2698ce-4e39-4b2b-9d36-0864c55be17a
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '291'
ht-degree: 0%

---

# Anpassade åtgärder

Du kan spåra användarinteraktion genom att skicka anpassade åtgärder. När din mobilapp anropar Marketo SDK för att skicka en anpassad åtgärd sparas den anpassade åtgärden först på enheten. Marketo SDK kontrollerar sedan om det finns tillräcklig internetanslutning innan den anpassade åtgärden skickas. Det innebär att det kan uppstå en fördröjning mellan den tidpunkt då den anpassade åtgärden skickas och den tidpunkt då den tas emot av Marketo.

Anpassade åtgärder kan användas som utlösare och filter i smarta kampanjer. Mer information finns i [Mobilappsaktivitet](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/flow-actions/triggers-and-filters-for-mobile-smart-campaigns).

## Skicka anpassade åtgärder på iOS

Skicka anpassad åtgärd.

>[!BEGINTABS]

>[!TAB Mål C]

```
Marketo *sharedInstance = [Marketo sharedInstance];
[sharedInstance reportAction:@"Login" withMetaData:nil];
```

>[!TAB Swift]

```
sharedInstance.reportAction("Login", withMetaData:nil);
```

>[!ENDTABS]

Skicka anpassad åtgärd med metadata.

>[!BEGINTABS]

>[!TAB Mål C]

```
MarketoActionMetaData *meta = [[MarketoActionMetaData alloc] init];
[meta setType:@"Shopping"];
[meta setDetails:@"RedShirt"];
[meta setLength:20];
[meta setMetric:30];

[sharedInstance reportAction:@"Bought Shirt" withMetaData:meta];
```

>[!TAB Swift]

```
let meta = MarketoActionMetaData()
meta.setType("Shopping");
meta.setDetails("RedShirt");
meta.setLength(20);
meta.setMetric(30);

sharedInstance.reportAction("Bought Shirt", withMetaData:meta);
```

>[!ENDTABS]

Rapportera alla åtgärder direkt (skicka alla sparade åtgärder).

>[!BEGINTABS]

>[!TAB Mål C]

```
[sharedInstance reportAll];
```

>[!TAB Swift]

```
sharedInstance.reportAll();
```

>[!ENDTABS]

## Skicka anpassade åtgärder på Android

1. Skicka anpassad åtgärd.

   ```
   Marketo.reportAction("Login", null);
   ```

1. Skicka anpassad åtgärd med metadata.

   ```
   MarketoActionMetaData meta = new MarketoActionMetaData();
   meta.setActionType("Shopping");
   meta.setActionDetails("RedShirt");
   meta.setActionLength("20");
   meta.setActionMetric("30");
   
   Marketo.reportAction("Bought Shirt", meta);
   ```

1. Rapportera alla anpassade åtgärder direkt (skicka alla sparade åtgärder).

   ```
   Marketo.reportAll();
   ```

## Felsöka anpassade åtgärder

Det är enkelt att konfigurera anpassade åtgärder för mobiler, men det finns begränsningar för hur många tecken du kan skicka från Mobile SDK till Marketo. Se till att alla anpassade åtgärder som rapporterar tillbaka till Marketo via mobil-SDK är kortare än 20 tecken.

**Obs! Om flera användare använder en delad enhet:** När en användare loggar in i en mobilapp som är integrerad med Marketo SDK görs det första anropet för att associera leadet med appinstallationen. När det här anropet har slutförts kan ytterligare användaraktiviteter i appen visas i leadets aktivitetslogg. Observera, eftersom detta är ett asynkront anrop, om det finns anpassade åtgärder som loggas omedelbart efter inloggningen, kan de kopplas till användaren som tidigare var inloggad tills associationsanropet lyckas.
