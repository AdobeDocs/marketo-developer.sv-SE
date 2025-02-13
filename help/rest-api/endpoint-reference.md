---
title: Referens för slutpunkt
feature: REST API
description: Marketo API-slutpunktsreferenser
exl-id: 27d16b6f-865a-4e40-ab9c-cbabe2927472
source-git-commit: 3632d2b713d97a2c895c65f144c07e62e1d369cb
workflow-type: tm+mt
source-wordcount: '4676'
ht-degree: 0%

---

# Referens för slutpunkt

Nedan finns länkar till Marketo REST API-referenser.

- [Resurs](https://developer.adobe.com/marketo-apis/api/asset/)
- [Identitet](https://developer.adobe.com/marketo-apis/api/identity/)
- [Leaddatabas](https://developer.adobe.com/marketo-apis/api/mapi/)
- [Användarhantering](https://developer.adobe.com/marketo-apis/api/user/)

## Slutpunktslista {#endpoint_list}

Här är en omfattande lista över REST API-slutpunkter.

| Namn | Grupp | Metod | URI | Nödvändig behörighet |
|---|---|---|---|---|
| Lägg till anpassade aktiviteter | Aktiviteter | POST | /rest/v1/activities/external.json | Aktivitet för läsning/skrivning |
| Godkänn anpassad aktivitetstyp | Aktiviteter | POST | /rest/v1/tasks/external/type/{apiName}/approve.json | Metadata för aktiviteten Read-Write |
| Skapa anpassade aktivitetstypattribut | Aktiviteter | POST | /rest/v1/tasks/external/type/{apiName}/attributes/create.json | Metadata för aktiviteten Read-Write |
| Skapa anpassade aktivitetstyper | Aktiviteter | POST | /rest/v1/activities/external/type.json | Metadata för aktiviteten Read-Write |
| Ta bort anpassad aktivitetstyp | Aktiviteter | POST | /rest/v1/tasks/external/type/{apiName}/delete.json | Metadata för aktiviteten Read-Write |
| Ta bort anpassade aktivitetstypattribut | Aktiviteter | POST | /rest/v1/tasks/external/type/{apiName}/attributes/delete.json | Metadata för aktiviteten Read-Write |
| Beskriv anpassad aktivitetstyp | Aktiviteter | GET | /rest/v1/tasks/external/type/{apiName}/describe.json | Metadata för skrivskyddad aktivitet |
| Ignorera utkast för anpassad aktivitetstyp | Aktiviteter | POST | /rest/v1/tasks/external/type/{apiName}/discardDraft.json | Metadata för aktiviteten Read-Write |
| Hämta aktivitetstyper | Aktiviteter | GET | /rest/v1/activities/types.json | Skrivskyddad aktivitet |
| Hämta anpassade aktivitetstyper | Aktiviteter | GET | /rest/v1/activities/external/types.json | Metadata för skrivskyddad aktivitet |
| Hämta borttagna leads | Aktiviteter | GET | /rest/v1/activities/deletedleads.json | Skrivskyddad aktivitet |
| Hämta leadaktiviteter | Aktiviteter | GET | /rest/v1/activities.json | Skrivskyddad aktivitet |
| Hämta leadändringar | Aktiviteter | GET | /rest/v1/activities/leadchanges.json | Skrivskyddad aktivitet |
| Hämta växlingstoken | Aktiviteter | GET | /rest/v1/activities/pagingtoken.json | Skrivskyddad aktivitet |
| Uppdatera anpassad aktivitetstyp | Aktiviteter | POST | /rest/v1/tasks/external/type/{apiName}.json | Metadata för aktiviteten Read-Write |
| Uppdatera anpassade aktivitetstypattribut | Aktiviteter | POST | /rest/v1/tasks/external/type/{apiName}/attributes/update.json | Metadata för aktiviteten Read-Write |
| Identitet | Autentisering | GET eller POST | /identity/oauth/token | Ingen |
| Avbryt export av aktivitetsjobb | Massexportaktiviteter | POST | /bulk/v1/tasks/export/{exportid}/cancel.json | Skrivskyddad aktivitet |
| Skapa exportaktivitetsjobb | Massexportaktiviteter | POST | /bulk/v1/activities/export/create.json | Skrivskyddad aktivitet |
| Köa exportaktivitetsjobb | Massexportaktiviteter | POST | /bulk/v1/tasks/export/{exportid}/enqueue.json | Skrivskyddad aktivitet |
| Hämta exportaktivitetsfil | Massexportaktiviteter | GET | /bulk/v1/tasks/export/{exportid}/file.json | Skrivskyddad aktivitet |
| Hämta jobbstatus för exportaktivitet | Massexportaktiviteter | GET | /bulk/v1/tasks/export/{exportid}/status.json | Skrivskyddad aktivitet |
| Hämta exportaktivitetsjobb | Massexportaktiviteter | GET | /bulk/v1/activities/export.json | Skrivskyddad aktivitet |
| Avbryt export av anpassat objektjobb | Exportera anpassade objekt gruppvis | POST | /bulk/v1/custom objects/export/{exportid}/cancel.json | Skrivskyddat anpassat objekt |
| Skapa jobb för export av anpassat objekt | Exportera anpassade objekt gruppvis | POST | /bulk/v1/customobjects/export/create.json | Skrivskyddat anpassat objekt |
| Köa jobb för export av anpassat objekt | Exportera anpassade objekt gruppvis | POST | /bulk/v1/custom objects/export/{exportid}/enqueue.json | Skrivskyddat anpassat objekt |
| Hämta anpassad objektfil för export | Exportera anpassade objekt gruppvis | GET | /bulk/v1/custom objects/export/{exportid}/file.json | Skrivskyddat anpassat objekt |
| Jobbstatus för export av anpassat objekt | Exportera anpassade objekt gruppvis | GET | /bulk/v1/custom objects/export/{exportid}/status.json | Skrivskyddat anpassat objekt |
| Hämta anpassade objektjobb för export | Exportera anpassade objekt gruppvis | GET | /bulk/v1/customobjects/export.json | Skrivskyddat anpassat objekt |
| Avbryt export av lead-jobb | Gruppexportleads | POST | /bulk/v1/leads/export/{exportid}/cancel.json | Skrivskyddat lead |
| Skapa exporthuvudjobb | Gruppexportleads | POST | /bulk/v1/leads/export/create.json | Skrivskyddat lead |
| Kör exporthuvudjobb | Gruppexportleads | POST | /bulk/v1/leads/export/{exportid}/enqueue.json | Skrivskyddat lead |
| Hämta exportleadfil | Gruppexportleads | GET | /bulk/v1/leads/export/{exportid}/file.json | Skrivskyddat lead |
| Hämta status för exportlead-jobb | Gruppexportleads | GET | /bulk/v1/leads/export/{exportid}/status.json | Skrivskyddat lead |
| Hämta exportlead-jobb | Gruppexportleads | GET | /bulk/v1/leads/export.json | Skrivskyddat lead |
| Avbryt export av programmedlemsjobb | Medlemmar i programmet för massexport | POST | /bulk/v1/program/members/export/{exportid}/cancel.json | Skrivskyddat lead |
| Skapa medlemsjobb för exportprogram | Medlemmar i programmet för massexport | POST | /bulk/v1/program/members/export/create.json | Skrivskyddat lead |
| Köa medlemsjobb för exportprogram | Medlemmar i programmet för massexport | POST | /bulk/v1/program/members/export/{exportid}/enqueue.json | Skrivskyddat lead |
| Hämta medlemsfil för exportprogram | Medlemmar i programmet för massexport | GET | /bulk/v1/program/members/export/{exportid}/file.json | Skrivskyddat lead |
| Hämta jobbstatus för medlem i exportprogrammet | Medlemmar i programmet för massexport | GET | /bulk/v1/program/members/export/{exportid}/status.json | Skrivskyddat lead |
| Hämta medlemsjobb för exportprogram | Medlemmar i programmet för massexport | GET | /bulk/v1/program/members/export.json | Skrivskyddat lead |
| Fel vid import av anpassat objekt | Importera anpassade objekt gruppvis | GET | /bulk/v1/custom objects/import/{id}/failures.json | Anpassat objekt för läsning/skrivning |
| Hämta status för anpassat objekt för import | Importera anpassade objekt gruppvis | GET | /bulk/v1/custom objects/import/{id}/status.json | Anpassat objekt för läsning/skrivning |
| Hämta varningar för anpassade objekt vid import | Importera anpassade objekt gruppvis | GET | /bulk/v1/custom objects/import/{id}/warnings.json | Anpassat objekt för läsning/skrivning |
| Importera anpassade objekt | Importera anpassade objekt gruppvis | POST | /bulk/v1/custom objects/{apiName}/import.json | Anpassat objekt för läsning/skrivning |
| Fel vid import av lead | Massimportera leads | GET | /bulk/v1/leads/batch/{id}/failures.json | Läs/skriv lead |
| Hämta status för importlead | Massimportera leads | GET | /bulk/v1/leads/batch/{id}.json | Läs/skriv lead |
| Hämta importlead-varningar | Massimportera leads | GET | /bulk/v1/leads/batch/{id}/warnings.json | Läs/skriv lead |
| Importera leads | Massimportera leads | POST | /bulk/v1/leads.json | Läs/skriv lead |
| Fel vid import av programmedlemmar | Medlemmar i massimportprogram | GET | /bulk/v1/program/members/import/{id}/failures.json | Läs/skriv lead |
| Hämta medlemsstatus för importprogram | Medlemmar i massimportprogram | GET | /bulk/v1/program/members/import/{id}/status.json | Läs/skriv lead |
| Få medlemsvarningar för importprogram | Medlemmar i massimportprogram | GET | /bulk/v1/program/members/import/{id}/warnings.json | Läs/skriv lead |
| Importera programmedlemmar | Medlemmar i massimportprogram | POST | /bulk/v1/program/{programId}/members/import.json | Läs/skriv lead |
| Hämta kampanj efter ID | Kampanjer | GET | /rest/v1/campaign/{id}.json | Skrivskyddade kampanjer |
| Hämta kampanjer | Kampanjer | GET | /rest/v1/campaigns.json | Skrivskyddade kampanjer |
| Begär kampanj | Kampanjer | POST | /rest/v1/campaign/{id}/trigger.json | Läs/skriv-kampanjer |
| Schemalägg kampanj | Kampanjer | POST | /rest/v1/campaign/{id}/schedule.json | Läs/skriv-kampanjer |
| Hämta kanal efter namn | Kanaler | GET | /rest/asset/v1/channel/byName.json | Skrivskyddad resurs |
| Hämta kanaler | Kanaler | GET | /rest/asset/v1/channels.json | Skrivskyddad resurs |
| Ta bort företag | Företag | POST | /rest/v1/companies/delete.json | Läs/skriv företag |
| Beskriv företag | Företag | GET | /rest/v1/companies/describe.json | Skrivskyddat företag |
| Skaffa företag | Företag | GET | /rest/v1/companies.json | Skrivskyddat företag |
| Synkronisera företag | Företag | POST | /rest/v1/companies.json | Läs/skriv företag |
| Hämta företagsfält efter namn | Företag | GET | /rest/v1/companies/schema/fields/{fieldApiName}.json | Anpassat fält för läs- och skrivschema |
| Hämta företagsfält | Företag | GET | /rest/v1/companies/schema/fields.json | Anpassat fält för läs- och skrivschema |
| Lägg till anpassade objekttypsfält | Anpassade objekt | POST | /rest/v1/customobjects/schema/{apiName}/addField.json | Anpassad objekttyp för läsning/skrivning |
| Godkänn anpassad objekttyp | Anpassade objekt | POST | /rest/v1/customobjects/schema/{apiName}/approve.json | Anpassad objekttyp för läsning/skrivning |
| Ta bort anpassade objekt | Anpassade objekt | POST | /rest/v1/customobjects/{name}/delete.json | Anpassat objekt för läsning/skrivning |
| Ta bort anpassad objekttyp | Anpassade objekt | POST | /rest/v1/customobjects/schema/{apiName}/delete.json | Anpassad objekttyp för läsning/skrivning |
| Ta bort fält för anpassad objekttyp | Anpassade objekt | POST | /rest/v1/customobjects/schema/{apiName}/deleteField.json | Anpassad objekttyp för läsning/skrivning |
| Beskriv anpassade objekt | Anpassade objekt | GET | /rest/v1/customobjects/{name}/describe.json | Skrivskyddat anpassat objekt |
| Beskriv anpassad objekttyp | Anpassade objekt | GET | /rest/v1/customobjects/schema/{apiName}/describe.json | Skrivskyddad anpassad objekttyp |
| Ignorera utkast för anpassad objekttyp | Anpassade objekt | POST | /rest/v1/customobjects/schema/{apiName}/discardDraft.json | Anpassad objekttyp för läsning/skrivning |
| Hämta anpassade objekt | Anpassade objekt | GET | /rest/v1/customobjects/{name}.json | Skrivskyddat anpassat objekt |
| Hämta länkade objekt för anpassat objekt | Anpassade objekt | GET | /rest/v1/customobjects/schema/linkableObjects.json | Skrivskyddad anpassad objekttyp |
| Hämta anpassat objektberoende Assets | Anpassade objekt | GET | /rest/v1/customobjects/schema/{apiName}/dependentAssets.json | Skrivskyddad anpassad objekttyp |
| Hämta datatyper för fält för anpassad objekttyp | Anpassade objekt | GET | /rest/v1/customobjects/schema/fieldDataTypes.json | Skrivskyddad anpassad objekttyp |
| Lista med anpassade objekt | Anpassade objekt | GET | /rest/v1/customobjects.json | Skrivskyddat anpassat objekt |
| Lista över anpassade objekttyper | Anpassade objekt | GET | /rest/v1/customobjects/schema.json | Skrivskyddad anpassad objekttyp |
| Synkronisera anpassade objekt | Anpassade objekt | POST | /rest/v1/customobjects/{name}.json | Anpassat objekt för läsning/skrivning |
| Synkronisera anpassad objekttyp | Anpassade objekt | POST | /rest/v1/customobjects/schema.json | Anpassad objekttyp för läsning/skrivning |
| Uppdatera fält för anpassad objekttyp | Anpassade objekt | POST | /rest/v1/customobjects/schema/{apiName}/updateField.json | Anpassad objekttyp för läsning/skrivning |
| Godkänn e-postmallutkast | E-postmallar | POST | /rest/asset/v1/emailTemplate/{id}/approveDraft.json | Läs/skriv-resurs |
| Klona e-postmall | E-postmallar | POST | /rest/asset/v1/emailTemplate/{id}/clone.json | Läs/skriv-resurs |
| Skapa e-postmall | E-postmallar | POST | /rest/asset/v1/emailTemplates.json | Läs/skriv-resurs |
| Ta bort e-postmall | E-postmallar | POST | /rest/asset/v1/emailTemplate/{id}/delete.json | Läs/skriv-resurs |
| Ignorera e-postmallutkast | E-postmallar | POST | /rest/asset/v1/emailTemplate/{id}/discardDraft.json | Läs/skriv-resurs |
| Hämta e-postmall efter ID | E-postmallar | GET | /rest/asset/v1/emailTemplate/{id}.json | Skrivskyddad resurs |
| Hämta e-postmall efter namn | E-postmallar | GET | /rest/asset/v1/emailTemplate/byName.json | Skrivskyddad resurs |
| Hämta e-postmallinnehåll efter ID | E-postmallar | GET | /rest/asset/v1/emailTemplate/{id}/content.json | Skrivskyddad resurs |
| Hämta e-postmall som används av | E-postmallar | GET | /rest/asset/v1/emailTemplates/{id}/usedBy.json | Skrivskyddad resurs |
| Hämta e-postmallar | E-postmallar | GET | /rest/asset/v1/emailTemplates.json | Skrivskyddad resurs |
| Avgodkänn utkast för e-postmall | E-postmallar | POST | /rest/asset/v1/emailTemplate/{id}/unapprove.json | Läs/skriv-resurs |
| Uppdatera innehåll i e-postmall | E-postmallar | POST | /rest/asset/v1/emailTemplate/{id}/content.json | Läs/skriv-resurs |
| Uppdatera metadata för e-postmall | E-postmallar | POST | /rest/asset/v1/emailTemplate/{id}.json | Läs/skriv-resurs |
| Lägg till e-postmodul | E-post | POST | /rest/asset/v1/email/{id}/content/{moduleId}/add.json | Läs/skriv-resurs |
| Godkänn e-postutkast | E-post | POST | /rest/asset/v1/email/{id}/approveDraft.json | Läs/skriv-resurs |
| Klona e-post | E-post | POST | /rest/asset/v1/email/{id}/clone.json | Läs/skriv-resurs |
| Skapa e-post | E-post | POST | /rest/asset/v1/emails.json | Läs/skriv-resurs |
| Ta bort e-post | E-post | POST | /rest/asset/v1/email/{id}/delete.json | Läs/skriv-resurs |
| Ta bort modul | E-post | POST | /rest/asset/v1/email/{id}/content/{moduleId}/delete.json | Läs/skriv-resurs |
| Ignorera e-postutkast | E-post | POST | /rest/asset/v1/email/{id}/discardDraft.json | Läs/skriv-resurs |
| Duplicera e-postmodul | E-post | POST | /rest/asset/v1/email/{id}/content/{moduleId}/duplicate.json | Läs/skriv-resurs |
| Hämta e-post med ID | E-post | GET | /rest/asset/v1/email/{id}.json | Skrivskyddad resurs |
| Hämta e-post efter namn | E-post | GET | /rest/asset/v1/email/byName.json | Skrivskyddad resurs |
| Hämta e-postinnehåll | E-post | GET | /rest/asset/v1/email/{id}/content.json | Skrivskyddad resurs |
| Hämta dynamiskt e-postinnehåll | E-post | GET | /rest/asset/v1/email/{id}/dynamicContent/{dynamicContentId}.json | Skrivskyddad resurs |
| Hämta fullständigt e-postinnehåll | E-post | GET | /rest/asset/v1/email/{id}/fullContent.json | Skrivskyddad resurs |
| Hämta e-postvariabler | E-post | GET | /rest/asset/v1/email/{id}/variables.json | Skrivskyddad resurs |
| Hämta e-postCC-fält | E-post | GET | /rest/asset/v1/email/ccFields.json | Skrivskyddad resurs |
| Få e-post | E-post | GET | /rest/asset/v1/emails.json | Skrivskyddad resurs |
| Ordna om e-postmoduler | E-post | POST | /rest/asset/v1/email/{id}/content/rearrange.json | Läs/skriv-resurs |
| Byt namn på e-postmodul | E-post | POST | /rest/asset/v1/email/{id}/content/{moduleId}/rename.json | Läs/skriv-resurs |
| Skicka exempelmejl | E-post | POST | /rest/asset/v1/email/{id}/sendSample.json | Läs/skriv-resurs |
| Ogodkänd e-post | E-post | POST | /rest/asset/v1/email/{id}/unapprove.json | Läs/skriv-resurs |
| Uppdatera e-postinnehåll | E-post | POST | /rest/asset/v1/email/{id}/content.json | Läs/skriv-resurs |
| Uppdatera avsnitt med e-postinnehåll | E-post | POST | /rest/asset/v1/email/{id}/content/{htmlId}.json | Läs/skriv-resurs |
| Uppdatera avsnitt för dynamiskt e-postinnehåll | E-post | POST | /rest/asset/v1/email/{id}/dynamicContent/{dynamicContentId}.json | Läs/skriv-resurs |
| Uppdatera e-postens fullständiga innehåll | E-post | POST | /rest/asset/v1/emails/{id}/fullContent.json | Läs/skriv-resurs |
| Uppdatera e-postmetadata | E-post | POST | /rest/asset/v1/email/{id}.json | Läs/skriv-resurs |
| Uppdatera e-postvariabel | E-post | POST | /rest/asset/v1/email/{id}/variable/{name}.json | Läs/skriv-resurs |
| Skapa fil | Filer | POST | /rest/asset/v1/files.json | Läs/skriv-resurs |
| Hämta fil efter ID | Filer | GET | /rest/asset/v1/file/{id}.json | Skrivskyddad resurs |
| Hämta fil efter namn | Filer | GET | /rest/asset/v1/file/byName.json | Skrivskyddad resurs |
| Hämta filer | Filer | GET | /rest/asset/v1/files.json | Skrivskyddad resurs |
| Uppdatera filinnehåll | Filer | POST | /rest/asset/v1/file/{id}/content.json | Läs/skriv-resurs |
| Skapa mapp | Mappar | POST | /rest/asset/v1/folders.json | Läs/skriv-resurs |
| Ta bort mapp | Mappar | POST | /rest/asset/v1/folder/{id}/delete.json | Läs/skriv-resurs |
| Hämta mapp efter ID | Mappar | GET | /rest/asset/v1/folder/{id}.json | Skrivskyddad resurs |
| Hämta mapp efter namn | Mappar | GET | /rest/asset/v1/folder/byName.json | Skrivskyddad resurs |
| Hämta mappinnehåll | Mappar | GET | /rest/asset/v1/folder/{id}/content.json | Skrivskyddad resurs |
| Hämta mappar | Mappar | GET | /rest/asset/v1/folders.json | Skrivskyddad resurs |
| Uppdatera mappmetadata | Mappar | POST | /rest/asset/v1/folder/{id}.json | Läs/skriv-resurs |
| Lägg till fält i formulär | Formulärfält | POST | /rest/asset/v1/form/{id}/fields.json | Läs/skriv-resurs |
| Lägg till fältuppsättning i formulär | Formulärfält | POST | /rest/asset/v1/form/{id}/fieldSet.json | Läs/skriv-resurs |
| Lägg till visningsregler för formulärfält | Formulärfält | POST | /rest/asset/v1/form/{formId}/field/{fieldId}/visibility.json | Läs/skriv-resurs |
| Lägg till RTF-fält | Formulärfält | POST | /rest/asset/v1/form/{id}/richText.json | Läs/skriv-resurs |
| Ta bort fält från fältuppsättning | Formulärfält | POST | /rest/asset/v1/form/{id}/fieldSet/{fieldSetId}/field/{fieldId}/delete.json | Läs/skriv-resurs |
| Ta bort formulärfält | Formulärfält | POST | /rest/asset/v1/form/{id}/field/{fieldId}/delete.json | Läs/skriv-resurs |
| Hämta tillgängliga formulärfält | Formulärfält | GET | /rest/asset/v1/form/fields.json | Skrivskyddad resurs |
| Hämta tillgängliga medlemsfält för formulärprogram | Formulärfält | GET | /rest/asset/v1/form/programMemberFields.json | Skrivskyddad resurs |
| Hämta fält för formulär | Formulärfält | GET | /rest/asset/v1/form/{id}/fields.json | Skrivskyddad resurs |
| Uppdatera fältpositioner | Formulärfält | POST | /rest/asset/v1/form/{id}/reArrange.json | Läs/skriv-resurs |
| Uppdatera formulärfält | Formulärfält | POST | /rest/asset/v1/form/{id}/field/{fieldId}.json | Läs/skriv-resurs |
| Godkänn formulärutkast | Forms | POST | /rest/asset/v1/form/{id}/approveDraft.json | Läs/skriv-resurs |
| Klona formulär | Forms | POST | /rest/asset/v1/form/{id}/clone.json | Läs/skriv-resurs |
| Skapa formulär | Forms | POST | /rest/asset/v1/forms.json | Läs/skriv-resurs |
| Hämta formulär som används av | Forms | GET | /rest/asset/v1/form/{id}/usedBy.json | Läs/skriv-resurs |
| Ta bort formulär | Forms | POST | /rest/asset/v1/form/{id}/delete.json | Läs/skriv-resurs |
| Ignorera formulärutkast | Forms | POST | /rest/asset/v1/form/{id}/discardDraft.json | Läs/skriv-resurs |
| Hämta formulär efter ID | Forms | GET | /rest/asset/v1/form/{id}.json | Skrivskyddad resurs |
| Hämta formulär efter namn | Forms | GET | /rest/asset/v1/form/byName.json | Skrivskyddad resurs |
| Skaffa Forms | Forms | GET | /rest/asset/v1/forms.json | Skrivskyddad resurs |
| Hämta tacksida per formulär-ID | Forms | GET | /rest/asset/v1/form/{id}/thankYouPage.json | Skrivskyddad resurs |
| Uppdatera formulärmetadata | Forms | POST | /rest/asset/v1/form/{id}.json | Läs/skriv-resurs |
| Uppdatera Skicka-knapp | Forms | POST | /rest/asset/v1/{id}/submitButton.json | Läs/skriv-resurs |
| Uppdatera tacksida | Forms | POST | /rest/asset/v1/form/{id}/thankYouPage.json | Läs/skriv-resurs |
| Lägg till avsnitt för startsidans innehåll | Innehåll för landningssida | POST | /rest/asset/v1/landingPage/{id}/content.json | Läs/skriv-resurs |
| Ta bort avsnitt för startsidans innehåll | Innehåll för landningssida | POST | /rest/asset/v1/landingPage/{id}/content/{contentId}/delete.json | Läs/skriv-resurs |
| Hämta innehåll för landningssida | Innehåll för landningssida | GET | /rest/asset/v1/landingPage/{id}/content.json | Skrivskyddad resurs |
| Hämta dynamiskt innehåll för landningssidan | Innehåll för landningssida | GET | /rest/asset/v1/landingPage/{id}/dynamicContent/{dynamicContentId}.json | Skrivskyddad resurs |
| Uppdatera avsnitt för startsidans innehåll | Innehåll för landningssida | POST | /rest/asset/v1/landingPage/{id}/content/{contentId}.json | Läs/skriv-resurs |
| Uppdatera avsnitt om dynamiskt innehåll på landningssidan | Innehåll för landningssida | POST | /rest/asset/v1/landingPage/{id}/dynamicContent/{dynamicContentId}.json | Läs/skriv-resurs |
| Godkänn utkast för landningssidmall | Mallar för landningssidor | POST | /rest/asset/v1/landingPageTemplate/{id}/approveDraft.json | Läs/skriv-resurs |
| Klona startsidmall | Mallar för landningssidor | POST | /rest/asset/v1/landingPageTemplate/{id}/clone.json | Läs/skriv-resurs |
| Skapa mall för landningssida | Mallar för landningssidor | POST | /rest/asset/v1/landingPageTemplate.json | Läs/skriv-resurs |
| Ta bort mall för landningssida | Mallar för landningssidor | POST | /rest/asset/v1/landingPageTemplate/{id}/delete.json | Läs/skriv-resurs |
| Ignorera utkast av landningssidmall | Mallar för landningssidor | POST | /rest/asset/v1/landingPageTemplate/{id}/discardDraft.json | Läs/skriv-resurs |
| Hämta landningssidmall per ID | Mallar för landningssidor | GET | /rest/asset/v1/landingPageTemplate/{id}.json | Skrivskyddad resurs |
| Hämta landningssidmall efter namn | Mallar för landningssidor | GET | /rest/asset/v1/landingPageTemplates/byName.json | Skrivskyddad resurs |
| Hämta innehåll för landningssidmall | Mallar för landningssidor | GET | /rest/asset/v1/landingPageTemplate/{id}/content.json | Skrivskyddad resurs |
| Hämta mallar för landningssidor | Mallar för landningssidor | GET | /rest/asset/v1/landingPageTemplates.json | Skrivskyddad resurs |
| Ogodkänd mall för landningssida | Mallar för landningssidor | POST | /rest/asset/v1/landingPageTemplate/{id}/unapprove.json | Läs/skriv-resurs |
| Uppdatera innehåll för landningssidmall | Mallar för landningssidor | POST | /rest/asset/v1/landingPageTemplate/{id}/content.json | Läs/skriv-resurs |
| Uppdatera metadata för landningssidmall | Mallar för landningssidor | POST | /rest/asset/v1/landingPageTemplate/{id}.json | Läs/skriv-resurs |
| Godkänn startsidesutkast | Landningssidor | POST | /rest/asset/v1/landingPage/{id}/approveDraft.json | Läs/skriv-resurs |
| Clone Landing Page | Landningssidor | POST | /rest/asset/v1/landingPage/{id}/clone.json | Läs/skriv-resurs |
| Skapa landningssidor | Landningssidor | POST | /rest/asset/v1/landingPages.json | Läs/skriv-resurs |
| Ta bort landningssida | Landningssidor | POST | /rest/asset/v1/landingPage/{id}/delete.json | Läs/skriv-resurs |
| Ignorera startsidesutkast | Landningssidor | POST | /rest/asset/v1/landingPage/{id}/discardDraft.json | Läs/skriv-resurs |
| Hämta landningssida efter ID | Landningssidor | GET | /rest/asset/v1/landingPage/{id}.json | Skrivskyddad resurs |
| Hämta landningssida efter namn | Landningssidor | GET | /rest/asset/v1/landingPage/byName.json | Skrivskyddad resurs |
| Hämta landningssidvariabler | Landningssidor | GET | /rest/asset/v1/landingPage/{id}/variables.json | Skrivskyddad resurs |
| Hämta landningssidor | Landningssidor | GET | /rest/asset/v1/landingPages.json | Skrivskyddad resurs |
| Förhandsgranska landningssida | Landningssidor | GET | /rest/asset/v1/landingPage/{id}/preview.json | Skrivskyddad resurs |
| Ogodkänd landningssida | Landningssidor | POST | /rest/asset/v1/landingPage/{id}/unapprove.json | Läs/skriv-resurs |
| Uppdatera landningssidans metadata | Landningssidor | POST | /rest/asset/v1/{id}.json | Läs/skriv-resurs |
| Uppdatera landningssidvariabler | Landningssidor | POST | /rest/asset/v1/landingPage/{id}/variable/{variableId}.json | Läs/skriv-resurs |
| Skapa omdirigeringsregler för landningssidor | Landningssidor | POST | /rest/asset/v1/redirectRules.json | Läs/skriv omdirigeringsregler |
| Ta bort regel för omdirigering av startsida | Landningssidor | POST | /rest/asset/v1/redirectRule/{id}/delete.json | Läs/skriv omdirigeringsregler |
| Hämta regler för omdirigering av startsida | Landningssidor | GET | /rest/asset/v1/redirectRules.json | Skrivskyddade omdirigeringsregler |
| Hämta regel för omdirigering av startsida efter ID | Landningssidor | GET | /rest/asset/v1/redirectRule/{id}.json | Skrivskyddade omdirigeringsregler |
| Uppdatera regel för omdirigering av startsida | Landningssidor | POST | /rest/asset/v1/redirectRule/{id}.json | Läs/skriv omdirigeringsregler |
| Hämta domäner för landningssidor | Landningssidor | GET | /rest/asset/v1/landingPageDomains.json | Skrivskyddade omdirigeringsregler |
| Associera lead | Leads | POST | /rest/v1/leads/{id}/associate.json | Läs/skriv lead |
| Ändra status för Lead-program | Leads | POST | /rest/v1/leads/programs/{programId}/status.json | Läs/skriv lead |
| Ta bort leads | Leads | POST | /rest/v1/leads.json | Läs/skriv lead |
| Beskriv lead | Leads | GET | /rest/v1/leads/describe.json | Skrivskyddat lead |
| Beskriv lead2 | Leads | GET | /rest/v1/leads/describe2.json | Skrivskyddat lead |
| Beskriv programmedlem | Leads | GET | /rest/v1/program/members/describe.json | Skrivskyddat lead |
| Hämta lead efter ID | Leads | GET | /rest/v1/lead/{id}.json | Skrivskyddat lead |
| Hämta Lead-partitioner | Leads | GET | /rest/v1/leads/partitions.json | Skrivskyddat lead |
| Hämta leads efter filtertyp | Leads | GET | /rest/v1/leads.json | Skrivskyddat lead |
| Hämta leads per program-ID | Leads | GET | /rest/v1/leads/programs/{programId}.json | Skrivskyddat lead |
| Sammanfoga leads | Leads | POST | /rest/v1/leads/{id}/merge.json | Läs/skriv lead |
| Hämta listor efter lead-ID | Leads | GET | /rest/v1/leads/{leadId}.json | Skrivskyddad resurs |
| Hämta program efter lead-ID | Leads | GET | /rest/v1/leads/{leadId}programMembership.json | Skrivskyddad resurs |
| Hämta smarta kampanjer via lead-ID | Leads | GET | /rest/v1/leads/{leadId}/smartCampaignMembership.json | Skrivskyddad resurs |
| Push Lead to Marketo | Leads | POST | /rest/v1/leads/partitions.json | Läs/skriv lead |
| Skicka formulär | Leads | POST | /rest/v1/leads/submitForm.json | Läs/skriv lead |
| Synkronisera leads | Leads | POST | /rest/v1/leads.json | Läs/skriv lead |
| Uppdatera Lead-partition | Leads | POST | /rest/v1/leads/partitions.json | Läs/skriv lead |
| Hämta lead-fält efter namn | Leads | GET | /rest/v1/leads/schema/fields/{fieldApiName}.json | Anpassat fält för läs- och skrivschema |
| Hämta lead-fält | Leads | GET | /rest/v1/leads/schema/fields.json | Anpassat fält för läs- och skrivschema |
| Skapa leadfält | Leads | POST | /rest/v1/leads/schema/fields.json | Anpassat fält för läs- och skrivschema |
| Uppdatera lead-fält | Leads | POST | /rest/v1/leads/schema/fields/{fieldApiName}.json | Anpassat fält för läs- och skrivschema |
| Lägg till medlemmar i namngiven kontolista | Namngivna kontolistor | POST | /rest/v1/namedaccountList/{id}/namedaccounts.json | Läs/skriv namngivet konto |
| Ta bort namngivna kontolistor | Namngivna kontolistor | POST | /rest/v1/namedaccountlists/delete.json | Läs/skriv namngiven kontolista |
| Hämta medlemmar i namngiven kontolista | Namngivna kontolistor | GET | /rest/v1/namedaccountList/{id}/namedaccounts.json | Skrivskyddat namngivet konto |
| Hämta namngivna kontolistor | Namngivna kontolistor | GET | /rest/v1/namedaccountlists.json | Skrivskyddad lista över namngivna konton |
| Ta bort medlemmar i listan över namngivna konton | Namngivna kontolistor | POST | /rest/v1/namedaccountList/{id}/namedaccounts/remove.json | Läs/skriv namngivet konto |
| Synkronisera namngivna kontolistor | Namngivna kontolistor | POST | /rest/v1/namedaccountlists.json | Läs/skriv namngiven kontolista |
| Ta bort namngivna konton | Namngivna konton | POST | /rest/v1/namedaccounts/delete.json | Läs/skriv namngivet konto |
| Beskriv namngivna konton | Namngivna konton | GET | /rest/v1/namedaccounts/describe.json | Skrivskyddat namngivet konto |
| Hämta namngivna konton | Namngivna konton | GET | /rest/v1/namedaccounts.json | Skrivskyddat namngivet konto |
| Synkronisera namngivna konton | Namngivna konton | POST | /rest/v1/namedaccounts.json | Läs/skriv namngivet konto |
| Hämta namngivet kontofält efter namn | Namngivna konton | GET | /rest/v1/namedaccounts/schema/fields/{fieldApiName}.json | Anpassat fält för läs- och skrivschema |
| Hämta namngivna kontofält | Namngivna konton | GET | /rest/v1/namedaccounts/schema/fields.json | Anpassat fält för läs- och skrivschema |
| Ta bort affärsmöjligheter | Möjligheter | POST | /rest/v1/opportunities/delete.json | Möjligheter att läsa och skriva |
| Ta bort affärsmöjlighetsroller | Möjligheter | POST | /rest/v1/opportunities/roles/delete.json | Möjligheter att läsa och skriva |
| Beskriv affärsmöjlighet | Möjligheter | GET | /rest/v1/opportunities/describe.json | Skrivskyddad möjlighet |
| Beskriv säljprojektsroll | Möjligheter | GET | /rest/v1/opportunities/roles/describe.json | Skrivskyddad möjlighet |
| Få affärsmöjligheter | Möjligheter | GET | /rest/v1/opportunities.json | Skrivskyddad möjlighet |
| Hämta säljprojektsroller | Möjligheter | GET | /rest/v1/opportunities/roles.json | Skrivskyddad möjlighet |
| Synkroniseringsmöjligheter | Möjligheter | POST | /rest/v1/opportunities.json | Möjligheter att läsa och skriva |
| Synkronisera säljprojektsroller | Möjligheter | POST | /rest/v1/opportunities/roles.json | Möjligheter att läsa och skriva |
| Hämta fält för affärsmöjlighet efter namn | Möjligheter | GET | /rest/v1/possibilities/schema/fields/{fieldApiName}.json | Anpassat fält för läs- och skrivschema |
| Hämta fält för affärsmöjlighet | Möjligheter | GET | /rest/v1/opportunities/schema/fields.json | Anpassat fält för läs- och skrivschema |
| Ta bort programmedlemmar | Programmedlemmar | POST | /rest/v1/programs/{programId}/members/delete.json | Läs/skriv lead |
| Beskriv programmedlem | Programmedlemmar | GET | /rest/v1/programs/members/describe.json | Skrivskyddat lead |
| Hämta programmedlemmar | Programmedlemmar | GET | /rest/v1/programs/{programId}/members.json | Skrivskyddat lead |
| Synkronisera programmedlemsuppgifter | Programmedlemmar | POST | /rest/v1/programs/{programId}/members.json | Läs/skriv lead |
| Status för synkroniseringsprogrammedlem | Programmedlemmar | POST | /rest/v1/programs/{programId}/members/status.json | Läs/skriv lead |
| Hämta programmedlemsfält efter namn | Programmedlemmar | GET | /rest/v1/programs/members/schema/fields/{fieldApiName}.json | Anpassat fält för läs- och skrivschema |
| Hämta programmedlemsfält | Programmedlemmar | GET | /rest/v1/programs/members/schema/fields.json | Anpassat fält för läs- och skrivschema |
| Skapa programmedlemsfält | Programmedlemmar | POST | /rest/v1/programs/members/schema/fields.json | Anpassat fält för läs- och skrivschema |
| Medlemsfält för uppdateringsprogram | Programmedlemmar | POST | /rest/v1/programs/members/schema/fields/{fieldApiName}.json | Anpassat fält för läs- och skrivschema |
| Godkänn program | Program | POST | /rest/asset/v1/program/{id}/approve.json | Läs/skriv-resurs |
| Klonprogram | Program | POST | /rest/asset/v1/program/{id}/clone.json | Läs/skriv-resurs |
| Skapa program | Program | POST | /rest/asset/v1/programs.json | Läs/skriv-resurs |
| Ta bort program | Program | POST | /rest/asset/v1/program/{id}/delete.json | Läs/skriv-resurs |
| Hämta program efter ID | Program | GET | /rest/asset/v1/program/{id}.json | Skrivskyddad resurs |
| Hämta program efter namn | Program | GET | /rest/asset/v1/program/byName.json | Skrivskyddad resurs |
| Hämta program | Program | GET | /rest/asset/v1/programs.json | Skrivskyddad resurs |
| Hämta program efter tagg | Program | GET | /rest/asset/v1/program/byTag.json | Skrivskyddad resurs |
| Hämta smart lista per program-ID | Program | GET | /rest/asset/v1/program/{id}/smartList.json | Skrivskyddad resurs |
| Ogodkänt program | Program | POST | /rest/asset/v1/program/{id}/unapprove.json | Läs/skriv-resurs |
| Uppdatera programmetadata | Program | POST | /rest/asset/v1/program/{id}.json | Läs/skriv-resurs |
| Uppdatera programtagg | Program | POST | /rest/asset/v1/program/{id}/tag/{tagType}.json | Läs/skriv-resurs |
| Ta bort programtagg | Program | POST | /rest/asset/v1/program/{id}/tag/{tagType}/delete.json | Läs/skriv-resurs |
| Ta bort säljare | Säljare | POST | /rest/v1/salespersons/delete.json | Skrivskyddad säljare |
| Beskriv säljare | Säljare | GET | /rest/v1/salespersons/describe.json | Skrivskyddad säljare |
| Hämta säljare | Säljare | GET | /rest/v1/salespersons.json | Skrivskyddad säljare |
| Synkronisera säljare | Säljare | POST | /rest/v1/salespersons.json | Skrivskyddad säljare |
| Hämta segment | Segment | GET | /rest/asset/v1/segmentation.json | Skrivskyddad resurs |
| Hämta segment för segmentering | Segment | GET | /rest/asset/v1/segmentation/{id}/segments.json | Skrivskyddad resurs |
| Aktivera smart kampanj | Smarta kampanjer | POST | /rest/asset/v1/smartCampaign/{id}/activate.json | Aktivera kampanj |
| Klona smart kampanj | Smarta kampanjer | POST | /rest/asset/v1/smartCampaign/{id}/clone.json | Läs/skriv-resurs |
| Skapa smart kampanj | Smarta kampanjer | POST | /rest/asset/v1/smartCampaigns.json | Läs/skriv-resurs |
| Inaktivera smart kampanj | Smarta kampanjer | POST | /rest/asset/v1/smartCampaign/{id}/deactivate.json | Inaktivera kampanj |
| Ta bort smart kampanj | Smarta kampanjer | POST | /rest/asset/v1/smartCampaign/{id}/delete.json | Läs/skriv-resurs |
| Hämta smarta kampanjer | Smarta kampanjer | GET | /rest/asset/v1/smartCampaigns.json | Skrivskyddad resurs |
| Hämta Smart Campaign via ID | Smarta kampanjer | GET | /rest/asset/v1/smartCampaign/{id}.json | Skrivskyddad resurs |
| Hämta Smart Campaign efter namn | Smarta kampanjer | GET | /rest/asset/v1/smartCampaign/byName.json | Skrivskyddad resurs |
| Hämta smart lista med Smart Campaign-ID | Smarta kampanjer | GET | /rest/asset/v1/smartCampaign/{id}/smartList.json | Skrivskyddad resurs |
| Uppdatera smart kampanj | Smarta kampanjer | POST | /rest/asset/v1/smartCampaign/{id}.json | Läs/skriv-resurs |
| Klona smart lista | Smarta listor | POST | /rest/asset/v1/smartList/{id}/clone.json | Läs/skriv-resurs |
| Ta bort smart lista | Smarta listor | POST | /rest/asset/v1/smartList/{id}/delete.json | Läs/skriv-resurs |
| Hämta smart lista med ID | Smarta listor | GET | /rest/asset/v1/smartList/{id}.json | Skrivskyddad resurs |
| Hämta smart lista efter namn | Smarta listor | GET | /rest/asset/v1/smartList/byName.json | Skrivskyddad resurs |
| Hämta smarta listor | Smarta listor | GET | /rest/asset/v1/smartLists.json | Skrivskyddad resurs |
| Godkänn kodavsnittsutkast | Fragment | POST | /rest/asset/v1/snippet/{id}/approveDraft.json | Läs/skriv-resurs |
| Klonfragment | Fragment | POST | /rest/asset/v1/snippet/{id}/clone.json | Läs/skriv-resurs |
| Skapa fragment | Fragment | POST | /rest/asset/v1/snippets.json | Läs/skriv-resurs |
| Ta bort fragment | Fragment | POST | /rest/asset/v1/snippet/{id}/delete.json | Läs/skriv-resurs |
| Ignorera kodfragment | Fragment | POST | /rest/asset/v1/snippet/{id}/discardDraft.json | Läs/skriv-resurs |
| Hämta dynamiskt innehåll | Fragment | GET | /rest/asset/v1/snippet/{id}/dynamicContent.json | Skrivskyddad resurs |
| Hämta kodfragment med ID | Fragment | GET | /rest/asset/v1/snippet/{id}.json | Skrivskyddad resurs |
| Hämta fragmentinnehåll | Fragment | GET | /rest/asset/v1/snippet/{id}/content.json | Skrivskyddad resurs |
| Hämta fragment | Fragment | GET | /rest/asset/v1/snippets.json | Skrivskyddad resurs |
| Ogodkänt kodfragment | Fragment | POST | /rest/asset/v1/snippet/{id}/unapprove.json | Läs/skriv-resurs |
| Uppdatera fragmentinnehåll | Fragment | POST | /rest/asset/v1/snippet/{id}/content.json | Läs/skriv-resurs |
| Uppdatera dynamiskt innehåll för kodfragment | Fragment | POST | /rest/asset/v1/snippet/{id}/dynamicContent/{segmentId}.json | Läs/skriv-resurs |
| Uppdatera kodavsnittsmetadata | Fragment | POST | /rest/asset/v1/snippet/{id}.json | Läs/skriv-resurs |
| Lägg till i listan | Statiska listor | POST | /rest/v1/lists/{listId}/leads.json | Läs/skriv lead |
| Skapa statisk lista | Statiska listor | POST | /asset/v1/staticLists.json | Läs/skriv-resurs |
| Ta bort statisk lista | Statiska listor | POST | /asset/v1/staticList/{id}/delete.json | Läs/skriv-resurs |
| Hämta leads efter list-ID | Statiska listor | GET | /rest/v1/lists/{listId}/leads.json | Skrivskyddat lead |
| Hämta lista efter ID | Statiska listor | GET | /rest/v1/lists/{id}.json | Skrivskyddat lead |
| Hämta listor | Statiska listor | GET | /rest/v1/lists.json | Skrivskyddat lead |
| Hämta statisk lista efter ID | Statiska listor | GET | /asset/v1/staticList/{id}.json | Skrivskyddad resurs |
| Hämta statisk lista efter namn | Statiska listor | GET | /asset/v1/staticList/byName.json | Skrivskyddad resurs |
| Hämta statiska listor | Statiska listor | GET | /asset/v1/staticLists.json | Skrivskyddad resurs |
| List-medlem | Statiska listor | GET | /rest/v1/lists/{listId}/leads/ismember.json | Skrivskyddat lead |
| Ta bort från lista | Statiska listor | DELETE | /rest/v1/lists/{listId}/leads.json | Läs/skriv lead |
| Uppdatera metadata för statisk lista | Statiska listor | POST | /asset/v1/staticList/{id}.json | Läs/skriv-resurs |
| Hämta tagg efter namn | Taggar | GET | /rest/asset/v1/tagType/byName.json | Skrivskyddad resurs |
| Hämta taggtyper | Taggar | GET | /rest/asset/v1/tagTypes.json | Skrivskyddad resurs |
| Skapa token | Tokens | POST | /rest/asset/v1/folder/{id}/tokens.json | Läs/skriv-resurs |
| Ta bort token efter namn | Tokens | POST | /rest/asset/v1/folder/{id}/tokens/delete.json | Läs/skriv-resurs |
| Hämta token efter mapp-ID | Tokens | GET | /rest/asset/v1/folder/{id}/tokens.json | Skrivskyddad resurs |
| Lägg till roller | Användarhantering | POST | /userservice/management/v1/users/{userid}/roles/create.json | Åtkomst till API för användarhantering |
| Ta bort inbjuden användare | Användarhantering | POST | /userservice/management/v1/users/{userId}/invite/delete.json | Åtkomst till API för användarhantering |
| Ta bort roller | Användarhantering | POST | /userservice/management/v1/users/{userid}/roles/delete.json | Åtkomst till API för användarhantering |
| Ta bort användare | Användarhantering | POST | /userservice/management/v1/users/{userId}/delete.json | Åtkomst till API för användarhantering |
| Hämta inbjuden användare via ID | Användarhantering | GET | /userservice/management/v1/users/{userid}/invite.json | Åtkomst till API för användarhantering |
| Hämta roller | Användarhantering | GET | /userservice/management/v1/users/roles.json | Åtkomst till API för användarhantering |
| Hämta roller och arbetsytor efter ID | Användarhantering | GET | /userservice/management/v1/users/{userid}/roles.json | Åtkomst till API för användarhantering |
| Hämta användare | Användarhantering | GET | /userservice/management/v1/users/allusers.json | Åtkomst till API för användarhantering |
| Hämta användare efter ID | Användarhantering | GET | /userservice/management/v1/users/{userid}/user.json | Åtkomst till API för användarhantering |
| Hämta arbetsytor | Användarhantering | GET | /userservice/management/v1/users/workspaces.json | Åtkomst till API för användarhantering |
| Bjud in användare | Användarhantering | POST | /userservice/management/v1/users/invite.json | Åtkomst till API för användarhantering |
| Uppdatera användarattribut | Användarhantering | POST | /userservice/management/v1/users/{userId}/update.json | Åtkomst till API för användarhantering |