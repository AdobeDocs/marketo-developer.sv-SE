---
title: Felkoder
feature: SOAP
description: Felkoder för SOAP
exl-id: 71796520-7bd6-4a37-94e7-b073d17df06f
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '390'
ht-degree: 6%

---

# Felkoder

När du utvecklar för Marketo är det mycket viktigt att begäranden och svar loggas när ett oväntat undantag inträffar.  Vissa typer av undantag, som autentisering som har upphört att gälla, kan hanteras säkert genom återautentisering, men andra kan kräva supportinteraktioner, och begäranden och svar kommer alltid att begäras i det här scenariot.

Nedan finns en lista SOAP API-felkoder.

| Code | Meddelande | Anteckningar |
|--- |--- |--- |
| 10001 | Internt fel | Allvarligt systemfel |
| 20011 | Internt fel | API-tjänstfel |
| 20012 | Förfrågan är inte förstådd | Oväntat SOAP |
| 20013 | Åtkomst nekad | Klienten blockeras från API-åtkomst |
| 20014 | Autentiseringen misslyckades | Klienten angav inte giltiga autentiseringsuppgifter |
| 20015 | Gränsen för begäran överskreds | Antalet samtal idag överskred prenumerationens kvot. Standardprenumerationskvoten är 10 000/dag. |
| 20016 | Förfrågan har gått ut | Begärandesignaturen är för gammal. Angiven tidsstämpel och begärandesignatur är tidigare och är inte längre giltiga. Begäran kan provas igen med en nyligen genererad tidsstämpel och signatur. |
| 20017 | Ogiltig begäran | Begäran saknar en förväntad parameter |
| 20019 | Åtgärden stöds inte | Åtgärden som anropas har inte definierats i Marketo API WSDL |
| 20022 | Tidsintervallet som angetts i frågefiltret överskred gränsen | Antalet dagar mellan fälten &quot;oldestUpdatedAt&quot; och &quot;latestUpdatedAt&quot; var större än 30 |
| 20023 | Hastighetsgränsen har överskridits | Antalet anrop under de senaste 20 sekunderna var större än 100 |
| 20024 | Samtidighetsgränsen har överskridits | Antalet samtidiga anrop var större än 10 |
| 20101 | Leadnyckel krävs | LeadKey är obligatorisk men har inte angetts |
| 20102 | Leadnyckeln är felaktig | LeadKeyType är inte giltig |
| 20103 | Lead hittades inte | LeadKey-värdet matchade inte någon lead |
| 20104 | Leadinformation krävs | LeadRecord krävs men har inte angetts |
| 20105 | Felaktigt lead-attribut | LeadRecord innehåller ett attribut med ett felaktigt namn |
| 20106 | Leadsynkronisering misslyckades | LeadRecord kunde inte uppdateras eller skapas |
| 20107 | Ogiltig aktivitetsnyckel | LeadActivityFilter innehåller en felaktig aktivitetstyp |
| 20108 | Leadägaren hittades inte | LeadKey anger en lead-ägare som inte finns |
| 20109 | Parametern krävs | Parametervärdet var null eller saknades |
| 20110 | Felaktig parameter | Ett parametervärde är felaktigt |
| 20111 | Listan hittades inte | ListKey anger en lista som inte finns |
| 20113 | Campaign not found | Kampanjen finns inte |
| 20114 | Felaktig parameter | Parametervärdet är felaktigt |
| 20122 | Ogiltig strömposition | Strömpositionen är dålig |
| 20123 | Direktuppspelning vid slutet | Strömpositionen anger att inga fler poster är tillgängliga |
