---
title: "Referens för slutpunkt"
feature: REST API
description: "Marketo API-slutpunktsreferenser"
source-git-commit: 2454f126dc4275697ef6773420453ad8853eae73
workflow-type: tm+mt
source-wordcount: '216'
ht-degree: 0%

---


# Referens för slutpunkt

- [Tillgång](https://developer.adobe.com/marketo-apis/api/asset/)
- [Identitet](https://developer.adobe.com/marketo-apis/api/identity/)
- [Lead-databas](https://developer.adobe.com/marketo-apis/api/mapi/)
- [Användarhantering](https://developer.adobe.com/marketo-apis/api/user/)

## Referens för slutpunkt

Marketo använder Swagger för att tillhandahålla en formell definition av det publika gränssnittet för sina REST API:er. Swagger har en omfattande definitionsmodell för URL-strukturer, frågemodeller och svarsmodeller och har ett utvecklat ekosystem av verktyg för användning med API-interaktion, testning och klientgenerering.

Slutpunktsreferensen använder [Swagger-UI](https://swagger.io/tools/swagger-ui/) JavaScript-paket som genererar referenssidorna på klientsidan. Varje publik slutpunkt listas och ger strukturen för svarsmodellen, obligatoriska frågeparametrar och förfrågningsmodellen om det behövs.

## Använda Marketo Swagger-definitioner

Swagger-standarden kräver att en värd anges eller att värden genereras av den värd som hanterar filen. Det är viktigt att korrigera värden i definitionen innan du använder befintliga verktyg, eftersom Marketo tillhandahåller en tom värdparameter med definitionen. REST API-värden för varje Marketo-instans är unik och följer mönstret:

`{Munchkin ID}.mktorest.com`

## Resurs-API:er

API:erna för Marketo-resurser använder `application/x-www-url-formencoded` formatparametrar i begäranden för slutpunkter som kräver en POST-metod. I vissa fall, till exempel mappparametrar, kan värdet för parametern vara en JSON-array eller ett JSON-objekt. Dessa parametrar anges i slutpunktsreferensen.
