---
title: E-postmallar
feature: REST API
description: Skapa e-postmallar med Marketo API:er.
exl-id: 0ecf4da6-eb7e-43c1-8d5c-0517c43b47c8
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '569'
ht-degree: 0%

---

# E-postmallar

[Slutpunktsreferens för e-postmall](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates)

E-postmallar utgör grunden för varje nytt e-postmeddelande i Marketo.  E-postmeddelanden kan kopplas bort från mallar genom att HTML ersätter dem, men e-postmeddelanden måste först skapas med en mall som grund.  Mallar skapas som rena HTML-dokument i Marketo med metadata som namn och beskrivningar.  Det finns få begränsningar för innehåll, men mallens HTML måste vara giltig och innehålla minst ett redigerbart avsnitt, vilket uppfyller kraven [som beskrivs här](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/add-editable-sections-to-email-templates-v1-0).

## Fråga

När du frågar efter e-postmallar används standardmönstret för resurser, vilket tillåter att frågor [efter ID](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/getTemplateByIdUsingGET), [efter namn](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/getTemplateByNameUsingGET) och [bläddrar](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/getEmailTemplatesUsingGET) efter en viss mapp.

### Efter ID

```
GET /rest/asset/v1/emailTemplate/{id}.json
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "14f9e#14a12955df1",
    "result": [
        {
            "id": 19,
            "name": "aFgSxuZrBI",
            "description": "fUMhVfIyVkhHzRolYzjGyWouTMfjXCPIAZxHMAEmszAjguVKDtbznEeqbqiDuNBzQoHwBJFdXiMzYiMlGUwtuklUhjGfJlDbhaTL",
            "createdAt": "2014-11-14T02:41:26Z+0000",
            "updatedAt": "2014-11-14T02:41:26Z+0000",
            "folder": {
                "type": "Folder",
                "value": 15
            },
            "status": "Draft",
            "workspace": "Default"
        }
    ]
}
```

#### Efter namn

```
GET /rest/asset/v1/emailTemplate/byName.json?name=Test Template
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "14f9e#14a12955df1",
    "result": [
        {
            "id": 19,
            "name": "aFgSxuZrBI",
            "description": "fUMhVfIyVkhHzRolYzjGyWouTMfjXCPIAZxHMAEmszAjguVKDtbznEeqbqiDuNBzQoHwBJFdXiMzYiMlGUwtuklUhjGfJlDbhaTL",
            "createdAt": "2014-11-14T02:41:26Z+0000",
            "updatedAt": "2014-11-14T02:41:26Z+0000",
            "folder": {
                "type": "Folder",
                "value": 15
            },
            "status": "Draft",
            "workspace": "Default"
        }
    ]
}
```

#### Bläddra

```
GET /rest/asset/v1/emailTemplates.json
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
      },
      {
         "id":201,
         "name":"AIpwuwiaqb",
         "description":null,
         "createdAt":"2014-11-25T20:49:06Z+0000",
         "updatedAt":"2014-11-25T20:49:06Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      },
      {
         "id":240,
         "name":"aqZGoAskEF",
         "description":"uOMEhLpXOEWkwdZxkpcdDjTjKfokxuHEYHPVIVsADFIUEUobzIEaDiqFFxezwfovGfwjuPTJRxUmuHmGpyIklJdDdVosPJdyOVom",
         "createdAt":"2014-11-26T21:11:56Z+0000",
         "updatedAt":"2014-11-26T21:11:56Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      },
      {
         "id":199,
         "name":"BAxnkVfLGi",
         "description":"TzUMQKzKXdgukNCCcaiJHUWASceqlZswhCqDQFDFZULqzYkEiyKcwtQRzKERynReqtMHOhqjnhExCsZopyfzglmXAOjEJdxNURCX",
         "createdAt":"2014-11-25T20:49:06Z+0000",
         "updatedAt":"2014-11-25T20:49:06Z+0000",
         "folder":{
            "type":"Folder",
            "value":15
         },
         "status":"Draft",
         "workspace":"Default"
      },
      {
         "id":278,
         "name":"bcBNCUIHrL",
         "description":"UJEPYBRGTSYosZRnMnahMyVtdyxjRpzJMSXyncATKwcLlDAqDnSCFezGVsDZFpZwPzQvBlvaOZzOzBIsIAtqIerZhJFfpqMogoiB",
         "createdAt":"2014-11-30T11:30:07Z+0000",
         "updatedAt":"2014-11-30T11:30:07Z+0000",
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

Om du frågar efter själva posten returneras bara metadata om posten. Mer information finns i avsnittet #content.

## Skapa och uppdatera

[Det är ganska enkelt att skapa](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/createEmailTemplateUsingPOST) eller [uppdatera](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/updateEmailTemplateContentUsingPOST) en mall. Innehållet i varje mall lagras som ett HTML-dokument och måste skickas till Marketo med hjälp av POSTEN multipart/form-data. Du måste skicka rätt Content-Type-huvud som innehåller en gräns som beskrivs i RFC för [multipart](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html) och [multipart/form-data](https://www.ietf.org/rfc/rfc2388.txt).

När du skapar en mall måste du ta med tre parametrar: namn, mapp, innehåll. En valfri beskrivningsparameter kan inkluderas.  HTML-dokumentet skickas i content-parametern, som också måste inkludera den vanliga filename-parametern som en del av Content-Disposition-huvudet.

```
POST /rest/asset/v1/emailTemplates.json
```

```
Content-Type: multipart/form-data; boundary=mktoBoundary1480963323998
```

```html
--mktoBoundary1480963323998
Content-Disposition: form-data; name="name"

