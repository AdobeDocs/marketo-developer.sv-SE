---
title: Marketo Mobile Extension för  [!DNL Adobe Launch]
feature: Mobile Marketing
description: Marketo Mobile Extension för  [!DNL Adobe Launch] översikt
exl-id: 2f8691ff-0442-45a5-aeba-c91c3af5c711
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '269'
ht-degree: 0%

---

# Marketo Mobile Extension för [!DNL Adobe Launch]

Installationsanvisningar för Marketo Mobile SDK-tillägg i [!DNL Adobe Launch]. Stegen nedan krävs för att skicka push-meddelanden och/eller meddelanden i appen.

## Förutsättningar

- [Lägg till ett program i Marketo Admin](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app) (hämta programhemlig nyckel och Munchkin-ID)
- Följ anvisningarna i [!DNL Adobe Launch]-portalen för installation
- [Konfigurera push-meddelanden](push-notifications.md) (valfritt)

## iOS

### Ställ in Swift Bridging Header

1. Gå till Arkiv > Nytt > Arkiv och välj &quot;Huvudfil&quot;.
1. Ge filen namnet &lt;_ProjectName_>-Bridging-Header.
1. Gå till Project > Target > Build Phases > Swift Compiler > Code Generation. Lägg till följande sökväg till Objective-Bridging Header:

   `$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

För Swift-användare: Ta bort följande import-sats eftersom brygghuvudet läggs till i ovanstående steg.

`import Marketo/ALMarketo`

### iOS Test Devices

Följ anvisningarna på [Lägga till iOS testenheter](installation.md#ios_test_devices)

### Hantera anpassad URL-typ i AppDelegate

Följ instruktionerna [här](installation.md#ios_test_devices)

### Konfigurera push-meddelanden på iOS

Följ instruktionerna [här](push-notifications.md) och använd klassnamnet ALMarketo i stället för Marketo

## Android

### Konfigurera behörigheter

Öppna `AndroidManifest.xml` och lägg till följande behörigheter. Ditt program måste begära behörigheterna INTERNET och ACCESS_NETWORK_STATE. Om appen redan begär dessa behörigheter hoppar du över det här steget.

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

### ProGuard-konfiguration (valfritt)

Om du använder ProGuard för din app lägger du till följande rader i din `proguard.cfg`-fil. Filen finns i projektmappen. Om du lägger till den här koden utesluts Marketo SDK från döljningsprocessen.

```
-dontwarn com.marketo.*
-dontnote com.marketo.*
-keep class com.marketo.**{ *; }
```

### Android Test Devices

Följ instruktionerna [här](installation.md#android_test_devices)

## Konfigurera push-meddelanden på Android

Följ instruktionerna [här](installation.md#android_firebase_cloud_messaging_support) och använd klassnamnet ALMarketo i stället för Marketo

Följ instruktionerna [här](user-profiles.md) och för anpassade åtgärder [här](custom-actions.md#android_custom_action) för att konfigurera användarprofiler. I följande instruktioner använder du klassnamnet&quot;ALMarketo&quot; i stället för&quot;Marketo&quot;
