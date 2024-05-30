---
title: "REST API"
feature: REST API
description: "REST API overview"
source-git-commit: 2185972a272b64908d6aac8818641af07c807ac2
workflow-type: tm+mt
source-wordcount: '643'
ht-degree: 0%

---


# REST API

Marketo visar ett REST API som tillåter fjärrexekvering av många av systemets funktioner. Det finns många alternativ, från att skapa program till att importera leads, som ger detaljerad kontroll av en Marketo-instans.

Dessa API:er kan i allmänhet delas in i två kategorier: [Lead-databas](https://developer.adobe.com/marketo-apis/api/mapi/)och [Tillgång](https://developer.adobe.com/marketo-apis/api/asset/). Med API:er för lead-databaser kan du hämta och interagera med Marketo-personposter och associerade objekttyper som säljprojekt och företag. Tillgångs-API:er möjliggör interaktion med marknadsföringsmaterial och arbetsflödesrelaterade poster.

- **Daglig kvot:** Prenumerationer tilldelas 50 000 API-anrop per dag (som återställs dagligen med 02:00 CST). Du kan öka din dagliga kvot med din kontohanterare.
- **Hastighetsgräns:** API-åtkomst per instans begränsad till 100 anrop per 20 sekunder.
- **Samtidighetsgräns:**  Max tio samtidiga API-anrop.

Storleken på standardanrop är begränsad till en URI-längd på 8 kB och en kroppsstorlek på 1 MB, även om brödtexten kan vara 10 MB för våra större API:er. Om det uppstår ett fel i ditt anrop returnerar API vanligtvis fortfarande statuskoden 200, men JSON-svaret innehåller en &quot;success&quot;-medlem med värdet `false`och en array med fel i medlemmen &quot;errors&quot;. Mer om fel [här](error-codes.md).

## Komma igång

Följande steg kräver administratörsbehörighet i din Marketo-instans.

När du ringer till Marketo får du en leadpost. Om du vill börja arbeta med Marketo måste du ha API-autentiseringsuppgifter för att kunna göra autentiserade anrop till din instans. Logga in på din instans och gå till **[!UICONTROL Admin]** -> **[!UICONTROL Users and Roles]**.

![Administratörsanvändare och roller](assets/admin-users-and-roles.png)

Klicka på [!UICONTROL Roles] och sedan Ny roll och tilldela minst behörigheten&quot;Skrivskyddad lead&quot; (eller&quot;Skrivskyddad person&quot;) till rollen i Access API-gruppen. Ge den ett beskrivande namn och klicka på [!UICONTROL Create].

![Ny roll](assets/new-role.png)

Gå tillbaka till fliken Användare och klicka på Bjud in ny användare. Ge användaren ett beskrivande namn som anger att det är en API-användare och en e-postadress och klicka på **[!UICONTROL Next]**.

![Ny användarinformation](assets/new-user-info.png)

Markera sedan alternativet Endast API och tilldela användaren den API-roll som du skapade och klicka på **[!UICONTROL Next]**.

![Nya användarbehörigheter](assets/new-user-permissions.png)

Klicka på **[!UICONTROL Send]**.

![Nytt användarmeddelande](assets/new-user-message.png)

Gå sedan till Admin-menyn och klicka på **[!UICONTROL LaunchPoint]**.

![Startpunkt](assets/admin-launchpoint.png)

Klicka på menyn Nytt och välj [!UICONTROL New Service]. Ge tjänsten ett beskrivande namn och välj Anpassad i listrutan Tjänst. Ge den en beskrivning och välj sedan den nya användaren i listrutan Endast API-användare och klicka på [!UICONTROL Create].

![Ny startpunktstjänst](assets/admin-launchpoint-new-service.png)

Klicka på Visa detaljer för den nya tjänsten för att få åtkomst till klient-ID och klienthemlighet. Nu kan du klicka på [!UICONTROL Get Token] för att generera en åtkomsttoken som är giltig i en timme. Spara variabeln i en anteckning tills vidare.

![Hämta token](assets/get-token.png)

Gå sedan till Admin-menyn och till **[!UICONTROL Web Services]**.

![Webbtjänster](assets/admin-web-services.png)

Hitta slutpunkten i rutan REST API och spara den i en anteckning för tillfället.

![REST-slutpunkt](assets/admin-web-services-rest-endpoint-1.png)

Öppna en ny flik i webbläsaren och ange följande med lämplig information för att ringa [Hämta leads efter filtertyp](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadsByFilterUsingGET):

```
<Your Endpoint URL>/rest/v1/leads.json?access_token=<Your Access Token>&filterType=email&filterValues=<Your Email Address>
```

Om du inte har någon lead-post med din e-postadress i databasen kan du ersätta den med en som du vet finns där. Tryck på Retur i URL-fältet så får du tillbaka ett JSON-svar som påminner om detta:

```json
{
    "requestId":"c493#1511ca2b184",
    "result":[
       {
           "id":1,
           "updatedAt":"2015-08-24T20:17:23Z",
           "lastName":"Elkington",
           "email":"developerfeedback@marketo.com",
           "createdAt":"2013-02-19T23:17:04Z",
           "firstName":"Kenneth"
        }
    ],
    "success":true
}
```

## API-användning

Var och en av dina API-användare rapporteras separat i API-användningsrapporten, så om du delar upp dina webbtjänster efter användare kan du enkelt ta hänsyn till hur de olika integreringarna används. Om antalet API-anrop till din instans överstiger gränsen och leder till att efterföljande anrop misslyckas, kan du med den här metoden ta hänsyn till volymen från varje tjänst och låta dig utvärdera hur problemet kan lösas. Se hur du använder **[!UICONTROL Admin]** -> **[!UICONTROL Integration]** > **[!UICONTROL Web Services]** och klicka på antalet samtal de senaste sju dagarna.
