---
title: "Användarhantering"
feature: REST API
description: "Utför CRUD-åtgärder på användarposter."
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '1155'
ht-degree: 0%

---


# Användarhantering

[Slutpunktsreferens för användarhantering](https://developer.adobe.com/marketo-apis/api/user/)

Marketo har en uppsättning slutpunkter för användarhantering som gör att du kan utföra CRUD-åtgärder på användarposter i Marketo. Användare skapas genom att en inbjudan skickas till en användare som sedan anger ett lösenord och får åtkomst till Marketo för första gången.

Till skillnad från andra Marketo REST API:er, när du använder API:er för användarhantering:

- Du måste använda HTTP-huvudmetoden för att skicka åtkomsttoken för autentisering. Du kan inte skicka åtkomsttoken som en frågesträngparameter. Mer information om autentisering finns i [här](authentication.md).
- Du måste välja en rollbehörighet från två olika grupper när du skapar användarrollen för [Anpassad tjänst](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/additional-integrations/create-a-custom-service-for-use-with-rest-api) för REST API:
   1. Åtkomstbehörighet för användare från [Åtkomstadministratör](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/descriptions-of-role-permissions) grupp
   1. Använd API för användarhantering från [Åtkomst-API](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/descriptions-of-role-permissions) grupp
- Svarsorgan innehåller inte det booleska attributet &quot;success&quot; som anger om ett anrop lyckades eller inte. I stället måste du utvärdera statuskoden för HTTP-svar. Om ett anrop lyckas returneras 200-statuskoden. Om ett anrop misslyckas returneras en statuskod som inte är 200 och svarstexten innehåller standardarrayen &quot;errors&quot; med felkod och beskrivande felmeddelande.
- Formatet för datetime-strängar är &quot;yyyyMMdd&#39;T&#39;HH:mm:ss.SSS&#39;t&#39;+|-hhm&quot;. Detta gäller följande attribut: createdAt, updatedAt, expirresAt.
- API-slutpunkter för användarhantering har inte prefix med &quot;/rest&quot; som andra slutpunkter.

## Fråga

Frågestöd för användarhantering innefattar möjlighet att hämta alla användare, roller och arbetsytor. Du kan även hämta en enskild användarpost med användar-ID eller roll-/ordrymdspost med användar-ID.

### Användare efter ID

The [Hämta användare efter ID](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getUserUsingGET) slutpunkten tar en enda `userid` path-parameter och returnerar en enskild användarpost för en användare som har accepterat inbjudan.

```
GET /userservice/management/v1/users/{userid}/user.json
```

```json
{
  "userid": "jamie@houselannister.com",
  "firstName": "Jamie",
  "lastName": "Lannister",
  "emailAddress": "jamie@lannister.com",
  "optedIn": false,
  "failedLogins": 0,
  "failedDeviceCode": 0,
  "isLocked": false,
  "lockedReason": null,
  "id": 0,
  "apiOnly": false,
  "userRoleWorkspaces": [
    {
      "accessRoleId": 1,
      "accessRoleName": "Admin",
      "workspaceId": 0,
      "workspaceName": "AllZones"
    },
    {
      "accessRoleId": 2,
      "accessRoleName":
      "Standard User",
      "workspaceId": 1008,
      "workspaceName": "World"
    }
  ],
  "expiresAt": "2020-12-31T08:00:00.000t+0000",
  "lastLoginAt": "2020-02-05T01:02:23.000t+0000"
}
```

### Inbjuden användare av ID

The [Hämta inbjuden användare via ID](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getInvitedUserUsingGET) slutpunkten tar en enda `userid` path-parameter och returnerar en enskild användarpost för en väntande användare (har ännu inte accepterat inbjudan).

```
GET /userservice/management/v1/users/{userid}/invite.json
```

```json
{
    "id": 25112,
    "firstName": "Jamie",
    "lastName": "Lannister",
    "emailAddress": "jamie@lannister.com",
    "userId": "jamie@lannister.com",
    "subscriptionId": 3381,
    "status": "pending",
    "expiresAt": "20200807T20:49:54.0t+0000",
    "createdAt": "20200731T20:49:54.0t+0000",
    "updatedAt": "20200731T20:49:54.0t+0000"
}
```

### Roller och arbetsytor efter ID

The [Hämta roller och arbetsytor efter ID](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getUserRolesAndWorkspacesUsingGET) slutpunkten tar en enda `userid` path-parameter och returnerar en lista med användarroller och arbetsyteposter. Svaret innehåller en array med ett objekt som innehåller roll- och arbetsytans ID och namn för den angivna användaren.

```
GET /userservice/management/v1/users/{userid}/roles.json
```

```json
[
  {
    "accessRoleId": 1,
    "accessRoleName": "Admin",
    "workspaceId": 0,
    "workspaceName": "AllZones"
  },
  {
    "accessRoleId": 2,
    "accessRoleName": "Standard User",
    "workspaceId": 1008,
    "workspaceName": "World"
  }
]
```

### Bläddra bland användare

The [Hämta användare](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getUsersUsingGET) slutpunkten returnerar en lista med alla användarposter. Valfritt `pageSize` -parametern är ett heltal som anger det maximala antalet poster som ska returneras. Standardvärdet är 20. Max är 200. Valfritt `pageOffset` är ett heltal som anger var poster ska hämtas. Kan användas med `pageSize`. Standardvärdet är 0.

```
GET /userservice/management/v1/users/allusers.json
```

```json
[
  {
    "userid": "jamie@lannister.com",
    "firstName": "Jamie",
    "lastName": "Lannister",
    "emailAddress": "jamie@houselannister.com",
    "id": 6785,
    "apiOnly": false
  },
  {
    "userid": "jeoffery@housebaratheon.com",
    "firstName": "Jeoffery",
    "lastName": "Baratheon",
    "emailAddress": "jeoffery@housebaratheon.com",
    "id": 7718,
    "apiOnly": false
  },
  {
    "userid": "rickon@housestark.com",
    "firstName": "Rickon",
    "lastName": "Stark",
    "emailAddress": "rickon@housestark.com",
    "id": 8612,
    "apiOnly": false
  }
]
```

### Bläddra bland roller

The [Hämta roller](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getRolesUsingGET) slutpunkten returnerar en lista med alla rollposter.

```
GET /userservice/management/v1/users/roles.json
```

```json
[
    {
        "id": 1,
        "name": "Admin",
        "description": "All permissions",
        "type": "system",
        "hidden": false,
        "onlyAllZones": true,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20100327T18:27:42.0t+0000"
    },
    {
        "id": 2,
        "name": "Standard User",
        "description": "All permissions except Admin",
        "type": "system",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20180423T02:33:29.0t+0000"
    },
    {
        "id": 24,
        "name": "RTP Launcher",
        "description": "Role required for launcher in RTP",
        "type": "system",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20151024T01:45:40.0t+0000",
        "updatedAt": "20171024T23:41:24.0t+0000"
    },
    {
        "id": 25,
        "name": "RTP Editor",
        "description": "Role required for editor in RTP",
        "type": "system",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20151024T01:45:40.0t+0000",
        "updatedAt": "20171024T23:41:24.0t+0000"
    },
    {
        "id": 101,
        "name": "Analytics User",
        "description": "Has access to Analytics",
        "type": "custom",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20180423T02:33:29.0t+0000"
    },
    {
        "id": 102,
        "name": "Marketing User",
        "description": "All permissions except Admin",
        "type": "custom",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20100327T18:27:42.0t+0000"
    },
    {
        "id": 103,
        "name": "Web Designer",
        "description": "Has access to Design Studio except approval permission",
        "type": "custom",
        "hidden": false,
        "onlyAllZones": false,
        "createdAt": "20100327T18:27:42.0t+0000",
        "updatedAt": "20180423T02:33:29.0t+0000"
    }
]
```

### Bläddra bland arbetsytor

The [Hämta arbetsytor](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/getWorkspacesUsingGET) slutpunkten returnerar en lista med alla arbetsyteposter.

```
GET /userservice/management/v1/users/workspaces.json
```

```json
[
  {
    "id": 1,
    "name": "Default",
    "description": "Initial workspace for Marketing Activities, Design Studio, and so on.",
    "globalViz": 0,
    "status": "active",
    "currencyInfo": null,
    "createdAt": "20160910T23:08:05.0t+0000",
    "updatedAt": "20160910T23:08:05.0t+0000"
  },
  {
    "id": 1008,
    "name": "World",
    "description": "",
    "globalViz": 0,
    "status": "active",
    "currencyInfo": null,
    "createdAt": "20181119T21:59:36.0t+0000",
    "updatedAt": "20181119T21:59:36.0t+0000"
  },
  {
    "id": 1009,
    "name": "Reproduction - US English - All Leads",
    "description": "A Workspace for recreating customer-reported problems.",
    "globalViz": 1,
    "status": "active",
    "currencyInfo": null,
    "createdAt": "20190129T23:36:37.0t+0000",
    "updatedAt": "20190129T23:36:37.0t+0000"
  },
  {
    "id": 1010,
    "name": "US",
    "description": "United States - Qualified Leads",
    "globalViz": 0,
    "status": "active",
    "currencyInfo": null,
    "createdAt": "20190322T15:55:40.0t+0000",
    "updatedAt": "20190322T15:55:40.0t+0000"
  }
]
```

## Bjud in användare

På [Adobe IMS-integrerade prenumerationer](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-with-adobe-identity/adobe-identity-management-overview), den här slutpunkten stöder inbjudan till [Användare med endast API](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/create-an-api-only-user) endast. Till inbjudan [standardanvändare](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/managing-marketo-users), använder du [Adobe API för användarhantering](https://developer.adobe.com/umapi/) i stället.

The [Bjud in användare](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/inviteUserUsingPOST) slutpunkt för att skicka en&quot;Välkommen till Marketo&quot;-inbjudan till en ny användare. E-postmeddelandetexten innehåller länken&quot;Logga in på Marketo&quot; som gör att användaren kan komma åt Marketo för första gången. För att acceptera inbjudan klickar e-postmottagaren på länken Logga in på Marketo, skapar sitt lösenord och får tillgång till Marketo. Inbjudan väntar tills godkännandeprocessen är klar och användarposten kan inte redigeras. En väntande inbjudan går ut sju dagar efter att den har skickats. Mer information om hur du hanterar användare finns [här](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/managing-marketo-users).

Parametrar skickas i begärandetexten i application/json-format.

Följande parametrar är obligatoriska:  `emailAddress`, `firstName`, `lastName, userRoleWorkspaces`. The `userRoleWorkspaces` parameter är en array med objekt som innehåller `accessRoleId` och `workspaceId` attribut.

The `userid` parametern är ett unikt strängvärde för användaridentifierare som används för användarinloggning och måste formateras som en e-postadress. Om det inte anges i begäran, värdet för `userid` blir standardvärdet i `emailAddress` parameter.

Booleskt `apiOnly` parametern anger om användaren är en [Användare med endast API](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/create-an-api-only-user). The `expiresAt` -parametern anger när inloggningen upphör och formateras med W3C ISO-8601-format (utan millisekunder). Användaren förfaller aldrig om den inte anges i begäran. The `reason` -parametern är en sträng som beskriver orsaken till användarinbjudan.

Slutpunkten returnerar värdet &quot;true&quot; om det lyckas, annars returneras ett felmeddelande.

```
POST /userservice/management/v1/users/invite.json
```

```
Content-Type: application/json
```

```json
{
  "emailAddress": "daenerys@housetargaryen.com",
  "firstName": "Daenerys",
  "lastName": "Targaryen",
  "expiresAt": "2020-12-31T23:59:59-05:00",
  "reason": "Keeper of dragons",
  "userRoleWorkspaces": [
    {
      "accessRoleId": 1,
      "workspaceId": 0
    }
  ]
}
```

```
true
```

Nedan visas ett exempel på e-postinbjudan&quot;Välkommen till Marketo&quot; som skickas till den nya användaren. Ämnesraden för e-post är&quot;Marketo Inloggningsinformation&quot;, avsändaren är e-postadressen för den endast API-användare som är associerad med [REST API Custom Service](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/additional-integrations/create-a-custom-service-for-use-with-rest-api)och mottagaren anges via parametrarna firstName, lastName och emailAddress.

![Bjud in användarens e-postadress](assets/invite-user-email.png)

Användaren accepterar e-postinbjudan genom att ange sitt lösenord två gånger och klicka på knappen SKAPA LÖSENORD. Sedan beviljas hon åtkomst till Marketo för första gången.

## Uppdatera användare

Uppdateringsstöd för användare innefattar möjlighet att uppdatera användarattribut eller ta bort en användare. Endast användare som har accepterat inbjudan kan uppdateras. Attribut skickas som parametrar i begärandetexten i application/json-format.

### Uppdatera användarattribut

På [Adobe IMS-integrerade prenumerationer](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-with-adobe-identity/adobe-identity-management-overview), stöder den här slutpunkten uppdatering av attribut för [Användare med endast API](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/create-an-api-only-user) endast. Uppdatera attribut för [standardanvändare](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/managing-marketo-users), använder du [Adobe API för användarhantering](https://developer.adobe.com/umapi/) i stället.

The [Uppdatera användarattribut](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/updateUserAttributeUsingPOST) slutpunkten tar en enda `userid` path-parameter och returnerar en enskild användarpost. Begärandetexten innehåller ett eller flera användarattribut att uppdatera: `emailAddress`, `firstName`, `lastName`, `expiresAt`.

```
POST /userservice/management/v1/users/{userid}/update.json
```

```
Content-Type: application/json
```

```json
{
  "firstName": "JAMIE",
  "lastName": "LANISTER",
  "expiresAt": "20211231T08:00:00.000t+0000"
}
```

```json
{
  "userid": "jamie@houselannister.com",
  "firstName": "JAMIE",
  "lastName": "LANISTER",
  "emailAddress": "jamie@houselannister.com",
  "optedIn": false,
  "failedLogins": 0,
  "failedDeviceCode": 0,
  "isLocked": false,
  "lockedReason": null,
  "id": 0,
  "apiOnly": false,
  "userRoleWorkspaces": [
    {
      "accessRoleId": 1,
      "accessRoleName": "Admin",
      "workspaceId": 0,
      "workspaceName": "AllZones"
    },
    {
      "accessRoleId": 2,
      "accessRoleName":
      "Standard User",
      "workspaceId": 1008,
      "workspaceName": "World"
    }
  ],
  "expiresAt": "2021-12-31T08:00:00.000t+0000"
  "lastLoginAt": "2020-02-05T01:02:23.000t+0000"
}
```

#### Ta bort användare

På [Adobe IMS-integrerade prenumerationer](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/marketo-with-adobe-identity/adobe-identity-management-overview), stöder den här slutpunkten borttagning av [Användare med endast API](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/create-an-api-only-user) endast. Ta bort [standardanvändare](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/users-and-roles/managing-marketo-users), använder du [Adobe API för användarhantering](https://developer.adobe.com/umapi/) i stället.

The [Ta bort användare](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/deleteUserUsingPOST) slutpunkten tar en enda `userid` path-parameter och tar bort motsvarande användare från instansen. Det här är en destruktiv borttagning som inte kan ångras. Om det lyckas returneras en 200-statuskod, i annat fall returneras ett felmeddelande.

```
POST /userservice/management/v1/users/{userid}/delete.json
```

#### Ta bort inbjuden användare

The [Ta bort inbjuden användare](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/deleteInvitedUserUsingPOST) slutpunkten tar en enda `userid` path-parameter och tar bort motsvarande väntande användare från instansen (användaren har ännu inte accepterat inbjudan). Det här är en destruktiv borttagning som inte kan ångras. Om det lyckas returneras en 200-statuskod, i annat fall returneras ett felmeddelande.

```
POST /userservice/management/v1/users/{userid}/invite/delete.json
```

## Uppdatera roller

Uppdateringsstöd för roller innefattar möjlighet att lägga till och ta bort roller. Attribut skickas som parametrar i begärandetexten i application/json-format.

## Lägg till roller

The [Lägg till roller](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/addRolesUsingPOST) slutpunkten tar en enda `userid` path-parameter och lägger till en eller flera användarroller till motsvarande användare. Begärandetexten innehåller en lista med ett eller flera objekt som var och en innehåller en  `accessRoleId` och `workspaceId` -attribut. Om det lyckas, hela listan med `accessRoleId/workspaceId` par för den angivna användaren returneras.

```
POST /userservice/management/v1/users/{userid}/roles/create.json
```

```
Content-Type: application/json
```

```json
[
  {
    "accessRoleId": 2,
    "workspaceId": 1008
  }
]
```

```json
[
  {
    "accessRoleId": 1,
    "accessRoleName": "Admin",
    "workspaceId": 0,
    "workspaceName": "AllZones"
  },
  {
    "accessRoleId": 2,
    "accessRoleName": "Standard User",
    "workspaceId": 1008,
    "workspaceName": "World"
  }
]
```

## Ta bort roller

The [Ta bort roller](https://developer.adobe.com/marketo-apis/api/user/#tag/User-Management/operation/deleteRolesUsingPOST) slutpunkten tar en enda `userid` path-parameter och tar bort en eller flera användarroller från motsvarande användare. Begärandetexten innehåller en lista med ett eller flera objekt som var och en innehåller en  `accessRoleId` och `workspaceId` -attribut. Om det lyckas returneras den återstående listan med par av accessRoleId/workspaceId för den angivna användaren.

```
POST /userservice/management/v1/users/{userid}/roles/delete.json
```

```
Content-Type: application/json
```

```json
[
  {
    "accessRoleId": 2,
    "workspaceId": 1008
  }
]
```

```json
[
  {
    "accessRoleId": 1,
    "accessRoleName": "Admin",
    "workspaceId": 0,
    "workspaceName": "AllZones"
  }
]
```
