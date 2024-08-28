---
title: Referens för Munchkin API
description: Använd Munchkins Javascript-API för att anpassa dina Munchkin-data.
feature: Javascript
source-git-commit: c6c0a492ede415471e10efb6213eb3f590e63ebe
workflow-type: tm+mt
source-wordcount: '414'
ht-degree: 2%

---


# Referens för Munchkin API

Munchkin har flera funktioner som kan anropas manuellt via Javascript. Dessa kan användas för anpassad spårning av webbläsarhändelser, t.ex. videouppspelning, eller klick på icke-länkar.

## Funktioner

Munchkins API består av följande funktioner: `init`, `createTrackingCookie`, `munchkinFunction`.

### Munchkin.init()

`Munchkin.init()` måste anropas före andra funktioner. Det konfigurerar Munchkin på den aktuella sidan för att skicka aktiviteter till en viss instans och genererar aktiviteten &quot;Besök webbsida&quot; för den aktuella sidan.

| Parameternamn | Valfritt/obligatoriskt | Typ | Beskrivning |
| --- | --- | --- | --- |
| Munchkin-ID | Obligatoriskt | Sträng | Munchkins konto-ID finns under Admin > Integration > Munchkin-menyn. Anger målinstansen som aktiviteter ska skickas till. |
| [Konfigurationsinställningar](configuration.md) | Valfritt | Objekt | Aktiverar alternativa beteendeinställningar för Munchkin. |

```javascript
Munchkin.init('299-BYM-827');
```

### Munchkin.createTrackingCookie()

När den anropas görs en kontroll för att se om det finns en `_mkto_trk`-cookie i webbläsaren, och om den inte gör det skapas en. Detta är användbart för att spåra användare under specifika åtgärder, som registrering eller hämtning av en resurs, om `cookieAnon` är inställt på false.

| Parameternamn | Valfritt/obligatoriskt | Typ | Beskrivning |
| --- | --- | --- | --- |
| forceCreate | Obligatoriskt | Boolean | Skapa cookie även om `cookieAnon` har värdet false. |


```javascript
Munchkin.createTrackingCookie(true);
```

### Munchkin.munchkinFunction()

Används för att generera anpassade spårningsbeteenden, t.ex. uppspelning och pausning av videospelare, eller sidbesök för navigering som inte är standard, t.ex. hash-koder.

| Parameternamn | Valfritt/obligatoriskt | Typ | Beskrivning |
| --- | --- | --- | --- |
| Funktionstyp | Obligatoriskt | Sträng | Bestämmer aktiviteten som ska spelas in. Tillåtna värden: `visitWebPage`, `clickLink`, `associateLead` |
| Data | Obligatoriskt | Objekt | Innehåller data för aktiviteten som ska registreras. |

#### visitWebPage

När `munchkinFunction()` anropas med `visitWebPage` skickas en besöksaktivitet för den aktuella användaren till Marketo. Du kan anpassa URL:en och `querystring` som skickas med dataobjektet i det andra argumentet.

| Egenskapsnamn för data | Valfritt/obligatoriskt | Typ | Beskrivning |
| --- | --- | --- | --- |
| url | Obligatoriskt | Sträng | Den URL-filsökväg som används för att registrera ett sidbesök.  Det här värdet läggs till det aktuella domännamnet för att skapa ett fullständigt sidnamn. Om URL:en till exempel är `/index.html` och domännamnet är `www.example.com` registreras den besökta sidan som `www.example.com/index.html`. |
| parametrar | Valfritt | Sträng | En frågesträng med de parametrar som ska registreras. |

Exempel: `foo=bar&biz=baz`.

```javascript
Munchkin.munchkinFunction('visitWebPage', {
        'url': '/Football/Team/Seahawks',
        'params': 'defense=legion_of_boom&qb=wilson'
    }
);
```

#### clickLink

När `munchkinFunction()` anropas med `clickLink` skickas en klickaktivitet för den aktuella användaren till Marketo. Du kan anpassa klicknings-URL:en med egenskapen `href` i dataobjektet.

| Egenskapsnamn för data | Valfritt/obligatoriskt | Typ | Beskrivning |
| --- | --- | --- | --- |
| href | Obligatoriskt | Sträng | URL-filsökvägen som används för att spela in ett länkklick. Det här värdet läggs till det aktuella domännamnet för att skapa en fullständig länk. |

Om href till exempel är `/index.html` och domännamnet är `www.example.com` spelas länkklickningen in som `www.example.com/index.html`.

```javascript
Munchkin.munchkinFunction('clickLink', {
        'href': '/Football/Team/Seahawks'
    }
);
```

#### associateLead

Den här metoden har tagits bort och är inte längre tillgänglig för användning.
