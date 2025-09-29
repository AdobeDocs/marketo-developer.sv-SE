---
title: REST API
feature: REST API
description: Lär dig använda Marketo REST API, konfigurera API-användare och LaunchPoint, visa kvoter och begränsningar, autentisera med auktoriseringshuvud och hämta leads.
exl-id: 4b9beaf0-fc04-41d7-b93a-a1ae3147ce67
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '790'
ht-degree: 0%

---

# REST API

Marketo visar ett REST API som tillåter fjärrexekvering av många av systemets funktioner. Det finns många alternativ, från att skapa program till att importera leads, som ger detaljerad kontroll av en Marketo-instans.

Dessa API:er kan i allmänhet delas in i två kategorier: [Leaddatabas](https://developer.adobe.com/marketo-apis/api/mapi/) och [resurs](https://developer.adobe.com/marketo-apis/api/asset/). Med API:er för lead-databaser kan du hämta och interagera med Marketo-personposter och associerade objekttyper, som säljprojekt och företag. Tillgångs-API:er möjliggör interaktion med marknadsföringsmaterial och arbetsflödesrelaterade poster.

>[!NOTE]
>SOAP-API:t har tagits bort och kommer inte längre att vara tillgängligt efter den 31 oktober 2025. All ny utveckling ska utföras med Marketo [REST API](./rest-api.md) och befintliga tjänster ska migreras före detta datum för att undvika avbrott i tjänsten. Om du har en tjänst som använder SOAP API kan du läsa SOAP API [Migreringshandbok](../soap-api/migration.md) för information om hur du migrerar.
>

- **Daglig kvot:** Prenumerationer tilldelas 50 000 API-anrop per dag (som återställs dagligen med 12:00AM CST). Du kan öka din dagliga kvot med din kontohanterare.
- **Hastighetsgräns:** API-åtkomst per instans är begränsad till 100 anrop per 20 sekunder.
- **Samtidighetsgräns:**  Max tio samtidiga API-anrop.

Storleken på standardanrop är begränsad till en URI-längd på 8 kB och en kroppsstorlek på 1 MB, även om brödtexten kan vara 10 MB för våra större API:er. Om det finns ett fel i ditt anrop returnerar API vanligtvis fortfarande statuskoden 200, men JSON-svaret innehåller en &quot;success&quot;-medlem med värdet `false` och en matris med fel i &quot;errors&quot;-medlemmen. Mer information om fel [här](error-codes.md).

## Komma igång

Följande steg kräver administratörsbehörighet i din Marketo-instans.

Vid första samtalet till Marketo får du en lead-post. Om du vill börja arbeta med Marketo måste du ha API-autentiseringsuppgifter för att kunna göra autentiserade anrop till din instans. Logga in på din instans och gå till **[!UICONTROL Admin]** -> **[!UICONTROL Users and Roles]**.

![Administratörsanvändare och roller](assets/admin-users-and-roles.png)

Klicka på fliken **[!UICONTROL Roles]** och sedan på Ny roll och tilldela rollen i API-gruppen minst behörigheten Skrivskyddad lead (eller Skrivskyddad person). Ge den ett beskrivande namn och klicka på **[!UICONTROL Create]**.

![Ny roll](assets/new-role.png)

Gå tillbaka till fliken [!UICONTROL Users] och klicka på **[!UICONTROL Invite New User]**. Ge användaren ett beskrivande namn som anger att det är en API-användare och en e-postadress och klicka på **[!UICONTROL Next]**.

![Ny användarinformation](assets/new-user-info.png)

Kontrollera sedan alternativet [!UICONTROL API Only] och tilldela användaren den API-roll som du skapade och klicka på **[!UICONTROL Next]**.

![Nya användarbehörigheter](assets/new-user-permissions.png)

Klicka på **[!UICONTROL Send]** om du vill slutföra användarskapandeprocessen.

![Nytt användarmeddelande](assets/new-user-message.png)

Gå sedan till menyn [!UICONTROL Admin] och klicka på **[!UICONTROL LaunchPoint]**.

![Startpunkt](assets/admin-launchpoint.png)

Klicka på menyn **[!UICONTROL New]** och välj **[!UICONTROL New Service]**. Ge tjänsten ett beskrivande namn och välj **[!UICONTROL Custom]** i listrutan [!UICONTROL Service]. Ge den en beskrivning, välj sedan din nya användare i listrutan [!UICONTROL API Only User] och klicka på **[!UICONTROL Create]**.

![Ny startpunktstjänst](assets/admin-launchpoint-new-service.png)

Klicka på **[!UICONTROL View Details]** om du vill att den nya tjänsten ska få åtkomst till klient-ID och klienthemlighet. För tillfället kan du klicka på knappen **[!UICONTROL Get Token]** för att generera en åtkomsttoken som är giltig i en timme. Spara variabeln i en anteckning tills vidare.

![Hämta token](assets/get-token.png)

Gå sedan till menyn **[!UICONTROL Admin]** och till **[!UICONTROL Web Services]**.

![Webbtjänster](assets/admin-web-services.png)

Leta reda på [!UICONTROL Endpoint] i rutan REST API och spara i en anteckning för tillfället.

![REST-slutpunkt](assets/admin-web-services-rest-endpoint-1.png)

När anrop görs till REST API-metoder måste en åtkomsttoken inkluderas i varje anrop för att anropet ska lyckas. Åtkomsttoken måste skickas som en HTTP-rubrik.

```
Authorization: Bearer cdf01657-110d-4155-99a7-f986b2ff13a0:int
```

>[!IMPORTANT]
>
>Stöd för autentisering med frågeparametern **access_token** tas bort den 30 juni 2025. Om ditt projekt använder en frågeparameter för att skicka åtkomsttoken bör den uppdateras så att rubriken **Authorization** används så snart som möjligt. Ny utveckling bör endast använda rubriken **Authorization**.

Öppna en ny flik i webbläsaren och ange följande, med lämplig information för att anropa [Hämta leads efter filtertyp](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadsByFilterUsingGET)

```
<Your Endpoint URL>/rest/v1/leads.json?&filterType=email&filterValues=<Your Email Address>
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

Var och en av dina API-användare rapporteras separat i API-användningsrapporten, så om du delar upp dina webbtjänster efter användare kan du enkelt ta hänsyn till hur de olika integreringarna används. Om antalet API-anrop till din instans överstiger gränsen och leder till att efterföljande anrop misslyckas, kan du med den här metoden ta hänsyn till volymen från varje tjänst och låta dig utvärdera hur problemet kan lösas. Se din användning genom att gå till **[!UICONTROL Admin]** -> **[!UICONTROL Integration]** > **[!UICONTROL Web Services]** och klicka på antalet samtal under de senaste sju dagarna.
