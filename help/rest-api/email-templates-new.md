---
title: E-postmallar
feature: REST API
description: Använd Marketo Asset REST API för att fråga efter, skapa, uppdatera, klona, ta bort, godkänna och inspektera beroenden för e-postmallar.
exl-id: 50bb0047-d6ea-4c94-a900-18c37b17a147
source-git-commit: e2606d6cb12c572603ff069617de58417e43ca63
workflow-type: tm+mt
source-wordcount: '292'
ht-degree: 0%

---

# E-postmallar

[Slutpunktsreferens för e-postmall](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates)

E-postmallar definierar strukturen och den återanvändbara layout som används när e-postmeddelanden skapas.

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

Du kan hämta metadata för e-postmallar efter resurs-ID eller med filterslutpunkten.

### Efter ID

#### Begäran

```http
GET /rest/asset/v2/emailtemplate/{id}
```

#### Svar

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "14f9e#1900ab14001",
  "result": [
    {
      "id": "19",
      "name": "Base Newsletter Template",
      "description": "Core responsive template",
      "status": "draft"
    }
  ]
}
```

### Filter

Filterslutpunkten stöder sökning i en arbetsyta och begränsar resultaten med ytterligare frågeparametrar. `workspaceId` krävs.

Följande filter stöds: `folderId`, repeterad `folderIds`, repeterad `status`, `pageIndex`, `pageSize`, `createdBy`, `createdAtStart`, `createdAtEnd`, `modifiedBy`, `modifiedAtStart`, `modifiedAtEnd`, `name`, `sortKey`, `sortOrder`, `isCreatedByMe`, `isModifiedByMe`, `scriptEngine`, `isValueNonNullable` och `includeArchived` .

#### Begäran

```http
GET /rest/asset/v2/emailtemplate/filter?workspaceId=1001&name=Newsletter&pageIndex=0&pageSize=20
```

#### Svar

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "33c4#1900ab1402f",
  "result": [
    {
      "id": "19",
      "name": "Base Newsletter Template",
      "status": "draft"
    }
  ]
}
```

Använd parametern `name` när du behöver hitta en mall efter namn.

## Skapa

Skapa en e-postmall genom att skicka en JSON-nyttolast. `name` och `appData` krävs. `appData` måste innehålla minst `folderId` eller `workspaceId`.

### Begäran

```http
POST /rest/asset/v2/emailtemplate
Content-Type: application/json
```

```json
{
  "name": "Base Newsletter Template",
  "description": "Core responsive template",
  "appData": {
    "workspaceId": "1001",
    "folderId": "15",
    "editorType": "emailTemplate"
  },
  "themeId": "42"
}
```

### Svar

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "a99f#1900ab1407e",
  "result": [
    {
      "id": "1022",
      "name": "Base Newsletter Template",
      "status": "draft"
    }
  ]
}
```

Begärandetexten kan även innehålla `data`, `editorContext`, `appType` och `status`.

### Skapa fält

`appData` identifierar var mallen lagras och hur den redigeras.

`themeId` kan användas för att associera ett tema med mallen när det är tillämpligt.

## Uppdatera

Uppdatera en mall efter resurs-ID.

### Begäran

```http
POST /rest/asset/v2/emailtemplate/{id}/update
Content-Type: application/json
```

```json
{
  "name": "Base Newsletter Template v2",
  "description": "Updated responsive template",
  "appData": {
    "folderId": "15"
  }
}
```

### Svar

```json
{
  "success": true,
  "warnings": [],
  "errors": [],
  "requestId": "cf10#1900ab140b3",
  "result": [
    {
      "id": "1022"
    }
  ]
}
```

## Hantera läge

E-postmallar använder ett utkast och en godkänd livscykel. Använd slutpunkten för tillståndsövergång för att godkänna en mall, avgodkänna den, ta bort ett utkast eller skapa ett nytt utkast.

Giltiga `action`-värden är:

- `approve`
- `unapprove`
- `discard`
- `create_draft`

### Begäran

```http
POST /rest/asset/v2/emailtemplate/state/transition
Content-Type: application/json
```

### Svar

```json
{
  "contentId": "1022",
  "action": "approve"
}
```

## Klona

Använd klonslutpunkten för att skapa en kopia av en befintlig mall.

### Begäran

```http
POST /rest/asset/v2/emailtemplate/clone
Content-Type: application/json
```

### Svar

```json
{
  "assetId": "1022",
  "newAsset": {
    "name": "Base Newsletter Template Copy",
    "description": "Cloned template"
  }
}
```

## Ta bort

Ta bort en mall efter resurs-ID.

### Begäran

```http
POST /rest/asset/v2/emailtemplate/{id}/delete
Content-Type: application/json
```

Den här slutpunkten tar mallens ID i sökvägen och definierar inte någon begärandetext.

## Används av

Använd slutpunkten `usedby` för att hämta resurser som refererar till en viss mall.

### Begäran

```http
POST /rest/asset/v2/emailtemplate/usedby
Content-Type: application/json
```

### Svar

```json
{
  "assetId": "1022",
  "pageIndex": 0,
  "pageSize": 20,
  "type": "all"
}
```

## Anteckningar

Frågeslutpunkter returnerar metadata för resursen. Använd slutpunktsreferensen för det fullständiga svarsschemat och alla miljöspecifika egenskaper.
