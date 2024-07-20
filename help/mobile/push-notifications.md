---
title: Push-meddelanden
feature: Mobile Marketing
description: Aktivera push-meddelanden för Marketo Mobile
exl-id: 41d657d8-9eea-4314-ab24-fd4cb2be7f61
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '1329'
ht-degree: 0%

---

# Push-meddelanden

Så här aktiverar du push-meddelanden.

## Konfigurera push-meddelande för iOS

Det finns tre steg för att aktivera push-meddelanden:

1. Konfigurera push-meddelanden på Apple Developer Account.
1. Aktivera push-meddelanden i xCode.
1. Aktivera push-meddelanden i appen med Marketo SDK.

### Konfigurera push-meddelanden på Apple Developer Account

1. Logga in på Apple Developer [Member Center](http://developer.apple.com/membercenter).
1. Klicka på Certifikat, Identifierare och profiler.
1. Klicka på mappen &quot;Certificates->All&quot; under &quot;iOS, tvOS, watchOS&quot;.
1. Markera plustecknet (+) på den övre vänstra skärmen bredvid certifikaten ![](assets/certificates-plus.png)
1. Aktivera kryssrutan&quot;Apple Push Notification service SSL (Sandbox &amp; Production)&quot; och klicka på&quot;Continue&quot;.
1. Välj det program-ID som du använder för att skapa appen.![](assets/push-appid.png)
1. Skapa och överför CSR för att generera push-certifikatet. ![](assets/push-ssl.png)
1. Hämta certifikatet till den lokala datorn och dubbelklicka för att installera. ![](assets/certificate-download.png)
1. Öppna Nyckelhanterare, högerklicka på certifikatet och exportera 2 objekt till filen `.p12`.![key_chain](assets/key-chain.png)
1. Ladda upp den här filen via Marketo Admin Console för att konfigurera meddelanden.
1. Uppdatera provisioneringsprofiler för appar.

### Aktivera push-meddelanden i xCode

Aktivera funktionen för push-meddelanden i xCode-projektet.![](assets/push-xcode.png)

### Aktivera push-meddelanden i appen med Marketo SDK

Lägg till följande kod i filen `AppDelegate.m` för att leverera push-meddelanden till dina kunders enheter.

**Obs!** - Använd `ALMarketo` som klassnamn om du använder tillägget [!DNL Adobe Launch]

Importera följande i `AppDelegate.h`.

>[!BEGINTABS]

>[!TAB Mål C]

```
#import <UserNotifications/UserNotifications.h>
```

>[!TAB Swift]

```
import UserNotifications
```

>[!ENDTABS]

Lägg till `UNUserNotificationCenterDelegate` i `AppDelegate` enligt nedan.

>[!BEGINTABS]

>[!TAB Mål C]

```
@interface AppDelegate : UIResponder <UIApplicationDelegate, UNUserNotificationCenterDelegate>
```

>[!TAB Swift]

```
class AppDelegate: UIResponder, UIApplicationDelegate , UNUserNotificationCenterDelegate
```

>[!ENDTABS]

Initiera push-meddelandetjänst. Om du vill aktivera push-meddelanden lägger du till koden nedan.

>[!BEGINTABS]

>[!TAB Mål C]

```objectivec
BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
        center.delegate = self;
        [center requestAuthorizationWithOptions:(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge) completionHandler:^(BOOL granted, NSError * _Nullable error){
            if(!error){
                dispatch_async(dispatch_get_main_queue(), ^{
                    [[UIApplication sharedApplication] registerForRemoteNotifications];
                });
            }
        }];

    return YES;
}
```

>[!TAB Swift]

```
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
            
    UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound,    .badge]) { granted, error in
            if let error = error {
                print("\(error.localizedDescription)")
            } else {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
        
        return true
}
```

>[!ENDTABS]

Anropa den här metoden för att initiera registreringsprocessen med Apple Push Service. Om registreringen lyckas anropar appen `application:didRegisterForRemoteNotificationsWithDeviceToken:`-metoden för ditt programdelegatobjekt och skickar den som en enhetstoken.

Om registreringen misslyckas anropar appen i stället appdelegatens `application:didFailToRegisterForRemoteNotificationsWithError:`-metod.

Registrera push-token hos Marketo. För att få push-meddelanden från Marketo måste du registrera enhetstoken hos Marketo.

>[!BEGINTABS]

>[!TAB Mål C]

```
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
    // Register the push token with Marketo
    [[Marketo sharedInstance] registerPushDeviceToken:deviceToken];
}
```

>[!TAB Swift]

```
func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
    // Register the push token with Marketo
    Marketo.sharedInstance().registerPushDeviceToken(deviceToken)
}
```

>[!ENDTABS]

Token kan också avregistreras när användaren loggar ut.

>[!BEGINTABS]

>[!TAB Mål C]

```
[[Marketo sharedInstance] unregisterPushDeviceToken];
```

>[!TAB Swift]

```
Marketo.sharedInstance().unregisterPushDeviceToken
```

>[!ENDTABS]

Om du vill registrera om push-token extraherar du koden från steg 3 till en AppDelegate-metod och anropar från inloggningsmetoden ViewController.

Hantera push-meddelanden. För att få push-meddelanden från Marketo måste du registrera enhetstoken hos Marketo.

>[!BEGINTABS]

>[!TAB Mål C]

```
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
{
    [[Marketo sharedInstance] handlePushNotification:userInfo];
}
```

>[!TAB Swift]

```
func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
    Marketo.sharedInstance().handlePushNotification(userInfo)
}
```

>[!ENDTABS]

Lägg till följande metod i AppDelegate

Genom att använda den här metoden kan du antingen visa varningsmeddelanden, ljud eller öka märke när programmet är i förgrunden. Du måste anropa completeHandler som du väljer i den här metoden.

>[!BEGINTABS]

>[!TAB Mål C]

```
-(void)userNotificationCenter:(UNUserNotificationCenter *)center
    willPresentNotification:(UNNotification *)notification
        withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler{

    completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
}
```

>[!TAB Swift]

```
func userNotificationCenter(_ center: UNUserNotificationCenter, 
            willPresent notification: UNNotification, withCompletionHandler completionHandler: @escaping (
    UNNotificationPresentationOptions) -> Void) {
    completionHandler([.alert, .sound,.badge])
}
```

>[!ENDTABS]

Hantera nyligen mottagna push-meddelanden i AppDelegate

Metoden anropas för delegaten när användaren svarar på meddelandet genom att öppna programmet, stänga meddelandet eller välja en UNNotificationAction. Delegaten måste anges innan programmet returnerar från applicationDoFinishLaunching:.

>[!BEGINTABS]

>[!TAB Mål C]

```
- (void)userNotificationCenter:(UNUserNotificationCenter *)center
didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)(void))completionHandler {
    [[Marketo sharedInstance] userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
}
```

>[!TAB Swift]

```
func userNotificationCenter(_ center: UNUserNotificationCenter,
                                didReceive response: UNNotificationResponse,
                                withCompletionHandler
                                completionHandler: @escaping () -> Void) {
        Marketo.sharedInstance().userNotificationCenter(center, didReceive: response, withCompletionHandler: completionHandler)
}
```

>[!ENDTABS]

Spåra push-meddelanden

Om ditt program körs i bakgrunden (eller inte är aktivt) får enheten ett push-meddelande enligt nedan. Marketo spårar när användaren trycker på meddelandet.

![mobile8](assets/mobile8.png)

Om enheten får ett push-meddelande skickas det till `application:didReceiveRemoteNotification:`-återanropet på din appdelegat.

Här följer en aktivitetslogg från Marketo från Marketo som visar programhändelser och push-meddelandehändelser.

![mobile9](assets/mobile9.png)

## Konfigurera push-meddelande för Android

1. Lägg till följande behörighet i programtaggen.

   Öppna `AndroidManifest.xml` och lägg till följande behörigheter. Ditt program måste begära behörigheterna INTERNET och ACCESS_NETWORK_STATE. Om appen redan begär dessa behörigheter hoppar du över det här steget.

   ```xml
   <uses‐permission android:name="android.permission.INTERNET"/>
   <uses‐permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
   
   <!‐‐Following permissions are required for push notification.‐‐>
   <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
   <!‐‐Keeps the processor from sleeping when a message is received.‐‐>
   <uses-permission android:name="android.permission.WAKE_LOCK"/>
   <permission android:name="<PACKAGE_NAME>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
   <uses-permission android:name="<PACKAGE_NAME>.permission.C2D_MESSAGE" />
   <!-- This app has permission to register and receive data message. -->
   <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
   ```

1. Konfigurera FCM med HTTPv1 (Google har [föråldrat XMPP-protokoll](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref) den 12 juni 2023 och kommer att tas bort i juni 2024) 

- Aktivera MME FCM HTTPv1 i Marketo-funktionshanteraren ![](assets/feature-manager.png)
   - Överför JSON-fil för tjänstkonto för appen i MLM.
   - Du kan hämta JSON-filen för tjänstkontot från Firebase Console.   ![](assets/fcm-console.png)
   - Vänta i en timme efter att Json-filen för tjänstkontot har överförts till Marketo innan du skickar push-meddelanden.  

## Android Test Devices

Lägg till Marketo Activity i manifestfilen inuti programtaggen.

```xml
<activity android:name="com.marketo.MarketoActivity"  android:configChanges="orientation|screenSize">
    <intent-filter android:label="MarketoActivity">
        <action  android:name="android.intent.action.VIEW"/>
        <category  android:name="android.intent.category.DEFAULT"/>
        <category  android:name="android.intent.category.BROWSABLE"/>
        <data android:host="add_test_device" android:scheme="mkto"/>
    </intent-filter/>
</activity/>
```

## Registrera Marketo Push Service

1. Om du vill få push-meddelanden från Marketo måste du lägga till Firebase-meddelandetjänsten i `AndroidManifest.xml`. Lägg till före den avslutande programtaggen.

   ```xml
   <meta-data
       android:name="com.google.android.gms.version"
       android:value="@integer/google_play_services_version" />
   <service android:name=".MyFirebaseMessagingService">
   <intent-filter>
   <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
   <action android:name="com.google.firebase.MESSAGING_EVENT"/>
   </intent-filter>
   </service>
   ```

1. Lägg till Marketo SDK-metoder i filen `MyFirebaseMessagingService` enligt följande

   ```java
   import com.marketo.Marketo;
   
   public class MyFirebaseMessagingService extends FirebaseMessagingService {
   
       @Override
       public void onNewToken(String s) {
           super.onNewToken(s);
           Marketo marketoSdk = Marketo.getInstance(this.getApplicationContext());
           marketoSdk.setPushNotificaitonToken(s);
           // Add your code here...
       }
   
       @Override
       public void onMessageReceived(RemoteMessage remoteMessage) {
           Marketo marketoSdk = Marketo.getInstance(this.getApplicationContext());
           marketoSdk.showPushNotificaiton(remoteMessage);
           // Add your code here...
       }
   
   }
   ```

   **Obs!** - Om du använder tillägget Adobe lägger du till enligt nedan

   ```java
   import com.marketo.Marketo;
   
   public class MyFirebaseMessagingService extends FirebaseMessagingService {
   
       @Override
       public void onNewToken(String token) {
           super.onNewToken(token);
           ALMarketo.setPushNotificationToken(token);
           // Add your code here...
       }
   
       @Override
       public void onMessageReceived(RemoteMessage remoteMessage) {
           ALMarketo.showPushNotification(remoteMessage);
           // Add your code here...
       }
   
   }
   ```

**OBS!** FCM SDK lägger automatiskt till alla nödvändiga behörigheter samt de nödvändiga mottagarfunktionerna. Se till att ta bort följande föråldrade (och potentiellt skadliga, eftersom de kan orsaka duplicering av meddelanden) element från appens manifest om du använde tidigare versioner av SDK

```xml
<receiver android:name="com.marketo.MarketoBroadcastReceiver" android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <!‐‐Receives the actual messages.‐‐>
        <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
        <!‐‐Register to enable push notification‐‐>
        <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
        <!‐‐‐Replace YOUR_PACKAGE_NAME with your own package name‐‐>
        <category android:name="YOUR_PACKAGE_NAME"/>
    </intent-filter>
</receiver>

<!‐‐Marketo service to handle push registration and notification‐‐>
<service android:name="com.marketo.MarketoIntentService"/>
```

1. Initiera Marketo Push När du har sparat konfigurationen ovan måste du initiera Marketo Push Notification. Skapa eller öppna din Application-klass och kopiera/klistra in koden nedan. Du kan hämta ditt avsändar-ID från Firebase-konsolen.


   ```java
   Marketo marketoSdk = Marketo.getInstance(getApplicationContext());
   
   // Enable push notification here. The push notification channel name can by any string
   marketoSdk.initializeMarketoPush(SENDER_ID,"ChannelName");
   ```

   Om du använder [!DNL Adobe Launch]-tillägget ska du följa dessa instruktioner

   ```java
   // Enable push notification here. The push notification channel name can by any string
   ALMarketo.initializeMarketoPush(SENDER_ID,"ChannelName");
   ```

   Om du inte har något SENDER_ID aktiverar du Google Cloud Messaging Service genom att slutföra stegen som beskrivs i [den här självstudiekursen](https://developers.google.com/cloud-messaging/).

   Token kan också avregistreras när användaren loggar ut.

   ```java
   marketoSdk.uninitializeMarketoPush();
   ```

   Använd instruktionerna nedan om du använder tillägget [!DNL Adobe Launch]

   ```java
   ALMarketo.uninitializeMarketoPush();
   ```

   Obs! Om du vill registrera om push-token extraherar du koden från steg 3 till en AppDelegate-metod och anropar från inloggningsmetoden ViewController.

1. Ange meddelandeikon (valfritt) Om du vill konfigurera en anpassad meddelandeikon ska följande metod anropas.

   ```java
   MarketoConfig.Notification config = new MarketoConfig.Notification();
   // Optional bitmap for honeycomb and above
   config.setNotificationLargeIcon(bitmap);
   
   // Required icon Resource ID
   config.setNotificationSmallIcon(R.drawable.notification_small_icon); 
   
   // Set the configuration 
   //Use the static methods on ALMarketo class when using Adobe Extension
   Marketo.getInstance(context).setNotificationConfig(config); 
   
   // Get the configuration set 
   Marketo.getInstance(context).getNotificationConfig();
   ```

## Felsökning

Att konfigurera push-meddelanden för mobiler innebär många steg och samordning av utvecklare och marknadsförare. Om du har problem kan du kontrollera några enkla saker.

När du har sett till att de enkla sakerna är korrekta kan du fördjupa dig i programmeringsdetaljerna.

### Push-meddelandet visas inte

Kontrollera först om push-meddelanden är inaktiverade på telefonen. Mobilanvändare kan styra om de tar emot meddelanden för en viss app eller inte. Utvecklare (och marknadsförare) inaktiverar ofta dessa meddelanden vid något tillfälle under utvecklingen. Det första som ska kontrolleras är om mottagaren har inaktiverat push-meddelanden för din app.

För det andra är appen redan öppen och aktiv på enheten? När din app är den aktiva appen på enheten visas inga push-meddelanden på skärmen. De visas i stället i området&quot;lokala meddelanden&quot; i din app.

### Visa aktivitetsloggar i Marketo

Det första du bör titta på när du spårar fel är i Marketo aktivitetsloggar. Du kan använda aktivitetsloggar för att verifiera att ett meddelande har skickats.

I aktivitetsloggen tittar du på aktivitetsposterna för en person som skulle ta emot ett meddelande. Om meddelandet skickades finns det en post i aktivitetsloggen. Annars beror problemet troligen på konfigurationen av iOS-certifikatet eller Android API-nyckeln i Marketo.

### Certifikatet eller nyckeln är ogiltig

Kontrollera konfigurationen för att kontrollera att du har rätt certifikat inläst för sandbox eller Production. Ibland är det bäst att utvecklaren återexporterar certifikaten (iOS) eller nycklarna (Android) och sedan läser in dem igen till Marketo för att säkerställa att de är korrekta.

### .p12-filen saknar certifikat eller nyckel (iOS)

När du exporterar certifikatet kontrollerar du att du har exporterat nyckeln _och_ till certifikatet.

### Etablerar inaktuella profiler (iOS)

När du lägger till en ny enhet måste du uppdatera provisioneringsprofilerna och generera nya certifikat. Se till att Xcode-projektet pekar på rätt profiler och certifikat och importera dessa certifikat till Marketo.

### Det går inte att överföra iOS-certifikat (IOS)

Kontrollera att lösenordet som användes när certifikatet exporterades inte innehåller blanksteg.  I stället för detta:

`Hello World 123`

använd detta:

`HelloWorld123`

### Felsöka iOS-certifikat

För sandlådeprogram kan du använda antingen ett utvecklarcertifikat eller ett universellt certifikat. Men för produktionsprogram måste du ladda upp ett giltigt distributionscertifikat eller universellt certifikat.

### Push-studs/ogiltig token

En befintlig registreringstoken kan upphöra att gälla i ett antal scenarier, bland annat:

- Om klientappen avregistrerar sig hos GCM.
- Om klientprogrammet avregistreras automatiskt, vilket kan hända om användaren avinstallerar programmet. Om APNS Feedback Service till exempel rapporterade APNS-token som ogiltig i iOS.
- Om registreringstoken förfaller. Google kan till exempel bestämma sig för att uppdatera registreringstoken eller APNS-token har upphört att gälla för iOS-enheter.
- Om klientprogrammet uppdateras men den nya versionen inte har konfigurerats för att ta emot meddelanden.
