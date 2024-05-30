---
title: "Marketo Mobile Extension for [!DNL Adobe Launch]"
feature: Mobile Marketing
description: "Marketo Mobile Extension for [!DNL Adobe Launch] overview"
source-git-commit: 2185972a272b64908d6aac8818641af07c807ac2
workflow-type: tm+mt
source-wordcount: '269'
ht-degree: 0%

---


# Marketo Mobile Extension for [!DNL Adobe Launch]

Installationsanvisningar för Marketo Mobile SDK-tillägg i [!DNL Adobe Launch]. Stegen nedan krävs för att skicka push-meddelanden och/eller meddelanden i appen.

## Förutsättningar

- [Lägga till ett program i Marketo Admin](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app) (hämta programmets hemliga nyckel och Munchkin-ID)
- Följ instruktionerna i [!DNL Adobe Launch] portal för installation
- [Konfigurera push-meddelanden](push-notifications.md) (valfritt)

## iOS

### Ställ in Swift Bridging Header

1. Gå till Arkiv > Nytt > Arkiv och välj &quot;Huvudfil&quot;.
1. Namnge filen &quot;&lt;_ProjectName_>-Bridging-Header&quot;.
1. Gå till Project > Target > Build Phases > Swift Compiler > Code Generation. Lägg till följande sökväg till Objective-Bridging Header:

   `$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

För Swift-användare: Ta bort följande import-sats eftersom brygghuvudet läggs till i ovanstående steg.

`import Marketo/ALMarketo`

### iOS Test Devices

Följ instruktionerna på [Lägga till iOS testenheter](installation.md#ios_test_devices)

### Hantera anpassad URL-typ i AppDelegate

Följ instruktionerna [här](installation.md#ios_test_devices)

### Konfigurera push-meddelanden på iOS

Följ instruktionerna [här](push-notifications.md) och använd klassnamnet&quot;ALMarketo&quot; i stället för&quot;Marketo&quot;

## Android

### Konfigurera behörigheter

Öppna `AndroidManifest.xml` och lägga till följande behörigheter. Ditt program måste begära behörigheterna INTERNET och ACCESS_NETWORK_STATE. Om appen redan begär dessa behörigheter hoppar du över det här steget.

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

### ProGuard-konfiguration (valfritt)

Om du använder ProGuard för din app lägger du till följande rader i din `proguard.cfg` -fil. Filen finns i projektmappen. Om du lägger till den här koden utesluts Marketo SDK från döljningsprocessen.

```
-dontwarn com.marketo.*
-dontnote com.marketo.*
-keep class com.marketo.**{ *; }
```

### Android-testenheter

Följ instruktionerna [här](installation.md#android_test_devices)

## Konfigurera push-meddelanden på Android

Följ instruktionerna [här](installation.md#android_firebase_cloud_messaging_support) och använd klassnamnet&quot;ALMarketo&quot; i stället för&quot;Marketo&quot;

Följ instruktionerna för hur du konfigurerar användarprofiler [här](user-profiles.md) och för anpassade åtgärder följ instruktionerna [här](custom-actions.md#android_custom_action). I följande instruktioner använder du klassnamnet&quot;ALMarketo&quot; i stället för&quot;Marketo&quot;
