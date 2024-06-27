---
title: SOAP API
feature: SOAP
description: Marketo SOAP - översikt
exl-id: 6618cc82-15ae-4030-aa00-438e635d8369
source-git-commit: 6fc45ff98998217923e2a5b02d00d1522fe3272c
workflow-type: tm+mt
source-wordcount: '230'
ht-degree: 0%

---

# SOAP API

SOAP-API:t är inte längre under aktiv utveckling. Anropen fungerar fortfarande, men vår utveckling fokuserar på [REST](https://developer.adobe.com/marketo-apis/) gå framåt.

Med Marketo SOAP API kan du skapa, hämta och ta bort enheter och data som lagras i Marketo. Du hittar [Marketo-SOAP-SDK](https://github.com/Marketo/SOAP-API-Java-Client) på GitHub. Det finns också [klientbibliotek](https://github.com/Marketo/Community-Supported-Client-Libraries) för att spara tid åt dig.

Senaste API-version: 3_1

## SOAP WSDL

Hämta din SOAP API-slutpunkt från din dator för att hämta det SOAP WSDL-dokumentet **[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]** -menyn.

![SOAP](assets/endpoint-soap.png)

WSDL-URL:

`<SOAP API Endpoint> + ?WSDL`

Använd inte slutpunkten som definieras i WSDL. Varje Marketo-instans har en unik slutpunkt där anrop ska göras.

## Gränser

- **Daglig kvot:** De flesta prenumerationer tilldelas 10 000 API-anrop per dag (som återställs dagligen kl. 12:00 CST). Du kan öka din dagliga kvot med din kontohanterare.
- **Hastighetsgräns:** API-åtkomst per instans begränsad till 100 anrop per 20 sekunder.
- **Samtidighetsgräns:**  Max tio samtidiga API-anrop.

Vi rekommenderar att gruppstorlekarna inte är större än 300. Större storlekar stöds inte och kan resultera i timeout och i extrema fall strypas.

## SOAP API-inställningar i Marketo

1. Gå till **[!UICONTROL Admin]** och klicka **[!UICONTROL Web Services]**.

![admin-web-services2](assets/admin-web-services2.png)

1. Ange lämplig [!UICONTROL Encryption Key], klicka **[!UICONTROL Save Changes]** och använda SOAP API [!UICONTROL Endpoint], [!UICONTROL User ID]och [!UICONTROL Encryption Key] värden för att generera rätt [autentiseringssignatur](authentication-signature.md) för varje SOAP API-anrop.

![admin-web-services3](assets/admin-web-services3.png)
