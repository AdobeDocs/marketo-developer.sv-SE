---
title: Prestanda
feature: REST API
description: Prestandatips för att arbeta med Marketo API.
exl-id: 173a398a-9d36-4e8d-9dd3-7d0d375b085a
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '122'
ht-degree: 0%

---

# Prestanda

Den här sidan innehåller en lista med prestandarelaterade ämnen som du kan använda för att förbättra integreringens prestanda.

## HTTP-komprimering

Marketo REST API har stöd för HTTP-komprimering av svarsorgan med hjälp av standarder som definieras i specifikationen för HTTP 1.1. Du bör aktivera komprimering eftersom det minskar bandbreddsanvändningen och den tid det tar att hämta data.

>[!NOTE]
>
>Nyttolaster som är mindre än 1 024 byte är inte komprimerade och större API:er stöder inte komprimering.

Om du vill aktivera komprimering inkluderar du följande HTTP-huvud i begäran:

```html
Accept-Encoding: gzip
```

Marketo REST API komprimerar svarstexten och innehåller följande rubrik:

```html
Content-Encoding: gzip
```

Här är ett exempel där Curl används för att anropa slutpunkten [Hämta leads efter filtertyp](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadsByFilterUsingGET) för att hämta 5 leads:

```bash
curl -H 'Accept-Encoding: gzip' 'https://123-ABC-456.mktorest.com/rest/v1/leads.json?filterType=id&filterValues=4,5,7,12,13'
```
