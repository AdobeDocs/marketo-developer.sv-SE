---
title: Aktivera djupa länkar
feature: Mobile Marketing
description: Lär dig hur du aktiverar djupa länkar i din app för push-meddelanden från Marketo med anpassade URI-scheman, med hjälp av guiderna för iOS, Android och PhoneGap samt bästa praxis.
exl-id: c3647416-d81d-4f15-b660-bcb3e54cb9bc
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '397'
ht-degree: 0%

---

# Aktivera djupa länkar

Med hjälp av djuplänkning kan du dirigera om personer till specifikt innehåll (resurser) i din app. När en person till exempel klickar på ett mobilt push-meddelande som annonserar en lila t-shirt kan du öppna appen direkt till det lila t-shirtinnehållet (i stället för hemsidan).

Så här fungerar processen:

1. Marketo-användaren placerar en anpassad URI i Tap-åtgärden för sitt push-meddelande.
1. När en person trycker på push-meddelandet på sin enhet utlöser Marketo MME SDK en händelse med den anpassade URI:n.
1. Din app bearbetar sedan händelsen och dirigerar om personen till rätt innehåll i din app.

Detta kräver att du definierar en anpassad URI-struktur för appen, registrerar schemat i appens manifest och sedan lägger till kod för att bearbeta djupa länkhändelser och dirigera till rätt plats i appen.

Information om iOS finns i Apple-dokumentationen om [Definiera ett anpassat URL-schema för din app](https://developer.apple.com/documentation/xcode/defining-a-custom-url-scheme-for-your-app).

Information om Android finns i Google-dokumentationen om [Aktivera djuplänkar för appinnehåll](https://developer.android.com/training/app-links/deep-linking).

För PhoneGap-appar är djuplänkning inte lika enkelt som för inbyggda iOS- eller Android-appar, men det finns plugin-program som gör att din hybridapp kan svara på anpassade URL-scheman för djuplänkar och universella/applänkar på både iOS och Android. Överväg [dessa plugin-program](https://cordova.apache.org/plugins/?q=deeplink).

När du har aktiverat djuplänkning i din app kan du dela dina anpassade URI:er med dina Marketo-användare så att de kan infoga dem i Tryck-åtgärd för push-meddelanden.

Marketo använder en fördefinierad URI-struktur när testenheter konfigureras. Mer information finns under Testenheter i [installationshandboken](installation.md).

## Metodtips för att definiera en URI-struktur

Om ert varumärke har en befintlig mobilwebbplats är det bästa sättet att följa dess URL-struktur även för djuplänkens URI. Om till exempel `https://myappname.com/products/purple-shirt` är din webbplatsadress för produkten i fråga, skulle `myappname://products/purple-shirt` vara en bra URI-struktur för djup länk som kan användas i din app.

I allmänhet bör era scheman vara unika för ert varumärke. Det finns för närvarande inga regler för att göra scheman unika över hela världen, men ett sätt att se till att dina scheman är unika är att vända ditt domännamn (till exempel `org.companyname`).
