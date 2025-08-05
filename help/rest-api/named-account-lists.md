---
title: Namngivna kontolistor
feature: REST API
description: Konfigurera listor med namngivna konton.
exl-id: 98f42780-8329-42fb-9cd8-58e5dbea3809
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '696'
ht-degree: 0%

---

# Namngivna kontolistor

[Slutpunktsreferens för namngiven kontolista](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Named-Account-Lists)

[Namngivna kontolistor](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/target-account-management/target/account-lists) i Marketo representerar samlingar med namngivna konton. De kan användas i en mängd olika fall, t.ex. kategorisering, databerikning och smart kampanjfiltrering. API:erna för namngiven kontolista tillåter fjärrhantering av dessa listresurser och deras medlemskap.
`Content`

## Behörigheter

Om du vill fråga efter namngivna kontolistor måste du ha behörigheten Skrivskyddad lista över namngivna konton eller Lista med skrivskyddade konton. Om du vill skapa, uppdatera eller ta bort listor måste du ha behörighet att läsa och skriva namngiven kontolista. Medlemskap i frågelistan kräver behörigheterna Skrivskyddat namngivet konto eller Skrivskyddat namngivet konto, medan hantering av medlemskap kräver behörigheten Skrivskyddat namngivet konto.

## Modell

Namngivna kontolistor har ett begränsat antal standardfält och kan inte utökas med anpassade fält.
`Named Account List Field`

| Namn | Datatyp | Uppdaterbar | Anteckningar |
|---|---|---|---|
| marketoGUID | Sträng | Falskt | Unik strängidentifierare för den namngivna kontolistan. Det här fältet hanteras av systemet och tillåts inte som ett fält när en post skapas. Fält som används av&quot;deduplicpeBy&quot;:&quot;idField&quot; när du skapar eller uppdaterar. |
| name | Sträng | True | Listans namn. Fält som används av&quot;deduplicpeBy&quot;:&quot;deduplicpeFields&quot; när ett skapar eller uppdaterar. |
| createdAt | Datetime | Falskt | Datum när listan skapades. Det här fältet hanteras av systemet och är inte tillåtet som fält när du skapar eller uppdaterar en post. |
| updatedAt | Datetime | Falskt | Datum/tid för den senaste uppdateringen av listan. Det här fältet hanteras av systemet och är inte tillåtet som fält när du skapar eller uppdaterar en post. |
| type | Sträng | Falskt | Typ av lista. Kan ha värdet &quot;default&quot; eller &quot;external&quot;. Externa listor är de som har skapats av CRM-kontovyn. |

## Fråga

Det är enkelt och enkelt att fråga kontolistor. För närvarande finns det bara två giltiga filterTypes för att fråga efter namngivna kontolistor: dedupliceringsfält och idField. Fältet som ska filtreras anges i parametern `filterType` i frågan och värdena anges i `filterValues as` en kommaavgränsad lista. Filtren `nextPageToken` och `batchSize` är också valfria parametrar.

```
GET /rest/v1/namedAccountLists.json?filterType=idField&filterValues=dff23271-f996-47d7-984f-f2676861b5fb,dff23271-f996-47d7-984f-f2676861b5fc
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "seq": 0,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
         "name": "Saas List",
         "createdAt": "xxxxxxxx",
         "updatedAt": "xxxxxxxx",
         "type": "default",
         "updateable": true
      },
      {
         "seq": 1,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fc",
         "name": "My Account List",
         "createdAt": "xxxxxxxx",
         "updatedAt": "xxxxxxxx",
         "type": "default",
         "updateable": true
      }
   ]
}
```

## Skapa och uppdatera

När du skapar och uppdaterar poster för namngivna kontolistor följer du de etablerade mönstren för andra åtgärder för att skapa och uppdatera lead-databas. Tänk på att namngivna kontolistor bara har ett uppdateringsbart fält, `name`.

Slutpunkten tillåter de två standardåtgärdstyperna: &quot;createOnly&quot; och &quot;updateOnly&quot;.  `action defaults` till&quot;createOnly&quot;.

Den valfria `dedupeBy parameter` kan anges om åtgärden är `updateOnly`.  Tillåtna värden är &quot;deduplicpeFields&quot; (motsvarar &quot;name&quot;) eller &quot;idField&quot; (motsvarar &quot;marketoGUID&quot;).  I `createOnly`-lägen tillåts bara &quot;name&quot; som `dedupeBy`-fält. Du kan skicka upp till 300 poster åt gången.

```
POST /rest/v1/namedAccountLists.json
```