Sample Email Template
--mktoBoundary1480963323998
Content-Disposition: form-data; name="folder"

{"id":15,"type":"Folder"}
--mktoBoundary1480963323998
Content-Disposition: form-data; name="content"; filename="testHTML.html"
Content-Type: text/html

<html>
<body>
<h1>TEST HTML</h1>
</body>
</html>

--mktoBoundary1480963323998
Content-Disposition: form-data; name="description"

Create email template using API
--mktoBoundary1480963323998--
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "a99f#14e22b2b85e",
    "result": [
        {
            "id": 1022,
            "name": "Sample Email Template",
            "description": "Create email template using API",
            "createdAt": "2015-06-23T23:13:34Z+0000",
            "updatedAt": "2015-06-23T23:13:34Z+0000",
            "url": "https://app-abm.marketo.com/#ET1022B2ZN12",
            "folder": {
                "type": "Folder",
                "value": 15,
                "folderName": "Templates"
            },
            "status": "draft",
            "workspace": "Default",
            "version": 1
        }
    ]
}
```

Innehållet uppdateras med en [separat slutpunkt](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/updateEmailTemplateContentUsingPOST) som kräver e-postmallens ID. Den här slutpunkten tillåter bara att innehållsparametern skickas i brödtexten. När en uppdatering görs kommer det som skickas i innehållsparametern att helt ersätta det befintliga innehållet i e-postmeddelandet i ett nytt utkast om en godkänd version uppdateras, eller ersätta det aktuella utkastet om resursen är i ett utkastläge.

```
POST /rest/asset/v1/emailTemplate/{id}/content.json
```

```
Content-Type: multipart/form-data; boundary=mktoBoundaryEiJFFFPFKK2WovsT
```

```html
--mktoBoundaryEiJFFFPFKK2WovsT
Content-Disposition: form-data; name="content"; filename="testHTML2.html"
Content-Type: text/html

<html>
<body>
<h1>TEST HTML WITH UPDATE</h1>
<div class="mktEditable"></div>
</body>
</html>
--mktoBoundaryEiJFFFPFKK2WovsT--
```

```json
{
   "success": true,
   "warnings": [ ],
   "errors": [ ],
   "requestId": "f8e2#158d0ae24f8",
   "result":[
      {
         "id":1022,
         "status":"Draft",
         "content":"<html>\n<body>\n<h1>TEST HTML WITH UPDATE</h1>\n<div class="mktEditable"></div>\n</body>\n</html>"
      }
   ]
}
```

## Uppdatera metadata

Om du vill [uppdatera en malls metadata](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/updateEmailTemplateUsingPOST), namn och beskrivning, kan du använda samma slutpunkt som när du uppdaterar innehåll, men skicka en program/x-www-url-formencoded-POST i stället, med parametrarna name och description.

```
POST /rest/asset/v1/emailTemplate/{id}.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
description=Updated description&name=New Name
```

```json
{
    "success": true,
    "warnings": [ ],
    "errors": [ ],
    "requestId": "17ca5#14a12ab900a",
    "result": [
        {
            "id": 19,
            "name": "New Name",
            "description": "Updated description",
            "createdAt": "2014-11-14T02:41:26Z+0000",
            "updatedAt": "2014-11-14T02:41:26Z+0000",
            "folder": {
                "type": "Folder",
                "value": 15
            },
            "status": "Draft",
            "workspace": "Default"
        }
    ]
}
```

## Godkännande

E-postmallar följer standardmönstret för godkännande av tillgångsposter. Du kan godkänna ett utkast, avgodkänna en godkänd version och ignorera ett befintligt utkast av en e-postmall via sina egna slutpunkter.

### Godkänn

När du anropar slutpunkten för godkännandet valideras e-postmeddelandet mot reglerna för Marketo-e-postmeddelanden. Formulärnamnet, från e-post, svar till e-post och ämnet måste fyllas i innan e-postmeddelandet kan godkännas.

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

### Ogodkänd

Den ogodkända slutpunkten kan bara användas för godkända mallar.

```
POST /rest/asset/v1/emailTemplate/{id}/unapprove.json
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

