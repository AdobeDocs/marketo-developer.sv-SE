---
title: "SOAP FAQ"
feature: SOAP
description: "SOAP FAQ"
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '333'
ht-degree: 0%

---


# Vanliga frågor om SOAP

**F:** Hur får jag en lista över alla program i Marketo tillsammans med deras metadata?

**S:** Om du vill hämta en lista över alla program kan du använda [getMObjects](./getmobjects.md) skickar typen som är lika med &quot;Program&quot; och anger includeDetails som true.

**F:** Finns det något sätt att öka prestandan för getMultipleLeads?

**S:** Det finns ett par alternativ som snabbar upp prestandan för getMultipleLeads-anropet. Det första är att minska batchSize som du begär för varje samtal. 200 är den rekommenderade batchstorleken. Det andra alternativet är att ange de fält som du är intresserad av att använda filtret includeAttributes. Detta snabbar upp frågan och minskar nyttolasten för det svar du får. Det sista sättet är att använda LastUpdateAtSelector och ange oldestUpdatedAt och latestUpdatedAt. Du kan ange olika datumintervall och sedan koppla flera begäranden samtidigt. Kontrollera att SOAP-/WSDL-klienten har stöd för den kopplade metoden [beständiga anslutningar](https://www.w3.org/Protocols/rfc2616/rfc2616-sec8.html).

**F:** Hur skapar jag affärsmöjligheter via SOAP API när det inte är integrerat med en CRM som SalesForce eller Microsoft Dynamics?

**S:** Du kan skapa affärsmöjligheter med SOAP API med [syncMObjects](syncmobjects.md) anropa skrivning till OpportunityPersonRole och Opportunity [MObject](marketo-objects.md) typer.

**F:** Kan jag programmässigt skicka ett e-postmeddelande från Marketo? I så fall, hur skickar jag anpassat innehåll för varje e-postmottagare?

**S:** Absolut. Du kan begära att få ett e-postmeddelande från Marketo via [requestCampaign](requestcampaign.md) eller en kombination av [importToList](importtolist.md) och [scheduleCampaign](schedulecampaign.md) SOAP API:er. Om du omedelbart vill skicka ett e-postmeddelande till en eller flera personer använder du [requestCampaign](requestcampaign.md). Om du vill schemalägga att ett e-postmeddelande ska skickas vid ett visst datum och klockslag använder du [importToList](importtolist.md) för att ange mottagarna av e-postmeddelandet, och [scheduleCampaign](schedulecampaign.md) för att ange när dessa mottagare ska få e-postmeddelandet.

Om du vill anpassa innehållet för varje e-postmottagare kan du göra det genom att åsidosätta värdena för [programtoken](../rest-api/tokens.md) som anges i e-postmallen.
