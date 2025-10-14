---
title: Mappar
feature: REST API
description: Marketo REST API-guide för mappar som innehåller skapa, uppdatera, ta bort, fråga efter ID och namn, Bläddra bland flera med rot, arbetsyta, maxDepth och pagination.
exl-id: 4b55c256-ef0a-42b4-9548-ff8a4106f064
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '1025'
ht-degree: 0%

---

# Mappar

[Slutpunktsreferens för mappar](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders)

Mappar är den viktigaste organisationsresursen i Marketo, och alla andra typer av resurser har minst en mapp som överordnad. Den överordnade mappen kan antingen vara en mapp som är helt organisatorisk eller ett program som har en funktionell relation till andra resurstyper och kan också vara överordnad andra resurser. Mappar kan skapas, frågas, uppdateras och tas bort via API:t, och även en lista över deras innehåll kan hämtas. Även om program kan returneras genom frågor till programmeringsgränssnittet måste du skapa, uppdatera och ta bort program via programmeringsgränssnittet.

## Fråga

När mappar efterfrågas följer standardfrågetyperna för resurser i [via ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderByIdUsingGET), [efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderByNameUsingGET) och [bläddring](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderUsingGET).

### Efter ID

```
GET /rest/asset/v1/folder/{id}.json?type=Folder
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "1241b#14e21ca814a",
    "result": [
        {
            "name": "Social Media",
            "description": null,
            "createdAt": "2011-03-04T17:01:32Z+0000",
            "updatedAt": "2011-03-04T17:01:32Z+0000",
            "url": null,
            "folderId": {
                "id": 341,
                "type": "Folder"
            },
            "folderType": "Email",
            "parent": {
                "id": 11,
                "type": "Folder"
            },
            "path": "/Design Studio/Default/Emails/Social Media",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 341
        }
    ]
}
```

Typparametern är obligatorisk och måste vara antingen &quot;Mapp&quot; eller &quot;Program&quot;.  Typen anger om sökningen efter mappen görs mot ett mapp-ID eller ett program-ID. För den här slutpunkten returneras bara en post i resultatarrayen. Observera parametern folderType i svaret. Detta kan tyda på många olika typer av mappar. Marketo aktivitetsmappar har antingen en typ av Marketing Folder eller Program, som kan innehålla många olika typer av resurser, medan Design Studio-mappar har en typ som motsvarar resurstypen som de kan innehålla. En mapp med till exempel folderType som &quot;Email&quot; kan bara innehålla e-post, eller andra undermappar, som kan ha en folderType som &quot;Email&quot; eller &quot;Email Template&quot;. Typer kan vara:

- E-post
- E-postmall
- Landningssida
- Landningssidmall
- Fragment
- Fil

### Efter namn