### Ignorera

Utkastversionen av mallen skapas när ett godkänt e-postmeddelande uppdateras.

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

### Ta bort

```
POST /rest/asset/v1/emailTemplate/{id}/delete.json
```

```json
{
   "success":true,
   "warnings":[ ],
   "errors":[ ],
   "requestId":"15cef#149d3de83db",
   "result":[
      {
         "id":12
      }
   ]
}
```

## Klona

Marketo tillhandahåller en enkel metod för att [klona en e-postmall](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/cloneTemplateUsingPOST). Till skillnad från när du skapar en fil görs den här typen av begäran med en application/x-www-url-formencoded-POST och har två obligatoriska parametrar, name och folder, som är ett inbäddat JSON-objekt med id och type.  Beskrivning är också en valfri parameter.

```
POST /rest/asset/v1/emailTemplate/{id}/clone.json
```

```
Content-Type: application/x-www-form-urlencoded
```

```
name=Sample Template 01 - deverly&folder={"id":12,"type":"Folder"}&description=This is a sample template
```

```json
{
    "success": true,
    "warnings": [],
    "errors": [],
    "requestId": "905e#14e22c693f8",
    "result": [
        {
            "id": 1024,
            "name": "Sample Template 01 - deverly",
            "description": "This is a sample template",
            "createdAt": "2015-06-23T23:35:16Z+0000",
            "updatedAt": "2015-06-23T23:35:16Z+0000",
            "url": "https://app-abm.marketo.com/#ET1024B2ZN12",
            "folder": {
                "type": "Folder",
                "value": 12,
                "folderName": "Templates"
            },
            "status": "draft",
            "workspace": "Default"
        }
    ]
}
```

## Fråga efter e-postberoenden

Använd slutpunkten [Hämta e-postmall som används av](https://developer.adobe.com/marketo-apis/api/asset/#tag/Email-Templates/operation/getEmailTemplateUsedByUsingGET) för att hämta en lista med e-postmeddelanden som är beroende av en viss e-postmall.  Sökvägsparametern `id` anger den överordnade e-postmallen.

Det finns två valfria parametrar. `maxReturn`  är ett heltal som begränsar antalet resultat (standardvärdet är 20, maxvärdet är 200) och `offset` är ett heltal som kan användas med `maxReturn` för att läsa igenom stora resultatuppsättningar (standardvärdet är 0).

```
GET /rest/asset/v1/emailTemplates/{id}/usedBy.json
```

```json
{
    "success": true,
    "errors": [],
    "requestId": "18b0#16fa3344169",
    "warnings": [],
    "result": [
        {
            "id": 1022,
            "name": "EmailPr.Email2",
            "type": "Email",
            "status": "approved",
            "updatedAt": "2019-12-02T00:42:21Z+0000"
        },
        {
            "id": 1023,
            "name": "Default.Email1.email1",
            "type": "Email",
            "status": "approved",
            "updatedAt": "2019-12-02T00:42:21Z+0000"
        },
        {
            "id": 1025,
            "name": "Defa.E1",
            "type": "Email",
            "status": "draft",
            "updatedAt": "2019-12-02T00:42:21Z+0000"
        },
        {
            "id": 1052,
            "name": "Email v1 Program.Email v1 Email",
            "type": "Email",
            "status": "draft",
            "updatedAt": "2019-06-07T20:07:16Z+0000"
        }
    ]
}
```
