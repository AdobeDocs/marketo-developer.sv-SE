---
title: "Meddelanden i appen"
feature: "Mobile Marketing"
description: "Översikt över meddelanden i appen"
source-git-commit: e8bb45a7b3bee71c3d0ab6117296a75c8959d72e
workflow-type: tm+mt
source-wordcount: '293'
ht-degree: 0%

---


# Meddelanden i appen

Om du vill använda meddelandefunktionerna i appen från Marketo måste du utföra följande steg:

1. Installera Marketo Mobile SDK enligt anvisningarna i [Mobilinstallation](installation.md).
1. Lägg till din mobilapp i Marketo enligt beskrivningen i [Lägg till en mobilapp](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app).
1. Om du vill kan du lägga till kod i din mobilapp för att hämta [Anpassade åtgärder](custom-actions.md).

När du har installerat Marketo Mobile SDK och lagt till din app i Marketo är du redo att skicka meddelanden i appen som visas när en användare öppnar appen.

Som standard utlöses meddelanden i appen när appen öppnas. Om du vill utlösa meddelanden i appen för andra händelser, till exempel när en viss sida visas eller när en viss knapp skickas, måste du lägga till anpassade åtgärder i koden. Se [Anpassade åtgärder](custom-actions.md) -avsnitt för kodexempel på detta.

## Felsökning

**Meddelandet visas inte i appen**

Marketo svarar på utlösare från appar först efter att Marketo Mobile SDK har initierats med Marketo Platform. Initieringsprocessen inträffar när du installerar och öppnar programmet för första gången. Eftersom initieringen inträffar efter det att det första programmet har öppnats aktiveras inte händelsen&quot;App Open&quot; förrän programmet öppnas en andra gång. Stäng appen och öppna den igen. Ett meddelande som utlöses av App Open visas på enheten.

Anpassade händelser utlöses av användarinteraktion när appen är öppen. Anpassade händelser identifieras av Marketo under den första sessionen.

**Aktivitetsspårning i appen**

Se till att du tilldelar en åtgärd utöver&quot;visa&quot; till en av de primära eller sekundära knapparna för att spåra tryckningsaktiviteter och för att använda grundläggande visningsfrekvenser baserat på antalet tryckningar.

Mer information finns i [Meddelanden i appen](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/in-app-messages/creating-in-app-messages/create-an-in-app-message) i vår produktdokumentation.
