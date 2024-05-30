---
title: Social
description: "Socialt"
feature: Social, Javascript
source-git-commit: d335bdd9f939c3e557a557b43fb3f33934e13fef
workflow-type: tm+mt
source-wordcount: '776'
ht-degree: 0%

---


# Social

[Marketo Social Marketing](https://business.adobe.com/products/marketo/social-marketing.html) gör att marknadsförare kan bädda in sociala widgetar på webbplatser och landningssidor. Sociala widgetar omfattar omröstningar, knappar för sociala medier, videor, utlottningar och kampanjer som hänvisningserbjudanden.

## Exempel på widget för inbäddad delning

```html
<!-- Marketo Widget Loader Script --> 

<script type="text/javascript" src="//b2c-mlm.marketo.com/jsloader/271d8232-1500-4305-b7ed-05d451b9ee0c/loader.php.js">
</script>

 <!-- The Location of the Social Widget --> 

<divclass='cf_widgetloader cf_w_245d8f3c0955454cbd26abc39d0d874c'="" options="{&quot;outerHeight&quot;:400, &quot;outerWidth&quot;:600}">
</divclass='cf_widgetloader'>
```

![social-share-widget](assets/social-share-widget.png)

Det finns två grundläggande metoder för anpassning av en social widget:

1. Använda produktens normala användargränssnitt och bifoga händelseavlyssnare för att få information om när vissa åtgärder har utförts i användargränssnittet för att utföra ytterligare affärslogik.
1. Ersätta produktens normala användargränssnitt med ett anpassat användargränssnitt och aktivera popup-fönstrets&quot;steg&quot; när så önskas.

## Bifoga händelser i det normala gränssnittet

Det finns två sätt att prenumerera på händelser i CF JavaScript-biblioteket, globalt eller för en enda widget. Händelser beskrivs nedan i tabellen.

### Global händelseprenumeration

```html
<script>
cf_scripts.afterload(function(){
    CF.events.listen("event_name_here",
        function(event, arg1){
            //Your code to do something on the event goes here.
            //It will be fired whenever ANY widget fires the event "event_name_here".
        }
    );
});
</script>
```

### Prenumeration per widget

```html
<script>
cf_scripts.afterload(function(){
    CF.widget.listen("widget_name_here", "event_name_here",
        function(event, arg1){
            //Your code to do something on the event goes here.
            //It will be fired whenever the widget named "widget_name_here" fires the event "event_name_here".
        }
    );
});
</script>
```

## Ett exempel

I det här exemplet visas ett tidigare dolt element med ID &quot;signedUp&quot; efter att en användare har slutfört en registrering av erbjudanden för en widget med namnet &quot;reference_SignUp&quot;.

```html
<div id='signedUp'style='display:none; color:green;'>This is a custom message to let you know that you signed up!</div>
<div class='cf_widgetLoader cf_w_referral_SignUp'></div>

<script>
    cf_scripts.afterload(function(){
        CF.widget.listen("referral_SignUp", "offer_enrolled", function(){
        cf_jq("#signedUp").show();
    });
});
</script>
```

## Grundläggande händelsetabell

| Händelsenamn | Beskrivning | Widgetar som använder den här händelsen | Argument som stöds (skickas till funktionen för händelseåteranrop) |
| --- | --- | --- | --- | 
| share_sent | Utlöses varje gång en delningsbegäran skickas till servern för bearbetning | Alla widgetar som har möjlighet att dela | 1.&quot;share_sent&quot; (String)<br>2. Skickade parametrar (objekt) |
| share_success | Utlöses när delningsbegäran har bearbetats. | Alla widgetar som kan dela. | 1.&quot;share_success&quot; (String)<br>2. Dela svarsobjekt, som innehåller skickat meddelande och förkortad URL (objekt) |
| voice_success | Utlöses när en användare har röstat i en omröstning. | Omröstning, VS, Röstwidgetar | 1. &quot;voice_success&quot; (String)<br>2. Objekt som valts ut, inklusive titel, beskrivning, enhets-ID (objekt) |
| offer_enrollated | Utlöses när en användare har registrerat sig för ett erbjudande | Alla widgetar för erbjudanden | 1.&quot;offer_enrolled&quot; (String)<br>2. Ändrade användaregenskaper (Object),<br>3. Ändrade användarattribut (Object) |
| profile_saved | Utlöses när en användare har uppdaterat sin profil från profilhämtning | Alla widgetar som inte erbjuds och som har profilregistrering aktiverad | 1.&quot;profile_saved&quot; (String)<br>2. Ändrade användaregenskaper (Object)<br>3. Ändrade användarattribut (Object) |
| video_loaded | Utlöses när en inbäddad video är helt inläst och initierad. | VideoShare-widget | 1. &quot;video_loaded&quot; (String) 2. &quot;.cf_videoshare_wrap&quot; Element som innehåller videon (jQuery-objekt) |

## Ersätta användargränssnittet med ett anpassat användargränssnitt

Om du vill ersätta användargränssnittet med ett anpassat användargränssnitt måste du först inaktivera det normala användargränssnittet. Detta görs genom att du anger alternativet _popupUIOnly_ till _true_. När det här alternativet har angetts återges inte standardgränssnittet vid sidinläsning, utan widgeten hämtar data och väntar på att du ska starta en av popup-stegen genom att anropa _CF.widget.activate_ och ge alternativ för vad det ska göra.

Här är ett exempel på hur du skapar en anpassad knapp som startar anmälningsflödet för ett hänvisningserbjudandewidget med namnet _reference_SignUp_.

```html
<button id="myNewSignUpButton">My newSign Up button</button>

<!-- Turn off the defaultreferral offer UI by setting popupUIOnly to true-->
<div class="cf_widgetLoader cf_w_referral_SignUp" options="{popupUIOnly:true}"></div>

<script>
cf_scripts.afterload(function($, CF){
    // After the cf script library has loaded, find the button with
    // id="myNewSignUpButton", and attach a click listener to it.
    $("#myNewSignUpButton").click(function(){
        // When it is clicked, activate the popup widget flow for the referral,
        // asking it to point to the clicked button.
        CF.widget.activate("referral_SignUp", {pointTo:$(this)});
    });
});
</script>
```

Eftersom det är vanligt att lägga till klickningshanterare finns det en genvägsmetod för att lägga till dem. Följande är funktionellt ekvivalent med föregående exempel.

```html
<button id="myNewSignUpButton">My newSign Up button</button>
<div class="cf_widgetLoader cf_w_referral_SignUp" options="{popupUIOnly:true}"></div>

<script>
cf_scripts.afterload(function($, CF){
    // Use the addClickActivate convenience method, which will
    // automatically make the popup point at the clicked item with id myNewSignUpButton.
    CF.widget.addClickActivate("#myNewSignUpButton", "referral_SignUp", {});
});
</script>
```

## Hämtar gränssnittsdata för widget som ska placeras i ditt ersättningsgränssnitt

Om du behöver data om widgeten för att rita ditt ersättningsgränssnitt kan du hämta data från den speciella händelsen _ui_data_. Du kan lyssna efter den här händelsen med det normala `CF.widget.listen` -funktionen, men om du gör det kan det orsaka ett potentiellt konkurrenstillstånd där händelseavlyssnaren läggs till efter att widgeten redan har utlöst händelsen_ui_data_, så att du aldrig får några data. För att undvika detta använder du `CF.widget.uiData_ method instead, which will give you the most recent available _ui_data_, and listen for all future updates as well. The _ui_data` händelsen utlöses när en åtgärd har vidtagits som skulle ha gjort att widgetens standardgränssnitt ritades om, även om du har inaktiverat det gränssnittet med `popupUIOnly` alternativ.

Ett exempel som använder `uiData` funktion för att visa hur många poster en användare har för en utlottning med widgetnamn _sweeps_Sweepstakes_.

```html
<span>You have <span id="entryCount">?</span> entries.</span>

<div class="cf_widgetLoader cf_w_sweeps_Sweepstakes"></div>

<button id='myNewSweepsButton'>New Sweeps Up Button!</button>

<script>
cf_scripts.afterload(function($, CF){
    CF.widget.uiData("sweeps_Sweepstakes", function(uiData){
        if(uiData.user && uiData.userStatus && uiData.userEntries){
            $("entryCount").html(""+ uiData.userEntries);
        }
        else{
            $("entryCount").html("0");
        }
    });
});
</script>
```

## Referenserbjudande för registrering av gränssnittsdatareferens

| Typ | Beskrivning |
|---------------|----------------------------------------------------|
| datum | Datumvärde för formuläret &quot;yyyy-MM-dd&quot; |
| tal | Ett heltal eller flyttal |
| RTF | En HTML-sträng |
| score | Ett 32-bitars heltal med tecken |
| sfdc-kampanj | Används i Salesforce-kampanjhanteringsintegrering |
| text | En textsträng |

## Referenserbjudande för TrackProgress-användargränssnittets datareferens

| Typ | Beskrivning |
|---------------|----------------------------------------------------|
| datum | Datumvärde för formuläret &quot;yyyy-MM-dd&quot; |
| tal | Ett heltal eller flyttal |
| RTF | En HTML-sträng |
| score | Ett 32-bitars heltal med tecken |
| sfdc-kampanj | Används i Salesforce-kampanjhanteringsintegrering |
| text | En textsträng |

## Lotteriet UI Data Reference (för Lotteriet Social Campaign, inte Lotteriet LM Sweepstakes)

| Typ | Beskrivning |
|---------------|----------------------------------------------------|
| datum | Datumvärde för formuläret &quot;yyyy-MM-dd&quot; |
| tal | Ett heltal eller flyttal |
| RTF | En HTML-sträng |
| score | Ett 32-bitars heltal med tecken |
| sfdc-kampanj | Används i Salesforce-kampanjhanteringsintegrering |
| text | En textsträng |

## Datareferens för social inloggning (widgeten Formulärfyllning)

| Typ | Beskrivning |
|---------------|----------------------------------------------------|
| datum | Datumvärde för formuläret &quot;yyyy-MM-dd&quot; |
| tal | Ett heltal eller flyttal |
| RTF | En HTML-sträng |
| score | Ett 32-bitars heltal med tecken |
| sfdc-kampanj | Används i Salesforce-kampanjhanteringsintegrering |
| text | En textsträng |

```javascript
{
"alt_id": "http://www.facebook.com/profile.php?id=1526228678",
"provider_name": "facebook",
"default_photo_url": "https://graph.facebook.com/1526228678/picture?type=large",
"email": "ian.b.taylor@gmail.com",
"verified_email": "ian.b.taylor@gmail.com",
"gender": "male",
"preferred_user_name": "IanTaylor",
"display_name": "Ian Taylor",
"birth_date": 343954800000,
"first_name": "Ian",
"last_name": "Taylor",
"city": null,
"state": null,
"region": null,
"postal_code": null,
"country": null,
"time_zone": null,
"connection_count": 0,
"credentials": {
"uid": "1526228678",
"scopes": "publish_actions",
"expires": "1371994082",
"accessToken": "BAAGFJ0KUFpcBABuNMptmYY...",
"type": "Facebook"
},
"about_me": null,
"cur_pos_title": "Senior Staff Engineer",
"phone_number": null,
"company": "Marketo",
"cur_pos_start_date": 1333231200000,
"cur_pos_summary": null
}
```
