---
title: "Fält"
feature: REST API, Field Management
description: "En lista med fältnamn som stöds."
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '194'
ht-degree: 0%

---


# Fält

REST API och SOAP API använder olika namnkonventioner för lead-fält.

## Hämta listan med fältnamn

Hämta listan med alla fältnamn som stöds som är tillgängliga på dina lead-poster med REST-slutpunkten för &#39;Beskriv lead&#39;.

## Var använder man vilken fältnamnstyp?

Ibland är det svårt att veta vilken fältnamnstyp som du måste använda när du använder en viss integrationsrelaterad funktion. Här följer en snabbreferens där funktionerna använder namntyperna REST eller SOAP.

| Funktion | Fältnamnstyp som ska användas |
|--- |--- |
| API för spårning av leads (Munchkin) | SOAP |
| Forms 2.0 API | SOAP |
| Importera lista (UI) | SOAP |
| List Import (REST API) | REST |
| Webkroks svarsmappningar | SOAP |
| E-postskript (snabbhet) | SOAP |
| SOAP API | SOAP |
| REST API | REST |

### Varför returnerar REST API-fältet sfdcId alltid värdet null?

Fältet `sfdcId` är ett formelfält som felaktigt togs med i den ursprungliga fältmappningen för REST API. Poster som hämtas via REST API beräknar inte värdet för formelfält, så värdet kommer alltid att vara null. Om du vill hämta det riktiga SFDC-ID:t använder du fälten som kallas `sfdcLeadId` och `sfdcContactId`.
