---
title: SOAP API
feature: SOAP
description: Marketo SOAP - översikt
exl-id: 6618cc82-15ae-4030-aa00-438e635d8369
source-git-commit: 7a3df193e47e7ee363c156bf24f0941879c6bd13
workflow-type: tm+mt
source-wordcount: '207'
ht-degree: 0%

---

# SOAP API

SOAP API är borttaget och kommer inte att vara tillgängligt efter den 31 oktober 2025.  All ny utveckling ska utföras med Marketo [REST](https://developer.adobe.com/marketo-apis/) API, och befintliga tjänster ska migreras före detta datum för att undvika avbrott i tjänsten.

## SOAP WSDL

Hämta SOAP API-slutpunkten från menyn **[!UICONTROL Admin]** > **[!UICONTROL Integration]** > **[!UICONTROL Web Services]** om du vill hämta det SOAP WSDL-dokumentet.

![SOAP slutpunkt](assets/endpoint-soap.png)

WSDL-URL:

`<SOAP API Endpoint> + ?WSDL`

Använd inte slutpunkten som definieras i WSDL. Varje Marketo-instans har en unik slutpunkt där anrop ska göras.

## Gränser

- **Daglig kvot:** De flesta prenumerationer tilldelas 10 000 API-anrop per dag (som återställs dagligen kl. 12:00 CST). Du kan öka din dagliga kvot med din kontohanterare.
- **Hastighetsgräns:** API-åtkomst per instans begränsad till 100 anrop per 20 sekunder.
- **Samtidighetsgräns:**  Max tio samtidiga API-anrop.

Vi rekommenderar att gruppstorlekarna inte är större än 300. Större storlekar stöds inte och kan resultera i timeout och i extrema fall strypas.

## SOAP API-inställningar i Marketo

1. Gå till avsnittet **[!UICONTROL Admin]** och klicka på **[!UICONTROL Web Services]**.

![admin-web-services2](assets/admin-web-services2.png)

1. Ange ett lämpligt [!UICONTROL Encryption Key], klicka på **[!UICONTROL Save Changes]** och använd SOAP API [!UICONTROL Endpoint] -, [!UICONTROL User ID] - och [!UICONTROL Encryption Key] -värden för att generera rätt [autentiseringssignatur](authentication-signature.md) för varje SOAP API-anrop.

![admin-web-services3](assets/admin-web-services3.png)
