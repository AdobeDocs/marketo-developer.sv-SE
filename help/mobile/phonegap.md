---
title: PhoneGap
feature: Mobile Marketing
description: Använda PhoneGap med Marketo på mobila enheter
exl-id: 99f14c76-9438-4942-9309-643bca434d07
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '797'
ht-degree: 0%

---

# PhoneGap

Integrering av Marketo PhoneGap Plugin

## Förutsättningar

1. [Lägg till ett program i Marketo Admin](https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/mobile-marketing/admin/add-a-mobile-app) (hämta programhemlig nyckel och Munchkin-ID).
1. Konfigurera push-meddelanden ([iOS](push-notifications.md)) | [Android](push-notifications.md)).
1. [Installera PhoneGap/Cordova CLI](https://cordova.apache.org/docs/en/latest/guide/cli/).

## Installationsanvisningar

1. Konfigurera Marketo PhoneGap-plugin

   Förutsatt att Cordova CLI är installerat går du till din PhoneGap-programkatalog och kör följande kommando för att lägga till Marketo Plugin i programmet:

   `$ cordova plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

1. Installera plugin-programmet FCM

   `$ cordova plugin add cordova-plugin-fcm`

   Bekräfta att plugin-programmet har lagts till i programmet genom att köra följande kommando och verifiera

   `$ cordova plugin ls com.marketo.plugin 0.X.0 "MarketoPlugin" cordova-plugin-fcm 2.1.2 "FCMPlugin"`

**Migrera till senare version (valfritt)**

Om du vill ta bort ett befintligt plugin-program kör du följande kommando:

`$ cordova plugin remove com.marketo.plugin`

Kör följande kommando om du vill lägga till plugin-programmet igen:

`$ cordova plugin add https://github.com/Marketo/PhoneGapPlugin.git --variable APPLICATION_SECRET_KEY="YOUR_APPLICATION_SECRET"`

**Cordova version 8.0.0 (Cordova@Android7.0.0) och senare**

När Cordova Android-plattformen har byggts öppnar du appen med Android Studio och uppdaterar värdet `dirs` för filen `Marketo.gradle` som finns i mappen `com.marketo.plugin`.

```
repositories{
  jcenter()
  flatDir{
      dirs '../app/src/main/aar'
   }
}
```

Lägg till de plattformar som ska användas för appen `$cordova platform add android` `$ cordova platform add ios`

Kontrollera listan över plattformar som lagts till `$cordova platform ls`

1. Stöd för Firebase Cloud Messaging

1. Konfigurera Firebase-appen på Firebase Console.
   1. Skapa/lägg till ett projekt på [&#128279;](https://console.firebase.google.com/)Firebase-konsolen.
      1. Välj [ i ](https://console.firebase.google.com/)Firebase-konsolen **[!UICONTROL Add Project]**.
      1. Välj ditt GCM-projekt i listan över befintliga Google Cloud-projekt och välj **[!UICONTROL Add Firebase]**.
      1. I välkomstskärmen i Firebase väljer du Lägg till Firebase i din Android-app.
      1. Ange ditt paketnamn och SHA-1 och välj **[!UICONTROL Add App]**. En ny `google-services.json`-fil för din Firebase-app hämtas.
   1. Navigera till **[!UICONTROL Project Settings]** i [!UICONTROL Project Overview]
      1. Klicka på fliken **[!UICONTROL General]**. Hämta filen google-services.json.
      1. Klicka på fliken **[!UICONTROL Cloud Messaging]**. Kopiera [!UICONTROL Server Key] &amp; [!UICONTROL Sender ID]. Tillhandahåll dessa [!UICONTROL Server Key] &amp; [!UICONTROL Sender ID] till Marketo.
   1. Konfigurera FCM-ändringar i PhoneGap-appen
      1. Flytta den hämtade Google-services.json-filen till rotkatalogen i appmodulen Phonegap
      1. Ta bort filen MyFirebaseInstanceIDService från platsen `platforms/android/app/src/main/java/com/gae/scaffolder/plugin` (inaktuell)
      1. Ändra filen MyFirebaseMessagingService på platsen `platforms/android/app/src/main/java/com/gae/scaffolder/plugin` enligt följande:

         ```
         import com.marketo.Marketo;
         
         public class MyFirebaseMessagingService extends FirebaseMessagingService{
         
         @Override
         public void onNewToken(String s){
           super.onNewToken(s);
           MarketoExtension.setPushNotificaitonTokens(s);
           //Add your code here
         }
         
         @Override
         public void onMessageReceived(RemoteMessage remoteMessage) {
           MarketoExtension.showPushNotificaiton(remoteMessage);
           //Add your code here
         }
         }
         ```

         1. Ändra filen fcm_config_files_process.js på platsen plugins/cordova-plugin-fcm/scripts enligt följande

            ```
            //change
            var strings = fs.readFileSync("platforms/android/res/values/strings.xml").toString();
            //to
            var strings = fs.readFileSync("platforms/android/app/src/main/res/values/strings.xml").toString();
            
            //AND change
            fs.writeFileSync("platforms/android/res/values/strings.xml", strings);
            //to
            fs.writeFileSync("platforms/android/app/src/main/res/values/strings.xml", strings);
            ```

### &#x200B;3. Aktivera push-meddelanden i xCode

Aktivera funktionen för push-meddelanden i xCode-projekt.

### &#x200B;4. Spåra push-meddelanden

Klistra in följande kod i funktionen `application:didFinishLaunchingWithOptions:`.

>[!BEGINTABS]

>[!TAB Mål C]

Uppdatera metoden `applicationDidBecomeActive` enligt nedan

```
Marketo *sharedInstance = [Marketo sharedInstance];

[sharedInstance trackPushNotification:launchOptions];
```

>[!TAB Swift]

Uppdatera metoden `applicationDidBecomeActive` enligt nedan

```
let sharedInstance: Marketo = Marketo.sharedInstance()

sharedInstance.trackPushNotification(launchOptions)
```

>[!ENDTABS]

### &#x200B;5. Initiera Marketo Framework

Om du vill vara säker på att Marketo-ramverket initieras när appen startas lägger du till följande kod under funktionen `onDeviceReady` i JavaScript huvudfil.

Observera att vi måste skicka `phonegap` som ramverkstyp för PhoneGap-appar.

### Syntax

```
// This method will Initialize the Marketo Framework using Your MunchkinId and Secret Key
marketo.initialize(
  function() { console.log("MarketoSDK Init done."); },
  function(error) { console.log("an error occurred:" + error); },
  'YOUR_MUNCHKIN_ID',
  'YOUR_SECRET_KEY',
  'FRAMEWORK_TYPE'
);

// For session tracking, add following.
marketo.onStart(
  function(){ console.log("onStart."); },
  function(error){ console.log("Failed to report onStart." + error); }
);
```

### Parametrar

- Callback: funktionen som ska köras om Marketo Framework initieras utan fel.
- Fel vid återanrop: funktionen körs om Marketo Framework inte kan initieras.
- MUNCHKIN-id: Munchkin-id som togs emot från Marketo vid registreringen.
- SECRET KEY : Hemlig nyckel togs emot från Marketo vid registreringen.

### &#x200B;6. Initiera push-meddelanden för Marketo

För att vara säker på att Marketo push-meddelanden initieras lägger du till följande kod efter initieringsfunktionen i JavaScript huvudfil.

### Syntax

```
// This function will Enable user notifications (prompts the user to accept push notifications in iOS)
marketo.initializeMarketoPush(
    function() { console.log("Marketo push successfully initialized."); },
    function(error) { console.log("an error occurred:" + error); },
    'YOUR_GCM_PROJECT_ID' // This is required for Android and will be ignored in iOS
);
```

### Parametrar

- Callback: funktionen körs om Marketo push-meddelande initieras utan fel.
- Fel vid återanrop: funktionen körs om Marketo push-meddelande inte kan initieras.
- GCM_PROJECT_ID: GCM-projekt-ID hittades i [Google Developers Console](https://console.developers.google.com/) efter att appen har skapats.

Token kan också avregistreras vid utloggning.

```
marketo. uninitializeMarketoPush(
  function() { console.log("Marketo push successfully uninitialized."); } ,
  function(error) { console.log("an error occurred:" + error); }
);
```

## Associera lead

Du kan skapa en Marketo Lead genom att anropa funktionen associateLead.

### Syntax

```
marketo.associateLead(
  function(){ console.log("MarketoSDK : Lead Added"); },
  function(error){ console.log("an error occurred:" + error); },
  'Lead_Data_JSON_String'
);
```

### Parametrar

- Callback: funktionen som ska köras om Marketo Framework kopplar leadet.
- Fel vid återanrop: funktionen körs om Marketo Framework inte kan koppla leadet.
- Leaddata: lead-data i JSON-strängformat.

### Exempel

```
// First create a lead as shown below
var lead = {};
lead[marketo.KEY_FIRST_NAME] = "Phone";
lead[marketo.KEY_LAST_NAME] = "Gap";
lead[marketo.KEY_EMAIL] = email;
lead[marketo.KEY_ADDRESS] = "demo address";
lead[marketo.KEY_CITY] = "city";
lead[marketo.KEY_STATE] = "state";
lead[marketo.KEY_COUNTRY] = "country";
lead[marketo.KEY_POSTAL_CODE] = "postalCode";
lead[marketo.KEY_GENDER] = "gender";
// To use lead custom field, use the REST API NAME as key
lead["REST API NAME"] = "value";

// Use associateLead function to associate it.
marketo.associateLead(
  function() { console.log("MarketoSDK : Lead Associated"); },
  function(error) { console.log("an error occurred:" + error); },
  JSON.stringify(lead)
);
```

## Rapportåtgärd

Du kan rapportera alla användaråtgärder som har utförts genom att anropa funktionen `reportaction`.

### Syntax

```
marketo.reportaction(
  function(){ console.log("MarketoSDK : New event sent "); },
  function(error){ console.log("an error occurred:" + error); },
  'Action_Name',
  'Action_Data_JSON_String'
);
```

### Parametrar

- Callback: funktionen som ska köras om Marketo Framework rapporterar att åtgärden lyckades.
- Fel vid återanrop: funktionen körs om Marketo Framework inte kan rapportera åtgärd.
- Åtgärdsnamn : åtgärdsnamn.
- Åtgärdsdata: åtgärdsdata i JSON-strängformat.

### Exempel

```
// First create an event as below
var event = {
    "Action Type":"Add To Cart",
    "Action Details":"Adding Product in cart",
    "Action Metric":"10",
    "Action Length":"1"
}

marketo.reportaction(
    function(){ console.log("Reported action successfully."); },
    function(error){ console.log("Failed to report action." + error); },
    "Add To Cart",
    JSON.stringify(event)
);
```

## Sessionsrapportering

Bind händelsetyperna &quot;pause&quot; och &quot;resume&quot; enligt nedan för att rapportera Start- och Stop-händelser.  Det här används för att spåra hur mycket tid du tillbringar i ditt mobilprogram. Obs! Detta krävs i Android.

```
//Add the following code in your www/js/index.js

bindEvents: function() {
   document.addEventListener('pause', this.onStop, false);
   document.addEventListener('resume', this.onStart, false);
},
onStop: function() {
   marketo.onStop(
       function(){ console.log("onStop"); },
       function(error){ console.log("Failed to report onStop." + error); }
   );
},
onStart: function() {
   marketo.onStart(
       function(){ console.log("onStart."); },
       function(error){console.log( "Failed to report onStart." + error); }
   );
},
```

## Skapa leads

Det finns tre sätt att skapa leads från en hybridapp:

1. Marketo MME SDK
1. MARKETO REST API
1. Skicka formulär

Beroende på vilken metod som används identifieras en ny lead av olika utlösare och filter. Leads som skapats med MME SDK eller REST API visas i utlösarna och filtren&quot;Lead Created&quot;. Leads som skapas av formulärinskickningar visas i utlösarna och filtren för Fyll i formulär.

Det bästa sättet är att vara konsekvent med den metod som används i webbprogrammet när leads skapas. Om du redan har en webbapp som använder formulärinlämning som mekanism för att skapa leads, ska du använda samma mekanism när du skapar leads i din hybridapp. Om du redan har en webbapp som använder vårt REST API som mekanism för att skapa leads använder du samma mekanism när du skapar leads i din hybridapp. Om du varken använder formulärinlämning eller REST API som en mekanism för att skapa leads i webbprogrammet kan du använda MME SDK för att skapa leads i Marketo.
