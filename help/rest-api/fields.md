---
title: Fält
feature: REST API, Field Management
description: Lär dig REST och SOAP lead-fältnamn, lista fält via REST-beskriv lead, funktionsmappning, varför sfdcId är null och använd sfdcLeadId eller sfdcContactId.
exl-id: 9033f32a-c7cb-4bbf-abcf-38ca4112139f
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '212'
ht-degree: 0%

---

# Fält

REST API och SOAP API använder olika namnkonventioner för lead-fält.

## Hämta listan med fältnamn

Hämta listan med alla fältnamn som stöds som är tillgängliga på dina lead-poster med REST-slutpunkten för &#39;Beskriv lead&#39;.

## Var använder man vilken fältnamnstyp?

Ibland är det svårt att veta vilken fältnamnstyp som du måste använda när du använder en viss integrationsrelaterad funktion. Nedan följer en snabbreferens där funktionerna använder datafältnamnstyperna REST eller SOAP.

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

Fältet `sfdcId` är ett formelfält som felaktigt togs med i den ursprungliga fältmappningen för REST API. Poster som hämtas via REST API beräknar inte värdet för formelfält, så värdet kommer alltid att vara null. Om du vill hämta det riktiga SFDC-id:t ska du använda fälten `sfdcLeadId` och `sfdcContactId`.
