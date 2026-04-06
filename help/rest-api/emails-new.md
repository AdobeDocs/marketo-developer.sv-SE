---
title: E-post
feature: REST API
description: Använd Marketo Asset REST API för att fråga efter, skapa, uppdatera, klona, ta bort, godkänna och inspektera beroenden för e-postresurser.
exl-id: b41a3ae5-2b25-4103-84b4-320fc2c44bd6
source-git-commit: 0e0a3e5a08e81f349044cbc327d1aba963ab30e4
workflow-type: tm+mt
source-wordcount: '497'
ht-degree: 0%

---

# E-post

[Referens för e-postslutpunkt](https://developer.adobe.com/marketo-apis/api/asset/#tag/Emails_New)

E-postmeddelanden är resursposter som definierar meddelandemetadata, innehållskonfiguration, inställningar och godkännandetillstånd.

## Åtkomst

Slutpunkterna som beskrivs i den här artikeln kräver en åtkomsttoken:

```text
?access_token=<access_token>
```

Begäranden kräver även rubriken `x-app-type`:

```text
x-app-type: <app-type>
```

## Fråga

Du kan hämta e-postmetadata per resurs `id` eller med filterslutpunkten.

### Efter ID

#### Begäran

```text
GET /rest/asset/v2/email/{id}
```

#### Svar

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "7b3c#1900ab12cd3",
  "result": [
    {
      "id": "1017",
      "name": "Spring Launch Email",
      "description": "Main announcement email",
      "status": "draft"
    }
  ]
}
```

### Filter

Filterslutpunkten stöder sökning i en arbetsyta och begränsar resultaten med ytterligare frågeparametrar.

`workspaceId` krävs.

Frågeparametrar som stöds:

| Parameter | Beskrivning |
| - | - |
| `workspaceId` | Den obligatoriska arbetsyteidentifierare som används för att omsluta filterbegäran. |
| `folderId` | Filtrerar resultatet till en enda mapp. |
| `folderIds` | Upprepad parameter som filtrerar resultat till flera mappar. |
| `status` | Upprepade parametrar som filtrerar efter en eller flera e-poststatusar. |
| `pageIndex` | Nollbaserat sidindex för sidnumrerade resultat. |
| `pageSize` | Maximalt antal resultat som ska returneras per sida. |
| `createdBy` | Filtrerar resultat efter den användare som skapat filen. |
| `createdAtStart` | Returnerar resurser som skapats på eller efter den här tidsstämpeln. |
| `createdAtEnd` | Returnerar resurser som skapats på eller före den här tidsstämpeln. |
| `modifiedBy` | Filtrerar resultatet efter den senaste användaren som ändrat. |
| `modifiedAtStart` | Returnerar resurser som ändrats på eller efter den här tidsstämpeln. |
| `modifiedAtEnd` | Returnerar resurser som ändrats på eller före den här tidsstämpeln. |
| `name` | Filtrerar resultat efter e-postnamn. |
| `sortKey` | Markerar fältet som används för att sortera resultat. |
| `sortOrder` | Anger sorteringsriktning. |
| `isCreatedByMe` | Begränsar resultat till resurser som har skapats av den aktuella användaren. |
| `isModifiedByMe` | Begränsar resultat till resurser som senast ändrades av den aktuella användaren. |
| `templateId` | Filtrerar resultat efter mallidentifierare. |
| `scriptEngine` | Filtrerar resultat efter skriptmotorkonfigurationen. |
| `isValueNonNullable` | Filter som baseras på om värdet inte kan vara null. |
| `includeArchived` | Inkluderar arkiverade resurser i resultatet. |

#### Begäran

```text
GET /rest/asset/v2/email/filter?workspaceId=1001&name=Spring%20Launch&status=draft&status=approved&pageIndex=0&pageSize=20
```

#### Svar

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "5dd1#1900ab13011",
  "result": [
    {
      "id": "1017",
      "name": "Spring Launch Email",
      "status": "draft"
    }
  ]
}
```

Använd parametern `name` när du behöver hitta ett e-postmeddelande efter namn.

## Skapa

