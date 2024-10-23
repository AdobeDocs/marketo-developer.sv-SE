---
title: Autentisering
feature: REST API
description: Autentiserar Marketo-användare för API-användning.
exl-id: f89a8389-b50c-4e86-a9e4-6f6acfa98e7e
source-git-commit: 2bea5277a80ca99d98eb9b774f8cbea24cb6705f
workflow-type: tm+mt
source-wordcount: '579'
ht-degree: 0%

---

# Autentisering

Marketo REST API:er autentiseras med 2-legged OAuth 2.0. Klient-ID:n och kundhemligheter tillhandahålls av anpassade tjänster som du definierar. Varje anpassad tjänst ägs av en användare med endast API:er som har en uppsättning roller och behörigheter som tillåter tjänsten att utföra specifika åtgärder. En åtkomsttoken är associerad med en enda anpassad tjänst. Utgångsdatum för åtkomsttoken är oberoende av token som är kopplade till andra anpassade tjänster som kan finnas i en instans.

## Skapa en åtkomsttoken

`Client ID` och `Client Secret` finns på menyn **[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL LaunchPoint]** genom att markera den anpassade tjänsten och klicka på **[!UICONTROL View Details]**.

![Hämta REST-tjänstinformation](assets/authentication-service-view-details.png)

![Startpunktsreferenser](assets/admin-launchpoint-credentials.png)

`Identity URL` finns i menyn **[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]** i REST API-avsnittet.

Skapa en åtkomsttoken med en HTTP GET-begäran (eller POST-begäran) på följande sätt:

```
GET <Identity URL>/oauth/token?grant_type=client_credentials&client_id=<Client Id>&client_secret=<Client Secret>
```

Om din begäran var giltig får du ett JSON-svar som liknar följande:

```json
{
    "access_token": "cdf01657-110d-4155-99a7-f986b2ff13a0:int",
    "token_type": "bearer",
    "expires_in": 3599,
    "scope": "apis@acmeinc.com"
}
```

Svarsdefinition

- `access_token` - Den token som du skickar med efterföljande anrop för att autentisera med målinstansen.
- `token_type` - OAuth-autentiseringsmetoden.
- `expires_in` - Den återstående livstiden för aktuell token i sekunder (efter vilket den är ogiltig). När en åtkomsttoken ursprungligen skapades är dess livslängd 3 600 sekunder eller en timme.
- `scope` - Den ägande användaren av den anpassade tjänst som användes för att autentisera.

## Använda en åtkomsttoken

När anrop görs till REST API-metoder måste en åtkomsttoken inkluderas i varje anrop för att anropet ska lyckas.

Det finns två metoder som du kan använda för att ta med en token i dina anrop, som en HTTP-rubrik eller som frågesträngsparameter:

1. HTTP-huvud

   `Authorization: Bearer cdf01657-110d-4155-99a7-f986b2ff13a0:int`

1. Frågeparameter

   `access_token=cdf01657-110d-4155-99a7-f986b2ff13a0:int`

   >[!IMPORTANT]
   >
   >Stöd för autentisering med frågeparametern **access_token** tas bort den 30 juni 2025. Om ditt projekt använder en frågeparameter för att skicka åtkomsttoken bör den uppdateras så att rubriken **Authorization** används så snart som möjligt. Ny utveckling bör endast använda rubriken **Authorization**.

## Tips och bästa praxis

Det är viktigt att hantera utgångsdatum för åtkomsttoken så att integreringen fungerar smidigt och förhindra att oväntade autentiseringsfel uppstår under normala operationer. När du utformar autentisering för din integrering måste du spara token och förfalloperioden som finns i identitetssvaret.

Innan du gör ett REST-anrop bör du kontrollera giltigheten för token baserat på dess återstående livslängd. Om token har gått ut kan du förnya den genom att anropa [Identitet](https://developer.adobe.com/marketo-apis/api/identity/#tag/Identity/operation/identityUsingGET)slutpunkt. Detta säkerställer att ditt REST-anrop aldrig misslyckas på grund av en token som har gått ut. Detta gör att du kan hantera fördröjningen för dina REST-anrop på ett förutsägbart sätt, vilket är avgörande för slutanvändartillvända program.

Om en token som har gått ut används för att autentisera ett REST-anrop, misslyckas REST-anropet och returnerar en felkod på 602. Om en ogiltig token används för att autentisera ett REST-anrop returneras en felkod 601. Om någon av dessa koder tas emot bör klienten förnya token genom att anropa Identity-slutpunkten.

Om du anropar identitetens slutpunkt innan din token har upphört att gälla, returneras samma token och den återstående livscykeln i svaret.

Kom ihåg att dina åtkomsttokens ägs per anpassad tjänst och inte per användare. Även om två identitetssvar kan omfatta samma användare, är åtkomsttokens och förfalloperioder oberoende av varandra om de har skapats med autentiseringsuppgifter från två olika tjänster. Tänk på detta när du har flera uppsättningar autentiseringsuppgifter i samma program. Klient-ID kan vara en användbar nyckel för att hantera dem oberoende av varandra.
