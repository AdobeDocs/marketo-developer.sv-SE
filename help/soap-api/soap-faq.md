---
title: SOAP FAQ
feature: SOAP
description: Lär dig hur du listar program med getMObjects, optimerar getMultipleLeads, skapar möjligheter och skickar eller schemalägger personaliserade e-postmeddelanden via Marketo SOAP API.
exl-id: a2d8f144-cd5f-41bc-8231-29c42af935b8
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '353'
ht-degree: 0%

---

# SOAP FAQ

**F:** Hur får jag en lista över alla program i Marketo tillsammans med deras metadata?

**A:** Om du vill hämta en lista över alla program kan du använda [getMObjects](./getmobjects.md) och skicka typen som är lika med Program och ange includeDetails som true.

**Q:** Finns det något sätt att öka prestandan för getMultipleLeads?

**A:** Det finns några alternativ för att öka prestandan för getMultipleLeads-anropet. Det första är att minska batchSize som du begär för varje samtal. 200 är den rekommenderade batchstorleken. Det andra alternativet är att ange de fält som du är intresserad av att använda filtret includeAttributes. Detta snabbar upp frågan och minskar nyttolasten för det svar du får. Det sista sättet är att använda LastUpdateAtSelector och ange oldestUpdatedAt och latestUpdatedAt. Du kan ange olika datumintervall och sedan koppla flera begäranden samtidigt. Om du använder den kopplade metoden måste du kontrollera att din SOAP/WSDL-klient stöder [beständiga anslutningar](https://www.w3.org/Protocols/rfc2616/rfc2616-sec8.html).

**F:** Hur skapar jag affärsmöjligheter via SOAP API när det inte är integrerat med ett CRM-system som Salesforce eller Microsoft Dynamics?

**A:** Du kan skapa affärsmöjligheter med SOAP-API:t genom att använda [syncMObjects](syncmobjects.md) -anropet som skriver till [MObject](marketo-objects.md)-typerna OpportunityPersonRole och Opportunity.

**F:** Kan jag programmässigt skicka ett e-postmeddelande från Marketo? I så fall, hur skickar jag anpassat innehåll för varje e-postmottagare?

**A:** Absolut. Du kan begära att ett e-postmeddelande skickas från Marketo med SOAP-API:erna [requestCampaign](requestcampaign.md) eller kombinationen [importToList](importtolist.md) och [scheduleCampaign](schedulecampaign.md) . Om du omedelbart vill skicka ett e-postmeddelande till en eller flera personer använder du [requestCampaign](requestcampaign.md). Om du vill schemalägga ett e-postmeddelande som ska skickas vid ett angivet datum och tid använder du [importToList](importtolist.md) för att ange e-postmeddelandets mottagare och [scheduleCampaign](schedulecampaign.md) för att ange när dessa mottagare ska få e-postmeddelandet.

Om du vill anpassa innehållet för varje e-postmottagare kan du göra det genom att åsidosätta värdena för [programtokens](../rest-api/tokens.md) som anges i e-postmallen.
