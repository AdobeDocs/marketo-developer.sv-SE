---
title: "SOAP API"
feature: SOAP
description: "Marketo SOAP - översikt"
source-git-commit: 2185972a272b64908d6aac8818641af07c807ac2
workflow-type: tm+mt
source-wordcount: '242'
ht-degree: 0%

---


# SOAP API

SOAP API är inte längre under aktiv utveckling. Anropen fungerar fortfarande, men vår utveckling fokuserar på [REST](https://developer.adobe.com/marketo-apis/) gå framåt.

Med Marketo SOAP API kan du skapa, hämta och ta bort enheter och data som lagras i Marketo. Du hittar [Marketo-SOAP-SDK](https://github.com/Marketo/SOAP-API-Java-Client) på GitHub. Det finns också [klientbibliotek](https://github.com/Marketo/Community-Supported-Client-Libraries) för att spara tid åt dig.

Senaste API-version: 3_1

## SOAP WSDL

Hämta SOAP API-slutpunkten från din **[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]** -menyn.

![SOAP-slutpunkt](assets/endpoint-soap.png)

WSDL-URL:

`<SOAP API Endpoint> + ?WSDL`

Använd inte slutpunkten som definieras i WSDL. Varje Marketo-instans har en unik slutpunkt där anrop ska göras.

## Gränser

- **Daglig kvot:** De flesta prenumerationer tilldelas 10 000 API-anrop per dag (som återställs dagligen kl. 12:00 CST). Du kan öka din dagliga kvot med din kontohanterare.
- **Hastighetsgräns:** API-åtkomst per instans begränsad till 100 anrop per 20 sekunder.
- **Samtidighetsgräns:**  Max tio samtidiga API-anrop.

Vi rekommenderar att gruppstorlekarna inte är större än 300. Större storlekar stöds inte och kan resultera i timeout och i extrema fall strypas.

## SOAP API-inställningar i Marketo

1. Gå till avsnittet Admin och klicka på Webbtjänster.

![admin-web-services2](assets/admin-web-services2.png)

1. Ange en lämplig krypteringsnyckel, klicka på Spara ändringar och använd värdena för SOAP API-slutpunkt, användar-ID och krypteringsnyckel för att generera rätt [autentiseringssignatur](authentication-signature.md) för varje SOAP API-anrop.

![admin-web-services3](assets/admin-web-services3.png)
