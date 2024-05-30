---
title: "Svarsmappningar"
feature: Webhooks
description: "Svarsmappningar för Marketo"
source-git-commit: bcc0c0c8e8209cf9fb962a85c8e7da354d95a8fe
workflow-type: tm+mt
source-wordcount: '460'
ht-degree: 0%

---


# Svarsmappningar

Marketo kan översätta data som tagits emot av en Webkrok från två innehållstyper och returnera dessa värden till ett lead-fält: JSON och XML. Parametern Marketo Field kommer alltid att använda [SOAP API-namn](../rest-api/fields.md) av fältet. Varje webkrok kan ha ett obegränsat antal svarsmappningar som läggs till och redigeras genom att klicka på [!UICONTROL Edit] i rutan Svarsmappningar på din webkrok:

![Svarsmappning](assets/response-mapping.png)

Svarsmappningar skapas via en parsning av ett&quot;svarsattribut&quot;, sökvägen till önskad egenskap i XML- eller JSON-dokumentet och&quot;Marketo-fält&quot;, som anger det Lead-fält som har värdet skrivet till det från svarsattributet.

Tangenter för egenskaper måste bestå av alfanumeriska tecken, bindestreck (-), understreck(_), kolon (:) och blanksteg som ska användas via Marketo svarsmappningar.

## JSON-mappningar

JSON-egenskaper nås med punktnotation och arraynotation. Matrisnotation i Marketo accepterar inte strängar som indata och accepterar bara heltal. Om du vill hämta data från ett JSON-dokument måste svarstypen ställas in på JSON:

```json
{ "foo":"bar"}
```

Så här öppnar du `foo` -egenskapen i en svarsmappning använder du `name` för egenskapen eftersom den är på JSON-objektets första nivå, `foo`. Så här ser det ut i Marketo:

![Svarsmappning](assets/json-resp.png)

Här är ett mer komplicerat exempel med en array:

```json
{
    "profileId" : 1234,
    "firstName" : "Jane",
    "lastName" : "Doe",
    "orders" : [
        {
            "orderId" : 5678,
            "orderDate" : "2015-01-01",
            "orderProductId" : "4982"
        },
        {
            "orderId" : 5678,
            "orderDate" : "2014-05-07",
            "orderProductId" : "4982"
        }
    ]
}
```

Vi vill komma åt orderDate från det första elementet i orderarrayen. Använd följande för att få åtkomst till den här egenskapen: `orders[0].orderDate`

## XML-mappningar

Du kan komma åt värden från enskilda element i XML-dokument. Detta använder punktnotation som liknar JSON-mappningarna. Vi tittar på det här enkla exemplet:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<example>
    <foo>bar</foo>
</example>
```

Använd följande om du vill få åtkomst till egenskapen foo här: `example.foo`

Exempelelementet måste refereras innan åtkomst sker `foo`. Om du vill komma åt en egenskap måste du referera till alla element i hierarkin i mappningen. XML-dokument med arrayer är lite mer komplicerade. Använd följande exempel:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<elementList>
    <element>
        <foo>baz</foo>
    </element>
    <element>
        <foo>bar</foo>
    </element>
    <element>
        <foo>bar</foo>
    </element>
</elementList>
```

Dokumentet består av den överordnade arrayen `elementList`, med underordnade element, element som innehåller en egenskap: `foo`. I Marketo svarsmappningar kallas arrayen `elementList.element`, så att underordnade elementList nås via `elementList.element[i]`. För att hämta foovärdet från det första underordnade elementetList använder vi det här svarsattributet: `elementList.element[0].foo` Detta returnerar värdet &quot;baz&quot; till det angivna fältet. Om du försöker komma åt egenskaper i element som innehåller både unika och icke-unika elementnamn resulterar det i ett odefinierat beteende. Varje element måste vara en enda egenskap eller en array. Typerna kan inte blandas.

## Typer

När du mappar attribut till fält måste du se till att typen i webkrok-svaret är kompatibel med målfältet. Om värdet i svaret till exempel är en sträng och det markerade fältet är av typen heltal, skrivs inte värdet. Läs om [Fälttyper](../rest-api/field-types.md).
