---
title: Hämta Swagger-definitioner
feature: REST API, Programs
description: Hämta Swagger-definitionsfiler för lokal användning.
source-git-commit: 85062243d57a3fc6d15251163e926495858edf2a
workflow-type: tm+mt
source-wordcount: '170'
ht-degree: 0%

---

# Hämta Swagger-definitioner

[Swagger](https://swagger.io/) eller [OpenAPI](https://www.openapis.org/) definitioner är datafiler som beskriver alla metoder och parametrar i ett REST API. Du kan hämta och använda dessa datafiler lokalt som din egen API-referens.

Om du vill använda definitionerna för Marketo Engage Swagger/OpenAPI finns följande `host` -fältet i varje dokument måste uppdateras för att matcha REST API-värdnamnet från din Marketo Engage-instans.

Hämta först den OpenAPI-definition som du vill använda:

* [Tillgång](assets/swagger-asset.json)
* [Lead](assets/swagger-mapi.json)
* [Identitet](assets/swagger-identity.json)
* [Användarhantering](assets/swagger-user.json)

Hämta sedan ditt REST API-värdnamn från Marketo Admin. Gå till _Administratör_-> _Webbtjänster_ i Marketo Engage och kopiera värdnamnet från slutpunktsfältet. The `hostname` är strängen mellan protokollet, `https://`och `/rest`som ser ut som `AAA-999-AAA.mktorest.com`

Öppna OpenAPI-filen i en textredigerare och hitta `host` i JSON och ändra värdet till REST API-värdnamnet: `"host":"localhost:8080"` till `"host":"AAA-999-AAA.mktorest.com"`.

När du har sparat definitionsfilen kan du köra den i Swagger-gränssnittsinstansen eller något annat OpenAPI-program.
