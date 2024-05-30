---
title: "Strömposition"
feature: SOAP
description: "Översikt över ångposition"
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '138'
ht-degree: 0%

---


# Strömposition

Strömpositionselement innehåller en positionsreferens för en eller flera logiska strömmar med tidssekvensdata. Positionsreferensen kan vara en ungefärlig extern specifikation, som en tidsstämpel, eller en ogenomskinlig intern specifikation av positionen som returnerats av ett tidigare API-anrop. Strömpositioner kan definieras som komplexa, flerelementstyper eller vara en sträng.

Strömpositionen används för att hämta data i batchar och gör att anroparen kan paginera genom resultatet. Strömpositionen som skickas i en API-begäran är värdet för strömpositionen som returnerades i det föregående svaret. Det rekommenderas inte att strömpositionen som returneras från det tidigare API-anropet ändras, vilket kan leda till oväntat API-beteende.

## API:er som stöder strömposition

- [getCustomObjects](getcustomobjects.md)
- [getLeadChanges](getleadchanges.md)
- [getLeadActivity](getleadactivity.md)
- [getMObjects](getmobjects.md)
- [getMultipleLeads](getmultipleleads.md)

## Enkel strömposition

```
<streamPosition>8UJZetaMb1V6uUZl+L7DcPP2jG+PMmtpF</streamPosition>
```

## Komplex strömposition

```xml
<startPosition>
  <latestCreatedAt  />
  <oldestCreatedAt>2013-08-01T00:13:13+00:00</oldestCreatedAt>
  <activityCreatedAt  />
  <offset>ID:1086173</offset>
</startPosition>
```
