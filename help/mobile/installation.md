---
title: Installation
feature: Mobile Marketing
description: Installera SDK:er för Mobile Marketo
exl-id: e0b79d85-3509-46d2-a77d-cee211c5ec7f
source-git-commit: 981ed9b254f277d647a844803d05a1a2549cbaed
workflow-type: tm+mt
source-wordcount: '757'
ht-degree: 0%

---

# Installation

Installationsanvisningar för Marketo Mobile SDK. Stegen nedan krävs för att skicka push-meddelanden och/eller meddelanden i appen.

## Installera Marketo SDK på iOS

### Förutsättningar

1. [Lägg till ett program i Marketo Admin](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app) (hämta programhemlig nyckel och Munchkin-ID)
1. [Konfigurera push-meddelanden](push-notifications.md) (valfritt)

### Installera Framework via CocoaPods

1. Installera CocoaPods. `$ sudo gem install cocoapods`
1. Byt katalog till din projektkatalog och skapa en Podfile med smarta standardinställningar. `$ pod init`
1. Öppna din Podfile. `$ open -a Xcode Podfile`
1. Lägg till följande rad i din Podfile. `$ pod 'Marketo-iOS-SDK'`
1. Spara och stäng din Podfile.
1. Hämta och installera Marketo iOS SDK. `$ pod install`
1. Öppna arbetsytan i Xcode. `$ open App.xcworkspace`

### Installera ramverk med Swift Package Manager

1. Välj projektet i projektnavigeraren och klicka på + under Lägg till paketberoende enligt nedan:

   ![Lägg till beroende](assets/dependency-manager-add.png)

1. Lägg till Marketo-paket från denna repo. Lägg till den här URL:en för den här databasen: https://github.com/Marketo/ios-sdk.

   ![Repo URL](assets/dependency-manager-url.png)

1. Lägg nu till resurspaketet så som visas: Leta reda på `MarketoFramework.XCframework` i projektnavigeraren och öppna det i Finder. Dra och släpp `MKTResources.bundle` för att kopiera källresurser.

### Ställ in Swift Bridging Header

1. Gå till Arkiv > Nytt > Arkiv och välj &quot;Huvudfil&quot;.

   ![Välj Huvudfil](assets/choose-header-file.png)

1. Ge filen namnet &lt;_ProjectName_>-Bridging-Header.

1. Gå till Project > Target > Build Phases > Swift Compiler > Code Generation. Lägg till följande sökväg till Objective-Bridging Header:

   `$(PODS_ROOT)/<_ProjectName_>-Bridging-Header.h`

   ![Skapa faser](assets/build-phases.png)

## Initiera SDK

Innan du kan använda Marketo iOS SDK måste du initiera det med ditt Munchkin konto-ID och App Secret Key. Var och en av dessa finns under&quot;Mobilappar och enheter&quot; i Marketo Admin.

1. Öppna filen AppDelegate.m (Objective-C) eller filen Bridging (Swift) och importera Marketo.h-huvudfilen.

   ```
   #import <MarketoFramework/MarketoFramework.h>
   ```

1. Klistra in följande kod i funktionen `application:didFinishLaunchingWithOptions`:.

   Observera att vi måste skicka&quot;native&quot; som ramverkstyp för Native Apps.

>[!BEGINTABS]

>[!TAB Mål C]

```
Marketo *sharedInstance = [Marketo sharedInstance];

[sharedInstance initializeWithMunchkinID:@"munchkinAccountId" appSecret:@"secretKey" mobileFrameworkType:@"native" launchOptions:launchOptions];
```

>[!TAB Swift]

```
let sharedInstance: Marketo = Marketo.sharedInstance()

sharedInstance.initialize(withMunchkinID: "munchkinAccountId", appSecret: "secretKey", mobileFrameworkType: "native", launchOptions: launchOptions)
```

>[!ENDTABS]

1. Ersätt `munkinAccountId` och `secretKey` ovan med ditt Munchkin konto-ID och Hemlig nyckel som finns i avsnittet Marketo **[!UICONTROL Admin]** > **[!UICONTROL Mobile Apps and Devices]**.

## iOS Test Devices

1. Välj Projekt > Mål > Info > URL-typer.
1. Lägg till identifierare: ${PRODUCT_NAME}
1. Ange URL-scheman: `mkto-<Secret Key_>`
1. Inkludera programmet :openURL:sourceApplication:annotation: i filen AppDelegate.m (mål-C)

## Hantera anpassad URL-typ i AppDelegate

>[!BEGINTABS]

>[!TAB Mål C]

```
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options{

    return [[Marketo sharedInstance] application:app
                                         openURL:url
                                         options:options];
}
```

>[!TAB Swift]

```
private func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool
    {
        return Marketo.sharedInstance().application(app, open: url, options: options)
    }
```

>[!ENDTABS]

