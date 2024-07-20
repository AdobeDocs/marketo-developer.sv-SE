---
title: Taggar
feature: REST API, Tags
description: Hantera taggar för program i Marketo.
exl-id: 64731d1a-a749-4d6f-b336-16c733d002f0
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '205'
ht-degree: 0%

---

# Taggar

[Slutpunktsreferens för taggar](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tags)

Taggar är användardefinierade fält för program. Varje tagg kan användas för en eller flera programtyper och kan antingen vara obligatorisk eller valfri, beroende på hur taggen har definierats. Taggar kan också innehålla en lista över tillåtna värden som måste väljas för användning.

## Fråga

Taggar efterfrågas med standardtillgångsmönstret, men har ingen slutpunkt för By Id. Listan med tillåtna värden för en tagg returneras bara när taggen efterfrågas efter namn.

### Hämta taggar

```
GET /rest/asset/v1/tagTypes.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "1488a#1504ecfccf8",
    "result": [
        {
            "tagType": "AAA1 Required Tag Type",
            "applicableProgramTypes": "[program,email_batch,nurture,event,webinar]",
            "required": true
        },
        {
            "tagType": "AAA2 Required Event Tag Type",
            "applicableProgramTypes": "[event]",
            "required": true
        },
        {
            "tagType": "AAA3 Not Required Tag Type",
            "applicableProgramTypes": "[program,email_batch,nurture,event,webinar]",
            "required": false
        }
    ]
}
```

### Efter namn

```
GET /rest/asset/v1/tagType/byName.json?name=AAA1 Required Tag Type
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "8a44#1504ed0da2f",
    "result": [
        {
            "tagType": "AAA1 Required Tag Type",
            "applicableProgramTypes": "[program,email_batch,nurture,event,webinar]",
            "required": true,
            "allowableValues": "[AAA1 RT1, AAA1 RT2, AAA1 RT3, AAA1 RT4]"
        }
    ]
}
```

## Uppdatera

Med slutpunkten [Uppdatera programtagg](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/updateProgramUsingPOST) kan du uppdatera värdet för en viss taggtyp. Slutpunkten har en `id`- och `tagType`-sökvägsparameter som anger program-ID:t och den taggtyp som ska uppdateras. En `tagValue`-frågeparameter används för att ange det nya värdet för taggtypen. Alla parametrar är obligatoriska.

```
POST /rest/asset/v1/program/{id}/tag/{tagType}.json?tagValue=David
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "fd84#17f84a885a6",
    "warnings": [],
    "result": [
        {
            "id": 1067
        }
    ]
}
```

Taggar kan uppdateras en massa med slutpunkten [Uppdatera programmetadata](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/updateProgramUsingPOST). Ett exempel på det finns [här](programs.md#update).

## Ta bort

Med slutpunkten [Ta bort programtagg](https://developer.adobe.com/marketo-apis/api/asset/#tag/Programs/operation/deleteProgramUsingPOST) kan du ta bort en taggtyp som inte behövs. Slutpunkten har `id`- och `tagType`-sökvägsparametrar som anger program-id:t och den taggtyp som ska tas bort.

```
POST /rest/asset/v1/program/{id}/tag/{tagType}/delete.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "d998#17f84ad36a7",
    "warnings": [],
    "result": [
        {
            "id": 1067
        }
    ]
}
```
