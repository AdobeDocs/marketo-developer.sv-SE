---
title: '''[!DNL Adobe Launch] Tilläggsinstallation'
feature: Mobile Marketing
description: '[!DNL Adobe Launch] installationsöversikt för tillägg'
exl-id: d71b7cd7-309b-4882-9bba-7daaaa5ef32d
source-git-commit: 6fc45ff98998217923e2a5b02d00d1522fe3272c
workflow-type: tm+mt
source-wordcount: '665'
ht-degree: 0%

---

# [!DNL Adobe Launch] Tilläggsinstallation

Installationsanvisningar för [!DNL Adobe Launch] Marketo. Stegen nedan krävs för att skicka push-meddelanden och/eller meddelanden i appen.

## Förutsättningar

1. [Lägga till ett program i Marketo Admin](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app) (hämta programmets hemliga nyckel och Munchkin-ID)
1. [Konfigurera egenskapen i [!DNL Adobe Launch] portal](https://experience.adobe.com/#/@amc/data-collection/home)
1. Konfigurera programhemlig nyckel och Munchkin-ID för egenskapen i [!DNL Adobe Launch] portal
1. [Konfigurera push-meddelanden](push-notifications.md) (valfritt)

## Installera Marketo Extension på iOS

### Ställ in Swift Bridging Header

1. Gå till [!UICONTROL File] > [!UICONTROL New] > [!UICONTROL File] och välj **[!UICONTROL Header File]**.

1. Namnge filen &quot;&lt;_ProjectName_>-Bridging-Header&quot;.

1. Gå till [!UICONTROL Project] > [!UICONTROL Target] > [!UICONTROL Build Settings] > [!UICONTROL Swift Compiler] > [!UICONTROL Code Generation]. Lägg till följande sökväg i sidhuvudet &quot;Objective-Bridging&quot;:

`$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

## Initiera tillägg

>[!BEGINTABS]

>[!TAB Mål C]

Uppdatera `applicationDidBecomeActive` metod som nedan

```
(void)applicationDidBecomeActive:(UIApplication*) application
{
 [[ALMarketo sharedInstance] initializeMarketo:nil];
}
```

>[!TAB Swift]

Uppdatera `applicationDidBecomeActive` metod som nedan

```
func applicationDidBecomeActive(_ application: UIApplication)
{
 ALMarketo.sharedInstance().initializeMarketo(nil)
}
```

>[!ENDTABS]

## iOS Test Devices

1. Välj **[!UICONTROL Project]** > **[!UICONTROL Target]** > **[!UICONTROL Info]** > **[!UICONTROL URL Types]**.
1. Lägg till identifierare: ${PRODUCT_NAME}
1. Ange URL-scheman: mkto-&lt;s_ecret key_=&quot;&quot;>
1. Inkludera `application:openURL:sourceApplication:annotation:` till `AppDelegate.m file` (Mål-C)

### Hantera anpassad URL-typ i AppDelegate

>[!BEGINTABS]

>[!TAB Mål C]

```
#ifdef __IPHONE_10_0
-(BOOL)application:(UIApplication *)application 
           openURL:(NSURL *)url 
           options:(NSDictionary *)options{
    return [[ALMarketo sharedInstance] application:application
                                         openURL:url
                               sourceApplication:nil
                                      annotation:nil];
}
#endif

- (BOOL)application:(UIApplication *)application
            openURL:(NSURL *)url
  sourceApplication:(NSString *)sourceApplication
         annotation:(id)annotation {
    return [[ALMarketo sharedInstance] application:application
                                         openURL:url
                               sourceApplication:nil
                                      annotation:nil];
}
```

>[!TAB Swift]

```
func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    return ALMarketo.sharedInstance().application(application, open: url, sourceApplication: nil, annotation: nil)
}
```

>[!ENDTABS]

## Installera Marketo SDK på Android

### Installationsprogram för Android Extension

Följ instruktionerna i [!DNL Adobe Launch] portal

### Konfigurera behörigheter

Öppna `AndroidManifest.xml` och lägga till följande behörigheter. Ditt program måste begära behörigheterna INTERNET och ACCESS_NETWORK_STATE. Om appen redan begär dessa behörigheter hoppar du över det här steget.

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

## Initiera tillägg

ProGuard-konfiguration (valfritt)

Om du använder ProGuard för din app lägger du till följande rader i din `proguard.cfg` -fil. Filen finns i din `project` mapp. Om du lägger till den här koden utesluts Marketo SDK från döljningsprocessen.

```
-dontwarn com.marketo.*
-dontnote com.marketo.*
-keep class com.marketo.**{ *; }
```

## Android Test Devices

Lägg till MarketoActivity i `AndroidManifest.xml` inuti programtaggen.

```xml
<activity android:name="com.marketo.MarketoActivity"  android:configChanges="orientation|screenSize" >
    <intent-filter android:label="MarketoActivity" >
        <action  android:name="android.intent.action.VIEW"/>
        <category  android:name="android.intent.category.DEFAULT"/>
        <category  android:name="android.intent.category.BROWSABLE"/>
        <data android:host="add_test_device" android:scheme="mkto" />
    </intent-filter>
</activity>
```

## Stöd för Firebase Cloud Messaging

MME Software Development Kit (SDK) för Android har uppdaterats till ett modernare, stabilare och skalbart ramverk som innehåller mer flexibilitet och nya tekniska funktioner för din Android-apputvecklare.

Android apputvecklare kan nu använda Google direkt [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) (FCM) med denna SDK.

### Lägga till FCM i ditt program

1. Integrera den senaste versionen av Marketo Android SDK i Android App.  Steg finns på [GitHub](https://github.com/Marketo/android-sdk).
1. Konfigurera Firebase-appen på Firebase Console.
   1. Skapa/lägg till ett projekt på [](https://accounts.google.com/ServiceLogin?passive=1209600&amp;osid=1&amp;continue=https://console.firebase.google.com/&amp;followup=https://console.firebase.google.com/)Firebase Console.
      1. I [Firebase-konsol](https://accounts.google.com/ServiceLogin?passive=1209600&amp;osid=1&amp;continue=https://console.firebase.google.com/&amp;followup=https://console.firebase.google.com/), markera **[!UICONTROL Add Project]**.
      1. Välj GCM-projekt i listan över befintliga Google Cloud-projekt och välj **[!UICONTROL Add Firebase]**.
      1. I Firebase-välkomstskärmen väljer du **[!UICONTROL Add Firebase to your Android App]**.
      1. Ange ditt paketnamn och SHA-1, och välj **[!UICONTROL Add App]**. En ny `google-services.json` filen för din Firebase-app hämtas.
      1. Välj **[!UICONTROL Continue]** och följ instruktionerna för hur du lägger till Google Services-pluginprogrammet i Android Studio.

   1. Navigera till **[!UICONTROL Project Settings]** in [!UICONTROL Project Overview]
      1. Klicka **[!UICONTROL General]** -fliken. Ladda ned `google-services.json` -fil.
      1. Klicka på **[!UICONTROL Cloud Messaging]** -fliken. Kopiera [!UICONTROL Server Key] &amp; [!UICONTROL Sender ID]. Ange dessa [!UICONTROL Server Key] &amp; [!UICONTROL Sender ID] till Marketo.
   1. Konfigurera FCM-ändringar i Android App
      1. Växla till projektvyn i Android Studio för att visa projektets rotkatalog
         1. Flytta den hämtade filen `google-services.json` till rotkatalogen för Android-programmodulen
         1. På projektnivå `build.gradle` lägga till följande:

            ```
            buildscript {
              dependencies {
                classpath 'com.google.gms:google-services:4.0.0'
              }
            }
            ```

         1. Lägg till följande i build.gradle på appnivå:

            ```
            dependencies {
              compile 'com.google.firebase:firebase-core:17.4.0'
            } 
            // Add to the bottom of the file 
            apply plugin: 'com.google.gms.google-services'
            ```

         1. Klicka slutligen på **[!UICONTROL Sync now]** i fältet som visas i ID:t
   1. Redigera appens manifest FCM SDK lägger automatiskt till alla nödvändiga behörigheter och den nödvändiga mottagarfunktionen. Se till att ta bort följande föråldrade (och potentiellt skadliga, eftersom de kan orsaka duplicering av meddelanden) element från appens manifest:

      ```xml
      <uses-permission android:name="android.permission.WAKE_LOCK" />
      <permission android:name="<your-package-name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
      <uses-permission android:name="<your-package-name>.permission.C2D_MESSAGE" />
      
      ...
      
      <receiver>
        android:name="com.google.android.gms.gcm.GcmReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
          <action android:name="com.google.android.c2dm.intent.RECEIVE" />
          <category android:name="<your-package-name> />
        </intent-filter> 
      </receiver>
      ```


### Vanliga frågor om FCM

Vanliga frågor och svar om stöd för Firebase Cloud Messaging.

**F: Var hittar jag instruktioner om att uppdatera till den senaste versionen av MME SDK?** Instruktioner finns på Marketo Developer Site [HÄR](installation.md).

**F: Måste jag publicera en uppdaterad version av mitt Android-program till mina befintliga användare om jag uppdaterar till den senaste versionen av SDK?** Nej.

**F: Hur påverkar det befintliga MME-kunder som har publicerat Android-appar som är integrerade med Marketo Android SDK?** De kan migrera en befintlig GCM-klientapp på Android till Firebase Cloud Messaging (FCM) enligt följande:

1. I [Firebase-konsol](https://accounts.google.com/ServiceLogin?passive=1209600&amp;osid=1&amp;continue=https://console.firebase.google.com/&amp;followup=https://console.firebase.google.com/), markera **[!UICONTROL Add Project]**.
1. Välj GCM-projekt i listan över befintliga Google Cloud-projekt och välj **[!UICONTROL Add Firebase]**.
1. I Firebase-välkomstskärmen väljer du **[!UICONTROL Add Firebase to your Android App]**.
1. Ange ditt paketnamn och SHA-1, och välj **[!UICONTROL Add App]**. En ny Google-services.json-fil för
1. Firebase-programmet har laddats ned.
1. Välj **[!UICONTROL Continue]** och följ instruktionerna för hur du lägger till Google Services-pluginprogrammet i Android Studio.

**F: Kan vi rikta in oss på leads som skapats med den gamla Marketo SDK som använde GCM-appen?** Ja. Alla leads som skapas med Marketo SDK kan användas för att skicka push-meddelanden.
