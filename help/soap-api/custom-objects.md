---
title: "Egna objekt"
feature: SOAP
description: "Skapa anpassade objekt."
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '231'
ht-degree: 0%

---


# Anpassade objekt

Med ett Marketo Custom-objekt kan du skapa en till många relationer mellan dina Marketo-leads och de anpassade objektposterna. Du kan till exempel spåra alla bildspel som en lead deltar i. Eftersom leads kan delta i ett antal bildspel (över flera år) passar anpassade objekt bättre för att lagra den här informationen.

Anpassade objekt stöds i alla Marketo-utgåvor utom Spark. När det anpassade Marketo-objektet har etablerats på ditt konto kan du

- Skapa/läs/uppdatera/ta bort poster i det anpassade objektet via Marketo SOAP API
- Använd utlösaren för smart lista när nya poster läggs till i det anpassade objektet
- Använda anpassade objektdata som ett filter i Smarta listor
- Använd anpassade objektdata i e-postmeddelanden med Marketo e-postskript

## Struktur för anpassade objekt

Anpassade objekt består av:

- En liten uppsättning fasta attribut som är gemensamma för alla anpassade objekt:
   - Objektnamn (även objekttypsnamn)
   - Objektbeskrivning
   - Anpassat objekt till Marketo Lead Link-fältnamn - det här är ett fält på Lead-objektet (Person) som det anpassade objektet refererar till
   - Fältnamn för objektnyckel - den primärnyckel som används av objektet
- Ett eller flera objektspecifika fält (vi stöder högst 50 sådana fält)

## Anpassade objektåtgärder

Följande anrop kan användas för att interagera med CO:er.

- [getCustomObjects](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/getCustomObjectsUsingGET)
- [syncCustomObjects](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/syncCustomObjectsUsingPOST)
- [deleteCustomObjects](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Custom-Objects/operation/deleteCustomObjectsUsingPOST)
