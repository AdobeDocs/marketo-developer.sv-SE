---
title: Marketo Objects
feature: SOAP
description: Marketo Objects - översikt
exl-id: 99b9aed4-94e8-46e8-84d9-2cc5215b0c13
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '249'
ht-degree: 0%

---

# Marketo Objects

Marketo använder Marketo-objekt (MObjects) för att representera olika klasser som Program, Opportunity och OpportunityPersonRole.

## Struktur för objekt

MObjects kan vara en av tre typer: Standard, Custom eller Virtual. Standard- och Custom MObjects representerar distinkta enheter, som Lead eller Company, medan virtuella objekt, som LeadRecord, består av fält från ett eller flera objekt. Virtuella objekt är bekväma objekt som används i API:t men finns inte i Marketo-programmet.

MObjects består av:

- En liten uppsättning fasta attribut som är gemensamma för alla MObjects
   - Nödvändig typ
   - Valfri externalKey
   - skrivskyddat id, createdAt, updatedAt
- En lista med ett eller flera objektspecifika attribut, som namn/värde-par, varav vissa kan behövas. Exempel: namn på säljprojekt.
- En lista med associerade objektreferenser, som object-name plus
   - Marketo ID eller
   - Extern nyckel som ett par för attributnamn/attribut-värde.

### Externa tangenter

Externa nycklar är anpassade fält som definieras för Marketo-objekt, som lead eller säljprojekt. Namnet är fältnamnet och värdet är fältvärdet som genereras i ett externt system. **Marketo tillämpar ingen unik begränsning för dessa värden.** Det är API-användarens ansvar att se till att värdena är unika. Om en dubblett skulle inträffa använder Marketo det senast tillagda objektet. Detta liknar beteendet för standardfältet E-postadress.

### Tillgängliga API:er

| API | Kan fungera på |
|---|---|
| describeMObject | ActivityRecord, LeadRecord, Opportunity, OpportunityPersonRole |
| getMObjects | Möjligheter, möjlighetPersonRole, program |
| syncMObjects | Möjligheter, möjlighetPersonRole, program |
| deleteMObjects | Möjligheter, möjlighetPersonRole |
| listMObjects | ActivityRecord, LeadRecord, Opportunity, OpportunityPersonRole |
