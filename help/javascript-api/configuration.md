---
title: Konfiguration
description: Använd Configuration JavaScript API för att ange konfigurationsvärden när du använder Munchkin.
feature: Munchkin Tracking Code, Javascript
exl-id: 4700ce7b-f624-4f27-871e-9a050f203973
source-git-commit: 1ad2d793832d882bb32ebf7ef1ecd4148a6ef8d5
workflow-type: tm+mt
source-wordcount: '557'
ht-degree: 0%

---

# Konfiguration

Munchkin kan acceptera olika konfigurationsinställningar för att anpassa beteendet. Konfigurationsinställningar är egenskaper för ett JavaScript-objekt som skickas som den andra parametern vid anrop av [Munchkin.init()](api-reference.md#munchkin_init)

```json
Munchkin.init("AAA-BBB-CCC", {
        "configName":"configValue",
        "configName2":"configValue2"
    }
);
```

Konfigurationsinställningsobjektet kan innehålla valfritt antal egenskaper från tabellen nedan.

## Egenskaper

| Namn | Datatyp | Beskrivning |
|---|---|---|
| altIds | Array | Accepterar en array med Munchkin ID-strängar. När det här alternativet är aktiverat dupliceras all webbaktivitet till målprenumerationerna, baserat på deras Munchkin-ID. |
| anonymiszeIP | Boolean | Analyserar IP-adressen som registrerats i Marketo för nya besökare. |
| apiOnly | Boolean | Om värdet är true anropas inte `Munchkin.Init()` av funktionen `visitsWebPage`. Detta är användbart för webbprogram med en sida som behöver fullständig kontroll över varje `visitsWebPage`-händelse. |
| asyncOnly | Boolean | Om värdet är true skickas XMLHttpRequest asynkront. Standardvärdet är false. |
| clickTime | Heltal | Anger hur lång tid det tar att blockera efter en klickning för att tillåta klickspårningsbegäran (i millisekunder). Om du minskar detta minskar noggrannheten i klickspårningen. Standardvärdet är 350 ms. |
| cookieAnon | Boolean | Om värdet är false förhindras att nya anonyma leads spåras och cookies skapas. Leads har cookies och spåras när ett Marketo-formulär har fyllts i eller genom att klicka dig igenom via ett Marketo-e-postmeddelande. Standardvärdet är true. |
| cookieLifeDays | Heltal | Anger förfallodatumet för nya Munchkin-spårningscookies till så här många dagar i framtiden. Standardvärdet är 730 dagar (2 år). |
| customName | Sträng | Eget sidnamn. Endast systemanvändning. |
| <a name="domainlevel"></a>domainLevel | Heltal | Anger antalet delar från sidans domän som ska användas när domänattributet för cookien anges. Anta till exempel att den aktuella siddomänen är &quot;www.example.com&quot;.domainLevel: 2 anger cookie-domänattributet till &quot;.example.com&quot;domainLevel: 3 anger cookie-domänattributet till &quot;.www.example.com&quot;Background:Munchkin hanterar automatiskt vissa domäner med två bokstäver på översta nivån. Detta är som standard två delar i vanliga fall där domänen på den översta nivån är tre bokstäver. Till exempel &quot;www.example.com&quot; används de två delarna längst till höger för att ställa in cookien &quot;.example.com&quot;. För landskoder med två bokstäver, till exempel &quot;.jp&quot;, &quot;.us&quot;, &quot;.cn&quot; och &quot;.uk&quot;, är koden som standard tre delar. Exempel: &quot;www.example.co.jp&quot; använder tre domändelar längst till höger, &quot;.example.co.jp&quot;. Om domänmönstret kräver ett annat beteende måste detta anges med parametern `domainLevel`. |
| domainSelectorV2 | Boolean | Om värdet är true används en förbättrad metod för att avgöra hur domänattributet cookie ska anges. |
| httpsOnly | Boolean | Standardvärdet är false. Om värdet är true anges cookie som säker inställning när den spårade sidan opereras via https. |
| useBeaconAPI | Boolean | Standardvärdet är false. Om värdet är true används [Beacon API](https://developer.mozilla.org/en-US/docs/Web/API/Beacon_API) för att skicka icke-blockerande begäranden i stället för [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest). Om webbläsaren inte stöder detta API återgår Munchkin till att använda XMLHttpRequest. |
| wsInfo | Sträng | Tar en sträng som mål för en arbetsyta. Du får detta arbetsyte-ID genom att välja Workspace på menyn Admin > Integration > Munchkin. Den här inställningen gäller endast när en anonym lead-post skapas. När Munchkin cookie-värdet har etablerats för den lead-posten kan parametern wsInfo inte användas för att ändra partitionen. Eftersom den här inställningen endast påverkar anonyma leads är den bara relevant för partitionsspecifika [anonyma besökare i webbrapporter](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/reporting/basic-reporting/report-activity/display-people-or-anonymous-visitors-in-web-reports). |

## Exempel

### Skicka aktivitet till flera prenumerationer

I det här exemplet skickas all webbaktivitet till instanser med Munchkin-ID:n&quot;AAA-BBB-CCC&quot; och&quot;XXX-YYY-ZZZ&quot;.

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      // Add configuration settings to the init method
      Munchkin.init('AAA-BBB-CCCC', { 'altIds': ['XXX-YYY-ZZZ'] });
    }
  }
  var s = document.createElement('script');
  s.type = 'text/javascript';
  s.async = true;
  s.src = '//munchkin.marketo.net/munchkin.js';
  s.onreadystatechange = function() {
    if (this.readyState == 'complete' || this.readyState == 'loaded') {
      initMunchkin();
    }
  };
  s.onload = initMunchkin;
  document.getElementsByTagName('head')[0].appendChild(s);
})();
</script>
```

### Ställ in spårning till asynkron

Det här exemplet tvingar alla XMLHttpRequest-begäranden att skickas asynkront från huvudtråden.

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      // Add configuration settings to the init method
      Munchkin.init('AAA-BBB-CCC', { 'asyncOnly': true });
    }
  }
  var s = document.createElement('script');
  s.type = 'text/javascript';
  s.async = true;
  s.src = '//munchkin.marketo.net/munchkin-beta.js';
  s.onreadystatechange = function() {
    if (this.readyState == 'complete' || this.readyState == 'loaded') {
      initMunchkin();
    }
  };
  s.onload = initMunchkin;
  document.getElementsByTagName('head')[0].appendChild(s);
})();
</script>
```
