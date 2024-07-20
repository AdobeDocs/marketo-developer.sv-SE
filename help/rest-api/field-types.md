---
title: Fälttyper
feature: REST API
description: En lista över fälttyper i Marketo
exl-id: a0ba9e02-ed42-4be3-9cdd-a97fee9a726e
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '319'
ht-degree: 2%

---

# Fälttyper

Här följer en beskrivning av fälttyper i Marketo. Ytterligare information om fälttyper finns [här](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/field-management/custom-field-type-glossary). Ytterligare information om fälttypsbegränsningar finns [här](https://nation.marketo.com/t5/knowledgebase/tkb-p/support_solutions-documents).

| Fälttyp | Beskrivning | Exempel |
| --- | --- | --- |
| Datetime | Används för att ange datum och tid. Följer [W3C-format](https://www.w3.org/TR/NOTE-datetime) (ISO 8601). Det bästa sättet är att alltid inkludera tidszonsförskjutningen. Fullständigt datum plus timmar och minuter: YYYY-MM-DDThh:mm:ssTZD där TZD är &quot;+hh:mm&quot; eller &quot;-hh:mm&quot; Obs! Vissa resurs-API:er returnerar &quot;Z+0000&quot; som TZD för updatedAt och createdAt. | 2010-05-07T15:41:32-05:00 |
| E-post | Ett strängfält som accepterar e-postadresser | example@example.com |
| Float | Ett nummerfält som innehåller reella tal och kan använda en decimal. | 10,4 |
| Heltal | Hela tal | 10 |
| Formel | Fält vars värden genereras genom att data från andra fält i en lead-post ändras. De exporteras inte och kan inte användas i smarta kampanjer. | Se den här [artikeln](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/field-management/create-and-use-a-concatenated-string-formula-field) |
| Procent | Ett procenttal uttryckt som ett heltal | 30 |
| URL | Ett textfält som begränsar indata till URL:er, inklusive URL-adressens protokoll. | http://example.com/ |
| Tel. | Telefonnummer | 111-111-1111 |
| Textområde | Längre text. | Stöder upp till 30 000 byte. Standard-ASCII-tecken använder 1 byte per tecken (upp till 30 000 tecken tillåts). Unicode-tecken kan använda upp till 4 byte per tecken (vilket minskar antalet  antalet tecken som får vara mindre än 30 000 tecken). |
| Sträng | Kortare text (upp till 255 tecken) | Lorem ipsum dolor sit amet |
| Poäng | Ett heltalsfält som kan ändras med steget Ändra bakgrundsmusik | 10 |
| Boolean (tidigare kryssruta) | Tillåter användare att välja ett sant (markerat) eller falskt (omarkerat) värde. | True |
| Valuta | Ett flytande fält som representerar standardvalutatypen som valts för Marketo-prenumerationen | 10,40 |
| Datum | Används för datum. Följer W3C-format. | 2010-05-07 |
| Referens | Ett strängfält som innehåller en nyckel till en annan post (en sekundärnyckel). | Kontakta företag |
