---
title: Tokens
feature: REST API, Tokens
description: Hantera variabler i Marketo.
exl-id: 4f8d87d7-ba2a-4c90-8b39-4d20679d404a
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '297'
ht-degree: 0%

---

# Tokens

[Referens för tokenslutpunkt](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens)

Tokens i Marketo är specialsträngar som liknar kortkoder som ersätts av separata data vid körning. Det finns flera typer av token i Marketo, men bara Mina token kan redigeras via API:t. Mina token är underordnade token som är lokala för en viss mapp eller program. Token kan läsas, skapas och tas bort via API:t.

## Datatyp

Token kan skapas med följande datatyper:

| Typ | Beskrivning |
|---------------|----------------------------------------------------|
| datum | Datumvärde för formuläret &quot;yyyy-MM-dd&quot; |
| tal | Ett heltal eller flyttal |
| RTF | En HTML-sträng |
| score | Ett 32-bitars heltal med tecken |
| sfdc-kampanj | Används i Salesforce kampanjhanteringsintegrering |
| text | En textsträng |

Detta är de enda datatyperna som kan användas när en token skapas via API.

## Fråga

[Get Tokens by Folder Id](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/getTokensByFolderIdUsingGET) tar `id` som en sökvägsparameter av antingen typen Program eller Mapp. Den här typen anges av parametern `folderType`.

```curl
GET /rest/asset/v1/folder/{id}/tokens.json?folderType=Folder
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "4fbe#14e27fc9bbf",
    "result": [
        {
            "folder": {
                "type": "Folder",
                "value": 416
            },
            "tokens": [
                {
                    "name": "AprilFool - deverly",
                    "type": "date",
                    "value": "2015-04-01",
                    "computedUrl": "https://app-abm.marketo.com/#MF1047C3"
                }
            ]
        }
    ]
}
```

## Skapa och uppdatera

Slutpunkten [Skapa token](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/addTokenTOFolderUsingPOST) skapar token, eller om de finns, uppdaterar de dem med skickade värden. Token skapas i samband med en mapp eller ett program. Den obligatoriska `id`-sökvägsparametern är ID:t för mappen som token ska kopplas till. `name`, `type`, `value` och `folderType` är alla obligatoriska parametrar för token. Data skickas som POST x-www-form-urlencoded, inte som JSON. Fältet `name` i token får inte innehålla fler än 50 tecken.

```
POST /rest/asset/v1/folder/{id}/tokens.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=April Fools&type=date&value=2015-04-01&folderType=Folder
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "e3c2#14e280db5dc",
    "result": [
        {
            "folder": {
                "type": "Folder",
                "value": 416
            },
            "tokens": [
                {
                    "name": "April Fools",
                    "type": "date",
                    "value": "2015-04-01",
                    "computedUrl": "https://app-abm.marketo.com/#MF1047C3"
                }
            ]
        }
    ]
}
```

## Ta bort

[Ta bort token med namnet ](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/deleteTokenByNameUsingPOST) tar ett ID som en sökvägsparameter av antingen typen Program eller Mapp. Den här typen anges av parametern `folderType`. Tokens tas bort baserat på deras överordnade mapp, `name` och `type` för token, vilka alla är obligatoriska. Data skickas som POST x-www-form-urlencoded, inte som JSON.

```
POST /rest/asset/v1/folder/{id}/tokens/delete.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=AprilFool - deverly&type=date&folderType=Program
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "12ed2#14e2800f89c",
    "result": [
        {
            "id": 416
        }
    ]
}
```