Skapa ett e-postmeddelande genom att skicka en JSON-nyttolast. `name`, `appData` och `headers` krävs. `headers.subject` krävs och `appData` måste innehålla minst en av `folderId`, `workspaceId` eller `programId`.

### Begäran

```text
POST /rest/asset/v2/email
Content-Type: application/json
```

```json
{
  "name": "Spring Launch Email",
  "description": "Main announcement email",
  "appData": {
    "workspaceId": "1001",
    "folderId": "2002",
    "editorType": "email"
  },
  "headers": {
    "subject": "Introducing the Spring Launch",
    "fromName": "Marketing Team",
    "fromEmail": "marketing@example.com",
    "replyEmail": "reply@example.com",
    "preheader": "See what changed this quarter",
    "ccEmails": [
      "owner@example.com"
    ]
  },
  "settings": {
    "isOperational": false,
    "isTextOnly": false,
    "isWebPageView": true,
    "enableUrlTracking": true
  },
  "templateId": "3003"
}
```

### Svar

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "238c#1900ab1313f",
  "result": [
    {
      "id": "1017",
      "name": "Spring Launch Email",
      "status": "draft"
    }
  ]
}
```

Begärandetexten kan även innehålla `data`, `editorContext`, `themeId`, `appType` och `status`.

### Skapa fält

* `appData` identifierar var e-postmeddelandet skapas och hur det redigeras.
* `headers` innehåller meddelandehuvudets värden, inklusive ämne, avsändare, svarsadress, preheader och valfria CC-mottagare.
* `settings` styr användnings- och återgivningsbeteenden som textläge, webbsidesvy och URL-spårning.
* `templateId` identifierar den e-postmall som används när e-postmeddelandet skapas.

## Uppdatera

Uppdatera ett e-postmeddelande efter resurs-ID. Begärandetexten använder `UpdateEmailRequest`-schemat och alla egenskaper är valfria, så du kan bara skicka de fält som du vill ändra.

### Begäran

```text
POST /rest/asset/v2/email/{id}/update
Content-Type: application/json
```

```json
{
  "description": "Updated announcement email",
  "headers": {
    "subject": "Spring Launch Is Live",
    "preheader": "Read the latest release notes"
  },
  "settings": {
    "enableUrlTracking": true,
    "isWebPageView": true
  }
}
```

### Svar

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "9fd3#1900ab13210",
  "result": [
    {
      "id": "1017"
    }
  ]
}
```

## Hantera läge

E-postmeddelanden använder ett utkast och en godkänd livscykel. Använd slutpunkten för tillståndsövergång för att godkänna ett e-postmeddelande, avgodkänna det, ta bort ett utkast eller skapa ett nytt utkast.

Giltiga `action`-värden är:

* `approve`
* `unapprove`
* `discard`
* `create_draft`

### Begäran

```text
POST /rest/asset/v2/email/state/transition
Content-Type: application/json
```

### Svar

```json
{
  "contentId": "1017",
  "action": "approve"
}
```

## Klona

Använd klonslutpunkten för att skapa en kopia av ett befintligt e-postmeddelande.

### Begäran

```text
POST /rest/asset/v2/email/clone
Content-Type: application/json
```

### Svar

```json
{
  "assetId": "1017",
  "newAsset": {
    "name": "Spring Launch Email Copy",
    "description": "Cloned from Spring Launch Email"
  }
}
```

## Ta bort

Ta bort ett e-postmeddelande efter resurs-ID.

### Begäran

```text
POST /rest/asset/v2/email/{id}/delete
Content-Type: application/json
```

Den här slutpunkten tar resursen `id` i sökvägen och definierar inte någon begärandetext.

## Används av

Använd slutpunkten `usedby` för att hämta resurser som refererar till ett visst e-postmeddelande.

### Begäran

```text
POST /rest/asset/v2/email/usedby
Content-Type: application/json
```

### Svar

```json
{
  "assetId": "1017",
  "pageIndex": 0,
  "pageSize": 20,
  "type": "all"
}
```

## Anteckningar

Frågeslutpunkter returnerar metadata för resursen. Använd slutpunktsreferensen för det fullständiga schemat med tillgängliga fält och alla miljöspecifika egenskaper.
