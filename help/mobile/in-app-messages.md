---
title: Meddelanden i appen
feature: Mobile Marketing
description: Konfigurera Marketo In-App Messages med Mobile SDK, konfigurera anpassade händelseutlösare, spåra knackningsaktivitet och åtgärda problem med att öppna första appen.
exl-id: 73c9f862-d154-4b37-94ce-92311aa756e8
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '312'
ht-degree: 0%

---

# Meddelanden i appen

Om du vill använda meddelandefunktionerna i appen från Marketo måste du utföra följande steg:

1. Installera Marketo Mobile SDK enligt anvisningarna i [Mobile Installation](installation.md).
1. Lägg till din mobilapp i Marketo enligt beskrivningen i [Lägg till en mobilapp](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app).
1. Du kan också lägga till kod i din mobilapp för att hämta [anpassade åtgärder](custom-actions.md).

När du har installerat Marketo Mobile SDK och lagt till din app i Marketo är du redo att skicka meddelanden i appen som visas när en användare öppnar appen.

Som standard utlöses meddelanden i appen när appen öppnas. Om du vill utlösa meddelanden i appen för andra händelser, till exempel när en viss sida visas eller när en viss knapp skickas, måste du lägga till anpassade åtgärder i koden. I avsnittet [Anpassade åtgärder](custom-actions.md) finns kodexempel för detta.

## Felsökning

**Meddelande i appen visas inte**

Marketo svarar på utlösare från appar först när Marketo Mobile SDK har initierats med Marketo Platform. Initieringsprocessen inträffar när du installerar och öppnar programmet för första gången. Eftersom initieringen inträffar efter det att det första programmet har öppnats aktiveras inte händelsen&quot;App Open&quot; förrän programmet öppnas en andra gång. Stäng appen och öppna den igen. Ett meddelande som utlöses av App Open visas på enheten.

Anpassade händelser utlöses av användarinteraktion när appen är öppen. Anpassade händelser identifieras av Marketo under den första sessionen.

**Aktivitetsspårning i appen**

Se till att du tilldelar en åtgärd utöver&quot;visa&quot; till en av de primära eller sekundära knapparna för att spåra tryckningsaktiviteter och för att använda grundläggande visningsfrekvenser baserat på antalet tryckningar.

Mer information finns i avsnittet [Meddelanden i appen](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/in-app-messages/creating-in-app-messages/create-an-in-app-message) i vår produktdokumentation.