## Installera Marketo SDK på Android

### Förutsättningar

1. [Lägg till ett program i Marketo Admin](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app) (hämta programhemlig nyckel och Munchkin-ID)
1. [Konfigurera push-meddelanden](push-notifications.md#android_setup_push) (valfritt)
1. [Hämta Marketo SDK för Android](https://codeload.github.com/Marketo/android-sdk/zip/refs/heads/master)

### Android SDK Setup with Gradle

1. Lägg till under avsnittet Beroenden i filen build.gradle på programnivå

`implementation 'com.marketo:MarketoSDK:0.8.9'`

1. Rotfilen `build.gradle` bör ha

   ```
   buildscript {
       repositories {
           google()
           mavenCentral()
       }
   ```

1. Synkronisera projektet med grå filer

### Konfigurera behörigheter

Öppna `AndroidManifest.xml` och lägg till följande behörigheter. Ditt program måste begära behörigheterna INTERNET och ACCESS_NETWORK_STATE. Om appen redan begär dessa behörigheter hoppar du över det här steget.

```xml
<uses‐permission android:name="android.permission.INTERNET"></uses‐permission>
<uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses‐permission>
```

### Initiera SDK

1. Öppna klassen Application eller Activity i din app och importera Marketo SDK till din Activity före setContentView eller i Application Context.

   ```java
   // Initialize Marketo
   Marketo marketoSdk = Marketo.getInstance(getApplicationContext());
   marketoSdk.initializeSDK("native","munchkinAccountId","secretKey");
   ```

1. ProGuard-konfiguration (valfritt)

   Om du använder ProGuard för din app lägger du till följande rader i din `proguard.cfg`-fil. Filen finns i projektmappen. Om du lägger till den här koden utesluts Marketo SDK från den komplicerade processen.

   ```
   -dontwarn com.marketo.*
   -dontnote com.marketo.*
   -keep class com.marketo.`{ *; }
   ```

## Android Test Devices

Lägg till MarketoActivity i filen `AndroidManifest.xml` inuti programtaggen.

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

MME Software Development Kit (SDK) för Android har uppdaterats till ett modernare, stabilare och skalbart ramverk som innehåller större flexibilitet och nya tekniska funktioner för din Android-apputvecklare.

Android apputvecklare kan nu direkt använda Google [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) (FCM) med denna SDK.

### Lägga till FCM i ditt program

1. Integrera den senaste versionen av Marketo Android SDK i Android App.  Steg är tillgängliga på [GitHub](https://github.com/Marketo/android-sdk).
1. Konfigurera Firebase-appen på Firebase Console.
   1. Skapa/lägg till ett projekt på [&#128279;](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)Firebase-konsolen.
      1. Välj [ i ](https://accounts.google.com/ServiceLogin?passive=1209600&osid=1&continue=https://console.firebase.google.com/&followup=https://console.firebase.google.com/)Firebase-konsolen`Add Project`.
      1. Välj ditt GCM-projekt i listan över befintliga Google Cloud-projekt och välj `Add Firebase`.
      1. Välj `Add Firebase to your Android App` på välkomstskärmen i Firebase.
      1. Ange ditt paketnamn och SHA-1 och välj `Add App`. En ny `google-services.json`-fil för din Firebase-app hämtas.
      1. Välj `Continue` och följ instruktionerna för hur du lägger till Google Services-pluginprogrammet i Android Studio.

   1. Navigera till Projektinställningar i projektöversikten
      1. Klicka på fliken Allmänt. Hämta filen google-services.json.
      1. Klicka på fliken Cloud Messaging. Kopiera servernyckel och avsändar-ID. Ange dessa servernycklar och avsändar-ID till Marketo.
   1. Konfigurera FCM-ändringar i Android App
      1. Växla till projektvyn i Android Studio för att visa projektets rotkatalog
         1. Flytta den hämtade Google-services.json-filen till rotkatalogen i Android-appmodulen
         1. Lägg till följande i build.gradle på projektnivå:

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

         1. Klicka slutligen på Synkronisera nu i fältet som visas i ID:t
   1. Redigera appens manifest FCM SDK lägger automatiskt till alla nödvändiga behörigheter och den nödvändiga mottagarfunktionen. Se till att ta bort följande föråldrade (och potentiellt skadliga, eftersom de kan orsaka duplicering av meddelanden) element från appens manifest:

      ```xml
      <uses-permission android:name="android.permission.WAKE_LOCK" />
      <permission android:name="<your-package-name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
      <uses-permission android:name="<your-package-name>.permission.C2D_MESSAGE" />
      
      ...
      
      <receiver>
        android:name="com.google.android.gms.gcm.GcmReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND"
        <intent-filter>
          <action android:name="com.google.android.c2dm.intent.RECEIVE" />
          <category android:name="<your-package-name> />
        </intent-filter>
      </receiver>
      ```
