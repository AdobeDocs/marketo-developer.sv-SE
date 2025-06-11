---
title: Migrerar till REST API
feature: SOAP
description: Migrera från SOAP till REST API:er
exl-id: c2956db3-defe-4163-99f3-58654ce8ee2b
source-git-commit: 8a785b0719e08544ed1a87772faf90bd9dda3077
workflow-type: tm+mt
source-wordcount: '624'
ht-degree: 0%

---

# Migrerar till REST API

Marketo Engage SOAP API kommer att upphöra efter den 31 oktober 2025. Alla befintliga integreringar som använder SOAP API bör dras tillbaka eller migreras till [Marketo Engage REST API](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/rest-api) senast detta datum för att undvika avbrott i tjänsten.

## Migrering

SOAP API har stöd för ett begränsat antal användningsfall jämfört med [REST AP](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/rest-api)I. När du avgör vilka slutpunkter du ska mappa dina användningsfall bör du följa [Marketo Integration Best Practices ](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/marketo-integration-best-practices)

[Referensarkitekturer](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/reference-architectures) är tillgängliga för [CRM-synkronisering](https://experienceleague.adobe.com/docs/marketo-developer/assets/sync-architecture-whitepaper.pdf?lang=sv-SE) och [Data Warehouse Export](https://experienceleague.adobe.com/docs/marketo-developer/assets/reference_architecture.pdf?lang=sv-SE).

## Autentisering

[Autentiseringsdokumentation](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/authentication)

Marketo REST API använder OAuth 2.0-baserad autentisering med typen Klientautentiseringsuppgifter. Åtkomsttoken är giltiga i en timme efter att de har skapats.

## Leads

[Lead-API-dokumentation](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/lead-database/leads)

SOAP-API:t stöder synkronisering av huvuddata, [Munchkin cookie-associering](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/javascriptapi/leadtracking/lead-tracking) och sammanslagning av leads. Om ditt program anropar SOAP syncLead-metoden och ställer in parametern `marketoCookie` kan du migrera med något av följande:

1. Använda REST-metoden [Synkronisera leads](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST), följt av [Associerad lead](https://developer.adobe.com/marketo-apis/api/mapi/#operation/associateLeadUsingPOST)
2. Du kan anropa [Skicka formulär](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/lead-database/leads), men detta kräver att du konfigurerar viss Marketing Assets och viss interaktion med [Forms API](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/assets/forms)

Program som använder nyckeltypen `foreignSysPersonId` bör migreras till att använda ett anpassat lead-fält för att representera den här externa identifieraren, och antingen använda [Synkronisera leads](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/lead-database/leads#create-and-update) eller [bulklead-import](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/bulk-import/bulk-lead-import) REST-metoder.

| SOAP | REST-metoder |
| --- | --- |
| [getLead](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/leads/getlead) | [Hämta lead med ID](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET), [Hämta leads efter filtertyp](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) |
| [getMultipleLeads](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/leads/getmultipleleads) | [Hämta lead med ID](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET), [Hämta leads med filtertyp](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET), [Hämta leads med program-ID](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByProgramIdUsingGET), [Hämta leads med list-ID](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByListIdUsingGET), [Masslead-export](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Leads) |
| [mergeLeads](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/leads/mergeleads) | [Sammanfoga leads](https://developer.adobe.com/marketo-apis/api/mapi/#operation/mergeLeadsUsingPOST) |
| [syncLead](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/leads/synclead) | [Synkronisera leads](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST) [Skicka formulär](https://developer.adobe.com/marketo-apis/api/mapi/#operation/SubmitFormUsingPOST) [Associera lead](https://developer.adobe.com/marketo-apis/api/mapi/#operation/associateLeadUsingPOST) |
| [syncMultipleLeads](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/leads/syncmultipleleads) | [Synkronisera leads](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST) [Massimport](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads) |

## M-objekt

M Objects var ett koncept för att stödja export av data för attribut av säljprojekt för extern analys och arbetade med tre objekttyper: säljprojekt, säljprojektsroller och program.

REST-dokumentation:

- [Möjlighet](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/lead-database/opportunities)
- [Roller](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/lead-database/opportunity-roles)
- [Program](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/assets/programs)

| SOAP | REST-metoder |
| --- | --- |
| [deleteMObjects](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/marketo-objects/deletemobjects) | [Ta bort affärsmöjligheter](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteOpportunitiesUsingPOST), [Ta bort affärsmöjlighetsroller](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteOpportunityRolesUsingPOST) |
| [describeMObjects](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/marketo-objects/describemobject) | [Beskriv säljprojekt](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeUsingGET_4), [Beskriv säljprojektsroll](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeOpportunityRoleUsingGET) |
| [getMObjects](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/marketo-objects/getmobjects) | [Hämta affärsmöjligheter](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getOpportunitiesUsingGET), [Hämta affärsmöjlighetsroller](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeOpportunityRoleUsingGET) |
| [listMObjects](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/marketo-objects/listmobjects) | Ej tillämpligt |
| [syncMObjects](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/marketo-objects/syncmobjects) | [Synkronisera affärsmöjligheter](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncOpportunitiesUsingPOST), [Synkronisera säljprojektsroller](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncOpportunityRolesUsingPOST) |
| [getChannels](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/programs/getchannels) | [Hämta kanaler](https://developer.adobe.com/marketo-apis/api/asset/#operation/getAllChannelsUsingGET) |
| [getTags](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/programs/gettags) | [Hämta taggtyper](https://developer.adobe.com/marketo-apis/api/asset/#operation/getTagTypesUsingGET), [Hämta tagg efter namn](https://developer.adobe.com/marketo-apis/api/asset/#operation/getTagByNameUsingGET) |

## Statiska listor

Fall av statisk listanvändning i SOAP API är begränsade till inmatning av medlemskap och lead-data och borttagning av medlemskap som kan utföras med [Lägg till i lista](https://developer.adobe.com/marketo-apis/api/mapi/#operation/addLeadsToListUsingPOST), [Massimportleads](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/bulk-import/bulk-lead-import) eller [Ta bort från lista](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE) REST-metoder.

| SOAP | REST-metoder |
| --- | --- |
| [getImportToListStatus](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/static-lists/getimporttoliststatus) | [Massimport av leads](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads) |
| [importToList](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/static-lists/importtolist) | [Lägg till i listan](https://developer.adobe.com/marketo-apis/api/mapi/#operation/addLeadsToListUsingPOST) [Massimportleads](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads) |
| [listOperation](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/static-lists/listoperation) | [Ta bort från listan](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE) |

## Aktiviteter

SOAP API har bara stöd för hämtning av aktiviteter.

REST-dokumentation:

- [Synkrona aktiviteter](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/lead-database/activities)
- [Massaktivitetsextrahering](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/bulk-extract/bulk-activity-extract)

| SOAP | REST-metoder |
| --- | --- |
| [getLeadActivity](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/activities/getleadactivity) | [Massexportaktiviteter](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities) [Hämta leadaktiviteter](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET) |
| [getLeadChanges](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/activities/getleadchanges) | [Massexportaktiviteter](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Export-Activities) [Hämta leadändringar](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadChangesUsingGET) |

## Kampanjer

REST-dokumentation:

- [Smarta kampanjer](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/assets/smart-campaigns)

SOAP API har bara stöd för tre användningsfall för smarta kampanjer: [Utlösa leads för att kvalificera sig för en begärbar smart kampanj](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/assets/smart-campaigns#trigger), hämta de begärbara kampanjerna och [Schemalägga en framtida körning av en smart kampanj](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/assets/smart-campaigns#schedule).

| SOAP | REST-metoder |
| --- | --- |
| [getCampaignsForSource](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/campaigns/getcampaignsforsource) | [Hämta smarta kampanjer](https://developer.adobe.com/marketo-apis/api/asset/#operation/getAllSmartCampaignsGET) |
| [requestCampaign](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/campaigns/requestcampaign) | [Begär kampanj](https://developer.adobe.com/marketo-apis/api/mapi/#operation/triggerCampaignUsingPOST) |
| [scheduleCampaign](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/campaigns/schedulecampaign) | [Schemalägg kampanj](https://developer.adobe.com/marketo-apis/api/mapi/#operation/scheduleCampaignUsingPOST) |

## Anpassade objekt

REST-dokumentation:

- [Egna objekt](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/lead-database/custom-objects)

SOAP API stöder bara CRUD-åtgärder för anpassade objekt.

| SOAP | REST-metoder |
| --- | --- |
| [deleteCustomObjects](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/custom-objects/deletecustomobjects) | [Ta bort anpassade objekt](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteCustomObjectsUsingPOST) |
| [getCustomObjects](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/custom-objects/getcustomobjects) | [Hämta anpassade objekt](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getCustomObjectsUsingGET) |
| [syncCustomObjects](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/custom-objects/synccustomobjects) | [Synkronisera anpassade objekt](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncCustomObjectsUsingPOST) [Importera anpassat objekt gruppvis](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/bulk-import/bulk-custom-object-import) |
