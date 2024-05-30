---
title: Standardfält
feature: REST API, Field Management
description: "En tabell med Marketo standardfält."
source-git-commit: 8c1ffb6db05da49e7377b8345eeb30472ad9b78b
workflow-type: tm+mt
source-wordcount: '1140'
ht-degree: 7%

---


# Standardfält

Här är en lista över standardfält i Marketo som är tillgängliga via API:t.

Du kan hämta listan med alla fältnamn som stöds som är tillgängliga på dina lead-poster med hjälp av REST [Beskriv lead](https://developer.adobe.com/marketo-apis/api/mapi/) slutpunkt.

| REST API-namn | SOAP API-namn | Etikett | Beskrivning |
| --- | --- | --- | --- |
| adress | Adress | Adress | Leads adress |
| annualRevenue | Årsintäkt | Årlig intäkt | Årsintäkt för leadets företag |
| anonymousIP | AnonymIP | Anonym IP | IP-adress för leadets första registrerade webbbesök |
| billingCity | BillingCity | Faktureringsort | Ort för leadets faktureringsadress |
| faktureringsland | Faktureringsland | Faktureringsland | Land för leadets faktureringsadress |
| billingPostalCode | FaktureringPostnummer | Faktureringspostnummer | Postnummer för leadets faktureringsadress |
| billingState | BillingState | Faktureringsstat | Region för leadets faktureringsadress |
| billingStreet | BillingStreet | Faktureringsadress | Faktureringsgatuadress till leadets företag |
| stad | Ort | Ort | Leads stad |
| företag | Företag | Företagets namn | Leads företagsnamn |
| land | Land | Land | Leadens land |
| dateOfBirth | Födelsedatum | Födelsedatum | Leadens födelsedatum |
| avdelning | Avdelning | Avdelning | Leads avdelning i deras företag |
| doNotCall | DoNotCall | Ring inte | Leads inställning för att inte ringa |
| doNotCallReason | DoNotCallReason | Orsak till ring inte | Förklaring av leadets inställning för att inte ringa |
| e-post | E-post | E-postadress | Leads e-postadress. Marketo standardnyckelfält för lead-poster |
| fax | Fax | Faxnummer | Leads faxnummer |
| firstName | FirstName | Förnamn | Leads förnamn |
| bransch | Bransch | Bransch | Ledningsbranschen |
| inputCompany | IncedCompany | Berört företag | Företagsnamn som härleds av omvänd IP-sökning för leadets första inspelade webbbesök |
| incedCountry | IncedCountry | Berört land | Land som härleds av omvänd IP-sökning för leadets första inspelade webbbesök |
| lastName | LastName | Efternamn | Leads efternamn |
| leadRole | LeadRole | Roll | Leads roll i deras företag |
| leadScore | LeadScore | Leadpoäng | Heltalspoäng som tilldelats leadet genom poängsättning av kampanjer och program |
| leadSource | LeadSource | Leadkälla | Fältinspelning av källan som leadet kommer från |
| leadStatus | LeadStatus | Leadkälla | Fältregistrering av lead aktuella marknadsförings-/försäljningsstatus |
| mainPhone | MainPhone | Telefon | Huvudtelefonnummer till leadets företag |
| jigsawContactId | Kontakt-ID för Marketo Jigsaw | MARKETO Data.com ID | Leads Data.com ID om tillgängligt |
| jigsawContactStatus | Marketo Jigsaw-kontaktstatus | Marketo Data.com | Leadens Data.com status om den är tillgänglig |
| facebookDisplayName | MarketoSocialFacebookDisplayName | Marketo Social Facebook Display Name | Lead&#39;s Facebook display name. Systemet fylldes i under social inloggning |
| facebookId | MarketoSocialFacebookId | Marketo Social Facebook ID | Lead&#39;s Facebook Id. Systemet fylldes i under social inloggning |
| facebookPhotoURL | MarketoSocialFacebookPhotoURL | Marketo Social Facebook Photo URL | URL för leadets Facebook-profilfoto. Systemet fylldes i under social inloggning |
| facebookProfileURL | MarketoSocialFacebookProfilURL | Marketo Social Facebook Profile URL | URL för leadets Facebook-profil. Systemet fylldes i under social inloggning |
| facebookReach | MarketoSocialFacebookReach | Marketo Social Facebook Reach | Ledningens räckvidd för Facebook. Systemet fylldes i under social inloggning |
| facebookReferredEnrollments | MarketoSocialFacebookRefereradRegistreringar | Refererade registreringar från Marketo Social Facebook | Antal refererade registreringar som tilldelats leadet via Facebook. Systemhantering |
| facebookReferredVisits | MarketoSocialFacebookRefereradeBesök | Refererade Marketo Social Facebook-besök | Antal refererade besök som tillskrivits leadet via Facebook. Systemhantering |
| kön | MarketoSocialGenus | Marketo Social Genus | Ledens kön. Systemet fylldes i under social inloggning |
| lastReferredEnrollment | MarketoSocialLastRefereradRegistrering | Marketo Social, senast refererad registrering | Datum för senaste avslutade hänskjutning. Systemhantering |
| lastReferredVisit | MarketoSocialLastRefereradBesök | Marketo Social, senaste refererat besök | Datum för senaste refererat besök. Systemhantering |
| linkedInDisplayName | MarketoSocialLinkedInDisplayName | Marketo Social LinkedIn Display Name | Lead&#39;s LinkedIn display name. Systemet fylldes i under social inloggning |
| linkedInId | MarketoSocialLinkedInId | Marketo Social LinkedIn ID | Lead&#39;s LinkedIn Id. Systemet fylldes i under social inloggning |
| linkedInPhotoURL | MarketoSocialLinkedInPhotoURL | Marketo Social LinkedIn Photo URL | Lead&#39;s LinkedIn photo URL. Systemet fylldes i under social inloggning |
| linkedInProfileURL | MarketoSocialLinkedInProfileURL | Marketo Social LinkedIn Profile URL | Leads LinkedIn-profil. Systemet fylldes i under social inloggning |
| linkedInReach | MarketoSocialLinkedInReach | Marketo Social LinkedIn Reach | Leads LinkedIn är här. Systemet fylldes i under social inloggning |
| linkedInReferredEnrollments | MarketoSocialLinkedInRefereradRegistreringar | Refererade registreringar från Marketo Social LinkedIn | Antal refererade registreringar som tilldelats leadet via LinkedIn. Systemhantering |
| linkedInReferredVisits | MarketoSocialLinkedInRefereradBesök | Refererade Marketo Social LinkedIn-besök | Antal refererade besök som tillskrivits leadet via LinkedIn. Systemhantering |
| syndicationId |  - | Marketo Social Syndication ID | Leads interna Marketo Social ID. Systemhantering |
| totalReferredEnrollments | MarketoSocialTotalRefereradRegistreringar | Marketo Social, totalt antal refererade registreringar | Totalt antal slutförda hänvisningsregistreringar som tilldelats leadet |
| totalRefereradBesök | MarketoSocialTotalRefereradeBesök | Marketo Social, totalt antal refererade besök | Totalt antal refererade besök som tilldelats leadet |
| twitterDisplayName | MarketoSocialTwitterDisplayName | Visningsnamn för Marketo Social Twitter | Leads visningsnamn för Twitter. Systemet fylldes i under social inloggning |
| twitterId | MarketoSocialTwitterId | Marketo Social Twitter-ID | Leads Twitters-ID. Systemet fylldes i under social inloggning |
| twitterPhotoURL | MarketoSocialTwitterPhotoURL | Marketo Social Twitter Photo URL | Leads foto-URL för Twitter. Systemet fylldes i under social inloggning |
| twitterProfileURL | MarketoSocialTwitterProfilURL | Profil-URL för Marketo Social Twitter | Leads Twitter profile URL. Systemet fylldes i under social inloggning |
| twitterReach | MarketoSocialTwitterReach | Marketo Social Twitter Reach | Leads Twitter når. Systemet fylldes i under social inloggning |
| twitterReferredEnrollments | MarketoSocialTwitterRefereradeRegistreringar | Refererade registreringar för Marketo Social Twitter | Antal refererade registreringar som tilldelats leadet via Twitter. Systemhantering |
| twitterReferredVisits | MarketoSocialTwitterRefereradeBesök | Refererade besök i Marketo Social Twitter | Antal refererade besök som tilldelats leadet via Twitter. Systemhantering |
| middleName | MiddleName | Mellannamn | Leads mellannamn |
| mobilePhone | MobilePhone | Mobiltelefon | Leads mobiltelefonnummer |
| numberOfEmployees | AntalAnställda | Antal anställda | Antal anställda i lead-företaget |
| telefon | Tel. | Telefonnummer | Leads telefonnummer |
| mailCode | Postnummer | Postnummer | Leads postnummer |
| värdering | Klassificering | Leadvärdering | Marknadsföring/försäljningsvärdering för leadet |
| hälsningsfras | Titel | Titel | Lead&#39;s preferred salutation, dvs. Mister, Misses... osv. |
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
