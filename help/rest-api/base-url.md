---
title: Bas-URL
feature: REST API
description: "Beskriver hur du URL:er för Marketo används."
source-git-commit: 2185972a272b64908d6aac8818641af07c807ac2
workflow-type: tm+mt
source-wordcount: '138'
ht-degree: 0%

---


# Bas-URL

The [Referens för slutpunkt](endpoint-reference.md) I dokumentationen för varje API-anrop visas REST-metod, sökväg, resurs och parametrar som måste läggas till i bas-URL:en för att en begäran ska kunna skapas.

Följande är ett exempel på en korrekt formaterad REST-URL:

`https://284-RPR-133.mktorest.com/rest/v1/lead/318581.json?fields=email,firstName,lastName`

som består av följande delar:

- Bas-URL: `https://284-RPR-133.mktorest.com/rest`
- Sökväg: `/v1/lead/`
- Resurs: `318582.json`
- Frågeparameter: `fields=email,firstName,lastName`

Bas-URL:en innehåller konto-ID (även kallat Munchkin-ID) och är därför unik för varje Marketo-prenumeration. Du hittar din bas-URL genom att logga in på Marketo och navigera till **[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]** -menyn. Det är märkt som &quot;Endpoint:&quot; under avsnittet &quot;REST API&quot; som i följande skärmbilder.

![Bas-URL-slutpunkt för webbtjänster](assets/rest-api-base-url-web-services.png)

När du har hittat bas-URL:en kopierar du den och tar med den i URL:er som du använder när du anropar någon av REST API:erna.
