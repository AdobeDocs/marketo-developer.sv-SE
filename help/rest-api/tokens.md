---
title: "Tokens"
feature: REST API, Tokens
description: "Hantera variabler i Marketo."
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
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
| sfdc-kampanj | Används i Salesforce-kampanjhanteringsintegrering |
| text | En textsträng |


Detta är de enda datatyperna som kan användas när en token skapas via API.

## Fråga

[Hämta token efter mapp-ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/getTokensByFolderIdUsingGET) tar `id` som en sökvägsparameter av antingen typen Program eller Mapp. Den här typen anges av `folderType` parameter.

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

The [Skapa token](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/addTokenTOFolderUsingPOST) slutpunkten skapar token, eller om de finns, uppdaterar de dem med skickade värden. Token skapas i samband med en mapp eller ett program. Obligatoriskt `id` path-parametern är ID:t för den mapp som token ska kopplas till. The `name`, `type`, `value`och `folderType` är alla obligatoriska parametrar för token. Data skickas som POSTEN x-www-form-urlencoded, inte som JSON. The `name` tokenfältet får inte överskrida 50 tecken.

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

[Ta bort token efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Tokens/operation/deleteTokenByNameUsingPOST) tar ett id som en sökvägsparameter av antingen typen Program eller Mapp. Den här typen anges av `folderType` parameter. Token tas bort baserat på deras överordnade mapp, `name`och `type` av token, som alla är obligatoriska. Data skickas som POSTEN x-www-form-urlencoded, inte som JSON.

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
