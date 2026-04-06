---
title: Fragment
feature: REST API
description: Använd Marketo Asset REST API för att fråga efter, skapa, uppdatera, klona, ta bort, godkänna och inspektera beroenden för fragment.
exl-id: 9dd532d1-1dd7-4581-86dd-1943fab66cbb
source-git-commit: 0e0a3e5a08e81f349044cbc327d1aba963ab30e4
workflow-type: tm+mt
source-wordcount: '272'
ht-degree: 0%

---

# Fragment

Fragment är återanvändbara innehållsresurser som kan refereras av andra resurser, till exempel e-postmeddelanden.

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

Du kan hämta fragmentmetadata efter resurs-ID eller med filterslutpunkten.

### Efter ID

#### Begäran

```text
GET /rest/asset/v2/fragment/{id}
```

#### Svar

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "fa0f#1900ab15001",
  "result": [
    {
      "id": "83",
      "name": "Hero Banner Fragment",
      "description": "Reusable hero block",
      "status": "approved"
    }
  ]
}
```

### Filter

Filterslutpunkten stöder sökning i en arbetsyta och begränsar resultaten med ytterligare frågeparametrar. `workspaceId` krävs.

göra: gör detta till en tabell
Följande filter stöds: `folderId`, repeterad `folderIds`, repeterad `status`, `pageIndex`, `pageSize`, `createdBy`, `createdAtStart`, `createdAtEnd`, `modifiedBy`, `modifiedAtStart`, `modifiedAtEnd`, `name`, `fragmentType`, `sortKey`, `sortOrder`, `isCreatedByMe`, `isModifiedByMe`, `scriptEngine`, `isValueNonNullable`, och `includeArchived`.

#### Begäran

```text
GET /rest/asset/v2/fragment/filter?workspaceId=1001&fragmentType=email&pageIndex=0&pageSize=20
```

#### Svar

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "f9cc#1900ab1504a",
  "result": [
    {
      "id": "83",
      "name": "Hero Banner Fragment",
      "status": "approved"
    }
  ]
}
```

Använd parametern `name` när du behöver hitta ett fragment efter namn.

## Skapa

Skapa ett fragment genom att skicka en JSON-nyttolast. `name`, `appData` och `settings` krävs. `settings` måste innehålla `fragmentType` och `supportedChannels`.

### Begäran

```text
POST /rest/asset/v2/fragment
Content-Type: application/json
```

```json
{
  "name": "Hero Banner Fragment",
  "description": "Reusable hero block",
  "appData": {
    "workspaceId": "1001",
    "folderId": "395",
    "editorType": "fragment"
  },
  "settings": {
    "fragmentType": "email",
    "fragmentSubType": "html",
    "supportedChannels": [
      "email"
    ]
  }
}
```

### Svar

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "bd57#1900ab1509d",
  "result": [
    {
      "id": "13",
      "name": "Hero Banner Fragment",
      "status": "draft"
    }
  ]
}
```

Begärandetexten kan även innehålla `data`, `editorContext`, `themeId`, `appType` och `status`.

### Skapa fält

* `appData` identifierar var fragmentet lagras och hur det redigeras.
* `settings.fragmentType` identifierar fragmentkategorin, till exempel ett e-postfragment.
* `settings.fragmentSubType` kan användas för att ytterligare definiera fragmentformatet.
* `settings.supportedChannels` visar kanalerna där fragmentet kan användas.

## Uppdatera

Uppdatera ett fragment efter resurs-ID.

### Begäran

```text
POST /rest/asset/v2/fragment/{id}/update
Content-Type: application/json
```

```json
{
  "name": "Hero Banner Fragment v2",
  "description": "Updated reusable hero block",
  "settings": {
    "fragmentSubType": "html",
    "supportedChannels": [
      "email"
    ]
  }
}
```

### Svar

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "73d9#1900ab150f0",
  "result": [
    {
      "id": "13"
    }
  ]
}
```

## Hantera läge

Fragment använder ett utkast och en godkänd livscykel. Använd slutpunkten för lägesövergång för att godkänna ett fragment, avgodkänna det, ta bort ett utkast eller skapa ett nytt utkast.

Giltiga `action`-värden är:

* `approve`
* `unapprove`
* `discard`
* `create_draft`

### Begäran

```text
POST /rest/asset/v2/fragment/state/transition
Content-Type: application/json
```

### Svar

```json
{
  "contentId": "13",
  "action": "approve"
}
```

## Klona

Använd klonslutpunkten för att skapa en kopia av ett befintligt fragment.

### Begäran

```text
POST /rest/asset/v2/fragment/clone
Content-Type: application/json
```

### Svar

```json
{
  "assetId": "13",
  "newAsset": {
    "name": "Hero Banner Fragment Copy",
    "description": "Cloned fragment"
  }
}
```

## Ta bort

Ta bort ett fragment efter resurs-ID.

### Begäran

```text
POST /rest/asset/v2/fragment/{id}/delete
Content-Type: application/json
```

Den här slutpunkten tar fragment-ID:t i sökvägen och definierar inte någon begärandebrödtext.

## Används av

Använd slutpunkten `usedby` för att hämta resurser som refererar till ett visst fragment.

### Begäran

```text
POST /rest/asset/v2/fragment/usedby
Content-Type: application/json
```

### Svar

```json
{
  "assetId": "13",
  "pageIndex": 0,
  "pageSize": 20,
  "type": "all"
}
```