```json
{
   "action": "createOnly",
   "dedupeBy": "dedupeFields",
   "input": [
      {
         "name": "SAAS List"
      },
      {
         "name": "Manufacturing (Domestic)"
      }
   ]
}
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "seq": 0,
         "status": "created",
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
      },
      {
         "seq": 1,
         "status": "created",
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fc"
      }
   ]
}
```

## Ta bort

Det är enkelt att ta bort listor med namngivna konton. Du kan göra det utifrån antingen `name` eller `marketoGUID` i listan. Om du vill välja den nyckel du vill använda skickar du antingen &quot;dedupeFields&quot; för namn eller &quot;idField&quot; för marketoGUID i `deleteB`-medlemmen i din begäran. Om den tas bort används dedupliceringsfält som standard. Du kan ta bort upp till 300 poster åt gången.

```
POST /rest/v1/namedAccountLists/delete.json
```

```json
{
   "deleteBy": "dedupeFields",
   "input": [
      {
         "name": "Saas List"
      },
      {
         "name": "B2C List"
      },
      {
         "name": "Launchpoint Partner List"
      }
   ]
}
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "seq": 0,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
         "status": "deleted"
      },
      {
         "seq": 1,
         "id": "dff23271-f996-47d7-984f-f2676861b5fc",
         "status": "deleted"
      },
      {
         "seq": 2,
         "status": "skipped",
         "reasons": [
            {
               "code": "1013",
               "message": "Record not found"
            }
         ]
      }
   ]
}
```

Om det inte går att hitta en post för en viss nyckel kommer motsvarande resultatobjekt att ha `status` &quot;överhoppad&quot; och en orsak med en kod och ett meddelande som beskriver felet, vilket visas i exemplet ovan.

## Hantera medlemskap

### Frågemedlemskap

Det är enkelt att fråga om medlemskap i en lista med namngivna konton, vilket bara kräver `i` i kontolistan. Valfria parametrar är:

-`field` - en kommaavgränsad lista med fält som ska inkluderas i svarsposterna
-`nextPageToke` - för att växla genom resultatuppsättningen
- `batchSiz` - för att ange antalet poster som ska returneras

Om `field` inte anges returneras `marketoGUI`,`nam`, `createdA` och `updatedA`. `batchSiz` har ett högsta och standardvärde på 300.

```
GET /rest/v1/namedAccountList/{id}/namedAccounts.json
```

```json
{
   "requestId": "e42b#14272d07d78",
   "success": true,
   "result": [
      {
         "seq": 0,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
         "name": "Saas List",
         "createdAt": "2017-02-01T00:00:00Z",
         "updatedAt": "2017-03-05T17:21:15Z"
      },
      {
         "seq": 1,
         "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fc",
         "name": "My Account List",
         "createdAt": "2017-02-01T00:00:00Z",
         "updatedAt": "2017-03-05T17:21:15Z"
      }
   ]
}
```

### Lägg till medlemmar

Namngivna konton kan enkelt läggas till i en lista över namngivna konton. Konton kan bara läggas till med hjälp av deras marketoGUID. Du kan lägga till upp till 300 poster åt gången.

```
POST /rest/v1/namedAccountList/{id}/namedAccounts.json
```

```json
{
    "input": [
        {
             "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
        },
        {
             "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
        }
    ]
}
```

```json
{
    "requestId": "string",
    "result": [
        {
            "seq": 0,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
            "status": "added"
        },
        {
            "seq": 1,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
            "status": "added"
        }
    ],
    "success": true,
}
```

### Ta bort medlemmar

Borttagning av poster från en kontolista har en annan sökväg, men samma gränssnitt, som kräver `marketoGUI` för varje post som du vill ta bort. Du kan ta bort upp till 300 poster åt gången.

```
POST /rest/v1/namedAccountList/{id}/namedAccounts/remove.json
```

```json
{
    "input": [
        {
             "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
        },
        {
             "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb"
        }
    ]
}
```

```json
{
    "requestId": "string",
    "result": [
        {
            "seq": 0,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
            "status": "added"
        },
        {
            "seq": 1,
            "marketoGUID": "dff23271-f996-47d7-984f-f2676861b5fb",
            "status": "added"
        }
    ],
    "success": true
}
```

## Timeout

- Slutpunkter för namngiven kontolista har en timeout på 30 sekunder om inget annat anges nedan
   - Synkronisera namngivna kontolistor: 60s
   - Ta bort namngivna kontolistor: 60s
   - Hämta namngivna kontolistor: 60s
   - Lägg till medlemmar i namngiven kontolista: 60s
   - Ta bort medlemmar i listan över namngivna konton: 60s
   - Hämta medlemmar i namngiven kontolista: 60s