[Det är också tillåtet att fråga efter namn &#x200B;](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderByNameUsingGET). Frågan efter namnslutpunkt har namn som den enda obligatoriska parametern. Namn utför en exakt strängmatchning mot namnfältet för mapparna i instansen och returnerar resultat för varje mapp som matchar det namnet. Den har också de valfria frågeparametrarna av typen &quot;type&quot;, som kan vara Mapp eller Program, &quot;root&quot;, ID:t för mappen som ska genomsökas, eller &quot;workspace&quot;, namnet på arbetsytan som ska genomsökas i. Om rotparametern är inställd måste även typparametern anges.

```
GET /rest/asset/v1/folder/byName.json?name=Test%2010%20-%20deverly
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "19#14e1f2f3688",
    "result": [
        {
            "name": "Test 10 - deverly",
            "description": "This is a test",
            "createdAt": "2015-06-23T06:27:04Z+0000",
            "updatedAt": "2015-06-23T06:27:04Z+0000",
            "url": "https://app-abm.marketo.com/#MF1070A1",
            "folderId": {
                "id": 454,
                "type": "FOLDER"
            },
            "folderType": "Marketing Folder",
            "parent": {
                "id": 416,
                "type": "FOLDER"
            },
            "path": "/Marketing Activities/Default/Marketing Programs - deverly/Test 10 - deverly",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 454
        }
    ]
}
```

När du söker efter namn är det viktigt att tänka på att både marknadsföringsaktiviteter och Design Studio är deras egna rotmappar, så de kan hämtas efter namn och användas för att gå igenom resten av mapphierarkin i en målinstans.

### Bläddra

Mappar kan också [hämtas i bulk](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderUsingGET). Parametern root kan användas för att ange den överordnade mapp som frågan ska utföras under och formateras som ett JSON-objekt inbäddat som värde för frågeparametern. Roten har två medlemmar:

1. id - ID för mappen eller programmet.
1. type - antingen Folder or Program, Beroende på vilken typ av rotmapp som ska webbläsas.

Om rotmappen inte är känd, eller om avsikten är att hämta alla mappar i ett visst område, kan roten anges som områdena&quot;Marknadsföringsaktiviteter&quot;,&quot;Design Studio&quot; eller&quot;Lead Database&quot;. Du kan hämta ID:n för var och en av dem med API:t [Hämta mapp efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/getFolderByNameUsingGET) och ange namnet på det önskade området.

Precis som andra slutpunkter för hämtning av bulkresurser är offset och maxReturn valfria parametrar för sidindelning.   Andra valfria parametrar är:

- workSpace - namnet på arbetsytan som ska filtreras.
- maxDepth - Det maximala antalet nivåer att gå igenom i mapphierarkin. Om värdet är 0 returneras bara den mapp som anges i roten. Om inget anges är standardvärdet 2.

```
GET /rest/asset/v1/folders.json?root={"id":14,"type":"Folder"}
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "9bd8#14e1f49047c",
    "result": [
        {
            "name": "Marketing Activities",
            "description": "Root node for the Marketing Activities app area",
            "createdAt": "2010-03-27T18:27:45Z+0000",
            "updatedAt": "2010-03-27T18:27:45Z+0000",
            "url": null,
            "folderId": {
                "id": 14,
                "type": "Folder"
            },
            "folderType": "Zone",
            "parent": null,
            "path": "/Marketing Activities",
            "isArchive": false,
            "isSystem": true,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 14
        },
        {
            "name": "Default",
            "description": "Root node of the Marketing activities Default",
            "createdAt": "2010-03-27T18:27:45Z+0000",
            "updatedAt": "2010-03-27T18:27:45Z+0000",
            "url": null,
            "folderId": {
                "id": 15,
                "type": "Folder"
            },
            "folderType": "Zone",
            "parent": {
                "id": 14,
                "type": "Folder"
            },
            "path": "/Marketing Activities/Default",
            "isArchive": false,
            "isSystem": true,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 15
        },
        {
            "name": "Archive",
            "description": "",
            "createdAt": "2010-03-27T18:28:17Z+0000",
            "updatedAt": "2010-03-27T18:28:17Z+0000",
            "url": "https://app-abm.marketo.com/#MF157A1",
            "folderId": {
                "id": 310,
                "type": "Folder"
            },
            "folderType": "Marketing Folder",
            "parent": {
                "id": 15,
                "type": "Folder"
            },
            "path": "/Marketing Activities/Default/Archive",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 310
        }
    ]
}
```

## Svarsstruktur

En stor del av mappsvarsstrukturen är självförklarande, men ett fåtal fält är värda att uppmärksammas individuellt. `folderId` och de överordnade fälten är JSON-objekt som innehåller det explicita ID:t och typen för själva mappen. Det är den här typen som används i frågor, rot och överordnade parametrar av API:t för att se till att mapptyperna för mappar och program är korrekt avgränsade. `folderType` visar hur mappen används, vilket kan vara en av Marknadsföringsmapp, Program, E-post, E-postmall, Landningssida, Landningssida, Kodavsnitt, Bild, Zon eller Fil.  Typerna Marknadsföringsmapp och Program anger att de finns i marknadsföringsaktiviteter och kan innehålla flera typer av resurser. De andra typerna anger att de bara kan innehålla den typen av resurs, undermappar och mallversionen av den typen, om tillämpligt. Typen Zone representerar rotnivåmappar som hittas i marknadsföringsaktiviteter.

Sökvägen till en mapp visar sin hierarki i mappträdet, ungefär som en Unix-sökväg. Det första tävlingsbidraget i sökvägen är alltid Marknadsföringsaktiviteter eller Design Studio. Om målinstansen har arbetsytor är den andra posten i sökvägen namnet på den ägande arbetsytan. Fältet `url` visar den explicita URL:en för resursen i den angivna instansen. Detta är inte en universell länk och måste autentiseras som en användare för att fungera korrekt. `isSystem` anger om mappen är en systemmapp. Om värdet är true är själva mappen skrivskyddad, men mappar kan skapas som underordnade mappar.

## Skapa och uppdatera

[Det är enkelt att skapa mappar](https://developer.adobe.com/marketo-apis/api/asset/#tag/Folders/operation/createFolderUsingPOST) och det körs med ett program/x-www-form-urlencoded POST som har två obligatoriska parametrar, &quot;name&quot;, en sträng och &quot;parent&quot;, som är det överordnade objektet som skapar mappen i, vilket är ett inbäddat JSON-objekt med två medlemmar, id och typ, antingen Folder eller Program, beroende på målmappens typ. Om du vill kan du även använda&quot;description&quot;, en sträng, och den kan innehålla upp till 2 000 tecken.

```
POST /rest/asset/v1/folders.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
parent={"id":416,"type":"Folder"}&name=Test 10 - deverly&description=This is a test
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "111be#14e1f193e31",
    "result": [
        {
            "name": "Test 10 - deverly",
            "description": "This is a test",
            "createdAt": "2015-06-23T06:27:04Z+0000",
            "updatedAt": "2015-06-23T06:27:04Z+0000",
            "url": "https://app-abm.marketo.com/#MF1070A1",
            "folderId": {
                "id": 454,
                "type": "FOLDER"
            },
            "folderType": "Marketing Folder",
            "parent": {
                "id": 416,
                "type": "FOLDER"
            },
            "path": "/Marketing Activities/Default/Test 10 - deverly",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 454
        }
    ]
}
```

Uppdateringar av mappar görs via en separat slutpunkt, och description, name och `isArchive` är valfria parametrar för uppdatering. Om `isArchive` ändras av en uppdatering arkiveras mappen, om den ändras till true, eller om den inte arkiveras, om den ändras till false, i Marketo-gränssnittet. Program kan inte uppdateras med detta API.

```
POST /rest/asset/v1/folder/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
type=Folder&description=This is a test (update 01)
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "c5b2#14e1f3954bf",
    "result": [
        {
            "name": "Learning - deverly",
            "description": "This is a test (update 01)",
            "createdAt": "2015-03-17T00:17:02Z+0000",
            "updatedAt": "2015-06-23T07:02:07Z+0000",
            "url": "https://app-abm.marketo.com/#MF1044A1",
            "folderId": {
                "id": 407,
                "type": "FOLDER"
            },
            "folderType": "Marketing Folder",
            "parent": {
                "id": 15,
                "type": "FOLDER"
            },
            "path": "/Marketing Activities/Default/Learning - deverly",
            "isArchive": false,
            "isSystem": false,
            "accessZoneId": 1,
            "workspace": "Default",
            "id": 407
        }
    ]
}
```

### Ta bort

Du kan ta bort enstaka mappar om de är tomma, vilket innebär att de inte innehåller några resurser eller undermappar. Om en mapp är av typen Program eller har fältet isSystem inställt på true kan den inte tas bort med denna API.

```
POST /rest/asset/v1/folder/{id}/delete.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "4180#14e1f3fc017",
    "result": [
        {
            "id": 453
        }
    ]
}
```
