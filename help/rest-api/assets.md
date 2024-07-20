---
title: Assets
feature: REST API
description: Ett API för att arbeta med Marketo-resurser.
exl-id: 4273a5b1-1904-46e8-b583-fc6f46b388d2
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '876'
ht-degree: 0%

---

# Assets

Marketo tillhandahåller API:er för interaktion med de flesta marknadsförings- och organisationsresurser inom Marketo.

## Assets

Marketo resurser omfattar:

- Mappar
- Program
- E-post
- E-postmallar
- Landningssidor
- Mallar för landningssidor
- Fragment
- Forms
- Tokens
- Filer

## API

En fullständig lista över Resurs-API-slutpunkter, inklusive parametrar och modelleringsinformation, finns i [Resurs-API-slutpunktsreferens](endpoint-reference.md).

## Fråga

Assets har vanligtvis tre mönster som de kan hämtas med: efter ID, efter namn och genom att bläddra.  Med ID och efter namn hämtas både en enda resurs för en given parameter, medan bläddringen returneras och tillåter bläddring genom hela listan med resurser av den typen.  Enskilda typer av resurser har olika parametrar som de kan filtreras efter, så se till att titta närmare på deras enskilda dokument.

I vissa fall returnerar inte bläddringsslutpunkten för vissa resurstyper underordnade resurser, t.ex. tillåtna värden för en tagg, och de måste hämtas individuellt med hjälp av antingen Efter namn eller Efter ID-slutpunkten för att returnera hela uppsättningen metadata.  Andra kan ha separata slutpunkter helt för hämtning av beroende objekt, t.ex. formulärfält.

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

### Efter namn

Av tekniska skäl kan resurs-API:erna inte söka efter resursnamn som innehåller kommatecken (,).  Vi rekommenderar att namnkonventionen exkluderar kommatecken för alla resurstyper.

```
GET /rest/asset/v1/file/byName.json?name=My File
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":null,
   "result":[
      {
         "id":148,
         "size":270313,
         "mimeType":"image/jpeg",
         "url":"http://mlm.devlocal.marketo.com/rs/test/assets/piKLbhVFvW",
         "folder":{
            "type":"Email",
            "id":10614
         },
         "name":"My File",
         "description":null,
         "createdAt":"2014-12-09T22:33:57Z+0000",
         "updatedAt":"2014-12-09T22:33:57Z+0000"
      }
   ]
}
```

### Bläddra

Om du bläddrar bland resurserna kan du alltid använda två frågeparametrar:

- offset - En heltalsförskjutning att returnera resultat från.
- maxReturn - Begränsar antalet returnerade poster.  Standardvärdet är 20 om den tas bort och har högst 200.

```
GET /rest/asset/v1/emailTemplates.json?offset=10&maxReturn=50
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"33c4#14a1832b4a8",
   "result":[
      {
         "id":18,
         "name":"AAA0unit3CreateTestEmailTemplateName.2314673e-7bc2-47da-a1e8-66dfdd8a1f1d",
         "description":"AssetAPI: getTemplates test",
         "createdAt":"2014-11-03T19:52:58Z+0000",
         "updatedAt":"2014-11-03T19:52:58Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      },
      {
         "id":177,
         "name":"ABfRGutnwN",
         "description":"HMmHkdTRrGaRpPakdgGKICxfMunCEWDUWiThgAbInfaBXxGxSFfjKQIwerngCHRlGTnAJhKPmwlXLcsjGPtWEiILGyeIJTNVHoHg",
         "createdAt":"2014-11-20T19:31:06Z+0000",
         "updatedAt":"2014-11-20T19:31:06Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      },
      {
         "id":148,
         "name":"ADVHJBQLyw",
         "description":null,
         "createdAt":"2014-11-20T06:42:57Z+0000",
         "updatedAt":"2014-11-20T06:42:57Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      }
   ]
}
```

## Skapa och uppdatera

För enkla resurstyper som mappar, token och filer finns det vanligtvis bara en enda slutpunkt för att skapa och sedan ytterligare en slutpunkt för att uppdatera poster med ID.  Assets skapas med ett namn som alltid krävs, och därefter returneras alla metadata och ID:n från create- eller update-svaret.

Så här skapar du till exempel en token:

```
POST /rest/asset/v1/folder/{id}/tokens.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=April Fools&value=2015-04-01&type=date&folderType=Folder
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

Så här uppdaterar du en mapp:

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

Andra resurser har mer komplexa strukturer och kräver uppdateringar av ytterligare underavsnitt eller underordnade objekt, och måste sedan godkännas innan de kan användas.  De här resurstyperna är Forms, e-post, e-postmallar, landningssidor och landningssidmallar.  Dessa kommer att ha en enda slutpunkt för att skapa en post och ytterligare slutpunkter för att uppdatera metadata, innehåll och innehållsavsnitten.

Om du till exempel vill skapa en landningssida måste du anropa dess skapandeslutpunkt med ett mall-ID och sedan hämta dess innehållsavsnitt, och uppdatera varje del individuellt för att lägga till innehåll, innan du godkänner den så att den kan distribueras live.

### Komplext skapande

För att landningssidor ska kunna skapas måste en resurs för landningssida skapas med hjälp av en överordnad mall.  Då skapas en ny landningssida med standardinnehållet i mallen för varje innehållsavsnitt.

```
POST rest/asset/v1/landingPages.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=createLandingPage&folder={"type": "Folder", "id": 11}&template=1&description=this is a test&workspace=default&title=test create&keywords=awesome&formPrefill=false
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "7a39#154cf7922c6",
    "result": [
        {
            "id": 27,
            "name": "createLandingPage",
            "description": "this is a test",
            "createdAt": "2016-05-20T18:41:43Z+0000",
            "updatedAt": "2016-05-20T18:41:43Z+0000",
            "folder": {
                "type": "Folder",
                "value": 11,
                "folderName": "Landing Pages"
            },
            "workspace": "Default",
            "status": "draft",
            "template": 1,
            "title": "test create",
            "keywords": "awesome",
            "robots": "index, nofollow",
            "formPrefill": false,
            "mobileEnabled": false,
            "URL": "https://app-devlocal1.marketo.com/lp/622-LME-718/createLandingPage.html",
            "computedUrl": "https://app-devlocal1.marketo.com/#LP27B2"
        }
    ]
}
```

#### Hämta avsnitt

Om du vill fylla i innehållet för en landningssida måste du hämta listan med innehållsavsnitt och sedan utföra individuella uppdateringar för avsnitt som avviker från mallen.

```
GET /rest/asset/v1/landingPage/{id}/content.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "6307#154ea1689d7",
    "result": [
        {
            "id": "67",
            "type": "Form",
            "index": 1,
            "content": {
                "content": "189",
                "contentType": "Form",
                "contentUrl": "https://app-devlocal1.marketo.com/#FO189A1ZN13LA1"
            },
            "formattingOptions": {
                "zIndex": 15,
                "left": "359px",
                "top": "122px"
            }
        }
    ]
}
```

#### Uppdatera avsnitt

```
POST /rest/asset/v1/landingPage/{id}/content/{contentId}.json?type=Form&value=1
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "5c37#154ea32cf11",
    "result": [
        {
            "id": 174
        }
    ]
}
```

## Godkännande

Många resurstyper har ett tillhörande utkasts- och godkännandesystem, inklusive e-post, landningssidor, kodfragment, Forms och deras motsvarande mallar.  Om du försöker godkänna en resurs utvärderas den mot en viss uppsättning valideringsregler och ställs sedan in på ett godkänt tillstånd eller returnerar en felorsak.  För dessa typer av tillgångar görs ändringarna i ett utkast av tillgången varje gång en uppdatering görs av innehållet i en viss tillgång, vilket inte påverkar den godkända versionen.  Detta gör att ändringar i innehållet kan göras på ett säkert sätt utan att det påverkar liveversioner av resursen.  Ändringarna kan sedan tillämpas på den aktiva versionen med slutpunkten för godkännande.  Detta rensar också tillgångens utkastläge tills ytterligare uppdateringar görs.

```
POST /rest/asset/v1/emailTemplate/{id}/approveDraft.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"abe2#14a1832a97d",
   "result":[
      {
         "id":338,
         "name":"lvAVYMZqPS",
         "description":"fZLJQSJRvnYbjGTUpIHHqDOuQgQzXQcWIXoOUPwrVLdMHKcbRqwLoSLkWZTUmaMiCIJSfQiufnnrgITUIqjuAPBLpmliiKuIUFYG",
         "createdAt":"2014-12-05T02:06:21Z+0000",
         "updatedAt":"2014-12-05T02:06:21Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Approved",
         "workspace":"Default"
      }
   ]
}
```

Godkännandet ersätter den tidigare versionen med den uppdaterade versionen.

Utkasten kan också tas bort via en slutpunkt för varje giltig resurstyp.  Om du använder detta på en tillgång som är i ett godkänt utkasttillstånd kommer det aktuella utkastet och eventuella väntande ändringar att tas bort.  Om du använder detta på en resurs som för närvarande inte har någon godkänd version kommer ingenting att göras och ett fel returneras.  Resurser som bara är för utkast kan tas bort, men de kan inte tas bort.

```
POST /rest/asset/v1/emailTemplate/{id}/discardDraft.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"17bfa#14a1832b3c4",
   "result":[
      {
         "id":344,
         "name":"LkilkvKrkp",
         "description":"yAyUEXuWMtdhpODUmnCkGjpBcyEKnYucxaSoTyYeQzyNbYanxCXWPOzwiIWmeXPUwjfGAUmgnxlhgOPluVqwNittuvxJmNTaHxYM",
         "createdAt":"2014-12-05T02:06:23Z+0000",
         "updatedAt":"2014-12-05T02:06:23Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      }
   ]
}
```

Assets kan också avvisas om de är i ett godkänt läge.  Detta tar ned alla aktiva versioner av resursen och återställer resursen till ett läge med bara utkast, samtidigt som eventuella associerade utkast tas bort.  Den här åtgärden kan bara utföras på de flesta resurser om den inte används någonstans i Marketo, till exempel ett e-postmeddelande som hänvisas till i ett skicka-e-postflödessteg eller ett utdrag som bäddas in i ett e-postmeddelande.

```
POST /rest/asset/v1/email/{id}/unapprove.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"3514#14a1832b0fa",
   "result":[
      {
         "id":1364
      }
   ]
}
```

## Ta bort

Assets med tillstånd för godkännande och utkast, med undantag för formulär, får inte tas bort när de godkänns, och måste tas bort innan de tas bort.  Borttagningar kan i allmänhet bara utföras när en resurs inte är godkänd och inte används, och när det gäller mappar är den tom.  Ett viktigt undantag är program som kan tas bort tillsammans med allt underordnat innehåll, så länge programmet och dess innehåll inte används någonstans utanför programmets gränser.

```
POST /rest/asset/v1/program/{id}/delete.json
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "16501#14db042c6b7",
    "result": [
        {
            "id": 1109
        }
    ]
}
```

## Timeout

Tillgångs-API:er har en timeout på 300-tal
