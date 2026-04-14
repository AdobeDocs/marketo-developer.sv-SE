---
title: Standardfält
feature: REST API, Field Management
description: Bläddra i den fullständiga listan över Marketo standardlead-fält med REST- och SOAP-namn, etiketter och beskrivningar samt hur du hämtar dem via API:t Beskriv lead.
exl-id: 147dbdff-4bc9-4ab3-8918-c4de3e1aa97a
source-git-commit: ff0a95e838cecd1d8b1f90ca029a320043824242
workflow-type: tm+mt
source-wordcount: '742'
ht-degree: 7%

---

# Standardfält

Här är en lista över standardfält i Marketo som är tillgängliga via API:t.

Du kan hämta listan med alla fältnamn som stöds i dina lead-poster med REST-slutpunkten [Beskriv lead](https://developer.adobe.com/marketo-apis/api/mapi).

| REST API-namn | SOAP API-namn | Etikett | Beskrivning |
| --- | --- | --- | --- |
| adress | Adress | Adress | Leads adress |
| annualRevenue | Årsintäkt | Årlig intäkt | Årsintäkt för leadets företag |
| anonymousIP | AnonymIP | Anonym IP | IP-adress för leadets första registrerade webbbesök |
| billingCity | BillingCity | Faktureringsort | Ort för leadets faktureringsadress |
| faktureringsland | Faktureringsland | Faktureringsland | Land för leadets faktureringsadress |
| billingPostalCode | FaktureringPostnummer | Postnummer för fakturering | Postnummer för leadets faktureringsadress |
| billingState | BillingState | Faktureringsläge | Region för leadets faktureringsadress |
| billingStreet | BillingStreet | Faktureringsadress | Faktureringsgatuadress till leadets företag |
| stad | Ort | Ort | Leads stad |
| företag | Företag | Företagsnamn | Leads företagsnamn |
| land | Land | Land | Leadens land |
| dateOfBirth | Födelsedatum | Födelsedatum | Leadens födelsedatum |
| avdelning | Avdelning | Avdelning | Leads avdelning i deras företag |
| doNotCall | DoNotCall | Ring inte | Leads inställning för att inte ringa |
| doNotCallReason | DoNotCallReason | Anropa inte orsak | Förklaring av leadets inställning för att inte ringa |
| e-post | E-post | E-postadress | Leads e-postadress. Marketo standardnyckelfält för lead-poster |
| fax | Fax | Faxnummer | Leads faxnummer |
| firstName | FirstName | Förnamn | Leads förnamn |
| bransch | Bransch | Bransch | Ledningsbranschen |
| inputCompany | IncedCompany | Infört företag | Företagsnamn som härleds av omvänd IP-sökning för leadets första inspelade webbbesök |
| incedCountry | IncedCountry | Infört land | Land som härleds av omvänd IP-sökning för leadets första inspelade webbbesök |
| lastName | LastName | Efternamn | Leads efternamn |
| leadRole | LeadRole | Roll | Leads roll i deras företag |
| leadScore | LeadScore | Lead-poäng | Heltalspoäng som tilldelats leadet genom poängsättning av kampanjer och program |
| leadSource | LeadSource | Lead Source | Fältinspelning av källan som leadet kommer från |
| leadStatus | LeadStatus | Leadkälla | Fältregistrering av lead aktuella marknadsförings-/försäljningsstatus |
| mainPhone | MainPhone | Huvudtelefon | Huvudtelefonnummer till leadets företag |
| jigsawContactId | Kontakt-ID för Marketo Jigsaw | MARKETO Data.com ID | Leads Data.com ID om tillgängligt |
| jigsawContactStatus | Marketo Jigsaw-kontaktstatus | Marketo Data.com | Leadens Data.com status om den är tillgänglig |
| middleName | MiddleName | Mellannamn | Leads mellannamn |
| mobilePhone | MobilePhone | Mobiltelefon | Leads mobiltelefonnummer |
| numberOfEmployees | AntalAnställda | Antal anställda | Antal anställda i lead-företaget |
| telefon | Telefon | Telefonnummer | Leads telefonnummer |
| mailCode | Postnummer | Postnummer | Leads postnummer |
| värdering | Klassificering | Lead-klassificering | Marknadsföring/försäljningsvärdering för leadet |
| hälsningsfras | Titel | Titel | Leads hälsningsfras, det vill säga &quot;Mister&quot;, &quot;Misses&quot; osv. |
| sicCode | SICCode | SNI-kod | Standard Industrial Classification Code of the lead&#39;s company |
| webbplats | Plats | Plats |  |
| läge | Stat | Stat | Lead&#39;s state |
| title | Titel | Befattning | Leads befattning |
| avbeställa | Avprenumererad | Avprenumererad | Leadens status för e-postavbeställning. Delvis systemhanterad. Hindrar från att ta emot icke-fungerande e-postmeddelanden om värdet är true. |
| unsubscribedReason | UnsubscribedReason | Orsak till avprenumeration | Orsak till leadets avbeställningsstatus. Delvis systemhanterad. Fylls i med e-postinformation om lead-avbeställning direkt från ett Marketo-mejl. |
| webbplats | Webbplats | Webbplats | URL till webbplatsen för leadföretagets företag |
| createdAt |  - | Skapad den | Den tid då lead-posten skapades. Systemhantering |
| updatedAt |  - | Uppdaterat den | Den senaste gången lead-posten uppdaterades. Systemhantering |
| emailInvalid |  - | Ogiltig e-postadress | Ogiltig e-poststatus. Alla e-postmeddelanden till adressen blockeras om värdet är true. Satser som anger att e-postadressen är ogiltig anger automatiskt att det här fältet är true. |
| emailInvalidreason |  - | Ogiltig e-postorsak | Orsak till e-postadress med ogiltig status. Det inledande studsmeddelandet spelas in i det här fältet när e-postadressen ogiltig är inställd på true. |
| incedCity |  - | Inaktuell ort | Leadens stad framgår av omvänd IP-sökning efter leadets första inspelade webbbesök. |
| inputMetropolitanArea |  - | Ingående metropolitområde | Leads storstadsområde framgår av omvänd IP-sökning efter Leads första inspelade webbbesök. |
| inputPhoneAreaCode |  - | Riktnummer för inkommande telefon | Leads telefonområdeskod framgår av omvänd IP-sökning för leadets första inspelade webbbesök. |
| inppedPostalCode |  - | Infört postnummer | Leadens postnummer framgår av omvänd IP-sökning av leadets första inspelade webbbesök. |
| inputStateRegion |  - | Ingångsregion | Leads delstatsregion framgår av omvänd IP-sökning av leadets första inspelade webbbesök. |
| isAnonymous |  - | Är anonym | Anonym status för lead-posten. Systemhanterad. |
| prioritet |  - | Prioritet | Lead&#39;s Sales Insight priority. Systemhanterad. |
| relativeScore |  - | Relativa poäng | Lead&#39;s Sales Insight relative score. Systemhanterad. |
| brådska |  - | Akut | Leads Sales Insight trängsel. Systemhanterad. |
