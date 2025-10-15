---
title: Bloggarkiv
description: Marketo Developer Blog archive 2014-2023 med historiska inlägg på Forms 2.0, Zapier, API-uppdateringar, SOAP-borttagning och migrering till REST.
exl-id: d7ae88dd-9938-4957-9798-db43090dab4e
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '61723'
ht-degree: 0%

---

# Bloggarkiv

>[!INFO]
>
>Detta är ett arkiv i Marketo-bloggen som sträcker sig från 2014 till 2023. Den anges här endast som en historisk referens.
>&#x200B;>Viss information kan vara inaktuell.  Kontrollera alltid den aktuella dokumentationen för att se om det finns de senaste funktionerna.
>

>[!IMPORTANT]
>SOAP-API:t har tagits bort och är inte längre tillgängligt efter den 31 januari 2026. All ny utveckling bör ske med Marketo REST API, och befintliga tjänster bör migreras före detta datum för att undvika avbrott i tjänsten. Om du har en tjänst som använder SOAP API kan du läsa [SOAP API-migreringshandbok](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/migration) för mer information om hur du migrerar.
>

>[!IMPORTANT]
>Stöd för autentisering med frågeparametern `access_token` tas bort den 31 januari 2026. Om ditt projekt använder en frågeparameter för att skicka åtkomsttoken bör den uppdateras så att [auktoriseringshuvudet](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/rest/authentication#using-an-access-token) används så snart som möjligt. Ny utveckling bör endast använda auktoriseringshuvudet.
>

## Välkommen till Marketo Developer Blog

Välkommen Marketo-utvecklare! Vi har varit mycket upptagna här på Marketo när vi har släppt nya funktioner som hjälper marknadsförare att bli mer framgångsrika än någonsin tidigare. Idag stöds marknadsförare av ett fenomenalt utvecklarforum. Den här bloggen riktar sig till webbutvecklare och programvaruutvecklare som stöder den moderna marknadsförarens snabbt föränderliga behov. I takt med att Marketo Platform utvecklas presenterar vi nya integreringsalternativ och API-versionsuppdateringar i takt med att de släpps. Vi kommer också att lansera en ny serie instruktionsartiklar där vi delar kodexempel och bästa praxis för integrering med Marketo Platform. Den första artikeln i den här serien visar hur du effektivt hämtar information om de personer (kunder/kontakter/leads) som lagras i Marketo med API:t. Prenumerera med hjälp av formuläret ovan för att hålla dig uppdaterad. Vi skickar ett e-postmeddelande med uppdateringar när nya artiklar publiceras.

Publicerad _2014-03-06_ av _David_

## Versionsuppdateringar från januari 2014

### Forms 2.0

Forms 2.0 ger marknadsförarna möjlighet att skapa snygga, stabila och flexibla webbformulär utan programmeringskunskaper. Förutom den förbättrade redigeraren, den villkorsstyrda logiken och den robusta designen är det nu enklare än någonsin att bädda in Marketo-formulär på vilken sida som helst på webbplatsen. Utvecklarna kan använda JavaScript för att utöka kärnfunktionaliteten. Exempel:

* Dölja ett formulär efter att det har skickats så att besökaren finns kvar på sidan när formuläret har fyllts i
* Visa ett anpassat felmeddelande vid sändning baserat på anpassad affärslogik
* Visa formuläret i en dialogruta för ljuslådeformat

Utvecklardokumentation finns [här](/help/javascript-api/forms-api-reference.md).

### SOAP API version 2_3 är nu tillgänglig

* [getLeadChanges:](/help/soap-api/getleadchanges.md) Infört begärandefält `activityNameFilter`
* [ListOperation:](/help/soap-api/listoperation.md) Borttaget begärandefält `skipActivityLog`

**Obs!** API-revisioner för SOAP är bakåtkompatibla

Publicerad _2014-01-26_ av _Travis Kaufman_

## Zapier Part II: Marketo Integration Announcement

I ett tidigare inlägg diskuterade vi hur du skulle kunna använda Zapier för att integrera externa datakällor med Marketo. Inlägget presenterade ett praktiskt tillvägagångssätt för att skapa ett eget Zapier-arbetsflöde (eller&quot;Zap&quot;) från scratch för att integrera Marketo och andra appar. Så du gillar att använda Zapier med Marketo, men behöver hjälp med att komma igång? Goda nyheter! Zapier har just släppt ett antal exempel på Zaps för Marketo som gör att du snabbt kommer igång:

* Hämta nya leads
* Ge era kunder en bättre upplevelse
* Distribuera kontaktinformation
* Reagera på nya potentiella kunder

Förutom de här exemplen kan du bläddra på [Marketo-integreringssidan](https://zapier.com/apps/marketo/integrations) med hundratals andra appar i Zapier och skapa egna automatiserade arbetsflöden på några minuter. Ingen kodning krävs. Spara tid och låt automatiseringen göra det manuella arbetet. Bli kreativ. Himlen är gränsen!

Publicerad _2016-06-01_ av _David_

## Hämta information om kund och potentiell kund från Marketo med API:t

Du kan hämta information om kunder och potentiella kunder som lagras i Marketo med SOAP-API:t `getLead` och [`getMultipleLeads`](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads). Det är ofta önskvärt att extrahera denna information regelbundet för att hålla ett annat system uppdaterat i takt med att kundinformation uppdateras eller nya poster skapas i Marketo. Vi visar det kodexempel som skulle utföras regelbundet för att avfråga Marketo om det finns uppdateringar. Diagrammet nedan visar de API-anrop som görs för en angiven periodisk timer. Beroende på användningsfallet kan den periodiska timern ställas in så att koden nedan körs var 10:e minut.

Det första anropet till [`getMultipleLeads`](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads) anger tidsintervallet, batchSize och vilka fält som ska returneras. Alla leads inom Marketo som uppdaterades inom det angivna tidsintervallet returneras tillsammans med en streamPosition när fler poster är tillgängliga än den angivna batchSize.

**SOAP Request for first call to getMultipleLeads:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:paramsGetMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <leadSelector xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="ns2:LastUpdateAtSelector">
    <latestUpdatedAt>2014-02-13T11:51:08.710-08:00</latestUpdatedAt>
    <oldestUpdatedAt>2014-02-12T11:51:08.713-08:00</oldestUpdatedAt>
  </leadSelector>
  <batchSize>2</batchSize>
  <includeAttributes>
    <stringItem>FirstName</stringItem>
    <stringItem>LastName</stringItem>
    <stringItem>Email</stringItem>
  </includeAttributes>
</ns2:paramsGetMultipleLeads>
```

**SOAP-svar från det första anropet till getMultipleLeads:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:successGetMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <result>
 <returnCount>2</returnCount><remainingCount>24</remainingCount><newStreamPosition>id:1089965:to:1392234668:tl:1392321068:os:2:rc:24</newStreamPosition><leadRecordList>
      <leadRecord>
        <Id>84105</Id>
        <Email>eimang@marketo.com</Email>
        <ForeignSysPersonId xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
        <ForeignSysType xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
        <leadAttributeList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true">
          <attribute>
            <attrName>FirstName</attrName>
            <attrType>string</attrType>
            <attrValue>French</attrValue>
          </attribute>
          <attribute>
            <attrName>LastName</attrName>
            <attrType>string</attrType>
            <attrValue>Lead</attrValue>
          </attribute>
        </leadAttributeList>
      </leadRecord>
      <leadRecord>
        <Id>1089965</Id>
        <Email>t@t.com</Email>
        <ForeignSysPersonId xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
        <ForeignSysType xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
        <leadAttributeList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true">
          <attribute>
            <attrName>FirstName</attrName>
            <attrType>string</attrType>
            <attrValue>George</attrValue>
          </attribute>
          <attribute>
            <attrName>LastName</attrName>
            <attrType>string</attrType>
            <attrValue>of the Jungle</attrValue>
          </attribute>
        </leadAttributeList>
      </leadRecord>
    </leadRecordList>
  </result>
</ns2:successGetMultipleLeads>
```

Så länge värdet `<remainingCount/>` är större än 0 gör du efterföljande anrop till `getMultipleLeads` för att paginera genom resten genom att skicka värdet `<newStreamPosition/>` som returnerades i det föregående anropet till parametern `<streamPosition/>`. **SOAP-begäran för efterföljande anrop till getMultipleLeads:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:paramsGetMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <leadSelector xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="ns2:LastUpdateAtSelector">
    <latestUpdatedAt>2014-02-13T11:51:08.710-08:00</latestUpdatedAt>
    <oldestUpdatedAt>2014-02-12T11:51:08.713-08:00</oldestUpdatedAt>
  </leadSelector><streamPosition>id:1089965:to:1392234668:tl:1392321068:os:2:rc:24</streamPosition><batchSize>2</batchSize>
  <includeAttributes>
    <stringItem>FirstName</stringItem>
    <stringItem>LastName</stringItem>
    <stringItem>Email</stringItem>
  </includeAttributes>
</ns2:paramsGetMultipleLeads>
```

Den här logiken fortsätter så länge som `<remainingCount/>` är större än noll. **Se nedan ett exempel på ett Java-program som kör det scenario som beskrivs ovan.**

```java
import com.marketo.mktows.\*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.GregorianCalendar;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;
import javax.xml.datatype.DatatypeFactory;
import javax.xml.datatype.XMLGregorianCalendar;

public class GetMultipleLeads {
  public static void main(String[] args) {
    System.out.println("Executing GetMultipleLeads");
        try {
        URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
        String marketoUserId = "CHANGE ME";
        String marketoSecretKey = "CHANGE ME";
        QName serviceName = new QName("<http://www.marketo.com/mktows/>",
        "MktMktowsApiService");
        MktMktowsApiService service = new
        MktMktowsApiService(marketoSoapEndPoint, serviceName);
        MktowsPort port = service.getMktowsApiSoapPort();

        // Create Signature
        DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
        String text = df.format(new Date());
        String requestTimestamp = text.substring(0, 22) + ":" +         text.substring(22);
        String encryptString = requestTimestamp + marketoUserId ;

        SecretKeySpec secretKey = new         SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");

        Mac mac = Mac.getInstance("HmacSHA1");
        mac.init(secretKey);
        byte[] rawHmac = mac.doFinal(encryptString.getBytes());
        char[] hexChars = Hex.encodeHex(rawHmac);
        String signature = new String(hexChars);

        // Set Authentication Header
        AuthenticationHeader header = new AuthenticationHeader();
        header.setMktowsUserId(marketoUserId);
        header.setRequestTimestamp(requestTimestamp);
        header.setRequestSignature(signature);

        // Create Request
        ParamsGetMultipleLeads request = new ParamsGetMultipleLeads();

        // Build Request Using LastUpdateAtSelector
        LastUpdateAtSelector leadSelector = new LastUpdateAtSelector();
        GregorianCalendar gc = new GregorianCalendar();
        gc.setTimeInMillis(new Date().getTime());
        gc.add( GregorianCalendar.DAY_OF_YEAR, -20);
        DatatypeFactory factory = DatatypeFactory.newInstance();
        ObjectFactory objectFactory = new ObjectFactory();
        JAXBElement<XMLGregorianCalendar> until =         objectFactory.createLastUpdateAtSelectorLatestUpdatedAt(factory.newXMLGregorianCalendar(gc));

        GregorianCalendar since = new GregorianCalendar();
        since.setTimeInMillis(new Date().getTime());
        since.add( GregorianCalendar.DAY_OF_YEAR, -21);
        leadSelector.setOldestUpdatedAt(factory.newXMLGregorianCalendar(since));
        leadSelector.setLatestUpdatedAt(until);
        request.setLeadSelector(leadSelector);

        ArrayOfString attributes = new ArrayOfString();
        attributes.getStringItems().add("FirstName");
        attributes.getStringItems().add("LastName");
        attributes.getStringItems().add("Email");
        request.setIncludeAttributes(attributes);

        JAXBElement<Integer> batchSize = new
        ObjectFactory().createParamsGetMultipleLeadsBatchSize(2);
        request.setBatchSize(batchSize);

        SuccessGetMultipleLeads result = null;

        int count = 0;

        do {
        if (count > 0) {
        // Set the streamPosition on subsequent calls to paginate
        through large result sets
        String pos = result.getResult().getNewStreamPosition();
        JAXBElement<String> streamPos = new
        ObjectFactory().createParamsGetMultipleLeadsStreamPosition(pos);
        request.setStreamPosition(streamPos);
        }

        JAXBContext context =
        JAXBContext.newInstance(ParamsGetMultipleLeads.class);
        Marshaller m = context.createMarshaller();
        m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
        System.out.println("REQUEST:");
        m.marshal(request, System.out);
        result = port.getMultipleLeads(request, header);
        System.out.println("RESPONSE:");
        m.marshal(result, System.out);
        count = result.getResult().getReturnCount();
        } while (result.getResult().getRemainingCount() > 0);
        }

        catch(Exception e) {
        // Update to include appropriate retry/error handling
        e.printStackTrace();
        }

  }

}
```

### Tips och tricks

När du extraherar stora mängder kontakter från Marketo bör du justera API-begäran enligt följande parametrar:

* `<includeAttributes/>`: Vi rekommenderar att du bara begär de fält som du är intresserad av att synkronisera med datorn. Detta minskar nyttolasten för svaret och ökar frågeprestanda.
* `<batchSize/>`: API:t stöder upp till 1 000 poster som returneras i ett enda anrop. Om du ändrar värdet till 500 poster minskar även nyttolasten för svaret.
* `<LastUpdatedAtSelector/>`: Vi rekommenderar att du anger både parametern `<oldestUpdatedAt/>` och parametern `<latestUpdatedAt/>` för att begränsa datumintervallet. I stället för att göra en enda begäran för ett års data. Dela upp API-anropen för att begära mindre datumintervall.

Den här artikeln innehåller kod som används för att implementera anpassade integreringar. På grund av den anpassade karaktären kan Marketo tekniska support inte felsöka skräddarsytt arbete. Försök inte att implementera följande kodexempel utan lämplig teknisk erfarenhet, eller använd en erfaren utvecklare.

Publicerad _2014-03-05_ av _Travis Kaufman_

## Uppdateringar från februari 2014

### SOAP API Update

* [syncMObjects](/help/soap-api/syncmobjects.md): Nu kan du lägga till och uppdatera taggar och kanaler för befintliga program.

Uppdateringar ingår i [2_3 WSDL](http://app.marketo.com/soap/mktows/2_3?WSDL).

Publicerad _2014-02-26_ av _Travis Kaufman_

## Versionsuppdateringar mars 2014

### SOAP API Update

* Prestandaförbättringar för [syncLead](/help/soap-api/synclead.md) och [syncMultipleLeads](/help/soap-api/syncmultipleleads.md)

Uppdateringar ingår i [2_3 WSDL](http://app.marketo.com/soap/mktows/2_3?WSDL).

Publicerad _2014-03-20_ av _Travis Kaufman_

## Sammanfoga anonym besökaraktivitet när besökaren fyller i formuläret

I blogginlägget&quot;Capture Anonymous Visitor Activity Based on Business Logic&quot; diskuterade vi hur vi skulle skapa anonyma lead-poster i Marketo utifrån anpassade händelser. I det här blogginlägget kommer vi att bygga vidare på inlägget och koppla en anonym lead-post till en känd användare när du har tagit emot användarens kontaktinformation. Marketo [Munchkin spårningskod](/help/javascript-api/lead-tracking.md) hjälper dig att spåra besök på din webbplats. Första gången någon besöker en sida på din webbplats som har Munchkin spårningskod skapar Marketo ett anonymt lead och använder en webbläsarcookie för att spåra dem. När de identifierar sig själva blir de ett känt lead och historiken som är kopplad till deras webbläsarcookie sammanfogas i deras Marketo lead-post. **Anonyma leads skapas när någon:**

1. Besök Marketo startsida för första gången
1. Besöker en sida som har Munchkin spårningskod
1. Klicka på länken Visa som webbsida i ett e-postmeddelande från Marketo

**Ett anonymt lead sammanfogas till ett nytt eller befintligt känt lead när någon:**

1. Klicka på en länk till ett e-postmeddelande från Marketo
1. Fyller i ett Marketo-formulär
1. Använder en av teknikerna nedan för att koppla ett anonymt lead till en känd post.

Om du vill skicka persondata eller koppla en webbläsar-spårningskcookie till motsvarande personpost i Marketo använder du någon av följande tekniker: **Inlämning på webbläsarsidan** Om ditt användningsfall kräver att persondata skickas från webbläsaren bör du använda formuläröverföringen i bakgrunden. **Inlämning på serversidan** Om du inte behöver skicka data via webbläsaren erbjuder REST API många metoder för överföring av personuppgifter och en specialbyggd metod för att associera cookies med personposter.

Publicerad _2014-04-22_ av _Murta_

## Versionsuppdateringar från april 2014

### Marketo Forms - säkerhetsuppdatering

Vi införde en gräns för hur många formulär som får skickas in och hur ofta de får skickas från en enda IP-adress. Denna gräns gäller nu vid 30 inlägg per minut för att skydda våra kunder från skadlig användning av programmatiska blankettinlämningar. API:t [syncLead &#x200B;](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/leads/synclead) är det rekommenderade integrationsverktyget för programmatisk överföring av nya kontakter i Marketo.

Publicerad _2014-04-29_ av _Travis Kaufman_

## Ersätt din formulärpost-integrering med Marketo API

Det är vanligt att marknadsförare använder Marketo för att associera framgångar för en viss marknadsföringskampanj baserat på antalet kontakter/leads som har fyllt i ett visst webbformulär. Marknadsföraren skulle helt enkelt skapa ett nytt Marketo-formulär, placera det på en landningssida och sedan skapa en utlösarkampanj i Marketo för att associera alla kontakter som fyllt i det specifika formuläret som en framgång för det marknadsföringsprogrammet. (se skärmbild nedan). Det är en naturlig utökning att använda samma metod när programmet spelas in, även när kampanjframgången ligger utanför någon som fyller i ett formulär. När en marknadsförare till exempel genomför ett kampanjerbjudande och konverteringen är att ringa in och boka ett möte. Den presumtiva kunden fyller inte i något formulär under processen. I artikeln nedan visas hur du använder API:t Marketo syncLead för att skapa en ny kontakt om de är nya kontakter, och sedan requestCampaign API för att registrera ett visst marknadsföringsprogram.

Publicerad _1970-01-01_ av _Travis Kaufman_

## Ta bort en lead med Marketo API

Säg att du vill ta bort ett lead via Marketo API. Du kan uppnå detta genom att anropa API:t requestCampaign för en kampanj som har en fördefinierad flödesåtgärd för att ta bort ett lead. Vi visar först hur du skapar en smart kampanj, och sedan hur du ställer in en utlösare för att köra en kampanj via API:t, för det tredje hur du definierar att ett lead ska tas bort som en del av en flödesåtgärd och för det fjärde ett kodexempel som skulle användas för att köra den här kampanjen. **Så här skapar du en ny smart kampanj i Marketo** Smarta kampanjer i Marketo och utför alla dina marknadsföringsaktiviteter. I en Smart Campaign kan ni konfigurera en serie automatiska åtgärder som ska vidtas i en smart lista med kontakter. Om du tar bort ett lead ställer du in en utlösare i kampanjen enligt nedan. Först ställer vi in Smart Campaign.

1. Välj ett program i Marknadsföringsaktiviteter och klicka sedan på Ny lokal resurs 1 i listrutan Nytt. Klicka på Smart Campaign
1. Ange namnet på den smarta kampanjen och klicka på Create Add Triggers to a Smart Campaign*** Adding Triggers to a Smart Campaign så att du kan köra en smart kampanj på en person i taget baserat på en live-händelse, vilket i det här fallet är en begäran via API:t requestCampaign.
1. Sök efter utlösaren&quot;Campaign is Requested&quot; och dra och släpp den sedan på arbetsytan.
1. I utlösaren väljer du&quot;is&quot; och&quot;Web Service API&quot;.

**Så här skapar du en Ta bort ett lead-flöde i en kampanj**. Klicka på Flöde på den översta menyn. På menyn till höger söker du efter Ta bort lead och drar den till mitten för att lägga till den som en utlösare i kampanjen. Obs! Om du bara tar bort ett lead från Marketo och lämnar det i CRM återskapas leadet i Marketo när data uppdateras.  **Kodexempel som anropar API:t requestCampaign** När du har konfigurerat kampanjen och utlösarna i Marketo-gränssnittet visar vi hur du använder API:t för att köra den här kampanjen. Det första exemplet är en XML-begäran, det andra är ett XML-svar och det sista är ett Java-kodexempel som kan användas för att generera XML-begäran. Vi visar även hur du hittar det kampanj-ID som används för att anropa API:t requestCampaign. API-anropet kräver även att du känner till ID:t för Marketo-kampanjen i förväg. Du kan bestämma kampanj-ID med någon av följande metoder:

1. Använd API:t [getCampaignsForSource](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getCampaignsUsingGET)
1. Öppna Marketo-kampanjen i en webbläsare och se URL-adressfältet. Kampanj-ID (representerat som ett fyrsiffrigt heltal) finns omedelbart efter SC. Exempel: `https://app-stage.marketo.com/#SC**1025**A1`. Den fetstilta delen är kampanj-ID -&quot;1025&quot;. SOAP Request for requestCampaign

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809939944BFABAE58E5D27</mktowsUserId><requestSignature>48397ad47b71a1439f13a51eea3137df46441979</requestSignature><requestTimestamp>2013-08-01T12:31:14-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsRequestCampaign>
      <source>MKTOWS</source>
      <campaignId>4496</campaignId>
      <leadList>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>lead@company.com</keyValue>
        </leadKey>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>anotherlead@company.com</keyValue>
        </leadKey>
      </leadList>
    </ns1:paramsRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

SOAP-svar för requestCampaign

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successRequestCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

Nedan visas ett exempel på ett Java-program som kör det scenario som beskrivs ovan.

```java
import com.marketo.mktows.*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;


public class RequestCampaign {

    public static void main(String[] args) {
        System.out.println("Executing Request Campaign");
        try {
            URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
            String marketoUserId = "CHANGE ME";
            String marketoSecretKey = "CHANGE ME";

            QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
            MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
            MktowsPort port = service.getMktowsApiSoapPort();

            // Create Signature
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
            String text = df.format(new Date());
            String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
            String encryptString = requestTimestamp + marketoUserId ;

            SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
            Mac mac = Mac.getInstance("HmacSHA1");
            mac.init(secretKey);
            byte[] rawHmac = mac.doFinal(encryptString.getBytes());
            char[] hexChars = Hex.encodeHex(rawHmac);
            String signature = new String(hexChars);

            // Set Authentication Header
            AuthenticationHeader header = new AuthenticationHeader();
            header.setMktowsUserId(marketoUserId);
            header.setRequestTimestamp(requestTimestamp);
            header.setRequestSignature(signature);

            // Create Request
            ParamsRequestCampaign request = new ParamsRequestCampaign();

            request.setSource(ReqCampSourceType.MKTOWS);

            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Integer> campaignId = objectFactory.createParamsRequestCampaignCampaignId(4496);
            request.setCampaignId(campaignId);

            ArrayOfLeadKey leadKeyList = new ArrayOfLeadKey();
            LeadKey key = new LeadKey();
            key.setKeyType(LeadKeyRef.EMAIL);
            key.setKeyValue("lead@company.com");

            LeadKey key2 = new LeadKey();
            key2.setKeyType(LeadKeyRef.EMAIL);
            key2.setKeyValue("anotherlead@company.com");

            leadKeyList.getLeadKeies().add(key);
            leadKeyList.getLeadKeies().add(key2);

            JAXBElement<ArrayOfLeadKey> arrayOfLeadKey = objectFactory.createParamsRequestCampaignLeadList(leadKeyList);
            request.setLeadList(arrayOfLeadKey);

            SuccessRequestCampaign result = port.requestCampaign(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessRequestCampaign.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            m.marshal(result, System.out);

        }
        catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

Den här artikeln innehåller kod som används för att implementera anpassade integreringar. På grund av den anpassade karaktären kan Marketo tekniska support inte felsöka skräddarsytt arbete. Försök inte att implementera följande kodexempel utan lämplig teknisk erfarenhet, eller använd en erfaren utvecklare.

Publicerad _2014-05-16_ av _Murta_

## Anpassad aktivitetsspårning med Munchkin AP-

Låt oss se hur du kan följa upp anpassad aktivitet i Marketo. Du har till exempel en video på en webbsida och vill spåra besökare som tittar på mer än 50 % av en video. Du kan göra detta med Munchkin anpassade aktivitetsspårningsfunktion. Detta skulle implementeras genom att avlyssna en händelse på sidan, som är den video som når 50 %, och sedan anropa Munchkin API. För att göra detta måste vi skapa en anpassad aktivitet i Marketo som kan anropas baserat på den här händelsen på sidan. Vi använder YouTube för videospelaren och deras [YouTube Iframe API](https://developers.google.com/youtube/iframe_api_reference) för att anropa metoden i Munchkin API.

Vi visar först hur du genererar spårningskod för Munchkin i Marketo, dels hur du ändrar Munchkin exempelkod utifrån sidhändelser, dels hur du ställer in en kampanj med en smart lista som definieras av åtgärder på sidan med flödessteg, och dels hur du kontrollerar att ett sidbesök från en anonym användare spelades in i Marketo. ===== Det här blogginlägget är ett live exempel på koden som förklaras. Fyll i formuläret så att du är en känd användare i Marketo. När du ser 50 % av videon skickas resten av videon och om du tittar på 100 % av videon skickas en länk till ett annat blogginlägg. === <https://developers.google.com/youtube/iframe_api_reference>

**Så här genererar du spårningskod för Munchkin** Med spårningskoden för Munchkin kan du spåra besök på din webbplats. Det finns tre typer av Munchkin-kod som beskrivs nedan, men i det här exemplet använder vi den asynkrona Munchkin-spårningskoden. A) Enkel: har minst antal kodrader, men optimerar inte för webbsidans inläsningstid. Den här koden läser in jQuery-biblioteket varje gång en webbsida läses in. B) Asynkron: minskar inläsningstiden för webbsidor. Den här koden kontrollerar om jQuery-biblioteket redan finns, läser in det om det saknas och använder det för att köra spårningskod när resten av webbsidan har lästs in. C) Asynkron jQuery: minskar inläsningstiden för webbsidor och förbättrar även systemprestanda. Den här koden förutsätter att du redan har jQuery och inte kontrollerar att den har lästs in.

1. Klicka på Admin längst upp till höger i programmet.
1. Klicka på Munchkin i trädet till vänster.
1. Välj Asynkron för spårningskodtyp.
1. Klicka på och kopiera spårningskoden för JavaScript för att lägga ut den på webbplatsen. **YouTube Code** <https://developers.google.com/youtube/js_api_reference#EventHandlers> <https://developers.google.com/youtube/iframe_api_reference> spelare.

`getCurrentTime()` Returnerar förfluten tid i sekunder sedan videon startades. `player.getDuration()` Returnerar längden i sekunder för den video som spelas upp. Observera att `getDuration()` kommer att returnera 0 tills videons metadata har lästs in, vilket normalt inträffar precis när videon börjar spelas upp. När en icke-cookie-användare går till en sida med spårningskoden för Munchkin skapas en ny cookie i användarens webbläsare och en ny anonym lead skapas i Marketo. Om användaren redan är cookie och om användaren redan är en lead i Marketo, registreras besöket på sidan i aktivitetsloggen för användaren i Marketo. **Kodexempel till cookie-användare och spårningshändelse** Placera spårningskoden på dina webbsidor precis före `</body>` -taggen. Landningssidor som skapats i Marketo innehåller automatiskt spårningskod, så du behöver inte placera den här koden på dem. Detta kodexempel anropar Munchkin API när skriptet har lästs in:

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      Munchkin.init('XXX-XXX-XXX');
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

Det här kodexemplet anropar Munchkin API efter att användaren har varit på sidan i 5 sekunder och har även rullat 500 pixlar nedåt på sidan:

```javascript
<script src="https://code.jquery.com/jquery-2.1.0.min.js"></script>
<script type="text/javascript">
$(function(){
 setTimeout(function(){
  $(window).scroll(function() {
      var y_scroll_position = window.pageYOffset;
      var scroll_position = 500; //Sets number of pixels user must scroll to be tracked

  if(y_scroll_position > scroll_position) {
  //Munchkin tracking code
   (function() {
     var didInit = false;
     function initMunchkin() {
      if(didInit === false) {
        didInit = true;
        Munchkin.init('XXX-XXX-XXX');
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
   }
 },5000); //Sets time delay before tracking user
});
</script>
```

**Hur man verifierar sidbesök av anonyma användare spelades in i Marketo**

1. Klicka på Analytics (Analyser) i den övre menyn och klicka sedan på New Report (Ny rapport). Välj Webbsidesaktivitet som rapporttyp och ge sedan rapporten ett namn.
1. När du har skapat en rapport klickar du på Smart lista. Välj sedan filtret Besök webbsida i rutan till höger. Ange webbsidan där du placerar spårningskoden för Munchkin.
1. Klicka på Inställningar. Välj anonyma besökare från Internet-leverantörer och ändra alternativet till Visa.
1. Klicka på Rapport. Aktiviteten visas nu på den webbsida du valde.
1. Dubbelklicka på lead-posten, som visar aktivitetsloggen där du kan se den specifika sida som den anonyma användaren besökte.

Den här artikeln innehåller kod som används för att implementera anpassade integreringar. På grund av den anpassade karaktären kan Marketo tekniska support inte felsöka skräddarsytt arbete. Försök inte att implementera följande kodexempel utan lämplig teknisk erfarenhet, eller använd en erfaren utvecklare.

För sidor med multimediainnehåll kanske du vill göra en anpassad spårning. Ett vanligt exempel är att lägga till spårningskod för Munchkin på sidan och att använda Munchkin API för att generera händelser i din Marketo-instans för aktiviteter som att spela upp en video eller lyssna på ett ljudklipp. Vi rekommenderar att du lägger upp Munchkin spårningskod på de flesta eller alla dina webbsidor. Munchkin spårningskod inkluderas automatiskt på landningssidor som du skapar med Marketo. Använd det här anropet för att registrera att användaren gjorde något, som att besöka en sida i Ajax, Flash eller någon annan RIA-miljö. URL-adressen får inte innehålla &quot; eller någon domän, men den kan peka på vilken sida som helst - även sidor som inte finns. Om du vill lägga till URL-parametrar använder du parameterargumentet.
Händelsen visas som en Besök webbsida-händelse i användarens aktivitetslogg under domänen för den anropande webbsidan. Obs! Ditt första anrop till `mktoMunchkin()` skapar alltid en webbplatshändelse för den aktuella sidan. Du behöver inte ringa `visitWebPage` om du inte vill spåra ytterligare ett webbsidesbesök. `mktoMunchkinFunction('visitWebPage', { url: '/MyFlashMovie/Story1', params: 'x=y&2=3' });` Observera att du har tillgång till en erfaren JavaScript-utvecklare. Marketo tekniska support är inte konfigurerad för att hjälpa till med felsökning av anpassade JavaScript. Med Munchkin JavaScript API kan du integrera ett tredjeparts webbsystem med ditt Marketo-konto. Med viss webbutveckling kan du hämta nya leads eller uppdatera aktuella leads med befintliga program på din webbplats. Anta att ni har en webbapplikation för kundregistrering som samlar in ny kundinformation. Med bara en liten del av programmeringen kan du även ha huvudinformation för användare som tagits med i Marketo och en Marketo cookie-uppsättning för framtida webbspårning.

Dessutom kan webbutvecklare spela in och spåra webbaktivitetsinformation från webmiljöer som Flash eller Ajax. Obs! Om du har rätt utvecklingsresurser bör du överväga att använda vårt SOAP API för integrering i stället för detta API. SOAP API är stabilare och har fler funktioner än Munchkin API. Krav för Marketo SOAP API Du måste inkludera Munchkin JavaScript-koden på din webbsida för att detta ska fungera. Du hittar de script-taggar som behövs i självstudiekursen för Munchkin. Aktivera även Munchkin API, som också beskrivs i självstudiekursen.
Under Hood När du har gjort ett Munchkin API-anrop cookies användaren automatiskt om de inte har en cookie. I Marketo loggas händelsen (klicka på en länk, besök en webbsida eller ett nytt lead) i personens aktivitetslogg. Om du använder klicklänken eller besöker ett webbsidessamtal läggs händelsen till i leadets aktivitetslogg (känd eller anonym). Om det här är ett nytt lead och du använder det kopplade lead-anropet, blir det leadet ett känt lead och deras aktivitetshistorik bevaras. Om detta är ett befintligt lead (baserat på matchning av e-postadress) uppdateras alla ändrade eller nya värden i den lead-posten.

Här är den allmänna formen för ett `munchkinFunction`-samtal. Inkludera den som taggar på webbsidan var du vill anropa den. Du kan anropa detta som vilken annan JavaScript-funktion som helst. Du måste emellertid anropa spårningsfunktionen `mktoMunchkin()` för Munchkin innan du kan göra några `mktoMunchkinFunction()` anrop:

```javascript
<script src="http://munchkin.marketo.net/munchkin.js" type="text/javascript"> mktoMunchkin("###-###-###"); mktoMunchkinFunction('function', { key: 'value', key2: 'value'}, 'hash');
```

Där: `###-###-###` är Munchkin konto-ID:t för din kontofunktion är det anrop du vill göra parametrar till en matris med parametrar som behövs för anropshash-anropet behövs bara för `associateLead`

Publicerad _1970-01-01_ av _Murta_

## Hämta data till Marketo

Presentationen nedan visar olika sätt att hämta data till Marketo. Det fokuserar på formulär, anpassade objekt och integreringar.

[Hämta data till Marketo](https://www.slideshare.net/MurtzaManzur/getting-data-into-marketo-35662408) från [Murtza Manzur](https://www.slideshare.net/MurtzaManzur)

Publicerad _2014-06-06_ av _Murta_

## Skapa leads i en Workspace

Låt oss säga att ert företag har två divisioner: Nordamerika och Europa. Ni vill segmentera era leads baserat på företagets division i Marketo. Du kan göra detta med arbetsytor, som är en funktion i Marketo som gör att du kan begränsa åtkomsten till leads. För att göra detta skulle du skapa en arbetsyta för Nordamerika och en annan för Europa. Du kan sedan skapa ett lead på en viss arbetsyta med API:t [syncLead](/help/soap-api/synclead.md). Du bör överväga att använda arbetsytor och huvudpartitioner om din organisation har:

1. Olika marknadsföringsteam för flera produktrader
1. Olika marknadsföringsteam för olika territorier eller länder
1. Ett moderbolag och dotterföretag
1. Ett moderföretag och återförsäljare

När du använder leadpartitioner och arbetsytor kan du:

1. Begränsa åtkomsten till leads i organisationen
1. Begränsa åtkomst till resurser i organisationen
1. Dela resurser mellan marknadsföringsteam

Vi visar först hur du skapar en arbetsyta i Marketo via användargränssnittet och sedan hur du skriver en lead till den arbetsytan med [syncLead API](/help/soap-api/synclead.md). **Att skapa en Workspace** En arbetsyta är en uppsättning leads och Marketo-resurser. På en arbetsyta kan du bara se leads från den arbetsytan och resurser (e-post, kampanjer, listor osv.) på den arbetsytan. Smarta kampanjer i den arbetsytan påverkar bara leads i den arbetsytan. Så här visar du arbetsytorna i ditt konto:

1. Gå till sidan Arbetsytor och huvudpartitioner i administratörsavsnittet. Arbetsytorna visas på fliken Arbetsytor. 1. Om du vill skapa en ny arbetsyta klickar du på knappen Ny Workspace på menyraden på fliken Arbetsytor.
1. I dialogrutan måste du lägga till information om den nya arbetsytan:

* **Workspace-namn** - namnet på den här arbetsytan så som den visas i gränssnittet
* **Beskrivning** - en valfri textbeskrivning av arbetsytan
* **Leadpartitioner** - som leder visas i den här partitionen.
* **Alla huvudpartitioner** - visar leads från alla nuvarande och framtida partitioner
* **Välj enskilda partitioner** - endast leads från dessa partitioner (och inga framtida partitioner) visas
* **Primär leadpartition** - leads som besöker dina landningssidor läggs till i den här partitionen som standard
* **Språk** - arbetsytans affärsspråk

Vi visar hur du använder API-skrivningen för att skapa en viss arbetsyta. Det första exemplet är en XML-begäran, det andra är ett XML-svar och det sista är ett Ruby-kodexempel som kan användas för att generera XML-begäran. 1. När lead har skapats är Lead-partitionen ett fält i Lead-informationen. SOAP-begäran för `requestCampaign`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809939944BFABAE58E5D27</mktowsUserId><requestSignature>48397ad47b71a1439f13a51eea3137df46441979</requestSignature><requestTimestamp>2013-08-01T12:31:14-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsRequestCampaign>
      <source>MKTOWS</source>
      <campaignId>4496</campaignId>
      <leadList>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>lead@company.com</keyValue>
        </leadKey>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>anotherlead@company.com</keyValue>
        </leadKey>
      </leadList>
    </ns1:paramsRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

SOAP-svar för requestCampaign

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successRequestCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

Nedan visas ett exempel på ett Java-program som kör det scenario som beskrivs ovan.

```java
import com.marketo.mktows.*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;


public class RequestCampaign {

    public static void main(String[] args) {
        System.out.println("Executing Request Campaign");
        try {
            URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
            String marketoUserId = "CHANGE ME";
            String marketoSecretKey = "CHANGE ME";

            QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
            MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
            MktowsPort port = service.getMktowsApiSoapPort();

            // Create Signature
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
            String text = df.format(new Date());
            String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
            String encryptString = requestTimestamp + marketoUserId ;

            SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
            Mac mac = Mac.getInstance("HmacSHA1");
            mac.init(secretKey);
            byte[] rawHmac = mac.doFinal(encryptString.getBytes());
            char[] hexChars = Hex.encodeHex(rawHmac);
            String signature = new String(hexChars);

            // Set Authentication Header
            AuthenticationHeader header = new AuthenticationHeader();
            header.setMktowsUserId(marketoUserId);
            header.setRequestTimestamp(requestTimestamp);
            header.setRequestSignature(signature);

            // Create Request
            ParamsRequestCampaign request = new ParamsRequestCampaign();

            request.setSource(ReqCampSourceType.MKTOWS);

            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Integer> campaignId = objectFactory.createParamsRequestCampaignCampaignId(4496);
            request.setCampaignId(campaignId);

            ArrayOfLeadKey leadKeyList = new ArrayOfLeadKey();
            LeadKey key = new LeadKey();
            key.setKeyType(LeadKeyRef.EMAIL);
            key.setKeyValue("lead@company.com");

            LeadKey key2 = new LeadKey();
            key2.setKeyType(LeadKeyRef.EMAIL);
            key2.setKeyValue("anotherlead@company.com");

            leadKeyList.getLeadKeies().add(key);
            leadKeyList.getLeadKeies().add(key2);

            JAXBElement<ArrayOfLeadKey> arrayOfLeadKey = objectFactory.createParamsRequestCampaignLeadList(leadKeyList);
            request.setLeadList(arrayOfLeadKey);

            SuccessRequestCampaign result = port.requestCampaign(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessRequestCampaign.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            m.marshal(result, System.out);

        }
        catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

**Hur registrerar jag mig för arbetsytor och leadpartitioner?** För Marketo Enterprise-kunder har du kostnadsfri tillgång till arbetsytor och huvudpartitioner. Kontakta er kundaktiveringshanterare för mer information om hur ni aktiverar och implementerar dem. För andra kunder kan du kontakta din säljare på Marketo eller mejla till vårt säljteam för mer information om produkten.

Den här artikeln innehåller kod som används för att implementera anpassade integreringar. På grund av den anpassade karaktären kan Marketo tekniska support inte felsöka skräddarsytt arbete. Försök inte att implementera följande kodexempel utan lämplig teknisk erfarenhet, eller använd en erfaren utvecklare.

Publicerad _1970-01-01_ av _Murta_

## Versionsuppdateringar från juni 2014

### Marketo Real-Time Personalization API

Marketo Real-Time Personalization (RTP) JavaScript API utökar plattformens automatiserade personaliseringsfunktion. Det gör det möjligt att spåra händelser och anpassa en webbsida dynamiskt. Se fullständig dokumentation [här](/help/javascript-api/web-personalization.md).

Publicerad _2014-06-20_ av _Travis Kaufman_

## Lagra en sekundärnyckel i Marketo

När du synkroniserar kontakt- och lead-poster mellan system som ett leverantörsspecifikt CRM- eller datalager är det ett vanligt krav att associera en lead-post med en unik systemidentifierare. I Marketo kan du skapa eller uppdatera en lead-post via ett [syncMultipleLeads API](/help/soap-api/syncmultipleleads.md) -anrop med din unika systemidentifierare. För att uppnå detta lagrar du din unika systemidentifierare (primärnyckel) som en sekundärnyckel i Marketo. Namnet på det här fältet i Marketo för att lagra en sekundärnyckel är foreignSysPersonId. Här är tre viktiga saker att notera:

1. foreignSysPersonId är inte synligt i Marketo användargränssnitt. Därför är det bäst att även fylla i ett anpassat attributfält med det här värdet.
1. foreignSysPersonId är unikt för ett lead, men ett lead kan ha mer än ett ForeignSysPersonId.
1. foreignSysPersonId kan inte uppdateras eller tas bort, men kan tilldelas om till en annan post.

Vi visar hur du anropar [syncMultipleLeads API](/help/soap-api/syncmultipleleads.md) för att skriva ett ForeignSysPersonId-värde till två befintliga lead-poster i Marketo. **Skriva foreignSysPersonId med API:t syncMultipleLeads** Du kan infoga en ny lead-post och ange foreignSysPersonId. Du kan också lägga till det i en befintlig lead genom att ange både Marketo ID och ForeignSysPersonId. Vi går igenom det senare fallet. **Begär XML för syncMultipleLeads SOAP API Call**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809934544BFABAE58E5D27</mktowsUserId>
      <requestSignature>b5e21953ae9f1b263e644da5eccce9ff33802513</requestSignature>
      <requestTimestamp>2013-08-01T18:22:30-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsSyncMultipleLeads>
      <leadRecordList>
        <leadRecord>
          <leadId>1090240</leadId>
          <foreignSysPersonId>1224191</foreignSysPersonId>
          <leadAttributeList>
            <attribute>
              <attrName>Company</attrName>
              <attrValue>Marketo1000</attrValue>
            </attribute>
            <attribute>
              <attrName>Phone</attrName>
              <attrValue>650-555-1000</attrValue>
            </attribute>
          </leadAttributeList>
        </leadRecord>
        <leadRecord>
          <leadId>1090239</leadId>
          <foreignSysPersonId>1224192</foreignSysPersonId>
          <leadAttributeList>
            <attribute>
              <attrName>Company</attrName>
              <attrValue>Marketo1001</attrValue>
            </attribute>
            <attribute>
              <attrName>Phone</attrName>
              <attrValue>650-555-1001</attrValue>
            </attribute>
          </leadAttributeList>
        </leadRecord>
      </leadRecordList>
      <dedupEnabled>true</dedupEnabled>
    </ns1:paramsSyncMultipleLeads>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

**Svars-XML för syncMultipleLeads SOAP API Call**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successSyncMultipleLeads>
      <result>
        <syncStatusList>
          <syncStatus>
            <leadId>1090240</leadId>
            <status>UPDATED</status>
            <error xsi:nil="true" />
          </syncStatus>
          <syncStatus>
            <leadId>1090239</leadId>
            <status>UPDATED</status>
            <error xsi:nil="true" />
          </syncStatus>
        </syncStatusList>
      </result>
    </ns1:successSyncMultipleLeads>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

**Se nedan ett exempel på Ruby-program som kommer att visa XML för begäran ovan.**

```java
require 'savon' # Use version 2.0 Savon gem
require 'date'

mktowsUserId = "" # CHANGE ME
marketoSecretKey = "" # CHANGE ME
marketoSoapEndPoint = "" # CHANGE ME
marketoNameSpace = "<http://www.marketo.com/mktows/>"

# Create Signature
Timestamp = DateTime.now
requestTimestamp = Timestamp.to_s
encryptString = requestTimestamp + mktowsUserId
digest = OpenSSL::Digest.new('sha1')
hashedsignature = OpenSSL::HMAC.hexdigest(digest, marketoSecretKey, encryptString)
requestSignature = hashedsignature.to_s

# Create SOAP Header
headers = {
 'ns1:AuthenticationHeader' => { "mktowsUserId" => mktowsUserId, "requestSignature" => requestSignature,
 "requestTimestamp"  => requestTimestamp
 }
}

client = Savon.client(wsdl: '<http://app.marketo.com/soap/mktows/2_3?WSDL>', soap_header: headers, endpoint: marketoSoapEndPoint, open_timeout: 90, read_timeout: 90, namespace_identifier: :ns1, env_namespace: 'SOAP-ENV')

# Create Request
request = {
        :lead_record_list => {
                :lead_record => {
                        :lead_id => "1090240",
                        :foreign_sys_person_id => "1224191",
                :lead_attribute_list => {
                        :attribute => {
                                :attr_name => "Company",
                                :attr_value => "Marketo1000" },
                         :attribute! => {
                                :attr_name => "Phone",
                                :attr_value => "650-555-1000" }
                }
        },
        :lead_record! => {
                        :lead_id => "1090239",
                        :foreign_sys_person_id => "1224192",
                :lead_attribute_list => {
                        :attribute => {
                                :attr_name => "Company",
                                :attr_value => "Marketo1001" },
                         :attribute! => {
                                :attr_name => "Phone",
                                :attr_value => "650-555-1001" }
                }
        }
        },
    :dedup_enabled => "True"
}

response = client.call(:sync_multiple_leads, message: request)

puts response
```

Den här artikeln innehåller kod som används för att implementera anpassade integreringar. På grund av den anpassade karaktären kan Marketo tekniska support inte felsöka skräddarsytt arbete. Försök inte att implementera följande kodexempel utan lämplig teknisk erfarenhet, eller använd en erfaren utvecklare.

Publicerad _2014-06-27_ av _Murta_

## Uppdatera en leads e-postadress

Låt oss säga att en användare fyller i ett Marketo-formulär på din webbplats. Vad händer? Marketo cookies i användarens namn och kopplar dem till den e-postadress de har angett. Tänk om användaren nästa gång han/hon besöker er webbplats och fyller i samma formulär igen med ett annat mejl. Vad kommer att hända? Marketo skapar en ny lead-post och skriver över den första cookien i användarens webbläsare. Användaren är nu en ny/annorlunda lead i Marketo. Vi visar fyra sätt att uppdatera en leads e-postadress i Marketo, inklusive API-metoden [syncLead](/help/soap-api/synclead.md), det anpassade fältet i en formulärmetod, Marketo-gränssnittet och genom att importera en lista. **Via API:t syncLead** Du kan använda API:t [syncLead](/help/soap-api/synclead.md) för att uppdatera en lead-post med deras Marketo ID och nya e-postadress. Begär XML för `syncMultipleLeads` SOAP API-anrop

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>bigcorp1_461839624B16E06BA2D663</mktowsUserId>
      <requestSignature>92f05a7be4838ae1c0e5aafe814891ee72968a08</requestSignature>
      <requestTimestamp>2013-07-31T12:38:47-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsSyncLead>
      <leadRecord>
        <leadId>1090240</leadId>
        <Email>t@t.com</Email>
      </leadRecord>
      <returnLead>false</returnLead>
    </ns1:paramsSyncLead>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

Svars-XML för syncMultipleLeads SOAP API-anrop

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successSyncLead>
      <result>
        <leadId>1090240</leadId>
        <syncStatus>
          <leadId>1090240</leadId>
          <status>UPDATED</status>
          <error xsi:nil="true" />
        </syncStatus>
        <leadRecord xsi:nil="true" />
      </result>
    </ns1:successSyncLead>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

Se nedan ett exempel på Ruby-program som kommer att visa XML för begäran ovan.

```java
require 'savon' # Use version 2.0 Savon gem
require 'date'

mktowsUserId = "" # CHANGE ME
marketoSecretKey = "" # CHANGE ME
marketoSoapEndPoint = "" # CHANGE ME
marketoNameSpace = "<http://www.marketo.com/mktows/>"

# Create Signature
Timestamp = DateTime.now
requestTimestamp = Timestamp.to_s
encryptString = requestTimestamp + mktowsUserId
digest = OpenSSL::Digest.new('sha1')
hashedsignature = OpenSSL::HMAC.hexdigest(digest, marketoSecretKey, encryptString)
requestSignature = hashedsignature.to_s

# Create SOAP Header
headers = {
 'ns1:AuthenticationHeader' => { "mktowsUserId" => mktowsUserId, "requestSignature" => requestSignature,
 "requestTimestamp"  => requestTimestamp
 }
}

client = Savon.client(wsdl: '<http://app.marketo.com/soap/mktows/2_3?WSDL>', soap_header: headers, endpoint: marketoSoapEndPoint, open_timeout: 90, read_timeout: 90, namespace_identifier: :ns1, env_namespace: 'SOAP-ENV')

# Create Request
request = {
 :lead_record => {
  :Email => "<t@t.com>",
  :lead_id => "1090240",
 :return_lead => "false"
}

response = client.call(:sync_lead, message: request)

puts response
```

**Via ett anpassat fält i ett formulär** kan du skapa ett anpassat fält för den nya e-postadressen i Marketo. Be sedan användaren fylla i ett formulär som innehåller det nya fältet. Skapa sedan ett program i Marketo som skulle ändra datavärdet för systemets e-postadressfält med token `{{lead.newEmailAddress}}` när det finns en ändring i det nya anpassade fältet Ny e-postadress. **Via Marketo-gränssnittet** kan du uppdatera en leads e-postadress manuellt via Marketo användargränssnitt. Här är en [hjälpartikel](https://nation.marketo.com/) som beskriver hur du gör detta (Marketo-inloggning krävs för att se artikeln). **Genom att importera en lista** Du kan uppdatera en leads e-postadress med en listmetod i Marketo som beskrivs [här](https://nation.marketo.com/) (Marketo-inloggning krävs för att se artikeln).  

Den här artikeln innehåller kod som används för att implementera anpassade integreringar. På grund av den anpassade karaktären kan Marketo tekniska support inte felsöka skräddarsytt arbete. Försök inte att implementera följande kodexempel utan lämplig teknisk erfarenhet, eller använd en erfaren utvecklare.

Publicerad _1970-01-01_ av _Murta_

## Lagra en andra e-postadress för en lead

Säg att du vill ändra poängen för en lead i Marketo med API:erna. Detta kan du göra med REST API med hjälp av slutpunkten Skapa/uppdatera lead. Om du vill lagra mer än ett e-postmeddelande i en lead-post måste du skapa ett anpassat fält och lagra det andra e-postmeddelandet där. Här är en hjälpartikel med mer information: Nedan finns ett kodexempel i Ruby som visar hur du gör det här anropet.

```java
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/leads.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
request_url = marketo_instance + endpoint + auth_token

# Build request body
data = { "action" => "updateOnly", "input" => [ { "email" => "<example@email.com>", "leadScore" => "30" } ] }

# Make request
response = RestClient.post request_url, data.to_json, :content_type => :json, :accept => :json

# Returns Marketo API response
puts response
```

Publicerad _2015-02-20_ av _Murta_

## Skapa ett anpassat fält i Marketo och uppdatera det här fältet via AP

Låt oss säga att ni har ytterligare data om era leads som inte passar in i Marketo standardfält. Det här anpassade fältet kan till exempel vara ett poängvärde från en annan leverantör. Du kan skapa ett anpassat fält i Marketo för ditt poängvärde från tredje part och sedan uppdatera värdet för det här fältet via Marketo [REST API:er](https://developer.adobe.com/marketo-apis/) eller [SOAP API:er](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/soap/activity-type-filters). Vi visar först hur du skapar ett anpassat fält i Marketo och sedan hur du uppdaterar det här fältet med REST API.

### Skapa ett anpassat fält i Marketo

1. Klicka på Fälthantering under Admin.
1. Klicka på knappen Nytt anpassat fält.
1. Välj fälttyp. Detta ändrar hur det återges i smarta listor och Forms i Marketo.
1. Ange namnet som du vill att det ska visas i Marketo. Välj namn och API-namn noggrant eftersom det kan vara svårt att byta namn på fält och i vissa situationer inte är möjligt.
1. Det anpassade fältet som du skapade är nu tillgängligt via API:erna.

### Uppdatera anpassat fält via REST API

I föregående avsnitt skapade vi ett anpassat fält med namnet `myCustomField` och datatypsträngen. För att uppdatera värdet i det fältet använder vi REST API-slutpunkten som kallas Skapa/Uppdatera leads. Innan du kan göra en begäran till REST API måste du autentisera. Detta ligger utanför den här artikelns omfång, men detaljerad information [&#x200B; finns på webbplatsen &#x200B;](/help/rest-api/authentication.md) för Marketo-utvecklare.

**Slutpunkt**

`/rest/v1/leads.json`

**Begär brödtext**

```json
{
   "action":"createOrUpdate",
   "input":[
      {
         "email":"<example@example.com>",
         "myCustomField":"examplestring"
      }
   ]
}
```

Den här artikeln innehåller kod som används för att implementera anpassade integreringar. På grund av den anpassade karaktären kan Marketo tekniska support inte felsöka skräddarsytt arbete. Försök inte att implementera följande kodexempel utan lämplig teknisk erfarenhet, eller använd en erfaren utvecklare.

Publicerad _2014-08-19_ av _Murta_

## Integrera Unbounce och Marketo

**OBS! Detta är ett gästblogginlägg från Fab Capodicasa. Han är Marketo-certifierad konsult på [Hoosh Marketing](http://hooshmarketing.com.au/), en Marketo LaunchPoint-agentpartner som specialiserar sig på B2C. Han har arbetat i både SaaS och marknadsföring de senaste 13 åren. Hans bakgrund är en blandning av särskilt viktig IT, direktmarknadsföring och företagsförsäljning. Fab är även en tidigare Marketo-anställd.**

**Översikt** I den här artikeln visar vi hur du integrerar Unbounce, ett populärt landningssidverktyg, med Marketo. Vi visar först hur du infogar Marketo tracking i Unbounce och sedan hur du ändrar Unbounce-formulär för att infoga data direkt i Marketo. Utmaningen med att integrera Unbounce med Marketo är att Unbounce inte tillåter att standardfält får byta namn (till exempel kan inte first_name ändras till FirstName). Fältetiketter får inte heller skilja sig från fältnamn. Integreringen innebär att JavaScript anpassar de befintliga formulären så att de blir kompatibla med Marketo. Jag rekommenderar att du har minst en nybörjarnivå av JavaScript och en mellannivå av Marketo kunskaper för att kunna slutföra uppgifterna i den här artikeln.
**Del 1: Lägg till Marketo Tracking Code i Unbounce** Du måste lägga till Marketo Munchkin tracking-skript i Unbounce-sidor både för att analyser och formulärintegrering ska fungera. Gör så här: Kopiera din Munchkin-kod från Marketo: Navigera till Admin -> Munchkin och kopiera den enkla versionen av JavaScript. Öppna landningssidan Unbounce och klicka på JavaScript-> Add New JavaScript.  Klicka på Lägg till, anropa skriptet Munchkin, markera Before Body End Tag och klistra sedan in Munchkin-koden. Klicka på knappen Klar. För framtida Unbounce-sidor går du till JavaScript och aktiverar det Munchkin-skript vi har skapat. Det finns ingen anledning att återskapa det.
**Del 2: Konvertera Unbounce-formuläret till ett Marketo-formulär** Nu måste vi ändra Unbounce-formuläret genom att lägga till några nya dolda fält och JavaScript så att dina Unbounce-landningssidor kan skicka lead-information direkt till Marketo. Först skapar vi ett Marketo-platshållarformulär. Skapa ett tomt formulär och godkänn det i Marketo.

Detta är proxyformuläret i Marketo som representerar Unbounce-formuläret. Lägg till dolda fält i Unbounce-formuläret. Dessa dolda fält krävs av Marketo för att avgöra vilken Marketo-instans, vilket formulär och vilken användarsession som den här formuläröverföringen gäller för. Öppna formuläret genom att dubbelklicka i Unbounce. Lägg till ett dolt fält med namnet `_mkt_trk`. Lägg till ett andra dolt fält med namnet `formid`.  233 måste ersättas med ditt formulärs ID, som finns i Marketo formulärinbäddningskod i Marketo. Öppna formuläret i Marketo, välj Formuläråtgärder -> Bädda in kod. Lägg till ett dolt fält med namnet `returnurl`. `http://hooshmarketing.com.au/thank-you` måste ersättas med en uppföljnings-URL, det här är den URL som du vill att användare ska omdirigeras till efter att formuläret har skickats. Det här kan till exempel vara din tacksida.
**Del 3: Direkt fram från formulär till Marketo** Uppföljnings-URL:en är den sida som ditt lead kommer att omdirigeras till efter att deras lead har skickats till Marketo. Följ de här stegen i Unbounce: Klicka på formuläret. Ändra avsnittet Formulärbekräftelse. Ändra bekräftelsen så att den postar formulärdata till en URL. Ange URL-adressen till den uppföljningssida som du vill använda. `fpmarkets` måste ersättas med din Marketo-kontosträng, som finns i Marketo under Admin->Landing Pages.
**Del 4: Lägg till JavaScript på sidan Unbounce** Denna JavaScript konverterar formuläret så att det blir kompatibelt med Marketo och skickar det till Marketo. Följ de här stegen i Unbounce: Öppna landningssidan Unbounce och klicka på JavaScript-> Add New JavaScript (Lägg till ny). Klicka på Lägg till, anropa skriptet&quot;Marketo Form Convert&quot; och välj&quot;Before Body End Tag&quot;. Klistra in JavaScript-koden nedan:

```javascript
var MARKETO_MUNCHKIN_ID='614-CGT-700';
var MARKETO_ACCOUNT_STRING = 'fpmarkets';

var UNBOUNCE_MARKETO_FIELD_MAP = new Object();

//default field mappings
UNBOUNCE_MARKETO_FIELD_MAP['first_name'] = 'FirstName';
UNBOUNCE_MARKETO_FIELD_MAP['email'] = 'Email';
UNBOUNCE_MARKETO_FIELD_MAP['last_name'] = 'LastName';
UNBOUNCE_MARKETO_FIELD_MAP['phone_number'] = 'Phone';

function getMarketoField(k) {
    return UNBOUNCE_MARKETO_FIELD_MAP[k];
}


var formFields = [];
var hiddenClonedFields = [];
var firstForm = document.forms[0];

//Convert Unbounce form names to Marketo field names
for(i=0; i<firstForm.elements.length; i++){
 var formField = firstForm.elements[i];
 var newFieldName = getMarketoField(formField.name);

  if(newFieldName != undefined) {


    //save original field as hidden field
    var hiddenField = document.createElement("input");
    hiddenField.setAttribute("type", "hidden");
    hiddenField.setAttribute("name", formField.name);
    hiddenField.setAttribute("id", formField.id);
    hiddenClonedFields.push(hiddenField);

    //change original field
    console.log ( 'Changed form field name: ' + formField.name + '=>' + newFieldName );
    formField.name = newFieldName;
    formField.id = newFieldName;
    formFields.push(formField);


  } else {
    console.log ( 'Couldn't map:' + formField.name );
  }
}

//Add hidden cloned Unbounce fields to form
//for Unbounce validation
for(i=0; i<hiddenClonedFields.length; i++){
    firstForm.appendChild(hiddenClonedFields[i]);
    formFields[i].onchange = (function(hf) {
        return function(event) {
            hf.value = event.target.value;
          console.log('Changed hidden cloned field:' + hf.name + '=>' + hf.value);
        };
    }(hiddenClonedFields[i]));
    console.log ( 'Added cloned field: ' + hiddenClonedFields[i].name );
}


//Add MunchkinId to form
var input = document.createElement("input");
input.setAttribute("type", "hidden");
input.setAttribute("name", "munchkinId");
input.setAttribute("value", MARKETO_MUNCHKIN_ID);
firstForm.appendChild(input);
console.log('Added hidden field:' + input.name + '=' + input.value);
```

Om du har anpassade fält som har API-namn som inte är kompatibla med Unbounce, kan du lägga till dessa i mappningen i JavaScript. Om ett av dina anpassade fält i Marketo till exempel är `Comments_c`, men du vill att fältetiketten ska visas som kommentarer, kan du inte ändra fältnamnet till `Comments_c` med Unbounce.

```
//default field mappings
UNBOUNCE_MARKETO_FIELD_MAP['comments'] = 'Comments_c';
UNBOUNCE_MARKETO_FIELD_MAP['first_name'] = 'FirstName';
UNBOUNCE_MARKETO_FIELD_MAP['email'] = 'Email';
```

_comments är namnet på fältet i Unbounce. _Comments_c_ är namnet på fältet i Marketo. För framtida Unbounce-sidor går du bara till JavaScript och aktiverar det Munchkin-skript vi har skapat. Det finns ingen anledning att återskapa det.
**Del 5: Testning** Det sista steget är att testa att den här formulärintegreringen fungerar. Skapa en utlösare i Marketo som aktiveras i Marketo-formuläret och ser till att leads infogas korrekt i Marketo. När formuläret har skickats ska sidan dirigera om dig till uppföljnings-URL:en.

Bokfört _2014-08-04_ av _

## Versionsuppdateringar från juli 2014

### Munchkin Updates

Den nya versionen av Munchkin är 141. Version 142 stöds inte och tas bort i början av september 2011. I version 142 fanns funktioner som var tillgängliga för allmänheten och som inte dokumenterades på utvecklarens webbplats. Dessa odokumenterade funktioner är inte längre tillgängliga för allmänheten. Dokumenterade funktioner på utvecklarens webbplats kommer att få fortsatt stöd på lång sikt.

### RTP-uppdateringar

RTP-API:t har en ny funktion som heter Hämta Visitor-data. Med detta RTP API-anrop kan du få besöksdata i realtid, som till exempel organisation, bransch, plats och segmentkodmatchning.

Publicerad _2014-07-30_ av _Murta_

## Använda flera spårningskoder för Munchkin på en sida

Anta att du har flera instanser av Marketo och att du vill skicka webbspårningshändelser som besök på webben eller klickade länkar till dessa flera förekomster, så är det möjligt med Munchkin. Marketo spårar besökare på din webbplats efter domän (till exempel&quot;marketo.com&quot;). Om du är värd för det här Munchkin-skriptet på en domän som inte är din primära domän (till exempel&quot;company.com&quot;) visas dessa besökare som anonyma leads tills de fyller i ett formulär på den andra domänen. För att uppnå detta lägger du till parametern `altIds` i ditt `Munchkin.init`-samtal. Parametern altIds innehåller en array med ytterligare Munchkin-id:n dit webbhändelser ska skickas. Använd exemplet nedan för att ersätta de markerade Munchkin-ID:n (XXX-XXX-XXX, YYY-YYY och ZZZ-ZZZ) med Munchkin-ID:n från varje Marketo-instans där spårningsinformationen ska skickas.

```javascript
<script src="http://munchkin.marketo.net/munchkin.js" type="text/javascript"></script>
<script>Munchkin.init('XXX-XXX-XXX', { altIds:['YYY-YYY-YYY', 'ZZZ-ZZZ-ZZZ'] });</script>
```

Mer information om Munchkin initieringsparametrar finns i [det här dokumentet](/help/javascript-api/configuration.md).

Publicerad _2014-08-08_ av _Murta_

## Integrera Munchkin med Google Tagghantering

Med Google Tag Manager kan du lägga till taggar på webbplatsen. I stället för att manuellt lägga till varje spårningsskript som Munchkin till webbplatsens källkod kan du placera [Google Tag Manager](https://marketingplatform.google.com/about/tag-manager/) på webbplatsen och sedan lägga till taggar som [Munchkin](/help/javascript-api/lead-tracking.md) via Google Tag Managers användargränssnitt. I det här inlägget ska vi först visa hur du genererar spårningskod för Munchkin i Marketo och sedan se hur du lägger till den här spårningskoden för Munchkin i Google Tag Manager.

### Så här genererar du en spårningskod för Munchkin

1. Klicka på **Admin** längst upp till höger i programmet.
1. Klicka på **Munchkin** i trädet till vänster.
1. Välj **Asynkron** som spårningskodtyp.
1. Klicka och kopiera spårningskoden för JavaScript.

**Så här lägger du till Munchkin Tracking Code i Google Tag Manager**

1. Logga in på ditt Google Tag Manager-konto och **lägg till en ny tagg.**
1. Skapa en ny **anpassad HTML-tagg**.
1. Kopiera och klistra in din Munchkin-kod i fältet **HTML** och klicka på **Fortsätt**.
1. Välj **Starta på alla sidor** och klicka på **Skapa tagg.** Obs! Om du har en webbplats med mycket hög trafik kan du utesluta avsnitt av din webbplats med **Starta på vissa sidor**.
1. Klicka på Spara och kontrollera sedan att Munchkin spårningskod läses in på webbplatsen.

Den här artikeln innehåller kod som används för att implementera anpassade integreringar. På grund av den anpassade karaktären kan Marketo tekniska support inte felsöka skräddarsytt arbete. Försök inte att implementera följande kodexempel utan lämplig teknisk erfarenhet, eller använd en erfaren utvecklare.

Publicerad _2014-08-05_ av _Murta_

## Dölj ljuslåda efter att formulär har skickats

### Översikt

Den inbäddningskod för ljuslådan som genererats av Marketo försvinner inte när formuläret skickas. Sidan läses in igen när formuläret har skickats och formuläret visas igen. Om du vill skapa en ljuslåda som döljs när formuläret har skickats in följer du stegen nedan.

### Handbok

1. Generera inbäddningskoden när du har skapat formuläret i Marketo. Det gör du genom att klicka på Formuläråtgärder och sedan på Ljuslåda som kodtyp. Kopiera och klistra in koden i en textredigerare så att du kan redigera den i nästa steg.
1. Ersätt all kod efter&quot;(form)&quot; i din inbäddningskod med koden nedan:

```javascript
{
var lightbox = MktoForms2.lightbox(form).show();
form.onSuccess(function(){
lightbox.hide();
return false;
});
});
</script>
```

Efter steg 2 ska den färdiga versionen se ut som koden nedan. Koden kan nu användas på webbplatsen.

```javascript
<script src="http://app-sj04.marketo.com/js/forms2/js/forms2.js"></script>
<form id="mktoForm_0000"></form>
<script>MktoForms2.loadForm("http://app-sj04.marketo.com", "AAA-BBB-CCC", 0000, function (form){
var lightbox = MktoForms2.lightbox(form).show();
form.onSuccess(function(){
lightbox.hide();
return false;
});
});
</script>
```

Den här artikeln innehåller kod som används för att implementera anpassade integreringar. På grund av den anpassade karaktären kan Marketo tekniska support inte felsöka skräddarsytt arbete. Försök inte att implementera följande kodexempel utan lämplig teknisk erfarenhet, eller använd en erfaren utvecklare.

Publicerad _2014-08-21_ av _Murta_

## Snabbstartsguide för Marketo REST API

Den här guiden visar hur du gör ditt första anrop till Marketo REST API på tio minuter. Vi visar hur du hämtar ett enskilt lead med hjälp av REST API-slutpunkten [Get Lead by Id](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET). För att göra detta vägleder vi dig genom autentiseringsprocessen för att generera en åtkomsttoken, som du använder för att göra en HTTP GET-begäran till [Get Lead by Id](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET). Sedan skickar vi koden till dig för att göra en förfrågan som returnerar leadinformation formaterad som JSON.

### Så här genererar du en autentiseringstoken

>[!NOTE]
> Från och med juni 2025 stöds inte längre autentiseringstoken. Du måste använda autentiseringshuvudet.
>

Med en anpassad tjänst i Marketo kan du beskriva och definiera vilka data ditt program ska ha tillgång till. Du måste vara inloggad som Marketo-administratör för att kunna skapa en anpassad tjänst och associera den tjänsten med en enda API-användare.

1. Navigera till administratörsområdet för Marketo-programmet.
1. Klicka på noden Användare och roller på den vänstra panelen.
1. Skapa en ny roll. Visa listan över rollbehörigheter genom att klicka på Access API. Bläddra nu nedåt och välj bara de behörigheter du behöver. I det här fallet väljer vi bara skrivskyddad lead-behörighet.
1. Nästa steg är att skapa en användare med endast API och associera den med API-rollen som du skapade i föregående steg. Du kan göra det genom att markera kryssrutan Endast API-användare när användaren skapas.
1. En anpassad tjänst krävs för att unikt identifiera ditt klientprogram. Om du vill skapa ett anpassat program går du till skärmen Admin > LaunchPoint och skapar en ny tjänst.
1. Ange visningsnamn, välj Anpassad tjänsttyp, ange beskrivning och användarens e-postadress som skapades i steg 1. Vi rekommenderar att du använder ett beskrivande visningsnamn som representerar antingen företaget eller syftet med den här anpassade REST API-tjänsten.
1. Klicka på länken Visa detaljer i rutnätet för att hämta klient-ID och klienthemlighet. Klientprogrammet kan använda klient-ID och klienthemlighet för att generera en åtkomsttoken.
1. Kopiera och klistra in din autentiseringstoken i en textredigerare. Din autentiseringstoken ser ut ungefär som i exemplet nedan:

`cdf01657-110d-4155-99a7-f986b2ff13a0:int`

### Så här fastställer du slutpunkts-URL

När du skickar en begäran till Marketo API måste du ange din Marketo-instans i URL:en för slutpunkten. Alla icke-satsvisa API-begäranden till Marketo REST API kommer att följa formatet nedan:

`<REST API Endpoint URL>/rest/`

URL:en för REST API-slutpunkten finns i Marketo Admin > panelen Webbtjänster. URL-strukturen för din Marketo-slutpunkt ska se ut ungefär som i exemplet nedan:

`https://100-AEK-913.mktorest.com/rest/v1/lead/{id}.json`

**Obs! URL:er för massvis-API:er har inte prefixet /rest/. För API:t Bulk ska du bara använda värden och lägga till lämplig sökväg som:**

`https://100-AEK-913.mktorest.com/bulk/v1/leads/export/create.json`

### Så här använder du autentiseringstoken för att anropa Get Lead by Id AP

I föregående avsnitt genererade vi en autentiseringstoken och hittade slutpunkts-URL:en. Vi kommer nu att göra en begäran till REST API-slutpunkten med namnet [Hämta lead med ID](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET). Det enklaste sättet att göra din begäran till Marketo REST API är att klistra in URL:en i webbläsarens adressfält. Följ formatet nedan:

`https://<REST API Endpoint URL for your Marketo instance>/rest/v1/<API that you are calling>?access_token=<access_token>`

### Exempel

`https://100-AEK-913.mktorest.com/rest/v1/lead/318581.json?access_token=cdf01657-110d-4155-99a7-f986b2ff13a0:int`

Om samtalet lyckas returneras JSON med följande format:

```json
{
    "requestId": "d82a#14e26755a9c",
    "result": [
        {
            "id": 318581,
            "updatedAt": "2015-06-11T23:15:23Z",
            "lastName": "Doe",
            "email": "<jdoe@marketo.com>",
            "createdAt": "2015-03-17T00:18:40Z",
            "firstName": "John"
        }
    ],
    "success": true
}
```

Om du vill veta mer om Marketo REST API:er är detta en [bra startpunkt](https://developer.adobe.com/marketo-apis/).

Publicerad _2015-09-04_ av _David_

## Ta bort Marketo Tracking Cookie

Fråga:&quot;Vi har ställt upp ett formulär på vår webbplats så att säljarna kan avbeställa prenumerationer på personer som har bett att bli borttagna från alla e-postmeddelanden. Men det vi finner är när de skriver i e-postmeddelandet och skickar in att de är cookiekodade med den e-postadressen och börjar få varningar om sin aktivitet på vår webbplats. Formuläret är för närvarande inbäddat. Har någon kommit på ett sätt att inaktivera kock-tracking på ett inbäddat formulär, så förstår jag hur man gör det med en Marketo-landningssida.&quot; Vi kan göra det här. Om du vill implementera det här går det snabbare att ange förfallodatum för cookie. När cookien har skapats av formuläret kan du tvinga den att upphöra omedelbart. Eftersom cookie-filen har upphört att gälla tas den bort automatiskt av webbläsaren. Vi kommer att använda den inbyggda formulärfunktionen för att anropa funktionen `deleteCookie` för att starta den här processen.

Publicerad _2014-08-26_ av _Travis Kaufman_

## Integrera en Marketo-landningssida med Wordpress

Anta att din webbplats är byggd med Wordpress, och du vill bädda in en Marketo-landningssida på en av sidorna. Detta är möjligt med en iframe. Med en iframe kan du bädda in en sida på en annan sida. I det här inlägget visas hur du bäddar in en Marketo-landningssida på en Wordpress-sida. **Välj en Wordpress-plugin** Wordpress Det finns ett antal här som gör att du kan använda en Wordpress-plugin: `http://wordpress.org/plugins/iframe/` Här är några proffs och ikoner som använder den här metoden: `http://www.elixiter.com/marketo-landing-page-and-form-hosting-options/` Snyggt inlägg från Murtza! Colby, iframe skulle bevara den del där du efter att ha skickat formuläret omdirigeras till en PDF. Vi har använt iframes på vår webbplats länge. Snyggt inlägg Murtza! Colby, iframe skulle bevara den del där du efter att ha skickat formuläret omdirigeras till en PDF. Vi har använt iframes på vår webbplats länge. Observera att om du vill skicka genom URL-parametrar från huvud-URL:en till iframe måste du göra lite kodning. Kontrollera också att Google Analytics är rätt konfigurerat. Du vill inte att sidvyer ska räknas två gånger för varje besök på sidan med iframe.

Publicerad _1970-01-01_ av _Murta_

## Integrera optimalt med en Marketo startsida

[Optimalt](https://www.optimizely.com/) ger dig möjlighet att utföra A/B-testning, flersidiga tester och multivariata tester på din webbplats. Du kan använda Optimizely med en Marketo-landningssida. Så här gör du:

1. Hitta och kopiera det optimala kodfragmentet.** Gå till Dashboard i Optimizely och klicka på länken Projektkod. Kopiera kodraden som visas i popup-fönstret.
1. Logga in på Marketo och välj en landningssidmall. Klicka sedan på&quot;Redigera utkast&quot;.**
1. Klicka på Åtgärder för landningssida. Klicka sedan på Redigera sidmetataggar**
1. Klistra in det optimala kodfragmentet i sektionen Anpassad HEAD HTML och klicka på Spara.
1. Testa landningssidan för att bekräfta att Optimizely-fragmentet fungerar

Publicerad _2014-09-18_ av _Murta_

## Sök efter fullständigt namn via Marketo REST API

**Fråga:** Finns det något sätt att fråga en lead med Marketo API:er med bara ett leads fullständiga namn?
**Svar:** Det går inte direkt. Den lösning som beskrivs nedan gör att du kan göra detta.

1. Skapa ett anpassat fält med namnet&quot;Fullständigt namn&quot; i Marketo.
1. Använd antingen [getMultipleLeads](/help/soap-api/getmultipleleads.md) SOAP API eller [Hämta flera leads efter filtertyp](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET) för att fråga din lead-databas. Inkludera ditt förnamn och efternamn som attribut i din begäran till antingen REST eller SOAP API:er.
1. När du har frågat din lead-databas sammanfogar du Förnamn och Efternamn för varje lead och lagrar dessa data i en kolumn av typen Fullständigt namn. 1. Använd SOAP-API:t [syncMultipleLeads](/help/soap-api/syncmultipleleads.md) om du vill skicka dessa data till det anpassade fältet Fullnamn. Du kan också använda API:t [Importera lead](/help/rest-api/leads.md) eller importera en CSV- eller XLS-fil med Marketo-gränssnittet.
1. Nu kan du fråga efter fullständigt namn med [API:t Hämta flera leads efter filtertyp](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) för att söka efter det här anpassade fältet. Ange &quot;Fullnamn&quot; som &quot;filterType&quot; och &quot;filterValue&quot; som &quot;Joe Johnson&quot; med anropet Get Multiple Leads by Filter Type REST API.

Publicerad _2014-09-09_ av _Murta_

## Ta bort Marketo Tracking Cookie

Den här metoden bör endast användas om önskad effekt är att ta bort cookien helt.

Det här kodexemplet kan användas för att ta bort en Marketo tracking-cookie från en användares webbläsare när Marketo-formuläret har skickats. Det fungerar genom att anropa metoden `deleteMarketoCookie` efter att en användare har skickat in ett formulär. Den här metoden förfaller cookien genom att ange förfallodatumet till ett tidigare datum. Webbläsarens standardbeteende är då att ta bort denna cookie eftersom den har upphört att gälla.

Publicerad _2014-09-09_ av _Murta_

## Begränsa kostnadsfria e-postdomäner vid formulärifyllning

Säg att du vill hindra besökare från att skicka in ett formulär med en kostnadsfri e-postdomän, som Gmail eller Yahoo. För att uppnå detta inkluderar du skriptet nedan i källkoden för sidan med Marketo-formuläret. Den kontrollerar om en användare har angett ett icke-företagsmeddelande (Gmail, Hotmail osv.) och förhindrar att Marketo-formulär skickas in om en användare anger ett. Du kan utöka listan över blockerade e-postdomäner genom att ändra rad 9 så att den innehåller domäner som du vill begränsa.

Publicerad _2014-09-09_ av _Murta_

## Felsöka ett fält som inte är tillgängligt via API

Om du kommer till det här fältet <https://nation.marketo.com/> När vi försöker uppdatera fältet AnnualRevenue med SOAP API, säger svaret att posten uppdateras, men fältet AnnualRevenue kvarstår inte ändringen. Om vi försöker uppdatera fältet manuellt med hjälp av Lead Database bevaras inte heller ändringarna i det här fältet. Kontrollera om fältet är blockerat i Admin. Det andra som ibland kan hända är att det kan vara ett kontofält som synkroniseras från SFDC. Vi blockerar ändringar i dessa fält som standard eftersom SFDC i många fall är arkivsystemet. 1) Skapat vid 2) Marketo SFDC ID 3) Marketo Unik kod 4) SFDC Type 5) Uppdaterat vid 6) Urnummer 7 Prioritet 8) Försäljningsdatum Skapat

Publicerad _1970-01-01_ av _Murta_

## Lägg till anpassad kod på en Marketo-landningssida

Säg att du vill lägga till ett spårningsskript från tredje part, som Google Analytics, på Marketo landningssida. Detta är möjligt via Marketo användargränssnitt. Mer generellt kan du lägga till anpassad kod (HTML, CSS, JavaScript) till Marketo-landningen genom att följa instruktionerna nedan.

1. Välj landningssida och klicka på Redigera utkast.
1. Dra i HTML-elementet.
1. Ange din egen kod och klicka på Spara.

Publicerad _2014-09-10_ av _Murta_

## Post för formulär på serversidan

`https://community.marketo.com/MarketoResource?id=kA650000000GsXXCA0`

Publicerad _2014-09-11_ av _Murta_

## Rensa Marketo Tracking Cookie från Forms 2.0-inlämningar

### Översikt

Forms 1.0 innehöll värdet för Munchkin-spårningscookien som ett fält i DOM. Detta lades fram tillsammans med alla andra insatsvaror. [Forms 2.0](/help/javascript-api/forms-api-reference.md) utelämnar det här fältet och fyller i värdet dynamiskt när formuläret skickas i stället för vid formulärinläsning. Detta är i allmänhet acceptabelt, men skapar en klass av användningsfall, som kräver att spårningscookien rensas för att undvika oavsiktlig spårning och förifyllning. Detta kan till exempel inträffa vid ett bildspel där en Marketo-kund använder samma formulär på samma enhet och hämtar kontaktinformation från flera personer. Med kodutdraget nedan kan du rensa cookie-värdet när formuläret skickas, utan att behöva ta bort själva cookien från användarens webbläsare.

### Kodexempel

Det här fragmentet förväntar sig att ett enda formulär ska läsas in på sidan. Om det finns flera formulär bör du använda metoderna [loadForm eller getForm](/help/javascript-api/forms-api-reference.md) för att implementera återanropen.

```javascript
<script>
//add a callback to the first ready form on the page
MktoForms2.whenReady( function(form){
 //add the tracking field to be submitted
        form.addHiddenFields({"_mkt_trk":""});
        //clear the value during the onSubmit event to prevent tracking association
 form.onSubmit( function(form){
  form.vals({"_mkt_trk":""});
 })
})
</script>
```

Publicerad _2014-09-11_ av _Kenny_

## Versionsuppdateringar för september 2014

### Uppdateringar till REST API

Ett nytt valfritt fältvärde har lagts till i API:t [Hämta flera leads](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) som returnerar de Munchkin cookie-värden som är kopplade till en lead-post. Lägg bara till &quot;?fields=cookies&quot; i begäran.

Publicerad _2014-09-16_ av _Murta_

## Lägg till platsdata från RTP API till Marketo Form Fill Out

**Du behöver aktiva RTP- och MLM-prenumerationer för att implementera det användningsfall som beskrivs i det här blogginlägget.**
Med [&#x200B; RTP JavaScript API:er &#x200B;](/help/javascript-api/web-personalization.md) och [&#x200B; Marketo Forms 2.0](/help/javascript-api/forms-api-reference.md) kan du hämta härledda platsdata från RTP och överföra dem till Marketo via en formulärfyllning. På så sätt kan du se användarens härledda plats (baserat på IP-adressen) under den senaste formuläraktiviteten. För att komma igång måste du skapa tre anpassade strängfält i Marketo. Du kan göra detta antingen via CRM om det har en inbyggd integrering med Marketo, eller via menyn Fälthantering i Marketo administratörsavsnitt. Jag rekommenderar att du namnger fälten&quot;Senaste land&quot;,&quot;Senaste stat&quot; och&quot;Senaste stad&quot;. Vi fortsätter med den här bloggen med den här namnkonventionen. API-namnen för dessa fält är&quot;mostRecentCountry&quot;,&quot;mostRecentState&quot; och&quot;mostRecentCity&quot;. För att hämta platsdata använder vi metoden [RTP för att hämta besökarens platsdata](/help/javascript-api/web-personalization.md) och skickar dem sedan till formuläret med metoderna [addHiddenFields och vals](/help/javascript-api/forms-api-reference.md) från Marketo Forms 2.0. Lägg till RTP JS-taggen och ett Marketo-formulär på din sida. Inkludera sedan skriptet nedan. Du måste ändra namnen på målformulärfälten i exempelkoden om du använder en annan namnkonvention än den som beskrivs ovan.

```javascript
<script>
//modify the form and grab the user
MktoForms2.whenReady( function(form) {
        //add the hidden fields to the form
 form.addHiddenFields({
  "mostRecentCountry":"",
  "mostRecentState":"",
  "mostRecentCity":""});
 //Grab the visitor data, a JS object with it is passed in the callback function of the third argument
 rtp('get','visitor',function(visitor){
  //add the desired info from the visitor object to the form fields
  form.vals({
   "mostRecentCountry":visitor.results.location.country,
   "mostRecentCity":visitor.results.location.city,
   "mostRecentState":visitor.results.location.state});
  }
  );
 });
</script>
```

Publicerad _2014-09-17_ av _Kenny_

## Blockcrawlning och sökindexering för en Marketo-landningssida

Säg att du vill blockera en Marketo landningssida från att crawlas och visas i resultat av sökmotorer som Google. Så här gör du:

1. Logga in på Marketo och välj landningssida. Klicka sedan på&quot;Redigera utkast&quot;.
1. Klicka på Åtgärder för landningssida. Klicka sedan på Redigera sidmetataggar
1. Ändra rotfältet till Inget index, ingen uppföljning. Klicka sedan på Spara.

Publicerad _2014-09-19_ av _Murta_

## Vanliga frågor om Marketo REST vs. SOAP API:er

**Uppdaterad: Mars 2016** Här är svar på de vanligaste frågorna om Marketo [REST](/help/rest-api/rest-api.md) och [SOAP](/help/soap-api/soap-api.md) API:er. **F: Vilka är de viktigaste skillnaderna mellan Marketo REST och SOAP API:er?** A: Även om möjligheten att skicka/hämta specifika data via REST- och SOAP-API:er för det mesta överlappar, finns det vissa funktioner som bara finns i REST- eller SOAP-API:er. När det gäller prestanda har REST API bättre [genomströmning](https://en.wikipedia.org/wiki/Throughput) än SOAP API. När det gäller autentiseringsmodellen har REST API en autentiseringsmodell som använder en token som går ut. Vårt REST API ger även åtkomst till Marketo [resurser](https://developer.adobe.com/marketo-apis/api/asset/).   **Q: Vilka funktioner finns i REST API som inte är tillgängliga i SOAP API?** A: [Lista med list-API:t &#x200B;](/help/rest-api/list-of-standard-fields.md), [ta bort en lead från en list-API](/help/rest-api/lead-database.md), [användnings-API](/help/rest-api/rest-api.md) och [fel-API](/help/rest-api/rest-api.md) är bara tillgängliga med REST API:t. **F: Finns det några planer på att öka antalet API:er som är tillgängliga för SOAP API?** A: Nej. **F: Finns det några planer på att öka antalet API:er som är tillgängliga för REST API?** A: Ja. REST är för närvarande det primära fokus som Marketo API-utveckling har.

Publicerad _2014-09-20_ av _Murta_

## Post för formulär på serversidan

**Obs! Detta är ett API som inte är offentligt och som inte stöds, som inte stöds, och dess beteende kan ändras när som helst** Om du använder dina egna formulär på webbplatsen kan du ändå skicka dessa data till Marketo med ett inlägg på servern. Fördelen med detta är att du kan behålla befintliga formulär- och programlogik, men du kan fortfarande använda ett faktiskt formulärinlägg i Marketo. Detta ger Marketo-användare en händelse om att formuläret fylls i, som kan användas för att utlösa automatiserade processer eller för segmentering. **Obs! Det finns en hastighetsgräns på 30 formulärinlägg på serversidan per minut från en enda IP-adress.** I varje skript- eller programmeringsspråk finns det olika alternativ för att skicka formulär på serversidan, och de kan ha olika objekt/metoder som kan användas för att göra ett efteranrop. I PHP använder till exempel många personer biblioteket cURL. I samtliga fall skickar du namnvärdespar till en angiven URL. Namnen måste vara identiska med API-namnen för Marketo-fälten. Dessutom finns det några systemfält som måste inkluderas för att formuläret ska kunna skickas korrekt.

1. Skapa ett formulär. Det första steget är att skapa ett formulär i Marketo eller att använda ett befintligt formulär som du vill skicka in. Formulärets namn måste vara beskrivande, men behöver egentligen inte några formulärfält. Om du skapar ett nytt formulär anger du bara ett namn, avmarkerar kryssrutan Öppna formulärredigeraren så är du klar.
1. Hitta ett formulär-ID. I Marketo-gränssnittet markerar du formuläret och tittar på URL:en: det ska ha formatet `https://app-x.marketo.com/#FO8B2ZN12`. Bakom #-tecknet tittar du på numret som finns omedelbart efter FO för att hitta formulär-ID:t. I det här fallet är formulär-ID 8. I vissa fall kan ditt första formulär vara numrerat 1001 och räkna upp därifrån. Formulär-ID:t är en variabel, så att du kan aktivera inskickandet av olika formulär.
1. Hämta ditt Marketo konto-ID. Gå till Admin > Munchkin och kopiera Munchkin konto-ID:t, som har formatet 000-AAA-000. Du behöver detta för att formuläret ska kunna skickas till rätt Marketo-instans.
1. Bestäm POST-URL. Observera domänen i platsfältet, vanligtvis i formatet `<http://app-x.marketo.com/>`, när du är i Marketo-användargränssnittet. Ignorera allt efter snedstrecket och lägg sedan till index.php/leadCapture/save för att få den fullständiga POST-URL:en för formuläret. Anm. 1: Detta är skiftlägeskänsligt. Obs 2: Marketo Sandbox kan ha en annan domän än Production Marketo. Ett exempel-URL skulle därför vara: `http://app-x.marketo.com/index.php/leadCapture/save` Du kan också använda HTTPS i stället för HTTP (använd inte CNAME som det ger ett säkerhetsundantag).
1. Hitta formulärfältsnamnen** Gå till Admin > Fälthantering och klicka på knappen &quot;Exportera fältnamn&quot; för att ladda ned ett kalkylblad med API-fältnamnen. Använd API-namnet som namn i namnvärdespar.
1. Bestäm vilka fält som ska bokföras. Du kan inkludera vilket Marketo Lead-fält som helst i formulärinlämningen. Observera att fältnamn är skiftlägeskänsliga. Förutom de fält som du vill skicka finns det två obligatoriska fält och två rekommenderade fält: Obligatoriska fält i formuläret: (1) `munchkinId` - Det här fältet används för ditt Munchkin konto-ID (2) `formid` - Det här fältet anger vilket formulär i Marketo som har skickats Rekommenderade fält i formuläret: (1) E-post - det här fältet används som primärnyckel för borttagning av dubbletter. Om Marketo hittar en matchande e-postadress i Marketo-databasen uppdateras den befintliga posten, annars skapas en ny post. Om det finns flera matchningar uppdateras den senast uppdaterade posten (2) `_mkt_trk` - det här fältet innehåller cookie-informationen, så att du kan spåra besöken på personens webbsida. Om du har Munchkin på formulärsidan, kommer Munchkin automatiskt att ange ett värde i det här dolda formulärfältet. Om inte, läs den från cookien med samma namn och skicka den till Marketo i det här fältet. Obs! Innehållet i POST till ett Marketo-formulär måste vara URL-kodat.
1. Se svaret** Svaret på formulärinlägget blir en HTTP 302-omdirigeringskod. I vissa system visas detta som ett fel. I det här fallet innebär det dock att leadet har skapats eller uppdaterats. Om ett fel uppstår får du felkoden 4xx eller 5xx.

Här är ett [inlägg](https://nation.marketo.com:443/t5/product-blogs/how-to-build-an-external-subscription-center/ba-p/242185) om hur du använder den här tekniken för att avbryta prenumerationen [av Justin Cooperman, Sr. Product Manager]

Publicerad _2014-11-07_ av _Murta_

## Sök efter leads som uppdaterats på ett visst datumintervall

Säg att du vill hitta leads som uppdaterades på specifika datum via [Marketo API](/help/soap-api/soap-api.md). Detta är möjligt med SOAP-API:t [&#x200B; för &#x200B;](/help/soap-api/getmultipleleads.md)getMultipleLeads. Den här metoden returnerar alla leads med en datavärdesändring eller en ny aktivitet i Marketo för det datumintervall som du begär. För `leadSelector` anger du `LastUpdateAtSelector`. Sedan definierar du datumintervallen med `oldestUpdatedAt` och `latestUpdatedAt` tidsgränser. Se exemplet med Request XML nedan, som visar hur du hittar leads som uppdaterades mellan 6 juni 2014 och 7 juni 2011. Obs! Datumintervallet får inte överskrida 30 dagar.

**Exempel på XML för att hitta leads som uppdaterats den**

```xml
<soapenv:Envelope xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
<soapenv:Header>
<mkt:AuthenticationHeader>
<mktowsUserId>REPLACE</mktowsUserId>
<requestSignature>REPLACE</requestSignature>
<requestTimestamp>2014-10-23T12:19:51-07:00</requestTimestamp>
</mkt:AuthenticationHeader>
</soapenv:Header>
<soapenv:Body>
<mkt:paramsGetMultipleLeads>
<leadSelector xsi:type="mkt:LastUpdateAtSelector">
<oldestUpdatedAt>2014-06-06T00:00:00-08:00</oldestUpdatedAt>
<latestUpdatedAt>2014-06-07T00:00:00-08:00</latestUpdatedAt>
</leadSelector>
</mkt:paramsGetMultipleLeads>
</soapenv:Body>
</soapenv:Envelope>
```

Publicerad _2014-09-24_ av _Murta_

## Lägg till dynamiskt innehåll i ett e-postmeddelande

Anta att du skickar ut ett dagligt e-postmeddelande och att du automatiskt vill inkludera dagens datum i e-postmallen. För att göra detta använder du variabler och e-postskript i Marketo.

1. Skapa en token.** Navigera till programmet där du vill använda token. Klicka på Mina token.
1. Dubbelklicka på E-postskript. Ge den här variabeln ett namn. Klicka sedan på Redigera.
1. Klistra in e-postskriptet nedan i det här fönstret. Klicka sedan på Spara.

## Åtkomsthastighetens kalenderobjekt

`set($x = $date.calendar)`

## Formateringsdatum

`set($current_date = $date.format('dd-MM-yyyy', $x.getTime()))`

## Returnerar dagens datum

`$current_date`

1. Referera token i e-postmallen.** Observera namnet på token. Navigera till ditt e-postutkast. Inkludera token.  När e-postmeddelandet skickas fylls värdet i. Mer information finns i dokumentationen för [e-postskriptutvecklare](https://experienceleague.adobe.com/sv/docs/marketo-developer/marketo/email-scripting).

Publicerad _2014-11-22_ av _Murta_

## Säkerhetsanvisning för Flash

Marketo har utfört en grundlig utredning av Bash-sårbarheten, som också kallas [Shellshock (CVE-2014-6271)](https://nvd.nist.gov/view/vuln/detail?vulnId=CVE-2014-6271), och har dragit slutsatsen att vi inte är mottagliga för dessa attacker. Dessutom har vi vidtagit förebyggande åtgärder genom att uppdatera programvaran till den senaste versionen för att säkerställa efterlevnad av [CERT-rekommendationen](https://www.cisa.gov/news-events/alerts/2014/09/25/gnu-bourne-again-shell-bash-shellshock-vulnerability-cve-2014-6271).

Publicerad _2014-09-26_ av _Murta_

## Så här uppdaterar du SOAP API-autentiseringsuppgifter

Det är en god vana att regelbundet uppdatera dina [SOAP API](/help/soap-api/soap-api.md) -autentiseringsuppgifter. För närvarande finns det inget sätt att programmässigt göra detta via Marketo API. Instruktionerna nedan visar hur du uppdaterar dina SOAP API-autentiseringsuppgifter via Marketo användargränssnitt.

1. Gå till avsnittet Administratör och klicka på Webbtjänster.
1. Ange en krypteringsnyckel på minst 10 tecken och klicka på Spara ändringar.

Publicerad _2014-09-29_ av _Murta_

## Rensa din Marketo-databas

**OBS! Detta är ett gästblogginlägg. Josh Hill är Marketo Practice Lead i Perkuto, en reklambyrå. Josh arbetar i skärningspunkten mellan marknadsföring, försäljning och teknik för att leverera intäktsgenererande system. Han skriver om automatiserad marknadsföring och generering av efterfrågan på [MarketingRockstarGuides.com](https://www.marketingrockstarguides.com/).** Att hålla Marketo-databasen ren är en viktig del i att administrera det här kraftfulla systemet. Om era data från Marketo är smutsiga eller CRM-data är smutsiga blir ert jobb som marknadschef mycket svårare eftersom ni förklarar varför kampanjer har skickats till fel personer eller att era rapporter är&quot;riktade&quot;. [En Gartner-studie från 2011 konstaterade](https://www.data.com/export/sites/data/common/assets/pdf/DS_Gartner.pdf) att dålig datakvalitet sänkte arbetets produktivitet med 20 %. Det är 20 % av er tid (8 timmar/vecka eller 1 dag per vecka) som går förlorad eftersom ni måste åtgärda data. Men den här förlusten skiljer sig från de intäkter som gått förlorade eftersom er målinriktning var fel. Här är de fem viktigaste skälen att investera i att hålla era data rena: - Bättre segmentering av leads och kunder kan göras, så att ni kan fokusera ert budskap på rätt personer vid rätt tidpunkt. Att 20 % rabatt bara ska gå till nya kunder, inte till dina bästa kunder, eller hur? - Undvik att skicka e-postmeddelanden flera gånger. Trots alla försiktighetsåtgärder är det möjligt att skicka flera e-postmeddelanden till samma lead, vanligen på grund av att dubblettposter inte sammanfogas. - Rapportera korrekt till din chef. Eftersom marknadschefen har en plats i intäktstabellen måste ni ha korrekta, konsekventa och repeterbara rapporter ... eller så kan ni inte längre vara en intäktsmarknadsförare. - Gör oväntade avtal ogiltiga om du skickar fel marknadsföringsmaterial till viktiga potentiella kunder under försäljningsförhandlingar.

Om databasen inte innehåller den informationen under livscykeln kan det leda till ett eller två avtal. - Ta bort felaktiga eller gamla leads så att ni håller er till prisnivån. Mycket har skrivits om kostnaden för dåliga data. Det mest omedelbara problemet är att för många felaktiga, duplicerade eller gamla leads tvingar dig över prisnivån för Marketo eller Salesforce, vilket kan leda till tusentals avgifter per år. Så, hur håller du databasen ren? Du kan följa dessa principer om datarenhet, men hur når du dit inom Marketo? Jag gör några antaganden om ditt system: - Du har ett vanligt Marketo-system. - Du har ett vanligt Salesforce-konto för lead-kontakt. - Du synkroniserar alla poster mellan system. - Du använder inte målinriktade dubbletter.

Hitta rätt leads till Fix** Låt oss först hitta de leads som är potentiella problem. Med varje kund går jag igenom en serie smarta listor för att fastställa databasens hälsa. Jag föreslår att du gör likadant på månadsbasis. När de smarta listorna fungerar tar det inte mer än 15 minuter per månad. Detta är ett bra sätt att visa på avkastningen på ert arbete och på Marketo. Skapa en tabell för att förstå hur stor påverkan din databas har. Exemplet nedan innehåller de kriterier som jag letar efter, så se till att ta med andra fält som är viktiga för ditt företag. Uppdelning per grupp efter Marketo Only, SFDC Leads och SFDC Contacts.

Använd Automation för att korrigera datavärden: Användningen av automatiseringsregler för att korrigera vanliga felstavningar eller data som saknas går tillbaka flera årtionden tillbaka. Under 1960-talet kan dålig datakvalitet när man skickar direktreklam resultera i att tusentals dollar går förlorade. Idag förstör misstag i datakvaliteten rykten snabbare än en felriktad post. E-postens rykte, språkval och kundupplevelser är viktiga och de spelar större roll eftersom misstag kan bli allmänt virala på några minuter. Spara företagets anseende med automatiserad datarensning. Dessa datahanteringsflöden är en av de första sakerna jag konfigurerar i Marketo. De flesta företag skapar liknande flöden: - Landskorrigering (även om du borde ha följt princip 1 för att inte behöva detta) - Statskorrigering eller Mapper - är ofta till hjälp om du har land och härledd stat. - Antal anställda i intervallet för anställda. - Felaktig lead Source till Good Lead Source - E-postadressen är ogiltig till e-postadressen är bra om e-postmeddelandet ändras. - Översätt gamla fältvärden till ett nytt fält (Fullt till tomt). Det här flödet justerar t.ex. medarbetarintervall baserat på medarbetarnummer.

Datatilläggsverktyg Fylla i tomma fält är avgörande för att databasen ska kunna segmenteras bättre. Leads fyller inte alltid i rätt Bransch eller Function och inte ens Title i ett formulär. Ibland har du äldre data från ett äldre system. Bristen på data innebär att du antingen måste skicka e-postmeddelandet till färre personer eller kanske till fel personer. För att kunna åtgärda detta behöver du verktyg för att lägga till data.

Alternativ 1: Fyll i den själv. Du kan eventuellt interpolera data för att fylla i tomma fält baklänges. Du kanske har en SIC-kod istället för branschnamn eller årsomsättning jämfört med årsomsättning. Marketo kan enkelt automatisera dessa korrigeringar.

Alternativ 2: Hitta en leverantör för datautläggning/datanrikning via LaunchPoint Det finns [flera leverantörer i Launchpoint](https://exchange.adobe.com/apps/browse/ec?product=MRKTO), till exempel NetProspex och ReachForce som kan hjälpa dig att berika dina lead-data. Vissa ber dig om en datablad med dina data som de sedan rensar och skickar tillbaka. Det bättre alternativet är ett automatiskt verktyg i Marketo eller Salesforce som kontrollerar vilka fält du vill ha och sedan skickar tillbaka rätt data. De flesta leverantörer använder [Marketo API eller Webhooks](/help/home.md) för att uppnå detta.

Alternativ 3: Använd Marketo API:er för att uppdatera leads Du kan använda Marketo API:er för att identifiera leads som behöver rensas och sedan uppdatera dem via API:n. [Get Multiple Leads by Filter Type REST API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) är en bra startpunkt för att hämta data från Marketo som matchar vissa villkor. Titta på [Skapa/uppdatera leads REST API](/help/rest-api/leads.md) om du vill uppdatera leads.

Du kan också konfigurera en [Marketo Webkrok](/help/webhooks/webhooks.md) för att meddela ett externt system att en viss händelse har inträffat, till exempel en formulärifyllning. Du kan sedan svara tillbaka med värden för att uppdatera leadet med.

Vad ska du automatisera? Jag gav några förslag i steg 1. Men om vi vill rensa mer av databasen måste vi gå längre än att bara åtgärda datavärden. - Konkurrent Blacklisting: Se till att ni automatiserar insamlingen och svartlistningen av era konkurrenter. Om ni har partners är det fortfarande bra att markera dem på rätt sätt och placera dem i en lista för att eventuellt undertrycka.  - Flera hårda studsar: automatisera definitivt det här flödet. Om ett lead studsar mer än två gånger under en 30-dagarsperiod ska de ställas in på Suspended (Upphävd) eller Invalid (Ogiltig). Gå sedan in en gång i månaden för att kontrollera om problemet var stavfel eller något annat.  - Flera Soft Bounces på 30 dagar: ställ in dessa på Marketing suspenderas=TRUE i 30 dagar.  - Svällning/borttagning av skräppost: var försiktig om produkten innebär att personer använder möjliga svällningsadresser för skräppost. Visa en lista med skräppostsvällningar. Smart List.

**Vad som inte ska automatiseras** Automatisera aldrig borttagningen av leads eftersom det finns för stor risk att ta bort fel poster. Granska i stället en gång i månaden leads som markerats som papperskorg. Men om du vill ta bort brutna leads ska du köra ett sådant här flöde med en 30-dagars väntan.

Caveats: Att använda automatisering är ett bra sätt att spara tid och hålla databasen ren. Automatisering är ett svärd med dubbla kanter, för om du ställer in det fel kan det orsaka kaos på några minuter. Var försiktig när du går igenom dessa processer och håller alla informerade. I allmänhet rekommenderar jag att du inte tar bort några SFDC-kontakter eftersom det är mer sannolikt att de är aktiva affärsmöjligheter, klienter eller ex-klienter. Ekonomi eller juridisk person kan kräva att du behåller vissa poster och kontakter bifogas till dessa poster. Fokusera i stället på kontakter som sticker ut, blir ogiltiga eller inte längre finns där. Nu vet du några tekniker som håller din Marketo-databas ren. Meddela oss om du har kommit på andra sätt att automatisera dessa processer med API:t eller Webhooks!

Anslaget _2014-10-08_ av _Josh_

## Versionsuppdateringar i oktober 2014

### Extern sidförifyllning

Marketo-formulär har ingen inbyggd förifyllningsfunktion när de läses in utanför Marketo landningssida. Vi kan dock fortfarande implementera detta med [Marketo API:er](/help/rest-api/rest-api.md) och [Forms 2.0 JavaScript API](/help/javascript-api/forms-api-reference.md/). Det första steget är att hämta leaddata från Marketo via ett REST-anrop från servern. Om vi inte har ett omedelbart sätt att korsa lead-ID:n eller någon annan unik identifierare från servern, måste vi använda Munchkin cookie, _mkto_trk, för att hämta data från Marketo-servern med hjälp av metoden [Hämta leads efter filtertyp](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET).
För att kunna ringa det här samtalet behöver vi din autentisering och REST-slutpunkter från din instans. När du har autentiserat dig med din Marketo-instans måste vi anropa lead-API:t på `https://<host>/rest/v1/leads.json`. Sedan måste vi skapa en frågesträng för att filtrera på Marketo-cookien som denna `?filterType=cookie&filterValues=`. Du måste hämta det specifika värdet från nyckeln _mkto_trk som klienten skickar till servern. Obs! Cookie-värdet _mkto_trk innehåller ett et-tecken och måste vara URL-kodat till `%26` för att accepteras på rätt sätt av Marketo-slutpunkten. Som standard returnerar lead-API fyra fält: `id`, `email`, `firstName` och `updatedAt`. Om du vill ange en specifik uppsättning fält måste du ta med en `fields`-frågeparameter, med fältnamn avgränsade med kommatecken som: `&fields=email,firstName,lastName,company`. I slutändan kommer vårt samtal att se ut så här:

`https://<host>/rest/v1/leads.json?filterType=cookie&filterValues=<cookie>&fields=email,firstName,lastName,company&access_token=<token>`

När vi gör det här anropet returneras ett JSON-objekt som ser ut så här:

```json
{
    "requestId":"e42b#14272d07d78",
    "success":true,
    "result":[
        {
        "id":50,
        "firstName":"Kenny",
 "lastName":"Elkington",
        "email":"<mkto@example.com>",
 "company":"Marketo Inc."
        }]
};
```

Nu när vi har våra huvuduppgifter kan vi mappa dessa till ett Marketo-formulär med metoderna whenReady och vals i JavaScript. Först måste vi ange leadresultaten som en variabel på vår sida:

```javascript
<script>
//print your JSON object dynamically as the mktoLead variable
var mktoLead = {
    "requestId":"e42b#14272d07d78",
    "success":true,
    "result":[
        {
        "id":50,
        "firstName":"Kenny",
  "lastName":"Elkington",
        "email":"mkto@example.com",
  "company":"Marketo Inc."
        }]
}
</script>
```

Nu när vi har våra resultat på sidan kan vi mappa dem till våra formulärfält:

```javascript
<script>
MktoForms2.whenReady( function(form) {
 //set the first result as local variable
 var mktoLeadFields = mktoLead.result[0];
    //map your results from REST call to the corresponding field name on the form
 var prefillFields = {
   "Email" : mktoLeadFields.email,
   "FirstName" : mktoLeadFields.firstName,
   "LastName" : mktoLeadFields.lastName,
   "Company" : mktoLeadFields.company
   };
 //pass our prefillFields objects into the form.vals method to fill our fields
 form.vals(prefillFields);
 }
 );
</script>
```

Publicerad _2014-10-24_ av _Kenny_

## Ersätt e-post med HTML

I det här inlägget visas hur du tar bort den HTML som har genererats av Marketo för ett e-postmeddelande. Du kan sedan använda din egen HTML utan att Marketo formaterar om den.

1. Navigera till e-postmeddelandet och klicka sedan på Redigera utkast.
1. Klicka på E-poståtgärder, klicka på HTML-verktyg och sedan på Ersätt HTML.
1. Ersätt koden i den här rutan med din HTML. Klicka sedan på Spara.

Publicerad _2014-10-28_ av _Murta_

## Hämta en besökares cookie-ID och sedan fråga om associerade lead-data

Med hjälp av REST-slutpunkten [Hämta flera leads efter filtertyp](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) kan du hämta lead-data baserat på en användares cookie-id. Du kan till exempel använda den här metoden för att fylla i ett formulär i förväg på en landningssida som inte är Marketo. I det här inlägget visas hur du fångar användarens cookie-värde under ett webbsidesbesök, frågar [Hämta REST API:t för flera leads](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) med detta cookie-id och sedan returnerar användarens lead-data. Först behöver vi värdet för användarens Munchkin-cookie, &#39;_mkto_trk&#39;. Här är ett exempel på en JavaScript-funktion som du kan använda för att hämta cookie-värdet. Se [den här StackOverflow-sidan](https://stackoverflow.com/questions/10730362/get-cookie-by-name) för mer information om den här metoden. Jag rekommenderar att du anger en fördröjning på 500 ms efter sidans load-händelse innan du anropar den här funktionen. Detta ger Munchkin tid att läsa in och cookie-lagra användaren.

```javascript
//Function to read value of a cookie
function readCookie(name) {
    var cookiename = name + "=";
    var ca = document.cookie.split(';');
    for(var i=0;i < ca.length;i++) {
        var c = ca[i];
        while (c.charAt(0)==' ') c = c.substring(1,c.length);
        if (c.indexOf(cookiename) == 0) return c.substring(cookiename.length,c.length);
    }
    return null;
}

//Call readCookie function to get value of user's Marketo cookie
var value = readCookie('_mkto_trk');
```

Därefter skickar du värdet för cookien _mkto_trk till servern. Om du vill hämta lead-data från servern gör du ett anrop till REST API:t [Get Multiple Leads](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) med det här cookie-värdet. Du behöver dina Authentication- och REST-slutpunkter från din instans. Samtalet ska struktureras enligt följande:

Obs! Cookie-värdet `_mkto_trk` innehåller ett et-tecken och måste vara URL-kodad till `%26` för att accepteras korrekt av Marketo-slutpunkten.

```java
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/leads.json"
# Replace with your access token
auth_token =  "?access_token=" + "cde42eff-aca0-48cf-a1ac-576ffec65a84:ab"
# Replace with filter type and values
filter_type_and_values = "&filterType=cookies&filterValues=id:AAA-BBB-CCC%26token:_mch-marketo.com-1418418733122-51548&fields=cookies,email"
request_url = marketo_instance + endpoint + auth_token + filter_type_and_values

# Make request
response = RestClient.get request_url

# Returns Marketo API response
puts response
```

Exemplet ovan returnerar e-postmeddelandet och alla cookies som är kopplade till användaren. Du kan sedan använda dessa data för att anpassa den efterföljande sidan som användaren besöker.

`{"requestId":"aa00#14a405aa786","result":[{"id":583,"email":"<testaccount@gmail.com>","cookies":"_mch-marketo.com-1418418733122-51548"}],"success":true}`

Publicerad _2014-10-30_ av _Murta_

## När behöver ni en utvecklare som kan hjälpa er med automatiserad marknadsföring

OBS! Detta är ett gästblogginlägg. Josh Hill är Marketo Practice Lead i Perkuto, en reklambyrå. Josh arbetar i skärningspunkten mellan marknadsföring, försäljning och teknik för att leverera intäktsgenererande system. Han skriver om automatiserad marknadsföring och generering av efterfrågan på [MarketingRockstarGuides.com](https://www.marketingrockstarguides.com/).

Plattformar för automatiserad marknadsföring är otroligt kraftfulla från sin plats och i händerna på en erfaren operatör. Plattformar gör det per definition möjligt att använda tilläggsprogram för att få systemet att göra ännu mer fantastiska saker för ditt team. Du kanske tror att Marketo logikmotor klarar så mycket (och är det), men det finns vissa begränsningar. Marketo kan inte göra allt för dig, inte heller göra det.

Det finns andra verktyg där ute som utför sin funktion bättre än vad Marketo kan bygga den. Marketo plattform är mycket öppen, vilket gör att ekosystemet [LaunchPoint för program](https://exchange.adobe.com/apps/browse/ec?product=MRKTO) kan finnas. Du kan också använda den här öppenheten för att utöka möjligheterna på er webbplats och Marketo så att de passar era affärsbehov. Det fina med en plattform som Marketo är att den vanliga marknadsföraren kan bygga sidor, e-post och routningslogik utan att vara en fullfjädrad programmerare.
Idag måste en marknadsförare förstå logiken, men den verkliga programmeringen är bäst för experterna. Hur vet du när du behöver ringa en utvecklare? Jag har några grundläggande regler, eller heuristik, för att bestämma när en programmerare ska bli inblandad: - När Marketo inte har ett filter, en utlösare eller en funktion som behövs kan det ofta göras med vissa JavaScript eller jQuery. - Kommer detta att vara för komplext för Marketo? - Kan Marketo ens göra detta? - Är det inte så enkelt att anpassa en webbplats? - Måste Marketo kommunicera med en webbplats eller någon annan databas? - Låter det som något en dator kan göra, men Marketo har ingen funktion för det? Kom ihåg att Marketo kanske inte erbjuder en körklar funktion, men att den ansluter till många tredjepartsintegreringar samt anpassade anslutningar.

Ta en titt på några av dessa kategorier på marknadsplatsen [LaunchPoint](https://exchange.adobe.com/apps/browse/ec?product=MRKTO): - [Analysverktyg](https://exchange.adobe.com/apps/browse/ec?product=MRKTO) - [Datatilläggande](https://exchange.adobe.com/apps/browse/ec?product=MRKTO) - [Innehållshanteringssystem](https://exchange.adobe.com/apps/browse/ec?product=MRKTO) Vissa tredjepartsprogram erbjuder intuitiva kontrollpaneler och konfigurationsverktyg direkt på plattformen (GoToWebinar). Det är&quot;inbyggda&quot; integreringar där det mesta du behöver göra är att konfigurera inloggningen och sedan använda den i Marketo. Andra tillägg kräver dock att det mer komplexa API:t som måste programmeras mer direkt används.

**Marketo integreringsalternativ** - LaunchPoint-integrering - vanligtvis en inloggning eller enkla inställningar. - API-integrering - kräver konfiguration av API och programmering: (1) [REST API](/help/rest-api/rest-api.md) (2) [&#128279;](/help/soap-api/soap-api.md) (3) [Webkrosintegrering](/help/webhooks/webhooks.md) - kräver konfiguration av specialkod, men ganska enkelt. (4) [E-postskriptning](./email-scripting.md) (hastighet) - JavaScript och jQuery: (1) [Forms 2.0](/help/javascript-api/forms-api-reference.md) (2) [Lead Tracking (Munchkin)](/help/javascript-api/lead-tracking.md) (3) [RTP JS](/help/javascript-api/web-personalization.md) Här är några exempel på hur du kan använda en utvecklare för att utöka funktionerna i Marketo-plattformen. Har du några av dessa användningsfall? I så fall kan det vara dags att tala med en utvecklare. [Besök tjänstpartneravsnittet i LaunchPoint](https://exchange.adobe.com/apps/browse/ec?product=MRKTO).

Anslaget _2014-11-06_ av _Josh_

## Integrera Slack med Marketo

[Slack är en samarbetsplattform](https://slack.com/) för företag. Om ditt team arbetar på Slack kan du enkelt ta in Marketo-meddelanden i arbetsflödet. I det här inlägget visas hur du lägger till ett meddelande i din chattlogg när en viss lead-aktivitet inträffar i Marketo. Exempel på möjliga användningsområden är att meddela hela teamet om en formulärfyllning, ett besök på en prissida eller ett lead som inte har kontaktats på 30 dagar. Skärmbilden nedan visar hur Marketo-meddelandet kommer att se ut i Slack efter att du har följt stegen i den här hjälpartikeln.

1. Logga in på Slack. Klicka på Integreringar i Slack
1. Klicka på knappen Lägg till för inkommande webbböcker
1. Välj kanal för Marketo-meddelandet. Klicka sedan på Lägg till inkommande webbkrokintegrering.
1. Kopiera och klistra in webkroks-URL (som behövs för steg åtta)
1. Välj ett namn för meddelandet
1. Logga in på Marketo. Gå till Admin. Klicka på Webhooks.
1. Klicka på Ny webbkrok
1. Ange ett namn för webkroken. Ange webkroks-URL:en från steg fyra. Ange Posta som begärandetyp. Ange en nyttolastmall.

Här är nyttolastmallen från skärmbilden. Den använder tokens för förnamn, företag och e-postadresser på leadnivå.

`payload={"text": "DEVELOPER SITE ALERT: {{lead.First Name:default=edit me}} {{lead.Company=edit me}}, {{lead.Email Address:default=no email address}}" }`

1. Konfigurera utlösarkampanj i Marketo. Flow Step är att ringa Webkrok till Slack. Smart List är ett webbsidesbesök.
1. Verifiera att det fungerar.

Läs [utvecklardokumentationen](./webhooks/webhooks.md) om du vill ha mer information om webbplatser i Marketo.

Publicerad _2014-11-10_ av _Murta_

## Integrera Litmus med Marketo

[Litmus är en tjänst](https://www.litmus.com/) för att testa e-post i olika webbläsare och e-postklienter. Litmus tillhandahåller också analyser kring e-post, inklusive klickningar, öppningar och borttagningar. Här får du se hur du integrerar Marketo med Litmus.

1. När du konfigurerar ditt e-postprogram i Marketo klickar du på Mina token på programkontrollpanelen
1. Lägg till en e-postskripttoken genom att dra den till den mittersta panelen.
1. Namnge token och klicka på Klicka för att redigera
1. Till höger, under Standardobjekt, expanderar du kategorin Lead. Leta reda på fältet &quot;E-postadress&quot; och markera kryssrutan. I det tomma skriptutrymmet till vänster om samma sida klistrar du in spårningskoden från Litmus. Ersätt alla instanser av `{{lead.Email Address}}` med `${lead.Email}` i Litmus-koden. Klicka på Spara för att stänga ljuslådefönstret och klicka på Spara igen på tokensidan.
1. Observera namnet på token `{{my.LitmusToken}}`. Öppna det e-postmeddelande som du vill spåra. Placera din nya skripttoken längst ned i ditt e-postmeddelande. Du kan också lägga till standardinformation som matchar Litmus-versionen `{{my.LitmusToken:default=editme}}`.

När e-postmeddelandet skickas placeras skriptet i e-postmeddelandet av Marketo.

Publicerad _2014-11-18_ av _Murta_

## Ange en bild när en Marketo-landningssida delas på Facebook

Säg att du vill att en bild ska visas automatiskt när du delar en Marketo-landningssida på Facebook. Du kanske också vill att den här bilden inte ska finnas på Marketo landningssida, utan bara på Facebook. Du kan göra detta genom att lägga till en metatagg med öppen graf på Marketo landningssida. Stegen för att göra detta är nedan.

1. Välj landningssida. Klicka sedan på Redigera utkast.
1. Klicka på Redigera sidmetataggar.
1. Lägg till metadata för öppna diagram i avsnittet OG-taggar för Facebook. Klicka sedan på Spara. Här är formatet: `<meta property="og:image" content="http://example.com/example.jpg"/>`

[Mer information finns i Facebooks utvecklardokumentation](https://developers.facebook.com/docs/sharing/best-practices) om metataggar med öppen graf.

Publicerad _2014-11-17_ av _Murta_

## Omdirigeringssida baserad på referent

Säg att du vill förhindra direkttrafik till en landningssida för Marketo. Föreställ dig att den här sidan har hämtningsbart innehåll som en PDF som du vill att en användare först ska fylla i ett formulär innan han eller hon får det. Du kan lösa detta genom att kontrollera om användaren kommer från en viss sida. I det här fallet är det sidan där användaren måste fylla i ett formulär. Om användaren inte kommer från den sidan kan du dirigera om användaren till den ifyllda formulärsidan. För att uppnå detta måste du kontrollera om referenssidan till landningssidan med innehåll är formulärifyllningssidan.
Ersätt båda förekomsterna av `http://example.com/PageWithForm` i fragmentet nedan med en länk till sidan som du vill att användaren ska komma från. Det här kan vara formulärifyllningssidan.**

```javascript
<script>
window.onload = function() {
  if (document.referrer !== "http://example.com/PageWithForm") {
    document.location.href = "http://example.com/PageWithForm";
  };
 };
</script>
```

Inkludera det anpassade JavaScript-fragmentet före sluttaggen på Marketo landningssida med innehåll.** Om användaren inte skickas till landningssidan med innehåll från formulärifyllningssidan, kommer användaren nu att omdirigeras till sidan för ifyllnad av formulär.

Publicerad _2014-11-18_ av _Murta_

## Integrera Trello med Marketo

Trello är ett [populärt webbaserat projekthanteringsprogram](https://trello.com/). Om ditt team använder Trello kan du enkelt ta in Marketo-meddelanden i arbetsflödet. I det här inlägget visas hur du lägger till ett kort med ett Marketo-meddelande på din Trello-anslagstavla. Det här kortet läggs till när en viss lead-aktivitet inträffar i Marketo. Exempel på möjliga användningsområden är att meddela hela teamet om en formulärfyllning, ett besök på en prissida eller ett lead som inte har kontaktats på 30 dagar.

1. Logga in i Trello. Navigera till den Trello-panel där Marketo-meddelanden ska läggas till. Klicka på Lägg till en lista och ge den ett namn.
1. Klicka på Visa sidofält. Klicka på Inställningar för e-post till rityta. Skriv e-postadressen i rutan&quot;Din e-postadress för den här anslagstavlan&quot;. Det här e-postmeddelandet kommer att användas i steg sex. Välj vilken lista du vill lägga till Marketo-meddelandet i.
1. Logga in på Marketo. Klicka på den nya smarta kampanjen. Ange ett namn och klicka sedan på Spara.
1. Navigera till Smart List. Välj en utlösare för denna smarta kampanj. I det här exemplet använder vi utlösaren Formulärfyllning. Dra Fyller ut formulärutlösaren till panelen Mitten. Välj det formulär som du vill ska utlösa det här meddelandet.
1. Skapa ett mejl. Klicka på Ny. Klicka på Lokal resurs. Klicka på Ny e-postadress. Namnge e-postmeddelandet. Klicka sedan på Skapa.
1. Klicka på Redigera utkast för det e-postmeddelande du skapade i steg fem. Dra de relevanta tokens som du vill visa på ditt Trello-kort. Ämnesraden i Marketo-e-postmeddelandet visas i Trello-kortets titel, och brödtexten i Marketo-e-postmeddelandet visas i beskrivningen av Trello-kortet. Du kan till exempel använda LEAD-VARNING: `{{lead.First Name:default=edit me}}` `{{lead.Last Name:default=edit me}}` om du vill att leadets för- och efternamn ska vara i Trello-kortets titel. Godkänn sedan e-postmeddelandet.
1. Navigera till Smart Campaign. Klicka på Flöde. Dra Skicka avisering till panelen Mitten. Välj den e-postadress som skapades nyss. Välj &quot;Skicka till&quot; som Ingen. Välj&quot;Till andra e-postmeddelanden&quot; som Trello-e-postmeddelande från steg två.
1. Klicka på Schemalägg på den översta menyn. Klicka på Aktivera. Klicka på Bekräfta.
1. Testa integreringen. Ett kort med leadets för- och efternamn i titeln visas i Trello-tavlan. Mer information finns i [Trellos dokumentation](https://support.atlassian.com/trello/).

Publicerad _2014-11-18_ av _Murta_

## Sök efter leads efter anpassat fältvärde

Säg att du vill få leads från Marketo API som matchar vissa aktivitets- eller inaktivitetskriterier. Du kanske vill hitta ett leads vars poäng inte har ändrats under de senaste 30 dagarna. Genom att följa stegen i det här inlägget kan du hämta den här listan med leads. För att göra detta skapar vi en smart kampanj i Marketo som identifierar leads vars poäng inte har ändrats under de senaste 30 dagarna och lagrar sedan ett värde på dessa leads för att identifiera dem. Därefter frågar vi API:t med det här värdet.

1. Skapa ett nytt anpassat fält med namnet customLeadStatus** Logga in på Marketo och gå till Admin-panelen. Klicka på Fälthantering. Klicka på Nytt anpassat fält.  Namnge fältet. Klicka sedan på Skapa.
1. Skapa en smart kampanj med en smart lista som söker efter leads som inte har uppdaterats på 30 dagar.** Klicka på Ny smart kampanj. Kalla den nya smarta kampanjen. Dra inte bakgrundsmusik ändrades från den högra panelen till panelen i mitten.
1. Lägg till ett flödessteg i den smarta kampanjen från steg 3 för att uppdatera fältet customLeadStatus med ett nytt värde.** Dra Ändra datavärde från den högra panelen till den mittersta panelen.
1. Uppdatera Smart Campaign så att leads kan köras flera gånger.** Klicka på Schema. Klicka sedan på Redigera.  Markera varje gång. Klicka sedan på Spara. Kampanjen kommer nu att börja köras.
1. Fråga [Hämta flera leads efter filtertyp REST API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET). Ange parametrarna filterType=customLeadStatus &amp; filterValue=needsEnrichment.**

Detta är en exempelbegäran som returnerar dessa data.

`<https://AAA-BBB-CCC.mktorest.com/rest/v1/leads.json?access_token=><yourAccessToken>&filterType=customLeadStatus&filterValues=needsEnrichment`

Ett lyckat API-anrop returnerar JSON-data med leads vars customLeadStatus-fält matchar värdet för needsEnrichment. Mer information finns i [Hämta flera leads per filtertyp REST API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET).

Publicerad _2014-11-22_ av _Murta_

## Synkronisering av affärsmöjligheter via SOAP API

I det här inlägget beskrivs hur du infogar säljprojekt i Marketo via SOAP API och associerar dem med företag och leads. Den börjar med en förklaring av hur den här processen fungerar och innehåller sedan kodexempel för varje scenario.

**Tabellstrukturdiagram** Först och främst beskriver diagrammet nedan tabellstrukturen. Id:t genereras automatiskt när en post skapas (sekventiellt nummer). Fältet i fet stil är obligatoriskt: endast rollen säljprojektsperson har obligatoriska fält. Fälten inom parenteser är valfria och det gör även kopplingarna med en prickad linje.

**Grundläggande infogning av affärsmöjlighet** Du infogar först affärsmöjligheten och sedan säljprojektsrollen, som länkar affärsmöjligheten till lead(n). I det här exemplet använder du lead-ID och säljprojekt-ID för att ange säljprojektspersonens roll. Du får möjlighet-ID i SOAP-svaret när du skapar säljprojektet. Lead-ID:t visas på alla leads i Marketo Lead-databasen.

**Företagslänk** I de flesta fall vill du länka en möjlighet till ett företag, förutom en individ. I Marketo kan du inte komma åt företagsposterna via SOAP API, bara Lead-posterna (Lead-posterna innehåller företagsfälten). Det går fortfarande att länka affärsmöjligheter till ett företag genom att lägga till en unik företagsidentifierare för varje lead och använda detta ID i affärsmöjligheten. Steg 1 är att skapa ett&quot;företags-ID&quot;-fält på lead-posten och fylla i det med en unik identifierare, vanligtvis från ett back end-system. Steg 2 är att skapa ett&quot;företags-ID&quot;-fält i säljprojektet: du måste be Marketo Support eller Consulting om att skapa ett sådant fält åt dig. Fyll sedan i det här fältet när du skapar säljprojekt som kopplar säljprojektet till företaget. Detta är särskilt viktigt om du använder Marketo Revenue Cycle Analytics och vill använda säljprojektsanalysen, som är beroende av företagsinformationen.

**Använda externa identifierare** I många fall kan du ha egna unika identifierare när du integrerar med ett bakomliggande system. Det går att använda dessa unika identifierare i Marketo med hjälp av sekundärnycklar. För Leads använder du vanligtvis ID:t (Foreign System Person Id, FSPID), som används i stället för Marketo ID:t eller e-postadressen som unik identifierare. FSPID är ett dolt systemfält som inte är synligt i Marketo. Om du inte redan gör det är det nödvändigt att synkronisera säljprojekt för att även spara FSPID i ett anpassat fält, till exempel &quot;Utländskt ID&quot; (du kan namnge fältet vad som helst). Du kan skapa det här fältet själv som Marketo-administratör. För säljprojekt har du Marketo Support som skapar ett annat anpassat fält för säljprojekt, t.ex. &quot;Utländskt ID&quot; (du kan namnge det vad som helst). Fyll sedan i det här fältet när du infogar säljprojekt. När du skapar rollen säljprojektsperson använder du båda sekundärnycklarna för att ange länken mellan leads och säljprojekt i stället för att använda Marketo-ID:n. Du kan också använda de externa nycklarna för att uppdatera säljprojektsleads. För närvarande går det inte att lägga till en extern nyckel till posterna för säljprojektsrollen, så du måste använda det automatiskt genererade Marketo-ID:t för detta (som du får i SOAP-svaret när du har skapat säljprojektsrollen).

**Exempel på kod** steg: 1. Infoga/uppdatera lead med sekundärnyckel och företags-ID 1. Infoga affärsmöjlighet med sekundärnyckel 1. Infoga person för affärsmöjlighet med sekundärnycklar 1. SOAP-begäran - Uppdaterar befintlig lead med sekundärnyckel och företags-ID Detta uppdaterar en befintlig lead (Marketo-ID &quot;6&quot;) med sekundärnyckeln &quot;12346&quot; och konto-/företags-ID &quot;C123&quot;. Vi sparar också sekundärnyckeln i ett anpassat fält eftersom vi behöver den för rollen säljprojektsperson. Det är valfritt att använda en sekundärnyckel: du kan också använda Marketo-id:t för att länka denna lead till säljprojektet. Det är också valfritt att använda företags-ID, men det krävs om du vill använda analysen av säljprojektspåverkan i RCA. Begäran:

```xml
<soapenv:Envelope xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
   <soapenv:Header>
      <mkt:AuthenticationHeader>
         <mktowsUserId>\*\*\*</mktowsUserId>
         <requestSignature>\*\*\*</requestSignature>
         <requestTimestamp>2014-11-20T15:18:30-07:00</requestTimestamp>
      </mkt:AuthenticationHeader>
   </soapenv:Header>
   <soapenv:Body>
      <mkt:paramsSyncLead>
         <leadRecord>
            <Id>6</Id>
            <ForeignSysPersonId>12346</ForeignSysPersonId>
            <leadAttributeList>
               <attribute>
                  <attrName>FSPID</attrName>
                  <attrValue>12346</attrValue>
               </attribute>
               <attribute>
                  <attrName>cAccountFSID</attrName>
                  <attrValue>C123</attrValue>
               </attribute>
            </leadAttributeList>
         </leadRecord>
         <returnLead>false</returnLead>
      </mkt:paramsSyncLead>
   </soapenv:Body>
</soapenv:Envelope>
```

Svar:

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:xsi="<http://www.w3.org/2001/XMLSchema-instance>" xmlns:ns1="<http://www.marketo.com/mktows/">
   <SOAP-ENV:Body>
      <ns1:successSyncLead>
         <result>
            <leadId>6</leadId>
            <syncStatus>
               <leadId>6</leadId>
               <status>UPDATED</status>
               <error xsi:nil="true"/>
            </syncStatus>
            <leadRecord xsi:nil="true"/>
         </result>
      </ns1:successSyncLead>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

SOAP Request - Opportunity Creation (Skapa säljprojekt) I det här fallet har två anpassade fält skapats i säljprojektstabellen: - `opportunityId` → innehåller det unika säljprojekts-ID:t - `cAccountFSID` → innehåller företagsreferensen i stället för att ange ditt eget affärsmöjlighets-ID. Du kan också använda det säljprojekt-ID som genererats av Marketo. I så fall utelämnar du noden Extern nyckel. Företagsassociationen är också valfri, men krävs om du vill använda säljprojektsanalys i RCA. Begäran:

```xml
<soapenv:Envelope xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
   <soapenv:Header>
      <mkt:AuthenticationHeader>
         <mktowsUserId>\*\*\*</mktowsUserId>
         <requestSignature>\*\*\*</requestSignature>
         <requestTimestamp>2014-11-20T15:03:28-07:00</requestTimestamp>
      </mkt:AuthenticationHeader>
   </soapenv:Header>
   <soapenv:Body>
      <mkt:paramsSyncMObjects>
         <mObjectList>
            <!--Zero or more repetitions:-->
            <mObject>
               <type>Opportunity</type>
               <externalKey>
                  <name>opportunityId</name>
                  <value>Opportunity_4</value>
               </externalKey>
               <attribList>
                  <attrib>
                     <name>opportunityId</name>
                     <value>Opportunity_4</value>
                  </attrib>
                  <attrib>
                     <name>Name</name>
                     <value>Opportunity 4 for ACME</value>
                  </attrib>
                  <attrib>
                     <name>IsClosed</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>IsWon</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>Amount</name>
                     <value>501.00</value>
                  </attrib>
                  <attrib>
                     <name>CloseDate</name>
                     <value>2014-10-24</value>
                  </attrib>
                  <attrib>
                     <name>ExpectedRevenue</name>
                     <value>501</value>
                  </attrib>
                  <attrib>
                     <name>Probability</name>
                     <value>100</value>
                  </attrib>
               </attribList>
               <associationList>
                  <mObjAssociation>
                     <mObjType>Company</mObjType>
                     <externalKey>
                        <name>cAccountFSID</name>
                        <value>C123</value>
                     </externalKey>
                  </mObjAssociation>
               </associationList>
            </mObject>
         </mObjectList>
         <operation>UPSERT</operation>
      </mkt:paramsSyncMObjects>
   </soapenv:Body>
</soapenv:Envelope>
```

Svar:

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
   <SOAP-ENV:Body>
      <ns1:successSyncMObjects>
         <result>
            <mObjStatusList>
               <mObjStatus>
                  <id>40</id>
                  <externalKey>
                     <name>opportunityId</name>
                     <value>Opportunity_4</value>
                  </externalKey>
                  <status>CREATED</status>
               </mObjStatus>
            </mObjStatusList>
         </result>
      </ns1:successSyncMObjects>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

SOAP Request - Opportunity Person Role Denna begäran länkar leadet till säljprojektet. Du kan ange flera länkar i en enda SOAP-begäran (det här exemplet länkar säljprojektet till endast en lead). Detta använder de externa nycklarna för att ange länken, men i kommentarerna visas även hur de faktiska ID:na ska användas (i det här fallet 6 för lead-ID:t och 40 för säljprojekt-ID:t). Fälten IsPrimary och Role är valfria. Begäran:

```xml
<soapenv:Envelope xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
   <soapenv:Header>
      <mkt:AuthenticationHeader>
         <mktowsUserId>\*\*\*</mktowsUserId>
         <requestSignature>\*\*\*</requestSignature>
         <requestTimestamp>2014-11-20T15:18:30-07:00</requestTimestamp>
      </mkt:AuthenticationHeader>
   </soapenv:Header>
   <soapenv:Body>
      <mkt:paramsSyncMObjects>
         <mObjectList>
            <!--Zero or more repetitions:-->
            <mObject>
               <type>OpportunityPersonRole</type>
               <attribList>
                  <attrib>
                     <name>IsPrimary</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>Role</name>
                     <value>Marketing Manager</value>
                  </attrib>
               </attribList>
               <associationList>
                  <mObjAssociation>
                     <mObjType>Lead</mObjType>
                     <!--id>6</id-->
                     <externalKey>
                      <name>FSPID</name>
                      <value>12346</value>
                     </externalKey>
                  </mObjAssociation>
                  <mObjAssociation>
                     <mObjType>Opportunity</mObjType>
                     <!--id>40</id-->
                     <externalKey>
                      <name>opportunityId</name>
                      <value>Opportunity_4</value>
                    </externalKey>
                  </mObjAssociation>
               </associationList>
            </mObject>
         </mObjectList>
         <operation>UPSERT</operation>
      </mkt:paramsSyncMObjects>
   </soapenv:Body>
</soapenv:Envelope>
```

Svar:

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
   <SOAP-ENV:Body>
      <ns1:successSyncMObjects>
         <result>
            <mObjStatusList>
               <mObjStatus>
                  <id>11</id>
                  <status>CREATED</status>
               </mObjStatus>
            </mObjStatusList>
         </result>
      </ns1:successSyncMObjects>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

**Alternativ metod (gör steg 2 och 3 i ett samtal)** Du kan först infoga affärsmöjligheten och sedan rollen säljprojektsperson, men du kan även göra detta i ett SOAP-samtal. Du måste dock använda sekundärnyckeln för säljprojekt (du kan inte använda det automatiskt genererade affärsmöjlighets-ID:t i säljprojektsrollen eftersom säljprojektet inte har skapats än). Du kan förstås även länka flera leads till denna möjlighet i samma API-anrop (det här exemplet länkar säljprojektet till endast en lead). Begäran:

```xml
<soapenv:Envelope xmlns:soapenv="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:mkt="<http://www.marketo.com/mktows/">
   <soapenv:Header>
      <mkt:AuthenticationHeader>
         <mktowsUserId>\*\*\*</mktowsUserId>
         <requestSignature>\*\*\*</requestSignature>
         <requestTimestamp>2014-11-20T15:44:08-07:00</requestTimestamp>
      </mkt:AuthenticationHeader>
   </soapenv:Header>
   <soapenv:Body>
      <mkt:paramsSyncMObjects>
         <mObjectList>
            <!--Zero or more repetitions:-->
            <mObject>
               <type>Opportunity</type>
               <externalKey>
                  <name>opportunityId</name>
                  <value>Opportunity_5</value>
               </externalKey>
               <attribList>
                  <attrib>
                     <name>opportunityId</name>
                     <value>Opportunity_5</value>
                  </attrib>
                  <attrib>
                     <name>Name</name>
                     <value>Opportunity 5 for ACME</value>
                  </attrib>
                  <attrib>
                     <name>IsClosed</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>IsWon</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>Amount</name>
                     <value>1500</value>
                  </attrib>
                  <attrib>
                     <name>CloseDate</name>
                     <value>2014-10-24</value>
                  </attrib>
                  <attrib>
                     <name>ExpectedRevenue</name>
                     <value>1500</value>
                  </attrib>
                  <attrib>
                     <name>Probability</name>
                     <value>100</value>
                  </attrib>
               </attribList>
               <associationList>
                  <mObjAssociation>
                     <mObjType>Company</mObjType>
                     <externalKey>
                        <name>cAccountFSID</name>
                        <value>C123</value>
                     </externalKey>
                  </mObjAssociation>
               </associationList>
            </mObject>
             <mObject>
               <type>OpportunityPersonRole</type>
               <attribList>
                  <attrib>
                     <name>IsPrimary</name>
                     <value>1</value>
                  </attrib>
                  <attrib>
                     <name>Role</name>
                     <value>Marketing Manager</value>
                  </attrib>
               </attribList>
               <associationList>
                  <mObjAssociation>
                     <mObjType>Lead</mObjType>
                     <!--id>6</id-->
                     <externalKey>
                      <name>FSPID</name>
                      <value>12346</value>
                     </externalKey>
                  </mObjAssociation>
                  <mObjAssociation>
                     <mObjType>Opportunity</mObjType>
                     <externalKey>
                      <name>opportunityId</name>
                      <value>Opportunity_5</value>
                    </externalKey>
                  </mObjAssociation>
               </associationList>
            </mObject>
         </mObjectList>
         <operation>UPSERT</operation>
      </mkt:paramsSyncMObjects>
   </soapenv:Body>
</soapenv:Envelope>
```

Svar:

```xml
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
   <SOAP-ENV:Body>
      <ns1:successSyncMObjects>
         <result>
            <mObjStatusList>
               <mObjStatus>
                  <id>41</id>
                  <externalKey>
                     <name>opportunityId</name>
                     <value>Opportunity_5</value>
                  </externalKey>
                  <status>CREATED</status>
               </mObjStatus>
               <mObjStatus>
                  <id>12</id>
                  <status>CREATED</status>
               </mObjStatus>
            </mObjStatusList>
         </result>
      </ns1:successSyncMObjects>
   </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

Publicerad _2014-11-26_ av _Jep_

## REST API-begäranden för flera trådar

Om du vill förbättra prestandan när du anropar Marketo API kan du göra samtidiga begäranden. Med den här metoden kan du hämta mer data på en kortare tid. När du gör en API-begäran är överföringstiden på tråden en del av tiden mellan klienten och servern. Så om vi kan minska överföringstiden på tråden för förfrågningar i aggregat, förbättrar vi prestandan. I exempelkoden nedan visas hur du gör detta i Ruby. Den använder EventMachine, som är ett [händelsebearbetningsbibliotek som används för att göra flertrådiga begäranden](https://github.com/igrigorik/em-http-request/wiki/Parallel-Requests). Exemplet nedan anropar [API:t för lead-aktiviteter](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET) och gör två samtidiga begäranden. På så sätt elimineras överföringstiden från klienten till servern för den andra begäran. Den gör detta genom att ta med den andra begäran samtidigt som den första. API-svaren skrivs till en textfil.

```java
require 'em-http-request'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/activities.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
# Specify datetime needed as nextPageToken
since_date_time = ["&nextPageToken=A5YMOYZQBOGD2OSYYBYDAQGEMGLBDGDANAABQGRAQWAAKKID", "&nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ"]
# Specify activities needed
activity_type_ids = "&activityTypeIds=1&activityTypeIds=12"
requesturl_a = marketo_instance + endpoint + auth_token + since_date_time.at(0) + activity_type_ids
requesturl_b = marketo_instance + endpoint + auth_token + since_date_time.at(1) + activity_type_ids

# Make request
EventMachine.run do
  http1 = EventMachine::HttpRequest.new(requesturl_a).get
  http2 = EventMachine::HttpRequest.new(requesturl_b).get

# When API response is received, write response to a text file
  http1.callback {
  File.open('response1.txt', 'w') do |t|
  t.puts http1.response
  end }

  http2.callback {
  File.open('response2.txt', 'w') do |t|
  t.puts http2.response
  end }
end
```

Publicerad _2014-12-03_ av _Murta_

## API-begäranden om prestandajustering

I det här inlägget diskuteras strategier för att förbättra prestandan när data begärs från Marketo API. Du måste dock väga fördelarna med de här strategierna mot de begränsningar som gäller för driften av Marketo API:ns dagliga gränser.
**Strategi 1 - Begär färre data i varje API-anrop**. När du begär mer data i ett API-anrop ökar i allmänhet tiden det tar att söka efter data i databasen av Marketo-servern. Om du gör ett API-anrop med datumintervall, till exempel SOAP-API:t [getMultipleLeads](/help/soap-api/getmultipleleads.md), kan du korta ned tidsintervallet per anrop och kompensera för fler anrop. I stället för att begära data från 1 juni till 1 juli begär du en dag i taget, till exempel ett samtal för 1-2 juni och sedan ett annat samtal för 2-1 juni. Om du gör ett API-anrop som returnerar data från Marketo lead-fält, begär du bara de fält som behövs. Varje ytterligare lead-fält ökar stegvis tiden för ett API-anrop. Ett annat sätt är att minska gruppstorleken eller antalet begärda leads per samtal.
**Strategi 2 - Skapa samtidiga begäranden** Om du vill förbättra prestanda och hämta fler data samtidigt. Du kan göra samtidiga begäranden till API:t. Det här sättet minskar tiden för att lägga tid på trådiga API-förfrågningar i sammanställning. Anta till exempel att du gör förfrågningar till Hämta flera leads efter filtertyp. Du kan göra samtidiga förfrågningar för en frågefråga leder 1 till 300 och för en annan förfrågningsförfrågan leads 301-600.
**Strategi 3 - Cachelagra data** Vissa data i Marketo ändras mindre ofta, t.ex. listan med lead-fält, än andra data, t.ex. data för lead-aktivitet. Om du cachelagrar data som uppdateras mindre ofta minskar du antalet API-anrop du måste göra. Du får också bättre prestanda eftersom det oftast är snabbare att söka upp data lokalt än att komma åt dem från en fjärrwebbtjänst.

Publicerad _2014-12-05_ av _Murta_

## Skicka Marketo-formulärdata till Google Analytics

I Google Analytics kan du skicka anpassade datahändelser och sedan använda data för att segmentera och analysera webbplatsens prestanda. Med JavaScript kodutdrag nedan kan du automatiskt skicka Marketo 2.0-formulärdata till Google Analytics när en besökare har skickat in ett webbformulär. Så här konfigurerar du det här.

**Steg ett** Infoga JavaScript-taggen på en sida som innehåller Marketo Forms längst ned i koden (före taggen). JavaScript skickar bara icke-dolda fält (sendHiddenFields : false). Detta kan justeras genom att sendHiddenFields ändras från false till true. Du kan också välja fält som ska exkluderas genom att lägga till ytterligare fält-ID i arrayen fieldsToExclude.

```javascript
function pushFormDataToGa(a){
setTimeout(function () {
document.getElementsByTagName('form')[0].getElementsByClassName(a.submitButton)[0].addEventListener('click', function() {
  allFields = document.getElementsByTagName('form')[0].getElementsByTagName('input');
  for(i=0;i<allFields.length;i++){
   if( (allFields[i].type !="hidden" && allFields[i].type !="submit" && allFields[i].value !="" && a.fieldsToExclude.indexOf(allFields[i].id) === -1  ) || (allFields[i].type === "hidden" && a.sendHiddenFields) ){
    console.log( allFields[i].name + ": "  + allFields[i].value);
    if(typeof(_gaq) != "undefined"){
    //Classic
    _trackEvent("Marketo Form Submission", allFields[i].value , allFields[i].name
{'nonInteraction': 1});
    }else if(typeof(ga) !="undefined"){
    //Universal
    ga('send', 'event',"Marketo Form Submission", allFields[i].value , allFields[i].name, {'nonInteraction': 1});
}}}}, false);
}, 3000);}
pushFormDataToGa({
 submitButton: "mktoButton",
 fieldsToExclude: ["Email","LastName", "FirstName"],
 sendHiddenFields : false
});
```

**Steg två** Informationen i GA visas i rapportavsnittet. Gå till Beteende > Händelser > De vanligaste händelserna. **Skriptbegränsningar:** - Det här kodexemplet är bara kompatibelt med [Marketo Forms 2.0](/help/javascript-api/forms-api-reference.md). - På grund av Google sekretesspolicy får du inte skicka några personuppgifter (e-post eller namn). Förutom potentiella integritetsproblem är detta personligt identifierbar information och bryter därför mot [Google Analytics&#39; Terms of Service](https://marketingplatform.google.com/about/analytics/terms/us/):

&quot;Du kommer inte (och kommer inte att tillåta någon tredje part) att använda tjänsten för att spåra, samla in eller överföra data som identifierar en individ (t.ex. namn, e-postadress eller faktureringsinformation) eller andra data som Google rimligen kan koppla till sådan information.&quot;

Publicerad _2014-12-16_ av _Yanir_

## Lägga till ett fullständigt namnfält i ett Marketo-formulär

Vi vet att kortare webbformulär ökar konverteringsgraden. Med JavaScript-kodexemplet nedan kan du göra formulären ännu kortare genom att sammanfoga fälten för för- och efternamn i ett fält med fullständigt namn. När besökarna skriver in sitt fullständiga namn kommer skriptet automatiskt att dela upp texten i för- och efternamnsfält. För kända besökare sammanfogar skriptet för- och efternamn och kopierar dem sedan till det nya fältet så att de inte behöver fylla i fältet igen. Så här konfigurerar du det här.

**Steg ett** Skapa ett nytt anpassat fält i Marketo med namnet Fullständigt namn. Du behöver inte skapa det i CRM-plattformen eftersom skriptet bara använder det här fältet för att visa det fullständiga namnet.
**Steg två** Lägg till det här fältet i alla dina webbformulär. Ange för- och efternamnsfälten som dolda. I JavaScript ändrar du konfigurationen &quot;splitFullName&quot; så att den innehåller de tre fältnamnen. Obs! Kontrollera att dessa namn inte visas någon annanstans på sidan.
**Steg tre** Infoga JavaScript i alla dina landningssidor längst ned i koden, före -taggen.

```javascript
<script>
MktoForms2.whenReady(function (form){
        function splitFullName(a,b,c){
                String.prototype.capitalize = function(){
                        return this.replace( /(^|s)([a-z])/g , function(m,p1,p2){ return p1+p2.toUpperCase(); } );
                };
                document.getElementsByName[c](0).oninput=function(){
                        var fullName = document.getElementsByName[c](0).value;
                        if((fullName.match(/ /g) || []).length ===0 || fullName.substring(fullName.indexOf(" ")+1,fullName.length) === ""){
                                var first = fullName.capitalize();;
                                var last = "null";
                        }else if(fullName.substring(0,fullName.indexOf(" ")).indexOf(".")>-1){
                                var first = fullName.substring(0,fullName.indexOf(" ")).capitalize() + " " + fullName.substring(fullName.indexOf(" ")+1,fullName.length).substring(0,fullName.substring(fullName.indexOf(" ")+1,fullName.length).indexOf(" ")).capitalize();
                                var last = fullName.substring(first.length +1,fullName.length).capitalize();
                        }else{
                                var first = fullName.substring(0,fullName.indexOf(" ")).capitalize();
                                var last = fullName.substring(fullName.indexOf(" ")+1,fullName.length).capitalize();
                        }
                        document.getElementsByName[a](0).value = first;
                        document.getElementsByName[b](0).value = last;
                };
                //Initial Values
                if(document.getElementsByName[c](0).value.length < 2 && document.getElementsByName[b](0).value.length.length >2 && document.getElementsByName[a](0).value.length.length >2 ){
                        var first = document.getElementsByName[a](0).value.capitalize();
                        var last = document.getElementsByName[b](0).value.capitalize();
                        var fullName =  first + " " + last ;
                        console.log(fullName);
                        document.getElementsByName[c](0).value = fullName;
                }
        }
        splitFullName("FirstName","LastName","leadFullName");
});
</script>
```

Obs! Den här koden fungerar bara med Marketo Forms 2.0.

Publicerad _2014-12-16_ av _Yanir_

## Använd cURL för att importera leads via REST API

Vill du importera leads från en CSV-fil via REST API, men observera att detta är en utmaning att göra med Postman Chrome-tillägget. I det här inlägget går vi igenom hur du gör detta med cURL.

1. [Hämta och installera cURL](https://curl.se/download.html), ett kommandoradsverktyg som vi använder för att skicka data till Marketo REST API.
1. Öppna kommandoraden och navigera sedan till den plats där CSV-filen finns. Kolumnrubrikerna i CSV-filen måste matcha API-fältnamnen, inte Marketo-fältnamnen.
1. Du behöver en åtkomsttoken. Logga in på Marketo, gå till Admin och sedan till LaunchPoint. Hitta REST API-användaren och klicka på Visa detaljer. Klicka på knappen &quot;Get Token&quot;.
1. Du behöver också en REST-slutpunkt som är specifik för din Marketo-instans. Logga in på Marketo, gå till Admin och sedan till Webbtjänster. I avsnittet som är markerat som REST API hittar du slutpunkts-URL:en.
1. Följ det här formatet för cURL-anropet på kommandoraden. Ersätt `<accesstoken>` med din åtkomsttoken från steg tre och ersätt `<REST API Endpoint URL>` med REST API-slutpunktens URL från steg fyra. Mer information finns [här](https://developer.adobe.com/marketo-apis/api/mapi/#operation/importLeadUsingPOST). &quot;/bulk&quot; här ersätter &quot;/rest&quot; i slutet av URL:en för slutpunkten. Om du har angett slutpunkten för /rest/bulk returneras ett fel.

`curl -i -F format=csv -F file=@leaddata.csv -F access_token=<accesstoken> <REST API Endpoint URL>/bulk/v1/leads.json`

Publicerad _2014-12-16_ av _Jordan_

## Lägg till en bekräftelsemeddelande till en Marketo for

Låt oss säga att när en användare klickar på knappen &quot;Skicka&quot; i ett Marketo-formulär vill du visa ett meddelande som frågar användaren om det verkligen är OK att skicka?&quot;. Detta är möjligt genom att implementera några rader i JavaScript, som visar en bekräftelseruta när någon klickar på Skicka. Här är ett exempel på hur man gör detta. Lägg till funktionen onSubmit i ditt Marketo-formulär så som visas nedan. Mer information om Marketo Forms API finns i [utvecklardokumentationen](/help/javascript-api/forms-api-reference.md).

```javascript
<script src="//app-e.marketo.com/js/forms2/js/forms2.js"></script>
<form id="mktoForm_19"></form>
<script>
MktoForms2.loadForm("//app-e.marketo.com", "212-RBI-463", 19,function(form){

//Add this function to your Marketo form script
form.onSubmit(function(){
alert("Do you really want to submit the form?");
});
});
</script>
```

Publicerad _2014-12-17_ av _David_

## Visa tackmeddelande utan en uppföljningsstartsida

När du använder Marketo-formulär skapar du vanligtvis två landningssidor - en för att placera formuläret på och en för att dirigera om det när formuläret har fyllts i. I vissa fall kanske du inte vill ha två separata men mycket liknande landningssidor att underhålla. Du kan faktiskt använda samma landningssida för formuläret och för tackmeddelandet med Forms 2.0 JavaScript API. För att göra detta måste du först skapa en landningssida och ett formulär för registrering och placera formuläret på landningssidan som vanligt. Lägg sedan till ett HTML-element på sidan. I det här elementet lägger vi till kod som aktiveras när formuläret skickas. Sedan döljs formuläret och en dold <div> som innehåller tackmeddelandet. Din JavaScript bör se ut så här:

```javascript
//Edit host with your Marketo instance info
<script src="//<host>/js/forms2/js/forms2.js"></script>
<script>
MktoForms2.whenReady(function (form){
 //Add an onSuccess handler
  form.onSuccess(function(values, followUpUrl){
   //get the form's jQuery element and hide it
   form.getFormElem().hide();
   document.getElementById('confirmform').style.visibility = 'visible';
   //return false to prevent the submission handler from taking the lead to the follow up url.
   return false;
 });
});
</script>
```

Redigera tackmeddelandetext.

`<div id="confirmform" style="visibility:hidden;"><p><strong>Thank you. Check your email for details on your request.</strong></p></div>`

Du vill redigera värdnamnet och tackar för meddelandet i kodexemplet. Den första ska referera till din Marketo-instans (till exempel &quot;//app-sj06.marketo.com/js/forms2/js/forms2.js&quot;) och den andra ska innehålla den tacktext som du vill visa när formuläret har fyllts i. Texten visas på landningssidan exakt där du placerar HTML-elementet, så se till att du redigerar det i egenskapsbladet. Du bör också se till att lagret i ditt HTML-element är mindre än lagret för formuläret. Som standard placeras båda i Lager 15, så du är säker om du skapar HTML-elementlagret 11. Om du inte gör det kan du inte skriva in några formulärfältsrutor som överlappar tackmeddelandet. Det är inte nödvändigt att ändra uppföljningstypen på formuläret eller på landningssidan, eftersom JavaScript kommer att skriva över dessa inställningar. Mer information om Marketo Forms API finns i [utvecklardokumentationen](/help/javascript-api/forms-api-reference.md).

Publicerad _2014-12-19_ av _Kristin_

## Markera öppna Source-projekt som byggts på Marketo-plattformen

Det här är den första posten i en serie som markerar öppen källkodsprojekt som byggts runt Marketo-plattformen av utvecklarcommunityn. Vi har [en lista på Marketo GitHub-konto](https://github.com/Marketo/Community-Supported-Client-Libraries) där vi kan spåra klientbibliotek och projekt som skapats av Marketo utvecklarcommunity. Nedan finns tre projekt som utvecklats kring Marketo REST och SOAP API:er. Daniel Chesterton skapade [ett klientbibliotek i PHP för Marketo REST API](https://github.com/dchesterton/marketo-rest-api). Klientbiblioteket har för närvarande täckning för 12 REST API-slutpunkter.** Kyle Halstvedt från Elixiter skapade ett projekt för att [hämta leads från Marketo statiska listor till ett Google-kalkylblad](https://github.com/Elixiter/mkto_google-spreadsheet). Kyles projekt använder Marketo REST API.  David Santoso skapade en [Ruby Gem för Marketo SOAP API.](https://github.com/davidsantoso/markety) Det här projektet kan hjälpa dig att snabbare integrera Marketo SOAP API med en Ruby on Rails-app.  Vi är glada över att kunna se fler projekt som skapats av utvecklarcommunityn på Marketo-plattformen. Om du arbetar med ett öppen källkodsprojekt för Marketo-plattformen [skickar du det till detta GitHub-svar via en pull-begäran](https://github.com/Marketo/Community-Supported-Client-Libraries).

Publicerad _2015-01-02_ av _Murta_

## Ändra sidinnehåll dynamiskt baserat på en användares plats

Anta att du dynamiskt vill ändra telefonnumret på en landningssida beroende på var användaren befinner sig. Om personen till exempel befinner sig i Kalifornien vill du visa dem telefonnumret för ditt Kalifornikontor på din landningssida, men om de är i Japan vill du visa dem telefonnumret för det japanska kontoret.  Ett sätt att implementera detta är att använda JavaScript och [HTML5 Geolocation API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API). Fördelen med den här metoden är att vi kan skapa en landningssida och dynamiskt ändra den baserat på en användares plats, i stället för flera statiska landningssidor. Vi går igenom de tekniska genomförandedetaljerna nedan. Vi skapar ett objekt för kontorsplatser med latitud- och longitudkoordinater och ett andra objekt med telefonnummer till kontoret. I produktionen är det bättre att kombinera dessa två objekt till ett enda objekt.

```json
//Coordinates for Marketo offices
var officeLocations = {
    "San Mateo": {latitude: 37.5596465, longitude: -122.2870142},
    "Atlanta": {latitude: 33.8547013, longitude: -84.35552349999999},
    "Tokyo": {latitude: 35.6895, longitude: 139.6917},
    "Dublin": {latitude: 53.3478, longitude: -6.2603097},
    "Sydney": {latitude: -33.873651, longitude: 151.2068896},
    "Portland": {latitude: 45.512089, longitude: -122.6763367},
    "Tel Aviv": {latitude: 32.0852999, longitude: 34.78176759999999}
}

//Phone numbers for Marketo offices
var officePhoneNumbers = {
    "San Mateo": "+1-650-376-2300",
    "Atlanta": "+1-877-260-6586",
    "Tokyo": "+81-03-6759-8280",
    "Dublin": "+353-1-242-3000",
    "Sydney": "+61-2-9045-2711",
    "Portland": "+1-877-260-6586",
    "Tel Aviv": "+1-877-260-6586"
}
```

Vi skapar en metod för att begära en användares plats. Om användarens plats inte är tillgänglig används Marketo huvudkontor och dess telefonnummer som standard för att hantera fel.

```javascript
//Method to get user's current location. Returns a position object with user's geo coordinates
function getLocation() {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(findNearestOffice);
    } else {
        x.innerHTML = "Marketo Location: San Mateo
Marketo Phone Number: +1-877-260-6586";
    }
}
```

Slutligen skapar vi en metod för att hitta det närmaste kontoret till användarens plats och sedan returnera telefonnumret till det närmaste kontoret på sidan. Den här metoden använder metoden findNearest från [Geolib, som är ett JavaScript-bibliotek som tillhandahåller geospatiala åtgärder](https://github.com/manuelbieh/Geolib).

```javascript
//Find nearest Marketo office to user's location
function findNearestOffice(position) {
        var nearestOffice = geolib.findNearest({latitude: position.coords.latitude, longitude: position.coords.longitude}, officeLocations);
        x.innerHTML = "Marketo Location: " + nearestOffice.key + "
Marketo Phone Number: " +  officePhoneNumbers[nearestOffice.key];
}
```

Här är den fullständiga implementeringen. Vi utlöser metoden getLocation när användaren klickar på knappen på sidan. Den här [GitHub-repo](https://github.com/MurtzaM/Find-Nearest-Marketo-Office) har de filer som behövs för att konfigurera den här demon.

Publicerad _2014-12-20_ av _Murta_

## Spårning av leads och flera domäner

Med Marketo Munchkin spårningskod kan du spåra besök på din webbplats. Du vill antagligen använda Munchkin spårningskod för att cookie anonyma leads för de flesta eller alla sidor på webbplatsen. Låt oss gå igenom hur Munchkin fungerar. Besök på sidan registreras för befintliga leads, och om en besökare som inte är cookie besöker sidan skapas en ny cookie och lagras, och en ny anonym lead skapas i Marketo-databasen. Munchkin-spåraren cookies automatiskt för en besökare om de inte redan har en befintlig cookie för den aktuella domänen. I Marketo loggas händelsen (klicka på en länk, besök en webbsida eller ett nytt lead) i leadets aktivitetslogg. Värdet som lagras i cookien är unikt för en viss besökare. Värdet är en kombination av det unika ID:t för kontospårning i Munchkin, domännamnet, tidsstämpeln och det slumpmässiga heltalet.
**Vad händer om jag har flera domäner?** säger att du har två webbplatser som du vill spåra: `<www.apples.com>` och `<www.bananas.com>`. Du kan placera spårningskoden på båda platserna, men du måste tänka på följande. Marketo cookies är cookies från första part och är därför domänspecifika. Detta innebär att besökare på plats 1 kommer att skapas som ett anonymt lead i Marketo, och om samma lead sedan går till plats 2 kommer detta att skapa en andra separat anonym lead i Marketo. Om leadet fyller i ett formulär på plats 1 blir denna post känd, kommer den anonyma posten för plats 2 att finnas kvar och fortsätta samla in efterföljande besök på webbplatsen. Om leadet därefter fyller i ett formulär på webbplats 2 med exakt samma e-postadress som på webbplats 1, sammanfogas båda kända leads automatiskt och alla tidigare och framtida beteenden spåras på en enda post i Marketo. Båda cookie-ID:n är kopplade till samma lead och all webbaktivitet (från båda domänerna) kommer att finnas på leadet.
**Vad gäller för flera underdomäner?** underdomäner är inte ett problem. Vi använder Marketo.com som exempel. Den har flera underdomäner för olika språk, till exempel fr.marketo.com och de.marketo.com. Med underdomäner registreras all aktivitet mot samma lead-post/cookie.

Publicerad _2015-01-13_ av _David_

## Ändra tipstextfärgen i ett Marketo-formulär

Anta att du vill ändra tipstextfärgen (kallas även platshållartext) i Forms 2.0. Detta är möjligt via anpassad CSS. I skärmbilden nedan har jag t.ex. gjort tipstexten i det här Marketo-formuläret blå. Det finns tre alternativ beroende på hur du använder Marketo Forms.

**Alternativ 1: Om du bäddar in ett Marketo-formulär lägger du till CSS nedan direkt i CSS-huvudfilen.**

```css
::-webkit-input-placeholder {
  color: blue;
}
::-moz-placeholder {
  color: blue;
}
:-ms-input-placeholder {
  color: blue;
}
:-moz-placeholder {
  color: blue;
}
```

**Alternativ 2: När du bäddar in ett Marketo-formulär kan du lägga till CSS direkt på sidan mellan `<style></style>` -taggar i `<head>` -avsnittet.**

```css
<style>
::-webkit-input-placeholder {
  color: blue;
}
::-moz-placeholder {
  color: blue;
}
:-ms-input-placeholder {
  color: blue;
}
:-moz-placeholder {
  color: blue;
}
</style>
```

**Alternativ 3: Om du använder ett Marketo-formulär på en Marketo-landningssida kan du lägga till denna anpassade CSS via Marketo användargränssnitt.** Hitta landningssidan i Marketo navigeringsträd. Klicka sedan på Redigera utkast. Klicka på Redigera sidmetataggar. Lägg till CSS nedan i sektionen Anpassad HEAD HTML. `<style></style>`-taggarna ska inkluderas.

```css
<style>
::-webkit-input-placeholder {
  color: blue;
}
::-moz-placeholder {
  color: blue;
}
:-ms-input-placeholder {
  color: blue;
}
:-moz-placeholder {
  color: blue;
}
</style>
```

Klicka på Godkänn utkast. När du nu besöker Marketo landningssida är tipstexten den färg som du definierade i CSS. Mer information om Marketo Forms finns i [dokumentationen](/help/javascript-api/forms-api-reference.md).

Publicerad _2015-01-14_ av _Murta_

## Hämta aktivitetsdata via REST API

Säg att du vill få alla leads som har lagts till i en lista den här månaden. Med hjälp av REST API:t [&#x200B; för &#x200B;](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET)Hämta lead-aktiviteter kan du hämta dessa data. Innan du anropar API:t Hämta lead-aktiviteter måste du hämta en åtkomsttoken från autentiserings-API:t och även hämta en startdatumtoken från [Get Paging Token API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getActivitiesPagingTokenUsingGET). Nedan finns en exempelkod i Ruby som går igenom de enskilda API-slutpunkterna som du måste anropa för att returnera alla leads som läggs till i en lista den här månaden. 1. Hämta åtkomsttoken**

```ruby
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com/identity/oauth/token?grant_type=client_credentials>"
# Relace with your client id
client_id = "99985d09-22a9-3jl2-84av-f5baae7c3a45"
# Replace with your your  client secret
client_secret = "tZPVrKiEmUDezE18yZfeaPlTJ2vKn2fw"
request_url = marketo_instance + "&client_id=" + client_id + "&client_secret=" + client_secret

# Make request
response = RestClient.get request_url

# Parse reponse and return only access token
results = JSON.parse(response.body)
access_token = results["access_token"]
puts access_token
```

1. Hämta växlingstoken

```ruby
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/activities/pagingtoken.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
# Specify date
since_date_time = "&sinceDatetime=2015-01-01T00:00:00-08:00"
request_url = marketo_instance + endpoint + auth_token + since_date_time

# Make request
response = RestClient.get request_url

# Returns Marketo API response
puts response
```

1. Hämta aktivitetsdata** Om du vill avgöra vilket ID för aktivitetstyp som krävs för det här anropet kan du fråga [Gott Activity Types API](/help/rest-api/activities.md). API:t Hämta aktivitetstyper returnerar ett schema med alla aktivitetstyper och associerade ID:n. Till exempel returneras ID 12 för nya leads som skapats och ID 1 för webbsidesbesök.

```java
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/activities.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
# Specify datetime needed as nextPageToken
since_date_time = "&nextPageToken=GIYDAOBNGEYS2MBWKQYDAORQGA5DAMBOGAYDAKZQGAYDALBQ"
# Specify activities needed
activity_type_ids = "&activityTypeIds=24"
request_url = marketo_instance + endpoint + auth_token + since_date_time + activity_type_ids

# Make request
response = RestClient.get request_url

# Returns Marketo API response
puts response
```

1. API:t Hämta leadaktiviteter returnerar en sidtoken med varje svar som du kan använda för att paginera genom resultatuppsättningen.** Mer information finns i [REST API-dokumentationen](/help/rest-api/rest-api.md).

Publicerad _2015-01-20_ av _Murta_

## Markera öppna Source-projekt som byggts på Marketo-plattformen: Del två

Det här är det andra inlägget i en serie med fokus på öppen källkodsprojekt som byggts runt Marketo-plattformen av utvecklarcommunityn. Vi har [en lista på Marketo GitHub-konto](https://github.com/Marketo/Community-Supported-Client-Libraries) där vi kan spåra klientbibliotek och projekt som skapats av Marketo utvecklarcommunity. Nedan finns tre projekt som utvecklats runt API:erna för Marketo SOAP och Munchkin. [PunchTab](https://www.punchtab.com/) skapade [&#x200B; ett klientbibliotek i Python för Marketo SOAP API](https://github.com/PunchTab/suds-marketo). [Flickerbox](https://www.flickerbox.com/) skapade [ett klientbibliotek i PHP för Marketo SOAP API](https://github.com/flickerbox/marketo).* [Richard Morrison](https://x.com/mozz100) skapade [&#x200B; ett PHP-skript för att hämta lead-data från Marketo SOAP API, och skickade sedan dessa data till klienten med JavaScript.](https://github.com/mozz100/marketo-whodat) Det här projektet kan hjälpa dig att ändra en sida baserat på en användares data i Marketo.  Vi är glada över att kunna se fler projekt som skapats av utvecklarcommunityn på Marketo-plattformen. Om du arbetar med ett öppen källkodsprojekt för Marketo-plattformen [skickar du det till detta GitHub-svar via en pull-begäran](https://github.com/Marketo/Community-Supported-Client-Libraries).

Publicerad _2015-01-20_ av _Murta_

## Skicka RTP-rekommendationsmotorklickningar till Google Analytics

Här är en lösning där användare av Marketo Real-Time Personalization (RTP) kan se klickningar från Content Recommendation Engine i Google Analytics. När en besökare klickar på fältet Innehållsrekommendation skickas en händelse till Google Analytics under händelsekategorin&quot;RTP-Recommendations&quot;. I Analytics (Analys) läggs rekommendationstexten (så som den visas i fältet) till i händelseetiketten och URL:en för den rekommenderade resursen läggs till i händelseåtgärden. Skriptet fungerar för både Classic Google Analytics och Google Universal Analytics. Den här taggen bör klistras in i slutet av HTML-sidkoden, så det är den sista taggen före `</body>`-taggen.

```javascript
$( document ).ready(function() {
if(document.getElementsByClassName("insightera-bar-content").length
 >0){
document.getElementsByClassName("insightera-bar-content")[0].getElementsByTagName('a')[0].addEventListener("click",
 function(){
assetName
 = document.getElementsByClassName("insightera-bar-content")[0].getElementsByTagName('a')[0].innerText;
assetURL
 = document.getElementsByClassName("insightera-bar-content")[0].getElementsByTagName('a')[0].href;
assetURL=
 assetURL.substring(assetURL.lastIndexOf("/"),assetURL.indexOf("?iesrc"));
console.log(assetName

 * " | " + assetURL);
if(typeof(_gaq)
 != "undefined"){
//Classic
_trackEvent("RTP-Recommendations",
 assetName , assetURL , {'nonInteraction': 1});
}else
 if(typeof(ga) !="undefined"){
//Universal
ga('send',
 'event',"RTP-Recommendations", assetName , assetURL, {'nonInteraction': 1});
}
});
}
});
```

Publicerad _2015-01-22_ av _Yanir_

## Använda Marketo REST API med Boomi: Hämta och lagra en REST-autentiseringstak

Att konfigurera en automatisk export av leads som uppfyller vissa kriterier är mycket vanligt med Marketo. Även om detta för närvarande inte går att göra i Marketo-gränssnittet är det ganska enkelt att uppnå detta med tredjepartsverktyget som Dell Boomi, en statisk lista med vissa datahanteringskampanjer och Marketo REST API. REST API? Jag trodde att Boomi inte hade någon Marketo REST API Connector. Det gör det inte för närvarande, men det går att göra samma sak med HTTP-kopplingen och manuellt definiera jSON-svarsformerna. Det första steget är att konfigurera din Marketo-instans så att den använder REST API:t enligt beskrivningen på [REST API Marketo Developer-sidan](/help/rest-api/rest-api.md). Jag antar också att du har tillgång till ett Dell Boomi-konto och Boomi-kunskaper för att skapa den här typen av integreringsprocesser. Den sista processen ser ut så här: den kommer att innehålla anrop till följande Marketo REST API-åtgärder, som alla har en associerad jSON-svarsform som finns på utvecklarwebbplatsen. För att spara tid har jag listat dem nedan i Exempel-JSON för [autentisering](/help/rest-api/authentication.md)

```json
{
  "access_token": "",
  "token_type": "",
  "expires_in": 0,
  "scope": ""
}
```

Exempel-JSON för [Hämta flera leads efter list-ID](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)

```json
{
  "requestId": "",
  "success": true,
  "nextPageToken": "",
  "result": [
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    },
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    },
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    }
  ]
}
```

Exempel-JSON för [Ta bort leads från listan](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE)

```json
{
  "requestId": "",
  "success": true,
  "result": [
    {
      "id": 1,
      "status": ""
    },
    {
      "id": 2,
      "status": "",
      "reasons": [
        {
          "code": "",
          "message": ""
        }
      ]
    }
  ]
}
```

Definiera egenskaper: Innan vi börjar anropa REST är det viktigt att externalisera och kapsla in de variabler som du använder. Jag har definierat följande som visas nedan.

* Klient-ID: Hämta detta från REST-startpunktstjänsten
* Klienthemlighet: Hämta detta från REST-startpunktstjänsten
* AccessToken: Vi får detta från ett REST-anrop
* Statiskt ListID: LIST-ID för den statiska lista som vi ska arbeta med. Hämta den från URL:en i Marketo
* Fält: En kommaavgränsad lista med fält som den andra tjänsten hämtar från Marketo för varje lead. Min är &quot;id, email,firstName,lastName&quot; * IDStringToDelete: Innehåller så småningom ID för alla leads i den statiska listan som ska användas när de tas bort från listan
* ActivityTypes: Används i del 2 av den här bloggen, där jag utökar mig för detta!
* SinceDateTime: Kommer att användas i del 2 av den här bloggen, där jag utökar mig för detta!
* PagingToken: Kommer att användas i del 2 av den här bloggen, där jag utökar mig!
* Mapp - Utgående: Sökvägen till den utgående mappen på SFTP-servern. Jag använder &quot;/data/utgående&quot; i det här exemplet. Det gör att vi kan parametrisera SFTP-åtgärden så att den blir generisk.

Autentiseringstoken: Som jag nämnde placerar vi en koppling på arbetsytan efter att vi har skapat processen med startformen&quot;Inga data&quot; (detta är bara ett personligt val, jag gillar alla kontakter som ser ut som brittiska kontakter).
Kopplingen ska konfigureras så här: - Kopplingen är en HTTP-klient för GET - Anslutningen använder URL: `https://123-ABC-456.mktorest.com` (notera Nej/rest i slutet så att vi kan använda den här för REST-anrop och även använda den för att hämta ID-åtkomsttoken. och ändra 123-ABC-456 till höger för din Marketo-instans) - Åtgärden är Get Auth Token (ny!) - Request Profile = None - Response Profile = JSON - New Profile called &quot;Authentication Token Response&quot; - Content Type: Plain - HTTP Method: GET - Resource Path (add 4 without quote marks marks): identity/oAuth/token?grant_type=client_credentials&amp;client_id=&quot;; &quot;ClientID (Ersättningsvariabel)&quot;; &quot;&amp;client_secrets=&quot;; &quot;ClientSecret (ersättningsvariabel)&quot; - Ange parametrar under Konfigurera —> Parametrar —>(+): Ange ClientID = Process Property Client ID; Ange ClientSecret = Process Property Client Secret Lagra token i Process Properties &quot;AccessToken&quot;-variabeln som visas och extrahera den från jSON-svaret.
Mönstret för det här steget upprepas för nästa steg, men med nya åtgärder med olika jSON-returprofiler. Många av REST-samtalen kommer att behandlas på samma sätt med smärre ändringar! I nästa avbetalning kommer vi att utöka detta och få en lista över leads från en statisk lista med REST! För närvarande kör du processen, men placerar en stoppform efter&quot;Ange egenskaper&quot; och kör sedan felsökningen för att vara säker på att du ser samma token som du ser i Marketo. De borde matcha sig perfekt!

Publicerad _2015-01-26_ av _John_

## Använda Google Font API för att lägga till ett anpassat teckensnitt på en Marketo-landningssida

**Obs! Detta är ett blogginlägg av [Murtza Manzur](https://www.linkedin.com/in/murtzam). Murtza är en Marketo Developer Evangelist som är baserad i San Francisco Bay Area.**
Säg att du skapar en landningssida i Marketo och vill använda ett anpassat teckensnitt. Detta är möjligt med Google Font API.  Lägg till en importmetod i CSS-filen med referens till Google Fonts:

`@import url(http://fonts.googleapis.com/css?family=Open+Sans:400,300,600);`

Publicerad _2015-01-26_ av _Murta_

## Inled ett leads förnamn med e-postskript

Låt oss säga att en lead skriver sitt namn med gemener, som &quot;John doe&quot;. Men när du skickar ut en e-postkampanj vill du att bladets namn ska ändras till en versal i e-postmeddelandet, som John Doe. Du kan använda e-postskript för att ändra ett leadnamn till versaler. Så här gör du.

1. Klicka på fliken Mina token i e-postprogrammet.
1. Skapa en ny e-postskripttoken genom att dra&quot;E-postskript&quot; från den högra panelen till den mittersta panelen. Namnge token.
1. Klistra in koden nedan i textrutan Redigera skripttoken. Markera kryssrutan Förnamn under Lead-objektet på den högra panelen. Klicka sedan på Spara.

```javascript
# set($name = ${lead.FirstName})
# set($formattedFirstName = $name.substring(0).toUpperCase())
$formattedFirstName
```

1. Referera token i din e-postresurs. Förnamnet på leadet med den första bokstaven med inledande versal skrivs ut. Mer information om e-postskript finns i [dokumentationen för e-postskript](/help/email-scripting.md).

Publicerad _2015-01-26_ av _Murta_

## Hämta alla leads från Marketo REST API

En [fråga om StackOverflow besvarades med frågan hur du får en lista över alla leads från Marketo via REST API](https://stackoverflow.com/questions/28184900/how-do-i-get-the-list-of-all-the-leads-in-marketo). Du kan söka efter dessa data med [Hämta flera leads efter REST API-slutpunkt för filtertyp](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET). Leads i Marketo tilldelas lead-ID i sekventiell ordning från 1. Med hjälp av [Hämta flera leads per filtertyp REST API-slutpunkt](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) kan du fråga 300 leads efter lead-ID för varje anrop. Du måste ange id som filterType och lead-ID som filterValues för varje anrop till den här slutpunkten. Om du vill få alla leads itererar du igenom det totala antalet leads 300 i taget. Y
Du kan få det totala antalet leads i en Marketo-instans via Marketo användargränssnitt. Gå till fliken Leaddatabas i Marketo-användargränssnittet, klicka på Smarta listor för system, klicka på Smarta listor för alla Leads och klicka sedan slutligen på fliken Leads. Klicka sedan på kolumnen Id och sortera fallande. När leads har sorterats blir ID:t för den första leadet den övre gränsen för lead-ID:t när du frågar alla leads. Om du inte har tillgång till Marketo-gränssnittet för att få fram det totala antalet leads finns det ett [alternativt sätt att hämta det här värdet med hjälp av REST API:t för Get Lead Activity](https://stackoverflow.com/questions/28419967/get-all-leads-programmatically-in-marketo-v1).

1. Första API-anrop: ersätt ... med alla värden däremellan:

`/rest/v1/leads.json?filterType=Id&filterValues=1,2,3,...,298,299,300`

Här är en exempelkod i Ruby för första anropet.

```java
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "<https://AAA-BBB-CCC.mktorest.com>"
endpoint = "/rest/v1/leads.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
# Replace with filter type and values
ids_needed = (1..300).to_a.join(",")
filter_type_and_values = "&filterType=Id&filterValues=" + ids_needed
request_url = marketo_instance + endpoint + auth_token + filter_type_and_values

# Make request
response = RestClient.get request_url

# Returns Marketo API response
puts response
```

1. Det andra API-anropet och varje efterföljande API-anrop följer samma mönster tills det totala antalet leads nås:

```
//replace ... with all the values in between
/rest/v1/leads.json?filterType=Id&filterValues=301,302,303,...,598,599,600
```

Mer information finns i [REST API-dokumentationen](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET).

Publicerad _2015-01-28_ av _Murta_

## Kör formuläröverföringsåtgärder från iframe till överordnad sida

Vi har sett ett fåtal fall där användare använder iframe-formulär och vill dirigera besökare som fyller i formuläret till en tacksida eller PDF, video osv. Problemet är att eftersom formuläret är inbäddat på en landningssida som skiljer sig från den överordnade sidan, händer åtgärden bara på den inre sidan där formuläret finns. Nedan finns två JavaScript-taggar som vi har skapat. Infoga som ett HTML-element på dina iframe-sidor eller direkt i den startsidesmall som du använder för iframes. Placera den före den sista `</body>`-taggen. Den första taggen utför åtgärden på den överordnade sidan och den andra taggen öppnar den på en ny flik.

**Formuläråtgärd på en överordnad sida**

```javascript
<script>
MktoForms2.whenReady(function (form){
form.onSuccess(function (values, url){
window.parent.location.assign(url);
return false;
           });
});
</script>
```

**Formuläråtgärd på en ny flik**

```javascript
<script>
MktoForms2.whenReady(function (form){
var newWin;
form.onSubmit(function (){
newWin = window.open('about:blank', 'myWindow');
});
form.onSuccess(function (values, url){
newWin.location.replace(url);
return
 false;
});
});
</script>
```

Publicerad _2015-02-02_ av _Yanir_

## Skicka visningsdata från en YouTube Video till Market

Säg att du vill segmentera leads i Marketo baserat på om de har startat eller avslutat en viss video. Det här kan du göra med Munchkin, YouTube Iframe API och smarta listor i Marketo. Med exempelkoden i det här inlägget kan du skicka videostarter och videofärdiga händelser till Marketo via Munchkin. För att detta ska fungera måste Munchkin även läsas in på sidan innan du kan börja skicka videovyhändelser till Marketo. Videon som har startats och slutförts visas i leadets aktivitetslogg. När data finns i Marketo kan du skapa en smart lista och segmentera leads som har startat eller avslutat en video.

1. Hämta ID:t för den YouTube-video som du vill bädda in.** Observera det ID som är en serie slumpmässiga tecken efter `v=` från webbadressen för den YouTube-video som du vill använda.
1. Placera YouTube video-id:t från steg 1 på den åttonde raden i det här kodexemplet. Placera sedan koden före `</body>` i HTML på sidan.

```javascript
<div id="player"></div>
<script>
var tag = document.createElement('script');
tag.src = "https://www.youtube.com/iframe_api";
document.getElementsByTagName('head')[0].appendChild(tag);

//Change 'iiqxcjxJ5Us' to video needed
var player, videoId = 'iiqxcjxJ5Us';
function onYouTubeIframeAPIReady() {
player = new YT.Player('player', {
height: '390',
width: '640',
videoId: videoId,
events: {
'onStateChange': onPlayerStateChange
}
});
}

function onPlayerStateChange(event) {
switch( event.data ) {
//Send video started event to Marketo
case YT.PlayerState.PLAYING: Munchkin.munchkinFunction('visitWebPage', {
url: '/video/'+videoId
, params: 'video=started'
}
);
break;
//Send video finished event to Marketo
case YT.PlayerState.ENDED: Munchkin.munchkinFunction('visitWebPage', {
url: '/video/'+videoId
, params: 'video=finished'
}
);
break;
}

}
</script>
```

1. Skapa en smart lista i Marketo med URL:en för videon och den view-händelse som du letar efter som värdet för &quot;Querystring contains&quot;. Mer information om API:t för YouTube Iframe finns i [YouTube API-dokumentation](https://developers.google.com/youtube/iframe_api_reference). Mer information om Munchkin finns i [dokumentationen för Marketo-utvecklare](/help/javascript-api/lead-tracking.md).

Publicerad _2015-02-02_ av _Murta_

## Tips och tricks för Marketo SOAP API

OBS! Detta är ett gästblogginlägg. [Ed Blachman är Senior Architect](https://www.linkedin.com/uas/login?session_redirect=https%3A%2F%2Fwww.linkedin.com%2Fprofile%2Fview%3Fid%3D2777965) på [TIBCO Software, en välkänd leverantör av företagsprogramvara](https://exchange.adobe.com/apps/browse/ec?product=MRKTO). Ed arbetar med produkter som gör det möjligt för Gartner att integrera molntjänster utan att själva behöva göra någon programmering. [Marketo SOAP API](/help/soap-api/soap-api.md) är ett kraftfullt verktyg som utvecklare kan använda för att utnyttja kraften i Marketo och integrera den med våra egna program. Mellan [den formella dokumentationen](./getting-started.md) och [communityresurserna](https://nation.marketo.com/) finns det mycket information om hur den används. När jag började, lärde jag mig mycket om den informationen och tyckte att den var ovärderlig. I den processen har jag dock byggt upp några tips och trick som jag inte hade sett på någon av dessa platser. Här är några av vad jag kom på.

**Sandlådan för utvecklare** Sandlådan är förstås en fantastisk resurs för API-utvecklare: en säker plats där du kan experimentera med Marketo-funktioner, lägga till och ta bort objekt utan att störa de verkliga marknadsföringsaktiviteter som utförs av företagets verkliga Marketo-användare. Sandlådan är dock inte ett panacea.
Jag behövde till exempel dela vår sandlåda med en annan utvecklingsgrupp, och det tog en stund, eftersom de hade vant sig vid tanken att de ägde sandlådan. Till slut kom vi på några bra sätt att dela: - Skriv inte tester som är beroende av fullständig kunskap om innehållet i din sandlåda. Som delad resurs kan scheman ändras utan föregående meddelande, liksom hela poster i din lead-databas eller i dina lead-program eller andra enheter. Om dina tester får fullständig kunskap om sandlådan skapar din utvecklingscykel utbrottsperioder för de grupper som du delar den med. Eftersom deras utvecklingscykel vanligtvis inte sammanfaller med din, är det ungefär så att resursen blir lite för dyr. Det är inte heller nödvändigt, om du tänker igenom det. - Använd en konvention för att etikettera allt ditt innehåll - dina leads, dina lead-schemafält, dina program, oavsett. Om ni var och en kan identifiera era egna objekt, och om ni kan hålla med era medarbetare om att var och en av er kommer att lämna de andra objekten i fred, bör ni ligga på en fast grund för delning. För leads kan du skapa ett anpassat fält och skapa en konvention med det här anpassade fältet för att identifiera dessa leads när ditt test leder. För listor och program kan du börja namnge objekten med en sträng som identifierar de objekten som tillhör dig. - Överväg att skriva tester som rensar efter sig själva och först skapar de objekt du är intresserad av, sedan öppnar eller uppdaterar eller tar bort dem selektivt och slutligen tar bort dem. (Observera att detta inte är 100 % möjligt i SOAP API eftersom inte allt i sandlådan, eller i en verklig instans för den delen, kan hanteras via SOAP API. Trots det är det fortfarande värt att göra detta så mycket du kan.)

**Verkliga instanser** Problemet med sandlådan är att den inte används i produktionen, så det är svårt att förstå hur den verkliga användningen ser ut i en Marketo-instans. Om du har tur som har en Marketo-användare i teamet, eller om du håller på med skräddarsydd utveckling för Marketo-användare, är det inte ett sådant problem. Men i mitt lag var det en stor grej. Ingen av oss var Marketo experter, och eftersom vi blev ombedda att förstå ett stort antal molntjänster hade vi helt enkelt inte tillräckligt med personal för att bli experter på något. Här är några av de insikter vi har fått från åtkomst till en verklig instans: - Stora lead-scheman. Leadschemat i produktionsinstansen som vi använde har över 200 fält. Det gjorde det kristallklart för våra gränssnittsdesigners att användargränssnittet de designade var tvungna att anpassa scheman av den storleken (eller större). - Burstiskt bruk. Vi såg två sorteringsordrar med stor skillnad mellan de högsta användningstiderna och de låga användningstiderna (i antalet leads som skapas eller uppdateras). Detta påverkade både datavolymen vi fick tillbaka från API-anrop (uppenbart) och den tid det skulle ta för ett API-anrop att svara (eventuellt mindre uppenbart).

**Svarstid för API-anrop** Beroende på tid på dygnet, information om ditt API-anrop och innehållet i din instans kan det ta längre tid än genomsnittet för SOAP API:ts svarstid. Ibland hade vi API-anrop som tog en och en halv minut att svara. Du måste vara medveten om möjligheten att ta itu med det: Det här kanske inte är något problem för din användning. Men anta inte bara det, gör lite tester. - Förbättra din användning. I vårt fall var det största problemet att vi angav sidstorleken för våra anrop till [getMultipleLeads](/help/soap-api/getmultipleleads.md) som så stor som API:t tillåter. I vårt sammanhang är det en viss mening, eftersom vårt mål är att vara så effektivt som möjligt med vår kunds API-kvot. Men i ditt sammanhang behöver du kanske inte bekymra dig så mycket om användarnas API-samtalskvoter, och i så fall får du definitivt bättre svarstid genom att be om mindre datasidor.

**Leadpartitionering** Marketo har kraftfulla verktyg, partitioner och arbetsytor som gör att flera marknadsföringsgrupper kan dela en enda Marketo-instans. Dessa verktyg återspeglas dock inte direkt i SOAP API. När du till exempel använder getMultipleLeads för att hämta alla leads som har uppdaterats eller skapats sedan en viss datetime, återställs alla leads i instansen, utan hänsyn till (och utan något att ange) vilken partition eller arbetsyta som innehåller något visst lead. Leadframtagning och tillägg av leads till listor är andra sammanhang där leadpartitionering kan påverka vad API-anropen faktiskt gör. Observera att detta innebär att partitioner och arbetsytor kanske inte är den lösning du behöver för att lösa problemet med sandlådedelning som beskrivs ovan. Hur vet du om det här är ett problem för dig? Jag tycker att allt detta är till stor hjälp: Utvecklarevangelisterna är engagerade i att vi ska lyckas med API:erna, och där det finns några frågor är de otroligt bra på att arbeta för att hitta svar. - [API-dokumentation](./getting-started.md). Evangelisterna har redan tagit upp det här problemet i en del av dokumentationen, och som en del av deras engagemang i vår framgång är de verkligen bra på att uppdatera dokumentet. - Dina egna testfall. Även om det inte är en bra idé att använda partitioner och arbetsytor för att dela sandlådan är sandlådan en bra plats att leka med partitioner och arbetsytor för att ta reda på om de utgör en utmaning för din avsedda användning. (Det är också ett bra sätt att begränsa dina frågor till evangelisterna, som alltid är en bra idé.)

**TIMTOWTDI och testning**&quot;Det finns mer än ett sätt att göra det&quot; - programmeringsmotto Perl gäller i vissa sammanhang för Marketo SOAP API. Jag ville till exempel kombinera uppdatering av en uppsättning leads med tillägg av dessa leads till en lista. Med SOAP API kan du göra detta på två sätt: 1. [importToList](/help/soap-api/importtolist.md) + [getImportToListStatus](/help/soap-api/getimporttoliststatus.md). När du läser dokumentationen är detta helt klart det &quot;normala&quot; sättet att göra detta. Det faktum att du måste fråga efter statusen för din importåtgärd gav mig dock en gul flagga. Var det verkligen så här jag ville implementera min import? 1. [syncMultipleLeads](/help/soap-api/syncmultipleleads.md) + [listOperation](/help/soap-api/listoperation.md). Detta verkar mycket mindre elegant än ett unitärt importToList-anrop, men det kräver inte avsökning. Var det ett genomförbart alternativ? Fall som dessa är svåra för evangelisterna att hantera, eftersom de verkligen beror på vilken typ av förekomster du har att göra med och exakt vad du försöker göra. Som tur är bör du, om du har konfigurerat en robust enhetstestningsmiljö, även kunna använda den för att utforska frågor som dessa. I just det här fallet visade det sig att alternativ 2 var bättre för mig än alternativ 1 - inte på grund av avsökningen, utan snarare för att jag stötte på fältorienterade begränsningar för importToList, och även för att jag försökte skriva kod som kunde användas i sammanhang och instanser som jag inte hade någon kontroll över. Men ditt användningssätt kan vara annorlunda och testning är det enda sättet du får reda på.

**Slutsats** Jag tror inte att något av det här är en stor hemlighet. Å andra sidan hade jag varit före matchen om jag hade vetat allt det här innan jag började. Jag hoppas att du tycker att det är användbart.

Publicerad _2015-02-05_ av _David_

## Använda Marketo REST API med Boomi: Hämta och ta bort leads från en statisk lista

I del 1 av den här serien diskuterade jag hur det var möjligt att börja använda REST API via Boomi med Boomi HTTP-anslutaren, särskilt att få den autentiseringstoken som behövs för att få åtkomst till REST API och lagra den i en processvariabel. Härnäst börjar vi ringa samtal till Marketo, och i den här avbetalningen visar jag hur du kan både [Hämta flera leads efter list-ID](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists) och [ta bort leads från lista](/help/rest-api/lead-database.md). Var särskilt uppmärksam på borttagningen av leads från en lista eftersom det finns en mycket &quot;svagt dokumenterad&quot; och subtil aspekt av Boomi på jobbet som jag expanderar när vi kommer dit.

I nästa avbetalning utökar vi den här funktionaliteten för att börja göra intressanta saker som att hämta lead-aktivitet, men det är en blogg till en annan dag. För den här avbetalningen ska vi titta på de andra och tredje markerade områdena. Som recension har jag inkluderat de JSON-svar vi behöver nedan. Kom ihåg att om du vill skapa en JSON-profil i Boomi behöver du bara skapa en profilkomponent av typen JSON, klicka på Importera och markera filen. Boomi gör resten och extrapolerar saker som om flera ID:n ska vara tillåtna. Exempel-JSON för [Hämta flera leads efter list-ID](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Static-Lists)

```json
{
  "requestId": "",
  "success": true,
  "nextPageToken": "",
  "result": [
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    },
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    },
    {
      "id": 0,
      "email": "",
      "firstName": "",
      "lastName": ""
    }
  ]
}
```

Exempel-JSON för [Ta bort leads från listbegäran](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE)

```json
{
   "input":[
      {
         "id": ""
      },
      {
         "id": ""
      }
   ]
}
```

Exempel-JSON för [Ta bort leads från listsvar](https://developer.adobe.com/marketo-apis/api/mapi/#operation/removeLeadsFromListUsingDELETE)

```json
{
  "requestId": "",
  "success": true,
  "result": [
    {
      "id": 1,
      "status": ""
    },
    {
      "id": 2,
      "status": "",
      "reasons": [
        {
          "code": "",
          "message": ""
        }
      ]
    }
  ]
}
```

Med Hämta flera leads efter list-ID** Släpp en annan koppling (Get) i processen med samma anslutning som i föregående artikel. Skapa en ny åtgärd med namnet&quot;Hämta flera leads per list-ID&quot; (jag är en klistermärke för konsekvens). Attributen är följande: Begärandeprofil: Ingen (den här använder URL:en för begäran) - Svarsprofiltyp: jSON - Svarsprofil: Skapa en ny profil baserat på svaret på Hämta flera leads per lista-ID ovan. Observera att du kan ändra den så att den returnerar de fält som du vill ha, inte bara de som visas. Det är viktigt att komma ihåg att JSON-svarsprofilen verkligen bör matcha listan med fält som du ber om från REST API, och du bör bara begära de fält som du behöver. I objektet Processegenskaper definierade vi en egenskap med namnet&quot;fields&quot;, som är en kommaavgränsad lista över de fält som du vill att REST ska returnera. och det är listan som måste matcha profilen. Innehållstyp: text/normal (detta är bara en URL-begäran) HTTP-metod: GET (du söker efter detta i REST API-dokument, det finns alltid med i listan) Resurssökväg (lägg till 5) rest/v1/list/listID (ersättningsvariabel) /leads.json?access_token= access_token (ersättningsvariabel) &amp;fields= fields (ersättningsvariabel). På fliken Parametrar i kopplingen kan du ange variabelvärdena, som vi tidigare har fyllt i i processegenskaperna. I nästa avsnitt ska jag tala om hur du kan undvika att fylla i dessa manuellt. Jag hoppar över den del av processen där jag mappar svaret för Hämta flera leads efter list-ID till en platt filprofil och fäster det på en FTP-server eftersom det är en enkel Boomi-funktion.

Ta bort leads från en lista Så den här är intressant, en av mina medarbetare, [Ken Niwa](https://www.linkedin.com/uas/login?session_redirect=https%3A%2F%2Fwww.linkedin.com%2Fprofile%2Fview%3Fid%3D7429494) lärde mig nästa teknik och den är ganska cool och baserad på en Boomi-artikel med namnet&quot;How to Build a POST Request for a RESTful Application&quot; som visas nedan.  ...men första saker först. I processen, som kommer från&quot;Hämta flera leads efter list-ID&quot;, har vi formen Hämta flera leads efter list-ID-svar, och vi måste mappa det till&quot;Ta bort leads från listbegäran&quot; som är ganska enkel, bara mappa det ID som vi fick från leads i den ursprungliga listan till den id-lista som vi skickar till borttagningen av jSON. Släpp sedan en annan koppling med åtgärden &quot;Skicka&quot; med samma anslutning Skapa en ny åtgärd som heter &quot;Ta bort leads från listbegäran&quot;. vars attribut är Request Profile: jSON Content Type: application/json Request Profile: [JSON Profile] Ta bort leads från listbegäran (skapad från ovanstående fil) Svarsprofiltyp: jSON-svarsprofil: [JSON-profil ] Ta bort leads från listsvar (skapad från ovanstående fil) Innehållstyp: application/json HTTP Method: DELETE Resource Path (lägg till 4)/rest v1/lists/listID (ersättningsvariabel) /leads.json?access_token= access_token (ersättningsvariabel) Här är det intressanta med den här kopplingen. Vi kommer INTE att lägga till parametrarna explicit på fliken Koppling. I stället, som artikeln visar, skapar vi dynamiska dokumentegenskaper som har samma namn som ersättningsvariablerna. I det här fallet är variablerna listID och access_token. När du gör det flödar jSON-formen till REST-anropet och parametrarna visas på rätt plats på URL:en. Vi kan inte göra detta med föregående samtal eftersom det är en GET och inte en POST. Så nu har du sett ett GET- och ett POST REST API-anrop och du kan börja se mönstret för att göra dessa REST-anrop. I nästa avbetalning kommer vi att titta närmare på leadaktivitetsexport via REST API, som är lite mer involverat.

Publicerad _2015-02-06_ av _John_

## Bädda in en YouTube Video med Lead Tracking på en Marketo Landing Page

I ett tidigare blogginlägg beskrev jag hur leads i Marketo segmenteras baserat på om de har startat eller avslutat en viss YouTube-video. I det här blogginlägget går vi igenom hur du kan ta implementeringen från det inlägget och använda den på en Marketo-landningssida.

1. Navigera till det program i Marketo där du vill skapa den nya landningssidan. Klicka på Ny lokal resurs och sedan på Landningssida.
1. Namnge landningssidan. Tilldela en sidadress. Välj en mall. Klicka sedan på Skapa.
1. När landningssidan har skapats klickar du på Redigera utkast.
1. Dra HTML-knappen från den högra panelen till huvudarbetsytan till vänster.
1. I rutan Anpassad HTML Editor visas. Klicka sedan på Spara.
1. Justera storleken på ett HTML-element genom att dra ramens kontur. Klicka sedan på Godkänn och Stäng.
1. Testa landningssidans liveversion genom att klicka på Visa godkänd sida. En landningssida med YouTube öppnas i ett nytt fönster. Videon som har startats och slutförts visas i leadets aktivitetslogg som i den första och andra skärmbilden nedan. När data finns i Marketo kan du skapa en smart lista och segmentera leads som har startat eller slutfört en video, vilket visas i skärmbilden nedan. Mer information om API:t för YouTube Iframe finns i [YouTube API-dokumentation](https://developers.google.com/youtube/iframe_api_reference). Mer information om Munchkin finns i [dokumentationen för Marketo-utvecklare](/help/javascript-api/lead-tracking.md).

Publicerad _2015-02-09_ av _Murta_

## Webbspårning med ett enda program med Munchkin

Ett Single Page-program är en webbplats som läser in alla resurser som krävs för att navigera på webbplatsen vid första sidinläsningen. När en användare klickar på en länk läses innehållet in från den första sidan. För användaren fungerar webbplatsen som förväntat eftersom URL:en i adressfältet är som den traditionella sidnavigeringen. Munchkin fungerar bra tillsammans med traditionella webbplatser eftersom Munchkin körs varje gång användarna läser in en ny sida. Om du inte läser in en ny sida i ett program med en sida kommer Munchkin endast att köras en gång. Det jag gör i det här inlägget är att spåra när en användare klickar på en länk och sedan skicka informationen till Munchkin. Vi implementerar detta med Munchkin-funktionen `clickLink`. Nedan visas ett exempel på implementering i jQuery som binder klickningshändelser till Munchkin-metoden `clickLink`. När Munchkin-metoden `clickLink` anropas skickas parametern för den URL som klickades.

```javascript
<script>
$("a").on('click', function(event) {
    var urlThatWasClicked = $(this).attr('href');
    Munchkin.munchkinFunction('clickLink', { href: urlThatWasClicked});
});
</script>
```

Publicerad _2015-02-11_ av _Murta_

## Ändra en Lead-poäng via REST API

Säg att du vill ändra poängen för en lead i Marketo med API:erna. Detta kan du göra med REST API med hjälp av slutpunkten Skapa/uppdatera lead. Nedan visas ett kodexempel i Ruby som visar hur du gör det här anropet.

```ruby
require 'rest_client'
require 'json'

# Build request URL
# Replace AAA-BBB-CCC with your Marketo instance
marketo_instance = "https://AAA-BBB-CCC.mktorest.com"
endpoint = "/rest/v1/leads.json"
# Replace with your access token
auth_token =  "?access_token=" + "ac756f7a-d54d-41ac-8c3c-f2d2a39ee325:ab"
request_url = marketo_instance + endpoint + auth_token

# Build request body
data = { "action" => "updateOnly", "input" => [ { "email" => "<example@email.com>", "leadScore" => "30" } ] }

# Make request
response = RestClient.post request_url, data.to_json, :content_type => :json, :accept => :json

# Returns Marketo API response
puts response
```

I JSON-delen av begäran anger vi `updateOnly` som åtgärd. Detta innebär att begäran endast fungerar om leadet finns, annars misslyckas det. Om du vill skapa en lead om det inte finns någon, anger du `createOrUpdate` som åtgärd. Vi använder leadets e-postadress som primär identifierare för att hitta leadposten i Marketo. Slutligen anger vi värdet för leadets poäng med nyckeln `leadScore`. Det går att uppdatera 300 leads i taget med den här metoden.

Publicerad _2015-02-19_ av _Murta_

## Markera öppna Source-projekt som bygger på Marketo-plattformen: Del tre

Det här är den tredje posten i en serie om framhävande av öppen källkodsprojekt som byggts runt Marketo-plattformen av utvecklarcommunityn. Vi har [en lista på Marketo GitHub-konto](https://github.com/Marketo/Community-Supported-Client-Libraries) där vi kan spåra klientbibliotek och projekt som skapats av Marketo utvecklarcommunity. Nedan finns tre projekt som utvecklats kring Marketo REST API:er. **[UserCommand](http://www.usermind.com/) skapade [&#x200B; ett Node.js-klientbibliotek för Marketo REST API](https://github.com/MadKudu/node-marketo).** **[Arunim Samat](https://github.com/asamat) skapade [&#x200B; ett klientbibliotek i Python för Marketo REST API &#x200B;](https://github.com/asamat/python_marketo).** **[Jacques Lemieux från Marketo](https://www.linkedin.com/in/jalemieux) skapade [&#x200B; ett klientbibliotek i Ruby för Marketo REST API.](https://github.com/jalemieux/mkto_rest)** Vi är glada över att kunna se fler projekt skapade av utvecklarcommunityn på Marketo-plattformen. Om du arbetar med ett öppen källkodsprojekt för Marketo-plattformen [skickar du det till detta GitHub-svar via en pull-begäran](https://github.com/Marketo/Community-Supported-Client-Libraries).

Publicerad _2015-02-20_ av _Murta_

## Infoga ett Marketo-formulär i en RTP-kampanj

Många marknadsförare är intresserade av att lägga in ett Marketo-formulär i en Personalization-kampanj i realtid (Marketo Real-Time). Vare sig det är en kampanj av typen Dialog, In Zone eller Widget RTP kan du kopiera din Form HTML-kod och klistra in den i RTP:s kampanjredigerare. Jag har sett följande exempel på hur det här används: - Få besökare att registrera sig för nyhetsbrevet efter en andra eller tredje klickning på webbplatsen - Snabbt, effektivt anmälningsformulär för webbinarier - Ladda ned en kvalificerad fallstudie - Erbjuda leads som tidigare har slutat prenumerera på nytt Fyll i ett formulär i kampanjen och få det tack eller innehåll som efterfrågats, vilket resulterar i ett mindre klick för att nå era mål. Här följer en förklaring av hur man gör detta och bäddar in ett Marketo Form 2.0 i en Marketo RTP-kampanj. Se ett bra exempel nedan från eMarketer, RTP-användare som tog det ett steg längre och istället för att dirigera besökarna till en tacksida, bestämde sig för att visa ett tackmeddelande i RTP-kampanjen. Koden för det här alternativet anges också nedan. Njut och jag är glad att höra om din erfarenhet av det!

1. Högerklicka på ett godkänt formulär. Välj **Bädda in kod.**
1. Kopiera **koden.**
1. Gå till **Kampanjer** i Marketo RTP.
1. Klicka på **SKAPA NY KAMPANJ**.
1. Klicka på ikonen **HTML** i RTF-redigeraren.
1. Klistra in formulärinbäddningskoden i HTML Source Editor. Klicka på **Uppdatera**.
1. Formuläret visas inte i redigeringsvyn, men du kan förhandsgranska det för att se hur det kommer att visas i en kampanj.
1. Klicka på **Starta** för att starta kampanjen.

### Obs

Alla ändringar i formuläret måste göras i Marketo marknadsföringsaktiviteter i Redigera utkast till formuläret.

### Relaterade artiklar

* [Forms 2.0](/help/javascript-api/forms-api-reference.md)

Publicerad _2015-12-20_ av _Yanir_

## Lägga till en återställningsknapp i ett Marketo-formulär

```javascript
<script src="//app-sj01.marketo.com/js/forms2/js/forms2.min.js"></script>
<form id="mktoForm_116"></form>
<script>MktoForms2.loadForm("//app-sj01.marketo.com", "410-XOR-673", 116,
function(form) { form.getFormElem()[0].querySelector('button[type="submit"]').insertAdjacentHTML('afterend','<button type="reset" class="mktoButton">Reset</button>') });
</script>
```

Publicerad _2015-03-18_ av _Murta_

## Versionsuppdateringar mars 2015

[Marketo REST Asset API släpptes i mars 2015-utgåvan](https://developer.adobe.com/marketo-apis/api/asset/). Detta API ger åtkomst till Marketo filer, mappar, token, e-post och e-postmallobjekt. Observera att två rollbehörigheter lades till för att ge åtkomst till Resurs-API-slutpunkterna: Skrivskyddad Assets, Skrivskyddad Assets. Om din API-användarroll är före publiceringen av resurs-API:erna måste du skapa en ny API-användarroll med dessa behörigheter för att aktivera åtkomst. Annars får du felsvaret 603&quot;Åtkomst nekad&quot;. Förutom releasen av REST Asset API uppdaterades befintliga REST API-slutpunkter. REST API-slutpunkten [&#x200B; för sammanslagningslead &#x200B;](https://developer.adobe.com/marketo-apis/api/mapi/#operation/mergeLeadsUsingPOST)uppdaterades så att flera leads kan sammanfogas. Slutpunkten [Schedule Campaign REST API &#x200B;](https://developer.adobe.com/marketo-apis/api/mapi/#operation/scheduleCampaignUsingPOST) har uppdaterats så att en kampanj kan klonas när en kampanj schemaläggs.

Publicerad _2015-03-23_ av _Murta_

## Utlösa RTP-kampanjer med fördröjning

Med denna anpassade JavaScript kan RTP-användare visa kampanjer några sekunder efter att webbsidan har lästs in. Detta rekommenderas för dialogruta- och widgetkampanjer. Den kan användas för att visa en kampanj efter en fördröjning när besökaren har visat det reguljära innehållet på sidan. Vi rekommenderar att du bara implementerar koden på specifika sidor där kampanjen/kampanjerna ska visas. Du bör inte använda den här koden på alla sidor eftersom den kan påverka prestanda. **Installationsanvisningar** Den anpassade koden skickar en anpassad RTP-datahändelse (t=timeOnPage, d.v.s. t=60) och läser sedan in kampanjen som matchar den här händelsen. Som standard aktiveras den efter 60 sekunder. Du kan anpassa den genom att ändra parametern sendCustomRTPEvent till ett annat nummer. Placera koden omedelbart efter den vanliga RTP-koden:

```javascript
<script>
function sendCustomRTPEvent(a){
 var eventValue="t="+a;
 setTimeout(function(){
  rtp('send', 'event', {value: eventValue});
  rtp('get', 'campaign',true);
 }, 1000 \* a);
}
sendCustomRTPEvent(60); //Seconds
</script>
```

Så här ställer du in en RTP-kampanj att reagera efter en tidsfördröjning: 1. Logga in på ditt RTP-konto 1. Skapa ett nytt segment 1. Lägg till: `t=60` i segmenthändelseavsnittet. En besökare kan bara matcha varje RTP-segment en gång per session, vilket innebär att varje kampanj bara visas en gång (om den inte är inställd på att klistra in).

Publicerad _2015-03-24_ av _Yanir_

## Versionsuppdateringar från april 2015

### Marketo Mobile Engagement SDK v0.3.2

Marketo innehåller nu automatiserad marknadsföring och användarengagemang för mobilappar. Genom att installera [Marketo Mobile SDK](/help/mobile/mobile.md) i din iOS- eller Android-app kan marknadsförare lyssna efter apphändelser och skicka relevanta push-meddelanden.

### REST API-förbättringar

* Anpassade objekt

Nya [anpassade objektslutpunkter](/help/rest-api/custom-objects.md) har introducerats som gör att du kan lista, beskriva och CRUD-visa data som finns i ett anpassat Marketo-objekt.

Observera att rollbehörigheter lades till för att ge åtkomst till API-slutpunkterna för anpassade objekt: Skrivskyddat anpassat objekt, skrivskyddat anpassat objekt. Om din API-användarroll är före releasen av API:erna för anpassade objekt måste du skapa en ny API-användarroll med dessa behörigheter för att aktivera åtkomst. Annars får du felsvaret 603&quot;Åtkomst nekad&quot;.

* Schemalägg kampanj - klonprogram

En ny valfri parameter, cloneToProgramName, introducerades i [schemakampanj-API:t](/help/rest-api/data-ingestion.md). När den här parametern finns kommer kampanjens överordnade program att klonas och den nya kampanjen kommer att schemaläggas. Parametern anger önskat namn för det resulterande programmet.

Publicerad _2015-04-28_ av _Travis Kaufman_

## Synkroniserar e-postavbeställning över instanser

Hanterar du flera instanser av Marketo? Det kan vara en utmaning att synkronisera lead-information mellan instanser. Här beskrivs ett sätt att synkronisera e-postavbeställningar mellan instanser med hjälp av en webkrok som anropar en extern webbtjänst. Den externa webbtjänsten gör en slinga genom varje instans och letar efter det kända lead som utlöste händelsen unsubscribe. När en matchande lead hittas uppdateras fältet&quot;unsubscribed&quot; i motsvarande lead-post. Här är ett diagram som illustrerar idén.  Det är upp till dig att implementera webbtjänsten, men koden nedan bör hjälpa dig att komma igång snabbt!

### Extern webbtjänst

Den externa webbtjänsten utför följande steg för varje Marketo-instans som behöver synkroniseras:

1. Disponerar instansspecifikt REST API [Slutpunkts-URL](/help/rest-api/endpoint-reference.md)
1. Hämtar åtkomsttoken med [Identitet](/help/rest-api/authentication.md)
1. Hämtar en lista med lead-poster som matchar e-postadressen med hjälp av [Hämta flera leads efter filtertyp](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET)
1. Uppdaterar fältet&quot;unsubscribed&quot; för varje lead-post med Skapa/Uppdatera leads

Här följer ett diagram som visar det externa webbtjänstanropet och Marketo REST API-anrop i detalj.  Exempelkoden nedan är inte en webbtjänst som inte ingår i paketet. Det är i stället ett program i konsolläge som du kan skicka argument till via kommandoraden. Avsikten här är att visa hur man anropar lämpliga Marketo API:er för att uppdatera lead-poster mellan instanser. Implementering av webbtjänsten lämnas som en övning för läsaren.
**Exempelkod** Om du vill att exempelkoden ska fungera måste du skapa ett Java-projekt i din favoritutvecklingsmiljö. Därefter måste du göra följande ändringar: 1. Exempelkoden använder [json-simple](https://code.google.com/archive/p/json-simple) för att tolka JSON-strängar. Lägg till den enkla json-burken i Java-projektet. 1. Exempelkoden har en struktur som innehåller metadata för varje Marketo-instans. Placera faktiska värden från instanserna i strukturen enligt följande:

```java
public static String instanceInfo[][] = {
{ "AccountId1", "ClientId1","ClientSecret1" },    // Instance 1 metadata
{ "AccountId2", "ClientId2","ClientSecret2" },    // Instance 2 metadata
{ "AccountId3", "ClientId3","ClientSecret3" }     // Instance 3 metadata
};
```

Du hittar metadata för instansen på panelen Marketo Admin:

* Konto-ID-administratör > Integrering > Munchkin > Munchkin-konto
* Klient-ID och Klienthemlig administratör > Integrering > Startpunkt > Synkronisering för att avbryta prenumeration för e-post > Visa information

Exempelkoden innehåller två kommandoradsargument som simulerar frågeparametrarna&quot;id&quot; och&quot;email&quot; för den externa webbtjänsten som beskrivs ovan.

1. argument[0] = konto-ID
1. argument[1] = E-postadress

Skicka faktiska värden från instansen som argument till programmet. Här är en projektkonfigurationsskärmbild från Intellij IDEA.

**SyncEmailUnsubscribe.java**

```java
package com.marketo;

import org.json.simple.JSONArray;
import org.json.simple.JSONObject;
import org.json.simple.parser.JSONParser;
import org.json.simple.parser.ParseException;

import javax.net.ssl.HttpsURLConnection;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.Iterator;
import java.util.NoSuchElementException;
import java.util.Scanner;

public class SyncEmailUnsubscribe {
    // Define Marketo instance meta data here.
    // Each row contains three elements: Account Id, Client Id, Client Secret.
    // For example:
    //  public static String instanceData[][] = {
    //    {"111-AAA-222", "2f4a4435-f6fa-4bd9-3248-098754982345", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W"},
    //    {"222-BBB-333", "5f4a6657-f6fa-4cd9-4356-123083238821", "gfjgfIVE9h4Jjcl59cOMAKFSk78ut12W"},
    //    {"444-CCC-444", "9f4a4678-f6fa-4dd9-7735-908713247721", "xzcxvbVE9h4Jjcl59cOMAKFSk78ut12W"}
    //  };
    //
    public static String instanceData[][] = {
            // ADD YOUR INSTANCE META DATA HERE
    };

    public static void main(String[] args) {
        String accountId = args[0];     // Account id that processed the unsubscribe
        String emailAddress = args[1];  // Email address of lead that unsubscribed

        SyncEmailUnsubscribe seu = new SyncEmailUnsubscribe();

        // Loop through each Marketo instance
        for (int i = 0; i < instanceData.length; i++) {

            // Make sure we skip instance that triggered the webhook
            if (!accountId.equals(instanceData[i][0])) {
                String endpointUrl = String.format("https://%s.mktorest.com", instanceData[i][0]);

                // Generate access token
                String identityUrl = String.format("%s/identity/oauth/token?grant_type=client_credentials&client_id=%s&client_secret=%s", endpointUrl, instanceData[i][1], instanceData[i][2]);
                String token = seu.getToken(identityUrl);

                // Get lead records for given email address (may be duplicates)
                String getLeadsUrl = String.format("%s/rest/v1/leads.json?access_token=%s&filterType=email&filterValues=%s", endpointUrl, token, emailAddress);
                String leads = seu.getLeads(getLeadsUrl);

                // Update unsubscribed field in lead record
                String updateLeadsUrl = String.format("%s/rest/v1/leads.json?access_token=%s", endpointUrl, token);
                seu.updateLeads(updateLeadsUrl, leads, accountId);
            }
        }

        System.exit(0);
    }

    // Call Identity Service to generate access token
    public String getToken(String url) {
        // Call Identity Service
        String tokenData = getData(url);

        // Convert response into JSONObject
        JSONParser parser = new JSONParser();
        Object obj = null;
        try {
            obj = parser.parse(tokenData);
        } catch (ParseException pe) {
            System.out.println("position: " + pe.getPosition());
            System.out.println(pe);
        }

        // Retrieve access_token
        JSONObject jsonObject = (JSONObject)obj;
        return jsonObject.get("access_token").toString();
    }

    // Call Get Multiple Leads by Filter Type Service to get lead records
    public String getLeads(String url) {
        return getData(url);
    }

    // Call Create/Update Lead Service to update "unsubscribed" flag in lead record
    public void updateLeads(String url, String leads, String account) {
        JSONObject body = composeBody(leads, account);
        if (body != null) {
            postData(url, body);
        }
    }

    // Compose JSON body for Create/Update Leads Service
    private JSONObject composeBody(String leads, String account) {
        JSONObject body = new JSONObject();

        // Convert leads into JSONObject
        JSONParser parser = new JSONParser();
        Object obj = null;
        try {
            obj = parser.parse(leads);
        } catch (ParseException pe) {
            System.out.println("position: " + pe.getPosition());
            System.out.println(pe);
        }
        JSONObject leadsObj = (JSONObject)obj;

        Object success = leadsObj.get("success");
        if (success.equals(true)) {
            body.put("action", "updateOnly");
            body.put("lookupField", "id");
            body.put("asyncProcessing", "true");

            // Build array of lead objects
            JSONArray input = new JSONArray();
            JSONArray result = (JSONArray) leadsObj.get("result");
            Iterator<JSONObject> iterator = result.iterator();
            while (iterator.hasNext()) {
                JSONObject leadIn = (JSONObject)iterator.next();
                JSONObject lead = new JSONObject();
                lead.put("id", leadIn.get("id"));
                lead.put("unsubscribed", "true");
                lead.put("unsubscribedReason", "Cross instance synch triggered by webhook from: " + account);
                input.add(lead);
            }

            body.put("input", input);
        }
        return body;
    }

    // HTTP POST request
    private String postData(String endpoint, JSONObject body) {
        String data = "";
        try {
            // Make request
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("POST");
            urlConn.setAllowUserInteraction(false);
            urlConn.setDoOutput(true);
            urlConn.setRequestProperty("Content-type", "application/json");
            urlConn.setRequestProperty("accept", "application/json");
            urlConn.connect();
            OutputStream os = urlConn.getOutputStream();
            os.write(body.toJSONString().getBytes());
            os.close();
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                System.out.println("Status: 200");
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
                System.out.println(data);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    // HTTP GET request
    private String getData(String endpoint) {
        String data = "";
        try {
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("GET");
            urlConn.setAllowUserInteraction(false);
            urlConn.setDoOutput(true);
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                System.out.println("Status: 200");
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
                System.out.println(data);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    private String convertStreamToString(InputStream inputStream) {
        try {
            return new Scanner(inputStream).useDelimiter("A").next();
        } catch (NoSuchElementException e) {
            return "";
        }
    }
}
```

### Marketo Setup

Utför följande steg för varje Marketo-instans som du vill synkronisera.

1. Skapa en anpassad tjänst med rollbehörighet: Läs/skriv lead. Om du inte är van vid att skapa en anpassad tjänst klickar du [här](/help/rest-api/custom-services.md).
1. Skapa en webkrok som anropar din externa webbtjänst. Klicka [här](/help/webhooks/webhooks.md) om du inte är van vid att skapa webbböcker.
1. Lägg till webkrok som ett flödessteg i Smart Campaign.

Skärmbilden nedan visar hur du skapar en webkrok för att anropa den tjänst som anges ovan med hjälp av tokens för att automatiskt fylla i frågeparametrarna. Nu när vi har skapat vår webkrok kan vi lägga till den i en Smart Campaign som en flödesåtgärd.  Den smarta listan ska innehålla utlösaren&quot;Avsluta prenumeration på e-post&quot;.

### Validering

Om du vill testa allt detta skapar du ett lead med samma e-postadress i flera Marketo-instanser. Kontrollera att du äger e-postadressen! I en instans utlöser du en åtgärd för att skicka e-postflöde, öppnar det resulterande e-postmeddelandet och klickar på Avbryt prenumeration. Om du vill validera resultaten loggar du in på de andra instanserna och kontrollerar lead-posterna som är kopplade till e-postadressen. Kryssrutan &quot;Avsluta prenumeration&quot; ska vara markerad och fältet &quot;Orsak till prenumerationen&quot; ska innehålla en anteckning med det källkonto-ID som initierade synkroniseringen.

Publicerad _2015-05-11_ av _David_

## Synkronisera leaddataändringar med hjälp av REST API

Posten presenterade ett kodexempel som skulle kunna köras regelbundet för att avfråga Marketo om det finns uppdateringar. Tanken var att använda Marketo API:er för att identifiera ändringar i lead-data och extrahera lead-data som har ändrats. Dessa data kan sedan skickas till ett externt system för synkronisering. Kodexemplet som presenterades använde vårt SOAP API. Vi har ett [nytt sätt att gå](https://www.youtube.com/watch?v=G-7ZJjLy5D8&feature=youtu.be), och på det sättet använder vi [Marketo REST API](/help/rest-api/rest-api.md). I det här inlägget visas hur du kan uppnå samma mål med hjälp av två REST-slutpunkter: [Hämta lead-ändringar](/help/rest-api/rest-api.md), [Hämta lead med ID](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET). Programmet innehåller två huvudsteg:

1. Anropa Hämta lead-ändringar om du vill generera en lista med alla lead-ID:n som antingen hade specifika lead-fält ändrade eller som lades till under en viss tidsperiod.
1. Anropa Hämta lead-ID för varje lead-ID i listan för att hämta fältdata från lead-posten.

Vi tar de data som hämtas i steg 2 och formaterar dem för konsumtion i ett externt system.  **Programindata** Som standard &quot;återgår&quot; programmet en dag från dagens datum för att söka efter ändringar. Du kan till exempel köra det här programmet vid samma tidpunkt varje dag. Om du vill gå längre tillbaka i tiden kan du ange antalet dagar som ett kommandoradsargument och på så sätt öka tidsfönstret. Programmet innehåller flera variabler som du kan ändra: CUSTOM_SERVICE_DATA - Detta innehåller dina [anpassade Marketo-tjänstdata](/help/rest-api/custom-services.md) (konto-ID, klient-ID, klienthemlighet). LEAD_CHANGE_FIELD_FILTER - Detta innehåller en kommaseparerad lista över lead-fält som vi kommer att granska för ändringar. READ_BATCH_SIZE - Det här är antalet poster som ska hämtas åt gången. Använd detta för att justera svaret på kroppsstorleken. **Programutdata** Programmet samlar alla ändrade lead-poster och formaterar dem i JSON som en array med lead-objekt enligt följande:

```json
{
    "result": [
        {
            "leadId": "318592",
            "updatedAt": "2015-07-22T19:19:07Z",
            "firstName": "David",
            "lastName": "Everly",
            "email": "<deverly@marketo.com>"
        },

        ...more lead objects here...
    ]
}
```

Tanken är att du sedan kan skicka denna JSON som nyttolast för begäran till en extern webbtjänst för att synkronisera data. **Programlogik** Först skapar vi vårt tidsfönster, skapar REST-slutpunkts-URL:er och hämtar en åtkomsttoken för autentisering. Därefter startar vi en Get Paging Token/Get Lead Changes-loop som körs tills vi tar bort leveransen av leadändringar. Syftet med den här slingan är att samla ihop en lista med unika lead-ID:n så att vi kan skicka dem till Get Lead by Id senare i programmet. I det här exemplet anger vi att Hämta lead-ändringar ska söka efter ändringar i följande fält: firstName, lastName, email. Du kan själv välja valfri kombination av fält. Hämta lead-ändringar returnerar &quot;result&quot;-objekt som innehåller ett aktivitets-ID, som vi kan använda för att filtrera resultaten. Obs! Du kan få en lista över aktivitetstyper genom att anropa REST-slutpunkten för [Get Activity Types](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getAllActivityTypesUsingGET). Vi är intresserade av två aktivitetstyper som returneras: 1. Ny lead (12)

```json
{
    "id": 12024682,
    "leadId": 318581,
    "activityDate": "2015-03-17T00:18:41Z",
    "activityTypeId": 12,
    "primaryAttributeValueId": 318581,
    "primaryAttributeValue": "David Everly",
    "attributes": [
        {
            "name": "Created Date",
            "value": "2015-03-16"
        },
        {
            "name": "Source Type",
            "value": "New lead"
        }
    ]
}
```

1. Ändra datavärde (13) Vi kan se vilket fält som har ändrats genom att granska egenskapen name i svaret på Ändra datavärde.

```json
{
    "id": 12024689,
    "leadId": 318581,
    "activityDate": "2015-03-17T22:58:18Z",
    "activityTypeId": 13,
    "fields": [
        {
            "id": 31,
            "name": "lastName",
            "newValue": "Evely",
            "oldValue": "Everly"
        }
    ],
    "attributes": [
        {
            "name": "Source",
            "value": "Web form fillout"
        }
    ]
}
```

När någon av dessa två typer av aktiviteter returneras lagrar vi det associerade lead-ID:t i en lista. När vi har skapat vår lista kan vi iterera genom att anropa Get Lead by Id för varje objekt. Detta hämtar de senaste lead-data för varje lead i listan. I det här exemplet hämtar vi följande lead-fält: `leadId`, `updatedAt`, `firstName`, `lastName` och `email`. Du kan själv välja valfri kombination av fält. Detta görs genom att fältparametern anges till Hämta lead med ID. Slutligen ska vi JSONifiera resultatet som en array med lead-objekt enligt beskrivningen ovan.

**Programkod**

```java
package com.marketo;

// minimal-json library (<https://github.com/ralfstx/minimal-json>)
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;
import com.eclipsesource.json.JsonValue;

import java.io.\*;
import java.net.MalformedURLException;
import java.net.URL;
import java.text.DateFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.\*;

import javax.net.ssl.HttpsURLConnection;

public class LeadChanges {
    //
    // Define Marketo REST API access credentials: Account Id, Client Id, Client Secret.  For example:
    //    public static String CUSTOM_SERVICE_DATA[] =
    //      {"111-AAA-222", "2f4a4435-f6fa-4bd9-3248-098754982345", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W"};
    //
    private static final String CUSTOM_SERVICE_DATA[] =
            {INSERT YOUR CUSTOM SERVICE DATA HERE};

    // Lead fields that we are interested in
    private static final String LEAD_CHANGE_FIELD_FILTER = "firstName,lastName,email";

    // Number of lead records to read at a time
    private static final String READ_BATCH_SIZE = "200";

    // Activity type ids that we are interested in
    private static final int ACTIVITY_TYPE_ID_NEW_LEAD = 12;
    private static final int ACTIVITY_TYPE_ID_CHANGE_DATA_VALE = 13;

    public static void main(String[] args) {
        // Command line argument to set how far back to look for lead changes (number of days)
        int lookBackNumDays = 1;
        if (args.length == 1) {
            lookBackNumDays = Integer.parseInt(args[0]);
        }

        // Establish "since date" using current timestamp minus some number of days (default is 1 day)
        Calendar cal = Calendar.getInstance();
        cal.add(Calendar.DAY_OF_MONTH, -lookBackNumDays);
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
        String sinceDateTime = sdf.format(cal.getTime());

        // Compose base URL
        String baseUrl = String.format("https://%s.mktorest.com",
                CUSTOM_SERVICE_DATA[0]);

        // Compose Identity URL
        String identityUrl = String.format("%s/identity/oauth/token?grant_type=%s&client_id=%s&client_secret=%s",
                baseUrl, "client_credentials", CUSTOM_SERVICE_DATA[1], CUSTOM_SERVICE_DATA[2]);

        // Call Identity API
        JsonObject identityObj = JsonObject.readFrom(getData(identityUrl));
        String accessToken = identityObj.get("access_token").asString();

        // Compose URLs for Get Lead Changes, and Get Paging Token
        String leadChangesUrl = String.format("%s/rest/v1/activities/leadchanges.json?access_token=%s&fields=%s&batchSize=%s",
                baseUrl, accessToken, LEAD_CHANGE_FIELD_FILTER, READ_BATCH_SIZE);
        String pagingTokenUrl = String.format("%s/rest/v1/activities/pagingtoken.json?access_token=%s&sinceDatetime=%s",
                baseUrl, accessToken, sinceDateTime);

        HashSet leadIdList = new HashSet();

        // Call Get Paging Token API
        JsonObject pagingTokenObj = JsonObject.readFrom(getData(pagingTokenUrl));
        if (pagingTokenObj.get("success").asBoolean()) {

            String nextPageToken = pagingTokenObj.get("nextPageToken").asString();
            boolean moreResult;

            do {
                moreResult = false;

                // Call Get Lead Changes API
                JsonObject leadChangesObj = JsonObject.readFrom(getData(String.format("%s&nextPageToken=%s",
                        leadChangesUrl, nextPageToken)));
                if (leadChangesObj.get("success").asBoolean()) {
                    moreResult = leadChangesObj.get("moreResult").asBoolean();
                    nextPageToken = leadChangesObj.get("nextPageToken").asString();

                    if (leadChangesObj.get("result") != null) {
                        JsonArray resultAry = leadChangesObj.get("result").asArray();
                        for (JsonValue resultObj : resultAry) {
                            int activityTypeId = resultObj.asObject().get("activityTypeId").asInt();


                            // Store lead ids for later use
                            boolean storeThisId = false;
                            if (activityTypeId == ACTIVITY_TYPE_ID_NEW_LEAD) {
                                storeThisId = true;
                            } else if (activityTypeId == ACTIVITY_TYPE_ID_CHANGE_DATA_VALE) {
                                // See if any of the changed fields are of interest to us
                                JsonArray fieldsAry = resultObj.asObject().get("fields").asArray();
                                for (JsonValue fieldsObj : fieldsAry) {
                                    String name = fieldsObj.asObject().get("name").asString();
                                    if (LEAD_CHANGE_FIELD_FILTER.contains(name)) {
                                        storeThisId = true;
                                    }
                                }

                            }

                            if (storeThisId) {
                                leadIdList.add(resultObj.asObject().get("leadId").toString());
                            }
                        }
                    }
                }

            } while (moreResult);
        }

        JsonObject result = new JsonObject();
        JsonArray leads = new JsonArray();

        for (Object o : leadIdList) {
            String leadId = o.toString();

            // Compose Get Lead by Id URL
            String getLeadUrl = String.format("%s/rest/v1/lead/%s.json?access_token=%s",
                    baseUrl, leadId, accessToken);

            // Call Get Lead by Id API
            JsonObject leadObj = JsonObject.readFrom(getData(getLeadUrl));
            if (leadObj.get("success").asBoolean()) {
                if (leadObj.get("result") != null) {
                    JsonArray resultAry = leadObj.get("result").asArray();
                    for (JsonValue resultObj : resultAry) {

                        // Create lead object
                        JsonObject lead = new JsonObject();
                        lead.add("leadId", leadId);
                        lead.add("updatedAt", resultObj.asObject().get("updatedAt").asString());
                        lead.add("firstName", resultObj.asObject().get("firstName").asString());
                        lead.add("lastName", resultObj.asObject().get("lastName").asString());
                        lead.add("email", resultObj.asObject().get("email").asString());

                        // Add lead object to leads array
                        leads.add(lead);
                    }
                }
            }
        }

        // Add leads array to result object
        result.add("result", leads);

        // Print out result object
        System.out.println(result);

        System.exit(0);
    }

    // Perform HTTP GET request
    private static String getData(String endpoint) {
        String data = "";
        try {
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("GET");
            urlConn.setAllowUserInteraction(false);
            urlConn.setDoOutput(true);
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    private static String convertStreamToString(InputStream inputStream) {
        try {
            return new Scanner(inputStream).useDelimiter("A").next();
        } catch (NoSuchElementException e) {
            return "";
        }
    }
}
```

Så där har du det, [livet är en glad låt](https://www.youtube.com/watch?v=zFaBwZDywLk). Mycket nöje!

Publicerad _2015-07-31_ av _David_

## Versionsuppdateringar från maj 2015

### REST API

* Möjligheter-API. Nya [affärsmöjlighetsslutpunkter](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Opportunities) har introducerats som gör att du kan lista, beskriva och CRUD-visa data som finns i ett Marketo-affärsmöjlighetsobjekt.

Obs! Rollbehörigheter lades till för att ge åtkomst till säljprojektsslutpunkterna: Skrivskyddad möjlighet, Skrivskyddad möjlighet. Om din API-användarroll är före lanseringen av API:erna för säljprojekt måste du skapa en ny API-användarroll med de här behörigheterna för att aktivera åtkomst. Annars får du felsvaret 603&quot;Åtkomst nekad&quot;.

* Resurs-API - kodfragment. Nya [resursslutpunkter för fragment](https://developer.adobe.com/marketo-apis/api/asset/#snippet_endpoints) har introducerats så att du kan ändra fragmentobjekt programmatiskt. Fragment kan användas som dynamiska innehållsblock i e-postmeddelanden och landningssidor.
* Leads-API - uppdatera Leads-partition. En ny [lead-slutpunkt för partitioner](https://developer.adobe.com/marketo-apis/api/mapi/#operation/updatePartitionsUsingPOST) har lagts till så att du kan uppdatera partitionen för en eller flera leads.
* Ett problem har korrigerats där Lead-relaterade API:er saknade tidszonsförskjutning i attributen&quot;createdAt&quot; och&quot;updatedAt&quot;.
* Ett problem har korrigerats där Schedule Campaign inte returnerade rätt felkod när det högsta antalet anrop per dag hade överskridits.
* Ett problem har korrigerats där Get Folder by Id ibland returnerade null för attributen &quot;parent&quot; och &quot;description&quot;.
* Ett problem har korrigerats där Godkänn e-post med ID i vissa fall orsakade ett systemfel.
* Ett problem har korrigerats där Create Token by Folder Id skulle generera en token som inte kunde användas i vissa fall.

### Real-Time Personalization (RTP)

* Rekommendations-API för multimedia. Nya funktioner för [multimedierekommendation](/help/javascript-api/web-personalization.md) har lagts till i RTP JavaScript API. Rekommendationen för multimediematerial engagerar webbbesökarna med det mest relevanta innehållet som bygger på maskininlärning och prediktiv analys. Förbättra ert innehåll med textbeskrivningar och bilder och bädda in olika innehållsrekommendationer på er webbplats.

### Mobile Engagement SDK

iOS v0.3.4/Android v0.3.3

* Anpassade åtgärder. Lagt till möjlighet att spåra användarinteraktion genom att skicka anpassade åtgärder. Mer information finns i&quot;Skickar anpassade åtgärder&quot; [här](/help/mobile/mobile.md).
* Metoden trackPushNotification har tagits bort.

Publicerad _2015-05-26_ av _David_

## Versionsuppdateringar från juni 2015

### REST API

* Företags-API

Nya [företagsslutpunkter](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Companies) har introducerats som gör att du kan lista, beskriva och återanvända data som finns i ett Marketo-företagsobjekt.

Obs! Rollbehörigheter lades till för att ge åtkomst till programslutpunkterna: Read-Only Company, Read-Write Company. Om din API-användarroll är före lanseringen av företags-API:erna måste du uppdatera din API-användarroll med dessa behörigheter för att aktivera åtkomst. Annars får du felsvaret 603 &quot;Åtkomst nekad&quot;

* Resurs-API - program

Tillgångs-API:t har uppdaterats med stöd för både programmappar och programmappar. Flera resurs-API:er hade accepterat ett mapp-ID i begäran i form av ett heltal. Mappens ID kan skickas som en del av URI:n, eller som en del av begärandetexten, beroende på API:t.

Nu måste du ange mapptypen bredvid mapp-ID:t. Mapptypen är antingen Program eller Mapp. &quot;Folder&quot; anger en programmapp, medan &quot;Program&quot; anger en programmapp. Mapptypen anges på två olika sätt, beroende på vilket API du anropar:

1. Använd datastrukturen FolderIdType. FolderIdType är ett enkelt JSON-block som innehåller ID:t och typparet enligt följande:

`{ "id" : **id**, "type" : "**type**" }`

Där **id** är mapp-id:t och **type** är mapptypen. Tillåtna värden för **type** är &quot;Folder&quot; eller &quot;Program&quot;.

Exempel - Skapa mapp

`POST /asset/v1/folders.json`

`parent={"id":416,"type":"Folder"}&name=Test Folder&description=This is a test folder`

1. Använd den befintliga id-parametern och ange dess typ med en typparameter.

Exempel - Hämta mapp med ID

`GET /rest/asset/v1/folder/1016.json?type=Program`

Alla API-svar som innehöll ett mapp-ID i Result-objektet kommer nu även att innehålla ett folderId-attribut vars värde är FolderIdType. Detta kan användas för att bestämma mapptypen för ett visst mapp-ID.

Exempel - Hämta mapp efter namn

`GET /rest/asset/v1/folder/byName.json?name=Social Media`

```json
"result": [
{
...

"folderId": {"id":341, "type": "Program"},
...

"id":"341"
}
]
```

Du kan använda API:t [Bläddra bland mappar](https://developer.adobe.com/marketo-apis/api/asset/browse-folders/) om du vill ta reda på mapptypen för ett visst ID.

Attributet &quot;type&quot; i API-svar har bytt namn till &quot;folderType&quot;. Detta för att undvika förvirring med elementet &quot;type&quot; i FolderIdType.

Från detta exempel:

&quot;**typ**&quot;:&quot;Marknadsmapp&quot;

till detta:

&quot;**folderType**&quot;: &quot;Marknadsmapp&quot;

### Mobile Engagement SDK

iOS 0.3.5

* Korrigerade ett problem där dialogrutan för att ställa in testenhet kördes på huvudtråden. [MOB-638]
* Felhantering har lagts till om det inte går att registrera testenheten. [MOB-639]

Android 0.3.3

* Android:configChanges-attributet har lagts till i AndroidManifest.xml `<activity>` -elementet för att förloppsdialogen inte ska stängas när du har lagt till en testenhet och ändrat orienteringen. [MOB-687]

Publicerad _2015-06-30_ av _David_

## Personalization i appen (Beta) med RTP API

Flera av våra kunder tillhandahåller webbappslösningar för sina användare, och vi får förfrågningar om de kan använda Marketo Real-Time Personalization (RTP) i sin säkra webbappmiljö. Svaret är ja! Vi har släppt ett API för meddelanden i appen, så att ni kan personalisera innehåll och marknadsföra aktiviteter som webbinarier, nya funktionsreleaser, merförsäljning och engagera användarna baserat på era webbappdata. Anpassa till exempel innehåll i appen för:

* Testerbjudanden baserade på användaraktivitet
* Olika prenumerationstyper (merförsäljning, korsförsäljning eller webbinarier)
* Nya funktioner som är relevanta för användaraktiviteten

**Använd fallexempel** Marketo Customer Success Team använder In-App Web Personalization för att kommunicera med specifika prenumerationstyper (Spark, Standard, Select eller Enterprise) med personaliserat innehåll, för att se till att de ser progressiva kampanjer och vårdar användare i appen utifrån deras engagemang. Låt oss se hur detta kan göras för en användare med en Enterprise-prenumeration. **Förutsättning** Förstå [API:t för RTP-användarkontext](/help/javascript-api/web-personalization.md). **Aktivera API:t för användarkontext** från Marketo Support om du vill aktivera API:t för användarkontext för ditt RTP-konto. **Ange anpassad variabel** Det finns 5 anpassade variabelfack i RTP som data ska skickas till. I det här exemplet skickar vi en användarprenumeration av typen Enterprise till Custom Variable 1.

`rtp('set', 'customVar1', 'Enterprise');`

**Skapa ett nytt RTP-segment** Gå till **Segment**, klicka på **SKAPA NYTT**.

1. Dra **API:t för användarkontext** till segmentverktyget.
1. Välj den **anpassade variabeln** (prenumerationstyp) som **värdet** är **Enterprise**.

**Visa kampanj baserad på tidigare besökshistorik** Om du vill visa besökaren en annan kampanj om de redan klickat på en kampanj under ett tidigare besök.

1. I **API:t för användarkontext** klickar du på **(+)** för att lägga till ett annat API-attribut för användarkontext
1. Lägg till operatorn **(AND eller OR).**
1. Välj **Kampanjer - klickad.** Ange **kampanj-ID** som kampanjens ID. (Se anteckningen nedan om hur du hittar kampanj-ID:t.)
1. Klicka på **SPARA OCH DEFINIERA KAMPANJ** för att skapa kampanjen.

Generellt sett matchar det här segmentet om en besökare är associerad med den anpassade variabeln (prenumerationstyp) som är lika med Enterprise och om de klickade på kampanjen (ID: 5390) vid ett tidigare besök. Nästa steg är att definiera en personaliserad kampanj för det här segmentet. Skärmbilden nedan visar en kampanj i en dialogruta för RTP (längst ned till vänster) som visas på sidan My Marketo och som marknadsför ett webbinarium för Enterprise-användare.  **OBS!** **Hitta kampanj-ID:t** Gå till **Kampanjer**, håll muspekaren över **kampanjnamnet** för att hitta kampanj-ID:t.
Publicerad _2015-06-17_ av _David_

## Skicka transaktionsmeddelanden med Marketo REST API: Del 1

Det finns några konfigurationskrav i Marketo för att köra det anrop som krävs med Marketo REST API.

* Mottagaren måste ha en post i Marketo
* Det måste finnas ett Transactional Email som skapats och godkänts i din Marketo-instans.
* Det måste finnas en aktiv utlösarkampanj där Campaign begärs, Source: Web Service API, som är konfigurerad för att skicka e-postmeddelandet

[Skapa och godkänn din e-postadress](https://experienceleague.adobe.com/sv/docs/marketo/using/home) först. Om e-postmeddelandet verkligen är transaktionellt, kommer du troligen att behöva ställa in det på operativ nivå, men se till att det lagligen kvalificerar sig som operativt. Detta konfigureras från med redigeringsskärmen under E-poståtgärder > E-postinställningar. Godkänn det så är vi redo att skapa vår kampanj. Om du inte har skapat några kampanjer tidigare kan du läsa artikeln [Skapa en ny smart kampanj](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/creating-a-smart-campaign/create-a-new-smart-campaign) på docs.marketo.com. När ni väl har skapat er kampanj måste vi gå igenom dessa steg. Konfigurera din smarta lista med Campaign är en begärd utlösare: Nu måste vi konfigurera flödet så att det pekar ett skicka e-post-steg mot vår e-post. Innan aktiveringen görs måste du göra vissa inställningar på fliken Schema. Om det här e-postmeddelandet någonsin endast ska skickas en gång till en viss post låter du kvalificeringsinställningarna vara som de är. Men om det krävs att de får e-postmeddelandet flera gånger vill du justera det antingen varje gång eller till någon av de tillgängliga caddenserna. Nu är vi redo att aktivera.

### Skicka API-anrop

**Obs!** I Java-exemplen nedan använder vi paketet minimum-json för att hantera JSON-representationer i vår kod. Du kan läsa mer om det här projektet här: [https://github.com/ralfstx/minimal-json](https://github.com/ralfstx/minimal-json) Den första delen av att skicka ett transaktionsmeddelande via API:t är att se till att det finns en post med motsvarande e-postadress i din Marketo-instans och att vi har tillgång till dess lead-ID. För det här inlägget antar vi att e-postadresserna redan finns i Marketo och att vi bara behöver hämta postens ID. För detta använder vi anropet [Hämta flera leads efter filtertyp](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET), och vi återanvänder en del av Java-koden från föregående inlägg. Låt oss titta på vår huvudmetod för att begära kampanjen:

```java
package dev.marketo.blog_request_campaign;

import com.eclipsesource.json.JsonArray;

public class App
{
    public static void main( String[] args )
    {
     //Create an instance of Auth so that we can authenticate with our Marketo instance
        Leads leadsRequest = new Leads(auth).setFilterType("email").addFilterValue("<requestCampaign.test@marketo.com>");

        //Create and parameterize an instance of Leads
        //Set your email filterValue appropriately
        Leads leadsRequest = new Leads(auth).setFilterType("email").addFilterValue("test.requestCamapign@example.com");

        //Get the inner results array of the response
        JsonArray leadsResult = leadsRequest.getData().get("result").asArray();

        //Get the id of the record indexed at 0
        int lead = leadsResult.get(0).asObject().get("id").asInt();

        //Set the ID of your campaign from Marketo
        int campaignId = 0;
        RequestCampaign rc = new RequestCampaign(auth, campaignId).addLead(lead);

        //Send the request to Marketo
        rc.postData();
    }
}
```

För att komma till dessa resultat från JsonObject-svaret på leadRequest måste vi skriva lite kod. För att kunna hämta det första resultatet i arrayen måste vi extrahera arrayen från JsonObject och få objektet indexerat på 0:

`JsonArray leadsResult = leadsRequest.getData().get("result").asArray();`
`int leadId = leadsResult.get(0).asObject().get("id").asInt();`

Härifrån behöver vi bara ringa Request Campaign. För detta krävs ID i URL:en för begäran och en array med JSON-objekt som innehåller en medlem, &quot;id&quot;. Låt oss titta på koden för detta:

```java
package dev.marketo.blog_request_campaign;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import javax.net.ssl.HttpsURLConnection;
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

public class RequestCampaign {
 private String endpoint;
 private Auth auth;
 public ArrayList leads = new ArrayList();
 public ArrayList tokens = new ArrayList();

 public RequestCampaign(Auth auth, int campaignId) {
  this.auth = auth;
  this.endpoint = this.auth.marketoInstance + "/rest/v1/campaigns/" + campaignId + "/trigger.json";
 }
 public RequestCampaign setLeads(ArrayList leads) {
  this.leads = leads;
  return this;
 }
 public RequestCampaign addLead(int lead){
  leads.add(lead);
  return this;
 }
 public RequestCampaign setTokens(ArrayList tokens) {
  this.tokens = tokens;
  return this;
 }
 public RequestCampaign addToken(String tokenKey, String val){
  JsonObject jo = new JsonObject().add("name", tokenKey);
  jo.add("value", val);
  tokens.add(jo);
  return this;
 }
 public JsonObject postData(){
  JsonObject result = null;
  try {
   JsonObject requestBody = buildRequest(); //builds the Json Request Body
   String s = endpoint + "?access_token=" + auth.getToken(); //takes the endpoint URL and appends the access_token parameter to authenticate
   System.out.println("Executing RequestCampaign calln" + "Endpoint: " + s + "nRequest Body:n"  + requestBody);
   URL url = new URL(s);
   HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection(); //Return a URL connection and cast to HttpsURLConnection
   urlConn.setRequestMethod("POST");
   urlConn.setRequestProperty("Content-type", "application/json");
            urlConn.setRequestProperty("accept", "text/json");
            urlConn.setDoOutput(true);
   OutputStreamWriter wr = new OutputStreamWriter(urlConn.getOutputStream());
   wr.write(requestBody.toString());
   wr.flush();
   InputStream inStream = urlConn.getInputStream(); //get the inputStream from the URL connection
   Reader reader = new InputStreamReader(inStream);
   result = JsonObject.readFrom(reader); //Read from the stream into a JsonObject
   System.out.println("Result:n" + result);
  } catch (MalformedURLException e) {
   e.printStackTrace();
  } catch (IOException e) {
   e.printStackTrace();
  }
  return result;
 }

 private JsonObject buildRequest(){
  JsonObject requestBody = new JsonObject(); //Create a new JsonObject for the Request Body
  JsonObject input = new JsonObject();
  JsonArray leadsArray = new JsonArray();
  for (int lead : leads) {
   JsonObject jo = new JsonObject().add("id", lead);
   leadsArray.add(jo);
  }
  input.add("leads", leadsArray);
  JsonArray tokensArray = new JsonArray();
  for (JsonObject jo : tokens) {
   tokensArray.add(jo);
  }
  input.add("tokens", tokensArray);
  requestBody.add("input", input);
  return requestBody;
 }

}
```

Den här klassen har en konstruktor som tar en Auth och kampanjens ID. Leads läggs till i objektet antingen genom att skicka en ArrayList `<Integer>` som innehåller ID:n för posterna som setLeads, eller genom att använda addLead, som tar ett heltal och lägger till det i den befintliga ArrayList i egenskapen leads. För att aktivera API-anropet och skicka lead-posterna till kampanjen måste postData anropas, vilket returnerar ett JsonObject som innehåller svarsdata från begäran. När en begärandekampanj anropas kommer varje lead som skickas till anropet att bearbetas av målutlösarkampanjen i Marketo och skickas till det e-postmeddelande som skapades tidigare. Du har utlöst ett e-postmeddelande via Marketo REST API. Håll ett öga på del 2 där vi ska titta på att dynamiskt anpassa innehållet i ett e-postmeddelande med hjälp av Request Campaign.

Publicerad _2015-07-17_ av _Kenny_

## Autentisera och hämta lead-data från Marketo med REST API

**Obs!** I Java-exemplen nedan använder vi paketet minimum-json för att hantera JSON-representationer i vår kod. Du kan läsa mer om det här projektet här: [https://github.com/ralfstx/minimal-json](https://github.com/ralfstx/minimal-json) Ett av de vanligaste kraven när du integrerar med Marketo är hämtning av lead-data. För de flesta, om inte alla integreringar kräver att du hämtar eller skickar lead-data från en Marketo-prenumeration, så i dag ska vi titta på en grundläggande lead-informationsåterhämtningsåtgärd genom att [autentisera](/help/rest-api/authentication.md) med en prenumeration och sedan hämta lead-data från den. För att kunna ta fram våra leads måste vi först autentisera med Marketo målinstans med OAuth 2.0. Det finns tre uppgifter som vi behöver för att autentisera med Marketo, klient-ID, klienthemlighet och värddatorn för Marketo-instansen. Här är klassen som vi använder för att autentisera:

```java
package dev.marketo.blog_leads;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import javax.net.ssl.HttpsURLConnection;
import com.eclipsesource.json.JsonObject;

public class Auth {
 protected String marketoInstance; //Instance Host obtained from Admin -> Web Service
 private String clientId; //clientId obtained from Admin -> Launchpoint -> View Details for selected service
 private String clientSecret; //clientSecret obtained from Admin -> Launchpoint -> View Details for selected service
 private String idEndpoint; //idEndpoint constructed to authenticate with service when constructor is used
 private String token; //token is stored for reuse until expiration
 private Long expiry; //used to store time of expiration

 //Creates an instance of Auth which is used to Authenticate with a particular service on a particular instance
 public Auth(String id, String secret, String instanceUrl) {
  this.clientId = id;
  this.clientSecret = secret;
  this.marketoInstance = instanceUrl;
  this.idEndpoint = marketoInstance + "/identity/oauth/token?grant_type=client_credentials&client_id=" + clientId + "&client_secret=" + clientSecret;
 }
 //The only public method of Auth, used to check if the current value of Token is valid, and then to retrieve a new one if it is not
 public String getToken(){
  long now  = System.currentTimeMillis();
  if (expiry == null || expiry < now){
   System.out.println("Token is empty or expired. Trying new authentication");
   JsonObject jo = getData();
   System.out.println("Got Authentication Response: " + jo);
   this.token = jo.get("access_token").asString();
                        //expires_in is reported as seconds, set expiry to system time in ms + expires \* 1000
   this.expiry = System.currentTimeMillis() + (jo.get("expires_in").asLong() \* 1000);
  }
  return this.token;
 }
 //Executes the authentication request
 private JsonObject getData(){
  JsonObject jsonObject = null;
  try {
   URL url = new URL(idEndpoint);
   HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
   urlConn.setRequestMethod("GET");
            urlConn.setRequestProperty("accept", "application/json");
            System.out.println("Trying to authenticate with " + idEndpoint);
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                InputStream inStream = urlConn.getInputStream();
                Reader reader = new InputStreamReader(inStream);
                jsonObject = JsonObject.readFrom(reader);
            }else {
                throw new IOException("Status: " + responseCode);
            }
  } catch (MalformedURLException e) {
   e.printStackTrace();
  }catch (IOException e) {
            e.printStackTrace();
        }
  return jsonObject;
 }
}
```

Med den här koden kan du skapa en instans av Auth med vårt klient-ID, klienthemlighet och värd (som marketoInstance) från Admin -> Startpunkt (ID och hemlighet) och Admin -> Webbtjänster (värd). Den visar getToken-metoden som testar om den aktuella lagrade token är null eller har gått ut, och returnerar sedan antingen den befintliga token, eller gör en ny autentiseringsbegäran och returnerar sedan den nya token från &quot;access_token&quot;-medlemmen i JSON-svaret. Nu när du kan autentisera med din Marketo-instans är nästa steg att hämta våra leads. Vi använder den här klassen:

```java
package dev.marketo.blog_leads;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import javax.net.ssl.HttpsURLConnection;
import com.eclipsesource.json.JsonObject;

public class Leads {
 private StringBuilder endpoint;
 private Auth auth;
 public String filterType;
 public ArrayList filterValues = new ArrayList();
 public Integer batchSize;
 public String nextPageToken;
 public ArrayList fields = new ArrayList();

 public Leads(Auth a) {
  this.auth = a;
  this.endpoint = new StringBuilder(this.auth.marketoInstance + "/rest/v1/leads.json");
 }
 public Leads setFilterType(String filterType) {
  this.filterType = filterType;
  return this;
 }
 public Leads setFilterValues(ArrayList filterValues) {
  this.filterValues = filterValues;
  return this;
 }
 public Leads addFilterValue(String filterVal) {
  this.filterValues.add(filterVal);
  return this;
 }
 public Leads setBatchSize(Integer batchSize) {
  this.batchSize = batchSize;
  return this;
 }
 public Leads setNextPageToken(String nextPageToken) {
  this.nextPageToken = nextPageToken;
  return this;
 }
 public Leads setFields(ArrayList fields) {
  this.fields = fields;
  return this;
 }

 public JsonObject getData() {
        JsonObject result = null;
        try {
         endpoint.append("?access_token=" + auth.getToken() + "&filterType=" + filterType + "&filterValues=" + csvString(filterValues));
         if (batchSize != null && batchSize > 0 && batchSize <= 300){
             endpoint.append("&batchSize=" + batchSize);
            }
            if (nextPageToken != null){
             endpoint.append("&nextPageToken=" + nextPageToken);
            }
            if (fields != null){
             endpoint.append("&fields=" + csvString(fields));
            }
            URL url = new URL(endpoint.toString());
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("GET");
            urlConn.setRequestProperty("accept", "text/json");
            InputStream inStream = urlConn.getInputStream();
            Reader reader = new InputStreamReader(inStream);
            result = JsonObject.readFrom(reader);
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return result;
    }
 private String csvString(ArrayList fields) {
  StringBuilder fieldCsv = new StringBuilder();
     for (int i = 0; i < fields.size(); i++){
      fieldCsv.append(fields.get(i));
      if (i + 1 != fields.size()){
       fieldCsv.append(",");
      }
     }
  return fieldCsv.toString();
 }
}
```

Den här klassen har en enda konstruktor som accepterar ett Auth-objekt och visar sedan flera set-metoder för både valfria och obligatoriska parametrar. I det här fallet behöver vi egentligen bara bekymra oss om att ställa in filterType och filterValues för att få leads via e-postadress. Vi använder setFilterType för&quot;e-post&quot; och addFilterValue för den e-postadress som vi behöver hämta ett ID för. När du har angett dina parametrar kan du använda metoden getData för att hämta ett JsonObject från leads-slutpunkten, som innehåller en resultatarray som har en JSON-representation av de lead-poster som hämtades.

### Sammanfoga

Nu när vi har gått igenom exempelkoden som vi använder tittar vi på ett enkelt exempel för att hämta leads som matchar en test-e-postadress, <testlead@marketo.com>. Därför måste vi använda setFilterType för&quot;email&quot; och addFilterValue för den e-postadress som vi behöver hämta information för. När du har angett dina parametrar kan du använda metoden getData för att hämta ett JsonObject från leads-slutpunkten, som innehåller en resultatarray som har en JSON-representation av de lead-poster som hämtades.

```java
package dev.marketo.blog_leads;

import com.eclipsesource.json.JsonObject;

public class App
{
    public static void main( String[] args )
    {
     //Create an instance of Auth so that we can authenticate with our Marketo instance
        //Change the credentials here to your own
        Auth auth = new Auth("CHANGE ME", "CHANGE ME", "CHANGE ME");
        //Create and parameterize an instance of Leads
        Leads getLeads = new Leads(auth)
              .setFilterType("email")
              .addFilterValue("<testlead@marketo.com>");
        //get the inner results array of the response
        JsonObject leadsResult = getLeads.getData();
        System.out.println(leadsResult);
    }
}
```

I det här huvudmetodexemplet skapar vi en instans av Auth och skickar sedan detta till en ny Leads-konstruktor. Med setFilterType och addFilterValue konfigurerar vi vår instans av Leads för att hämta just leads som matchar e-postadressen <testlead@marketo.com>. I det här exemplet skrivs den ut till konsolen:

Token är tom eller har gått ut. Försöker med ny autentisering
Försöker autentisera med <https://299-BYM-827.mktorest.com/identity/oauth/token?grant_type=client_credentials&client_id=b417d98f-9289-47d1-a61f-db141bf0267f&client_secret=0DipOvz4h2wP1ANeVjlfwMvECJpo0ZYc>
Hämtade autentiseringssvar: {&quot;access_token&quot;:&quot;ec0f02c0-28ac-4d6c-b7d7-00e47ae85ff1:st&quot;,&quot;token_type&quot;:&quot;bearer&quot;,&quot;expirres_in&quot;:538,&quot;scope&quot;:&quot;<apiuser@mktosupport.com>&quot;}
{&quot;requestId&quot;:&quot;14fb6#14e6a7a9ad6&quot;,&quot;result&quot;:[{&quot;id&quot;:1026322,&quot;updatedAt&quot;:&quot;2015-07-07T21:43:25Z&quot;,&quot;lastName&quot;:&quot;Lead&quot;,&quot;email&quot;:&quot;<testlead@marketo.com>&quot;, &quot;createdAt&quot;:&quot;2015-07-07T21:43:25Z&quot;,&quot;firstName&quot;:&quot;Test&quot;},{&quot;id&quot;:1026323,&quot;updatedAt&quot;:&quot;2015-07-07T21:43:43Z&quot;,&quot;lastName&quot;:&quot;3 Lead2&quot;,&quot;email&quot;:&quot;<testlead@marketo.com>&quot;,&quot;createdAt&quot;:&quot;2015-07-07T21:43:43Z&quot;,&quot;firstName&quot;:&quot;Test&quot;}],&quot;success&quot;:true}

Nu har vi ledande data som vi kan behandla på det sätt vi behöver. Tack för att du läser och lämna eventuella kommentarer.

Publicerad _2015-07-10_ av _Kenny_

## Versionsuppdateringar från juli 2015

REST API

* API för säljare

Nya [slutpunkter för säljare](/help/rest-api/sales-persons.md) har introducerats som gör att du kan lista, beskriva och CRUD-visa data som finns i ett Marketo-säljpersonsobjekt. Dessutom kan en säljare tilldelas ett lead, en affärsmöjlighet eller ett företag. Detta görs genom att ange ett&quot;externalSalesPersonId&quot;-attribut när du anropar slutpunkten Create/Update/Upsert för lead, affärsmöjlighet eller företag.

Obs! Rollbehörigheter lades till för att ge åtkomst till programslutpunkterna: Skrivskyddad säljare, skrivskyddad säljare. Om din API-användarroll är före lanseringen av API:er för säljare måste du uppdatera din API-användarroll med dessa behörigheter för att aktivera åtkomst. Annars får du felsvaret 603&quot;Åtkomst nekad&quot;.

* Resurs-API - mall för landningssida

Nya [slutpunkter för landningssidmallar](https://developer.adobe.com/marketo-apis/api/asset/#landing_page_templates_endpoints) har introducerats som gör att du kan lista, skapa och uppdatera data som är kopplade till en landningssidmall med programkod.

* Tillgångs-API - segment

Två segmentrelaterade slutpunkter har lagts till:

[Hämta segment](https://developer.adobe.com/marketo-apis/api/asset/get-segments)

[Hämta segmentering med ID](https://developer.adobe.com/marketo-apis/api/asset/get-segmentation-by-id)

* Ett problem har korrigerats där Get Folder by Name inte uppfyllde parametern &quot;workSpace&quot;. [LM-61059]
* Flera prestandaförbättringar har gjorts i API:erna för anpassade objekt.

Publicerad _2015-07-17_ av _David_

## Skapa och associera leads, företag och affärsmöjligheter med Marketo REST API

För att kunna utnyttja Marketo analyser fullt ut är det viktigt att ni bygger upp korrekta och robusta associationer mellan era register över leads, företag och affärsmöjligheter. När du inte använder en inbyggd CRM-synk kan det vara svårt att bygga upp dessa relationer, så idag går vi igenom dem.

### Objektrelationer

I Marketo finns det några viktiga relationer som fullt ut skapar rapporter om affärsmöjligheter:

* Leads och säljprojekt har många till många relationer med objektet OpportunityRole.
* OpportunityRole har både ett leadId- och ett externt möjlighet-ID-fält för att skapa relationen från lead till affärsmöjlighet.
* För att kvalificera ett smart listfilter för Har affärsmöjlighet måste en lead ha en OpportunityRole som är relaterad till en affärsmöjlighet.
* Affärsmöjligheter har en många-till-ett-relation till företagsobjektet via fältet externalCompanyId.
* Leads har en en-till-många-relation till företag via fältet externalCompanyId.
* Affärsmöjligheter tillskrivs ett program som baseras på ett leadets förvärvsprogram eller deras medlemskap och framgång i ett program (Se [Förstå attribut](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/reporting/revenue-cycle-analytics/revenue-tools/attribution/understanding-attribution)).

Genom att bygga upp dessa relationer i er lead-databas kan ni utnyttja Marketo analyser fullt ut och se hur era program påverkar möjligheterna och vinsterna.

### Företag

Det enklaste sättet att bygga upp dessa relationer är att börja med att skapa företag. Detta garanterar att vi kan skicka externalCompanyId till våra affärsmöjligheter när de skapas, i stället för att behöva utföra ytterligare API-anrop för att uppdatera affärsmöjligheterna när de har skapats. Beroende på den befintliga konfigurationen kan det här vara ett nödvändigt steg eller inte, men nya leads och kontakter med associerade företag måste läggas till i din Marketo-instans för att relationen ska kunna skapas, så vi tittar på lite kod för att skapa företagsposter.

```java
package dev.marketo.opportunities;

import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import java.util.List;

import javax.net.ssl.HttpsURLConnection;

import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

public class UpsertCompanies {
 public List<JsonObject> input; //a list of Companies to use for input.  Each must have a member "externalCompanyId".
 public String action; //specify the action to be undertaken, createOnly, updateOnly, createOrUpdate
 public String dedupeBy; //select mode of Deduplication, dedupeFields for all dedupe parameters(externalCompanyId), idField for marketoId
 private String endpoint; //endpoint URL created with Constructor
 private Auth auth; //Marketo Auth Object


 //Constructs an UpsertOpportunities with Auth, but with no input set
 public UpsertCompanies(Auth auth){
  this.auth = auth;
  this.endpoint = this.auth.marketoInstance + "/rest/v1/companies.json";
 }
 //Constructs and UpsertOpportunities with Auth and input set
 public UpsertCompanies(Auth auth, List<JsonObject> input) {
  this(auth);
  this.input = input;
 }
 //adds input to existing list, creates arraylist if it was built without a list
 public UpsertCompanies addCompanies(JsonObject... companies){
  if (this.input == null){
   this.input = new ArrayList();
  }
  for (JsonObject jo : companies) {
   System.out.println(jo);
   this.input.add(jo);
  }
  return this;
 }

 public JsonObject postData(){
  JsonObject result = null;
  try {
   JsonObject requestBody = buildRequest(); //builds the Json Request Body
   String s = endpoint + "?access_token=" + auth.getToken(); //takes the endpoint URL and appends the access_token parameter to authenticate
   URL url = new URL(s);
   HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection(); //Return a URL connection and cast to HttpsURLConnection
   urlConn.setRequestMethod("POST");
   urlConn.setRequestProperty("Content-type", "application/json");
            urlConn.setRequestProperty("accept", "text/json");
            urlConn.setDoOutput(true);
   OutputStreamWriter wr = new OutputStreamWriter(urlConn.getOutputStream());
   wr.write(requestBody.toString());
   wr.flush();
   InputStream inStream = urlConn.getInputStream(); //get the inputStream from the URL connection
   Reader reader = new InputStreamReader(inStream);
   result = JsonObject.readFrom(reader); //Read from the stream into a JsonObject
   urlConn.disconnect();
  } catch (MalformedURLException e) {
   e.printStackTrace();
  } catch (IOException e) {
   e.printStackTrace();
  }
  return result;
 }

 private JsonObject buildRequest(){
  JsonObject requestBody = new JsonObject(); //Create a new JsonObject for the Request Body
  JsonArray in = new JsonArray(); //Create a JsonArray for the "input" member to hold Opp records
  for (JsonObject jo : input) {
   in.add(jo); //add our company records to the input array
  }
  requestBody.add("input", in);
  if (this.action != null){
   requestBody.add("action", action); //add the action member if available
  }
  if (this.dedupeBy != null){
   requestBody.add("dedupeBy", dedupeBy); //add the dedupeBy member if available
  }
  return requestBody;
 }
 //Getters and Setters
 //Setters return the UpsertCompanies instance to allow simple formatting:
 public List<JsonObject> getInput() {
  return input;
 }
 //sets or replaces existing input with list
 public UpsertCompanies setInput(List input) {
  this.input = input;
  return this;
 }
 public String getAction() {
  return action;
 }
 public UpsertCompanies setAction(String action) {
  this.action = action;
  return this;
 }
 public String getDedupeBy() {
  return dedupeBy;
 }
 public UpsertCompanies setDedupeBy(String dedupeBy) {
  this.dedupeBy = dedupeBy;
  return this;
 }

}
```

I företags-API:t finns två alternativ för borttagning av dubbletter som anges av parametern dedupeBy i begäran, &quot;dedupeFields&quot; och &quot;idField&quot;. Dessa kan hämtas explicit genom att anropa Describe Companies. Om dedupeBy inte anges används dedupeFields som standard. När det gäller företagsposter motsvarar dedupliceringsfält alltid&quot;externalCompanyId&quot;, som är en godtycklig sträng som anges av en extern källa, och idField, som motsvarar fältet&quot;marketoId&quot;, som är ett heltal som genereras och returneras av Marketo efter att det har skapats. Beroende på valet för deduplicpeBy måste en av externalCompanyId eller marketoId inkluderas i alla upsert-anrop för en företagspost. Samma krav gäller för objekt-API:erna för säljprojektsrollen och säljprojektsrollen. Vår kod visar två konstruktorer: en som accepterar ett argument för ett Auth-objekt och en annan som godkänner Auth och en lista över [JsonObject](https://github.com/ralfstx/minimal-json)-företagsposter. Om de skapas utan en indatalista måste företagsposter läggas till med metoden addCompanies, som kontrollerar att en ny ArrayList skapas om indata är null och sedan lägger till alla JsonObject-argument i indatalistan. Här är ett exempel:

```java
//Create a new company to associate to
JsonObject myCompany = new JsonObject().add("externalCompanyId", "myCompany");
UpsertCompanies upsertCompanies = new UpsertCompanies(auth).addCompanies(myCompany);
JsonObject companiesResult = upsertCompanies.postData();
System.out.println(companiesResult);
```

Vi skapar ett enda företag, JsonObject, med bara ett fält, `externalCompanyId`, och skapar sedan en instans av UpsertCompanies och lägger till vårt företag i indatalistan med `addCompanies`.

### Möjligheter

Precis som för företagsobjekten har affärsmöjlighets-API en `dedupeBy`-parameter som godkänner &quot;deduplicpeFields&quot; eller &quot;idField&quot;, som motsvarar &quot;externalOpportunityid&quot; respektive &quot;marketoGUID&quot;. Här är vår kod som ser ut ungefär som klassen UpsertCompanies:

```java
package dev.marketo.opportunities;

import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import java.util.List;

import javax.net.ssl.HttpsURLConnection;

import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

public class UpsertOpportunities {
 public List<JsonObject> input; //a list of Opportunities to use for input.  Each must have a member "externalopportunityid".  Each can optionally include "externalCompanyId" for company association
 public String action; //specify the action to be undertaken, createOnly, updateOnly, createOrUpdate
 public String dedupeBy; //select mode of Deduplication, dedupeFields for all dedupe parameters, idField for marketoId
 private String endpoint; //endpoint URL created with Constructor
 private Auth auth; //Marketo Auth Object


 //Constructs an UpsertOpportunities with Auth, but with no input set
 public UpsertOpportunities(Auth auth){
  this.auth = auth;
  this.endpoint = this.auth.marketoInstance + "/rest/v1/opportunities.json";
 }
 //Constructs and UpsertOpportunities with Auth and input set
 public UpsertOpportunities(Auth auth, List<JsonObject> input) {
  this(auth);
  this.input = input;
 }
 public UpsertOpportunities addOpportunities(JsonObject... opp){
  if (this.input == null){
   this.input = new ArrayList();
  }
  for (JsonObject jo : opp) {
   System.out.println(jo);
   this.input.add(jo);
  }
  return this;
 }

 public JsonObject postData(){
  JsonObject result = null;
  try {
   JsonObject requestBody = buildRequest(); //builds the Json Request Body
   String s = endpoint + "?access_token=" + auth.getToken(); //takes the endpoint URL and appends the access_token parameter to authenticate
   URL url = new URL(s);
   HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection(); //Return a URL connection and cast to HttpsURLConnection
   urlConn.setRequestMethod("POST");
   urlConn.setRequestProperty("Content-type", "application/json");
            urlConn.setRequestProperty("accept", "text/json");
            urlConn.setDoOutput(true);
   OutputStreamWriter wr = new OutputStreamWriter(urlConn.getOutputStream());
   wr.write(requestBody.toString());
   wr.flush();
   InputStream inStream = urlConn.getInputStream(); //get the inputStream from the URL connection
   Reader reader = new InputStreamReader(inStream);
   result = JsonObject.readFrom(reader); //Read from the stream into a JsonObject
   urlConn.disconnect();
  } catch (MalformedURLException e) {
   e.printStackTrace();
  } catch (IOException e) {
   e.printStackTrace();
  }
  return result;
 }

 private JsonObject buildRequest(){
  JsonObject requestBody = new JsonObject(); //Create a new JsonObject for the Request Body
  JsonArray in = new JsonArray(); //Create a JsonArray for the "input" member to hold Opp records
  for (JsonObject jo : input) {
   in.add(jo); //add our Opportunity records to the input array
  }
  requestBody.add("input", in);
  if (this.action != null){
   requestBody.add("action", action); //add the action member if available
  }
  if (this.dedupeBy != null){
   requestBody.add("dedupeBy", dedupeBy); //add the dedupeBy member if available
  }
  return requestBody;
 }
 //Getters and Setters
 //Setters return the UpsertOpportunites instance to allow simple formatting:
 public List<JsonObject> getInput() {
  return input;
 }
 public UpsertOpportunities setInput(List<JsonObject> input) {
  this.input = input;
  return this;
 }
 public String getAction() {
  return action;
 }
 public UpsertOpportunities setAction(String action) {
  this.action = action;
  return this;
 }
 public String getDedupeBy() {
  return dedupeBy;
 }
 public UpsertOpportunities setDedupeBy(String dedupeBy) {
  this.dedupeBy = dedupeBy;
  return this;
 }

}
```

Samma konstruktoralternativ anges, som tar en Auth eller `Auth+List<JsonObject>` och en `addOpportunities`-metod för att mata in JsonObject-möjligheter. Här är ett exempel:

```java
//Create some JsonObjects for Opportunity Data
JsonObject opp1 = new JsonObject().add("name", "opportunity1")
    .add("externalopportunityid", "Opportunity1Test")
    .add("externalCompanyId", "myCompany")
    .add("externalCreatedDate", "2015-01-01T00:00:00z");
JsonObject opp2 = new JsonObject().add("name", "opportunity2")
    .add("externalopportunityid", "Opportunity2Test")
    .add("externalCompanyId", "myCompany")
    .add("externalCreatedDate", "2015-01-01T00:00:00z");

//Create an Instance of UpsertOpportunities and POST it
UpsertOpportunities upsertOpps = new UpsertOpportunities(auth)
                        .setAction("createOnly")
                        .addOpportunities(opp1, opp2);
JsonObject oppsResult = upsertOpps.postData();
System.out.println(oppsResult);
```

Här skapar vi två exempelmöjligheter och ger dem sedan värden för fälten name, externalOpportunityid, externalCompanyId och externalCreatedDate. Vi har inte diskuterat externalCreatedDate än, men det är viktigt att utnyttja eftersom det behandlas som huvudfält i RCE när en affärsmöjlighet skapades, vilket gör det viktigt för korrekt attribuering. Du kan använda din organisations affärslogik för att avgöra vad du anger i det här fältet, baserat på om du har fyllt i befintliga affärsmöjlighetsdata eller om du snabbt skapar nya. Vi skapar vår instans av UpsertOpportunity och lägger sedan till våra JsonObjects via addOpportunity. Nu när instansen är konfigurerad kan du skicka den till Marketo med postData och skriva ut resultatet

### Roller

Roller liknar de två föregående objekten, förutom att de har ett något annorlunda krav när dedupeBy ställs in för dedupeFields. Roller kräver att tre fält inkluderas när en post skapas eller uppdateras via den här metoden, &quot;leadId&quot;, &quot;role&quot; och&quot;externalOpportunityid&quot;. &quot;role&quot; kan vara vilket strängvärde som helst, men de andra två måste referera till ett giltigt ID för en lead och ett giltigt ID för en affärsmöjlighet.

```java
package dev.marketo.opportunities;

import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.Reader;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.ArrayList;
import java.util.List;

import javax.net.ssl.HttpsURLConnection;

import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

public class UpsertOpportunityRoles {
 public List<JsonObject> input; //Array of Opportunity Roles as JsonObjects, must have "leadId", "role" and "externalopprtunityid"
 public String action; //specify the action to be undertaken, createOnly, updateOnly, createOrUpdate, defaults to createOrUpdate if unset
 public String dedupeBy;//select mode of Deduplication, dedupeFields for all dedupe parameters, idField for marketoId
 private String endpoint; //endpoint URL created with Constructor
 private Auth auth; //Marketo Auth Object

 //Constructs an UpsertOpportunityRoles with Auth, but with no input set
 public UpsertOpportunityRoles(Auth auth) {
  this.auth = auth;
  this.endpoint = this.auth.marketoInstance + "/rest/v1/opportunities/roles.json";
 }
 //Constructs and UpsertOpportunities with Auth and input set
 public UpsertOpportunityRoles(Auth auth, List<JsonObject> input) {
  this(auth);
  this.input = input;
 }
 public UpsertOpportunityRoles addRoles(JsonObject... role){
  if (this.input == null){
   this.input = new ArrayList();
  }
  for (JsonObject jo : role) {
   System.out.println(jo);
   this.input.add(jo);
  }
  return this;
 }
 //executes the request to Marketo, body will be empty if input is not set
 public JsonObject postData(){
  JsonObject result = null;
  try {
   JsonObject requestBody = buildRequest(); //builds the Json Request Body
   String s = endpoint + "?access_token=" + auth.getToken(); //takes the endpoint URL and appends the access_token parameter to authenticate
   URL url = new URL(s);
   HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection(); //Return a URL connection and cast to HttpsURLConnection
   urlConn.setRequestMethod("POST");
   urlConn.setRequestProperty("Content-type", "application/json");//"application/json" content-type is required.
            urlConn.setRequestProperty("accept", "text/json");
            urlConn.setDoOutput(true);
   OutputStreamWriter wr = new OutputStreamWriter(urlConn.getOutputStream());
   wr.write(requestBody.toString());
   wr.flush();
   InputStream inStream = urlConn.getInputStream();  //get the inputStream from the URL connection
   Reader reader = new InputStreamReader(inStream);
   result = JsonObject.readFrom(reader); //Read from the stream into a JsonObject
   urlConn.disconnect();
  } catch (MalformedURLException e) {
   e.printStackTrace();
  } catch (IOException e) {
   e.printStackTrace();
  }
  return result;
 }

 public JsonObject buildRequest(){
  JsonObject requestBody = new JsonObject();
  JsonArray in = new JsonArray();
  for (JsonObject jo : input) {
   in.add(jo);
  }
  requestBody.add("input", in);
  if (this.action != null){
   requestBody.add("action", action);
  }
  if (this.dedupeBy != null){
   requestBody.add("dedupeBy", dedupeBy);
  }
  return requestBody;
 }
 //Getters and Setters
 //Setters return the UpsertOpportunites instance to allow simple formatting:
 public List<JsonObject> getInput() {
  return input;
 }
 public UpsertOpportunityRoles setInput(List<JsonObject> input) {
  this.input = input;
  return this;
 }
 public String getAction() {
  return action;
 }
 public UpsertOpportunityRoles setAction(String action) {
  this.action = action;
  return this;
 }
 public String getDedupeBy() {
  return dedupeBy;
 }
 public UpsertOpportunityRoles setDedupeBy(String dedupeBy) {
  this.dedupeBy = dedupeBy;
  return this;
 }

}
```

Vi följer ett liknande mönster för konstruktorerna och metoden addRoles som i föregående exempel. Här är ett exempel.

```java
//Create Some opp roles now
JsonObject opp1Role = new JsonObject()
                        .add("role", "Captain")
                        .add("externalopportunityid", opp1.get("externalopportunityid").asString())
                        .add("leadId", 318794);
JsonObject opp2Role = new JsonObject()
                        .add("role", "Commander")
                        .add("externalopportunityid", opp2.get("externalopportunityid").asString())
                        .add("leadId", 318795);

//Create an Instance of UpsertOpportunityRoles and POST it
UpsertOpportunityRoles upsertRoles = new UpsertOpportunityRoles(auth)
                        .setAction("createOnly")
                        .addRoles(opp1Role, opp2Role);
JsonObject rolesResult = upsertRoles.postData();
System.out.println(rolesResult);
```

Här skapar vi nya JsonObjects för våra två exempelroller, och lägger till deras obligatoriska dedupliceringsfält, så att vi kan utnyttja de möjligheter vi redan har skapat externt och sedan överföra dem till Marketo.

### Sammanfoga allt

Här är det fullständiga exemplet på vår huvudmetod:

```java
package dev.marketo.opportunities;

import com.eclipsesource.json.JsonObject;

public class App
{
    public static void main( String[] args )
    {
     //create an Instance of Auth
        Auth auth = new Auth("CLIENT_ID_CHANGE_ME", "CLIENT_SECRET_CHANGE_ME", "MARKETO_HOST_CHANGE_ME");

        //Create a new company to associate to
        JsonObject myCompany = new JsonObject().add("externalCompanyId", "myCompany");
        UpsertCompanies upsertCompanies = new UpsertCompanies(auth).addCompanies(myCompany);
        JsonObject companiesResult = upsertCompanies.postData();
        System.out.println(companiesResult);

        //Create some JsonObjects for Opportunity Data
        JsonObject opp1 = new JsonObject().add("name", "opportunity1")
           .add("externalopportunityid", "Opportunity1Test")
           .add("externalCompanyId", "myCompany")
           .add("externalCreatedDate", "2015-01-01T00:00:00z");
        JsonObject opp2 = new JsonObject().add("name", "opportunity2")
           .add("externalopportunityid", "Opportunity2Test")
           .add("externalCompanyId", "myCompany")
           .add("externalCreatedDate", "2015-01-01T00:00:00z");

        //Create an Instance of UpsertOpportunities and POST it
        UpsertOpportunities upsertOpps = new UpsertOpportunities(auth)
                                .setAction("createOnly")
                                .addOpportunities(opp1, opp2);
        JsonObject oppsResult = upsertOpps.postData();
        System.out.println(oppsResult);

        //Create Some opp roles now
        JsonObject opp1Role = new JsonObject()
           .add("role", "Captain")
           .add("externalopportunityid", opp1.get("externalopportunityid").asString())
           .add("leadId", 318794);
        JsonObject opp2Role = new JsonObject()
           .add("role", "Commander")
           .add("externalopportunityid", opp2.get("externalopportunityid").asString())
           .add("leadId", 318795);

        //Create an Instance of UpsertOpportunityRoles and POST it
        UpsertOpportunityRoles upsertRoles = new UpsertOpportunityRoles(auth)
           .setAction("createOnly")
           .addRoles(opp1Role, opp2Role);
        JsonObject rolesResult = upsertRoles.postData();
        System.out.println(rolesResult);

    }
}
```

Du kan se i vilken ordning du skapar företag, affärsmöjligheter och roller. Nu kan du synkronisera dina data om företag och säljprojekt med Marketo.

Publicerad _2015-08-07_ av _Kenny_

## Skicka transaktionsmeddelanden med Marketo REST API: Del 2, Anpassat innehåll

Den här veckan tittar vi på hur vi kan skicka dynamiskt innehåll till våra e-postmeddelanden via API-anropet för Request Campaign. Begärandekampanj tillåter inte bara utlösande av e-postmeddelanden externt, utan du kan även ersätta innehållet i [Mina token](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/core-marketo-concepts/programs/tokens/understanding-my-tokens-in-a-program) i ett e-postmeddelande. Mina tokens är återanvändbart innehåll som kan anpassas på program- eller marknadsföringsmappnivå. Dessa kan också bara finnas som platshållare som kan ersättas genom ert kampanjsamtal.

### Bygger din e-postadress

För att kunna anpassa vårt innehåll måste vi först konfigurera ett [program](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/core-marketo-concepts/programs/creating-programs/create-a-program) och ett [e-postmeddelande](https://experienceleague.adobe.com/sv/docs/marketo/using/home) i Marketo. För att kunna generera vårt anpassade innehåll måste vi skapa variabler i programmet och sedan placera dem i det e-postmeddelande som vi ska skicka. För enkelhetens skull använder vi bara en token i det här exemplet, men du kan ersätta ett valfritt antal token i ett e-postmeddelande, i Från e-post, Från namn, Svar till eller valfritt innehåll i e-postmeddelandet. Låt oss skapa en variabel med formaterad text för ersättning och kalla den för&quot;bodyReplacement&quot;. Med RTF kan vi ersätta innehåll i token med godtycklig HTML som vi vill mata in. Token kan inte sparas när den är tom, så infoga lite platshållartext här. Nu måste vi infoga vår token i e-postmeddelandet: Denna token kan nu ersättas via ett Request Campaign-samtal. Den här variabeln kan vara så enkel som en enda textrad som behöver ersättas per e-post, eller kan innehålla nästan hela layouten för e-postmeddelandet.

### Koden

Vi utökar koden från förra veckan för att skicka in anpassade tokens till vårt kampanjsamtal.

```java
package dev.marketo.blog_request_campaign;

import com.eclipsesource.json.JsonArray;

public class App
{
    public static void main( String[] args )
    {
     //Create an instance of Auth so that we can authenticate with our Marketo instance
        Auth auth = new Auth("Client ID - CHANGE ME", "Client Secret - CHANGE ME", "Host - CHANGE ME");

        //Create and parameterize an instance of Leads
        Leads leadsRequest = new Leads(auth).setFilterType("email").addFilterValue("requestCampaign.test@marketo.com");

        //get the inner results array of the response
        JsonArray leadsResult = leadsRequest.getData().get("result").asArray();

        //get the id of the record indexed at 0
        int lead = leadsResult.get(0).asObject().get("id").asInt();

        //Set the ID of our campaign from Marketo
        int campaignId = 1578;
        RequestCampaign rc = new RequestCampaign(auth, campaignId).addLead(lead);

        //Create the content of the token here, and add it to the request
        String bodyReplacement = "<div class="replacedContent"><p>This content has been replaced</p></div>";
        rc.addToken("{{my.bodyReplacement}}", bodyReplacement);
        rc.postData();
    }
}
```

Den här gången skapar vi innehållet i vår token i variabeln bodyReplacement och använder sedan metoden addToken för att lägga till den i begäran. addToken tar en nyckel och ett värde och skapar sedan en JsonObject-representation och lägger till den i den interna tokens-arrayen. Detta serialiseras sedan under metoden postData och skapar en brödtext som ser ut så här:

`{"input":{"leads":[{"id":1}],"tokens":[{"name":"{{my.bodyReplacement}}","value":"<div class="replacedContent"><p>This content has been replaced</p></div>"}]}}`

Tillsammans ser konsolutdata ut så här:

```bash
Token is empty or expired. Trying new authentication
Trying to authenticate with&nbsp;...
Got Authentication Response: {"access_token":"19d51b9a-ff60-4222-bbd5-be8b206f1d40:st","token_type":"bearer","expires_in":3565,"scope":"<apiuser@mktosupport.com>"}
Executing RequestCampaign call
Endpoint: .../rest/v1/campaigns/1578/trigger.json?access_token=19d51b9a-ff60-4222-bbd5-be8b206f1d40:st
Request Body:
{"input":{"leads":[{"id":1}],"tokens":[{"name":"{{my.bodyReplacement}}","value":"<div class="replacedContent"><p>This content has been replaced</p></div>"}]}}
Result:
{"requestId":"1e8d#14eadc5143d","result":[{"id":1578}],"success":true}
```

### Radbrytning

Den här metoden är utökningsbar på många olika sätt, och ändrar innehåll i e-postmeddelanden inom enskilda layoutavsnitt eller externa e-postmeddelanden, så att anpassade värden kan skickas till uppgifter eller intressanta ögonblick. Du kan anpassa varifrån som helst en token kan användas från i ett program med den här metoden. Liknande funktioner är också tillgängliga med anropet [Schemalägg kampanj](https://developer.adobe.com/marketo-apis/api/mapi/#operation/scheduleCampaignUsingPOST) som gör att du kan bearbeta tokens i en hel gruppkampanj. Dessa kan inte anpassas per lead, men är mycket användbara för att anpassa innehåll för en mängd leads.

Publicerad _2015-07-24_ av _Kenny_

## Uppdatera lead-data i Marketo med API:t

För över ett år sedan publicerade vi Updating customer and prospects information in Marketo using the API. Posten presenterade ett kodexempel som skulle kunna köras regelbundet för att avfråga ett externt system för uppdateringar. Tanken var att hämta externa data och överföra dem till Marketo för att uppdatera lead-informationen. Kodexemplet som presenterades använde vårt SOAP API. REST-slutpunkt för att uppnå samma mål. **Programindata** Du behöver troligtvis omvandla data från det externa systemet till ett format som kan användas av Marketo REST API:er. Eftersom vi använder API:t för att skapa/uppdatera leads måste data formateras som JSON, som skickas i begärandetexten. Detta förklaras bäst av ett exempel. I Java-exempelkoden nedan har vi placerat modellhuvuddata i en array med strängar. Varje sträng efter data för varje lead: förnamn, efternamn, e-postadress, jobbtitel.

```java
private static String externalLeadData[] = {
        "Henry, Adams, <henry@superstar.com>, Director of Demand Generation",
        "Suzie, Smith, <ssmith@gmail.com>, VP Marketing"
};
```

Exempelkoden omvandlar arrayen till JSON-blocket nedan.

```json
{
"input":
    [
        {"firstName":"Henry", "lastName":"Adams", "email":"<henry@superstar.com>", "title":"Director of Demand Generation"},
        {"firstName":"Suzie", "lastName":"Smith", "email":"<ssmith@gmail.com>", "title":"VP Marketing"}
    ]
}
```

Varje matrisobjekt för &quot;input&quot; motsvarar ett enskilt lead i Marketo. Arrayobjekten är JSON-objekt som innehåller ett eller flera Marketo lead-fältnamn och deras respektive värden. Fältnamnen som du anger (i det här fallet firstName, lastName, email och title) måste matcha REST API-namnet som definierats för Marketo-prenumerationen. Du hittar REST API-namnen i avsnittet för fälthantering på Marketo administratörspanel genom att exportera fältnamnen. Fältnamnen exporteras till en Excel-fil enligt nedan. Du kan också söka efter fältnamn programmatiskt genom att anropa API:t [Beskriv lead](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeUsingGET_2). Här följer till exempel ett beskrivande svarsfragment som innehåller REST API-namnet för fältet Förnamn.

```json
{
    "id": 29,
    "displayName": "First Name",
    "dataType": "string",
    "length": 255,
    "soap": {
        "name": "FirstName",
        "readOnly": false
    },
    "rest": {
        "name": "firstName",
        "readOnly": false
    }
},
```

**Programlogik** När nyttolasten har rätt format kan vi anropa Leads för att skapa/uppdatera. Observera att vi i det här exemplet inte anger någon av de valfria parametrarna. Standardbeteendet är att skapa eller uppdatera lead-poster (upsert), använda e-post för borttagning av dubbletter och använda synkron bearbetning. Om anropet till Create/Update leads lyckas innehåller svarstexten en resultatmatris som innehåller Marketo lead-ID och statusen för Create/Update-åtgärden. Beroende på vilken åtgärdsparameter du skickade i begäran kan statusen vara antingen&quot;uppdaterad&quot;,&quot;skapad&quot; eller&quot;överhoppad&quot;. Anta att den första ledaren inte fanns (Henry Adams) och att den andra ledaren fanns (Suzie Smith). Ett svar som liknar följande skulle visa att det första leadet skapades och att det andra ledet uppdaterades.

```json
{
    "success":true,
    "requestId":"118f8#14f1a0b82fc",
    "result":[
        {
            "id":318798,
            "status":"created"
        },
        {
            "id":318797,
            "status":"updated"
        }
    ]
}
```

Det är allt för tillfället. Glad kodning! **Programkod**

```java
package com.marketo;

// minimal-json library (<https://github.com/ralfstx/minimal-json>)
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

import javax.net.ssl.HttpsURLConnection;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStreamWriter;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.\*;

public class CreateUpdateLeads {
    //
    // Define Marketo REST API access credentials: Account Id, Client Id, Client Secret.  For example:
    //    public static String CUSTOM_SERVICE_DATA[] =
    //      {"111-AAA-222", "2f4a4435-f6fa-4bd9-3248-098754982345", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W"};
    //
    private static final String CUSTOM_SERVICE_DATA[] =
            { INSERT YOUR CUSTOM SERVICE DATA HERE };

    //
    // Mock up data that could be read from an external data source.
    // An array of CSV strings the form:
    //   "firstName, lastName, email, title"
    private static String externalLeadData[] = {
        "Henry, Adams, henry@superstar.com, Director of Demand Generation",
        "Suzie, Smith, ssmith@gmail.com, VP Marketing"
    };

    public static void main(String[] args) {
        // Compose base URL
        String baseUrl = String.format("https://%s.mktorest.com",
                CUSTOM_SERVICE_DATA[0]);

        // Compose Identity URL
        String identityUrl = String.format("%s/identity/oauth/token?grant_type=%s&client_id=%s&client_secret=%s",
                baseUrl, "client_credentials", CUSTOM_SERVICE_DATA[1], CUSTOM_SERVICE_DATA[2]);

        // Call Identity API
        JsonObject identityObj = JsonObject.readFrom(httpRequest("GET", identityUrl, null));
        String accessToken = identityObj.get("access_token").asString();

        // Compose URL for Create/Update Leads
        String createUpdateLeadsUrl = String.format("%s/rest/v1/leads.json?access_token=%s", baseUrl, accessToken);

        // Convert String array into JsonArray to pass as part of request body
        JsonArray input = convertExternalLeadDataToJson(externalLeadData);

        // Build request body JSON
        JsonObject requestObj = new JsonObject();
        requestObj.add("input", input);

        // Call Create/Update Lead API
        JsonArray result = new JsonArray();
        JsonObject leadObj = JsonObject.readFrom(httpRequest("POST", createUpdateLeadsUrl, requestObj.toString()));
        if (leadObj.get("success").asBoolean()) {
            if (leadObj.get("result") != null) {
                result = leadObj.get("result").asArray();
            }
        }

        // Print out results object
        System.out.println(result);
        System.exit(0);
    }

    // Convert array of CSV formatted Strings into an array of JSON objects
    private static JsonArray convertExternalLeadDataToJson(String input[]) {
        JsonArray output = new JsonArray();

        // Loop through each CSV row in array
        for (int i = 0; i < input.length; i++) {
            // Split CSV row into separate fields
            List items = Arrays.asList(input[i].split(","));

            // Add fields to JSON object
            JsonObject lead = new JsonObject();
            lead.add("firstName", items.get(0));
            lead.add("lastName", items.get(1));
            lead.add("email", items.get(2));
            lead.add("title", items.get(3));
            output.add(lead);
        }
        return output;
    }

    // Perform HTTP request
    private static String httpRequest(String method, String endpoint, String body) {
        String data = "";
        try {
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setDoOutput(true);
            urlConn.setRequestMethod(method);
            switch (method) {
                case "GET":
                    break;
                case "POST":
                    urlConn.setRequestProperty("Content-type", "application/json");
                    urlConn.setRequestProperty("accept", "text/json");
                    OutputStreamWriter wr = new OutputStreamWriter(urlConn.getOutputStream());
                    wr.write(body);
                    wr.flush();
                    break;
                default:
                    System.out.println("Error: Invalid method.");
                    return data;
            }
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    private static String convertStreamToString(InputStream inputStream) {
        try {
            return new Scanner(inputStream).useDelimiter("A").next();
        } catch (NoSuchElementException e) {
            return "";
        }
    }
}
```

Publicerad _2015-08-14_ av _David_

## Lägg till SalesPerson-data till Marketo

Med de nya API:erna för SalesPerson kan du fritt koppla Marketo leads till SalesPerson-poster i instanser utan någon intern CRM-integrering. Detta tillåter användning av {{lead.Lead Owner Email Address}} och relaterade fält och token inom Marketo.

### Skapar SalesPerson-poster

För att kunna associera leads med SalesPerson-poster måste vi först ange våra SalesPerson-poster i Marketo. Här är ett exempel i PHP:

```php
<?php

class SalesPerson{
 private $auth;//auth object
 private $action;// string designating request action, createOnly, updateOnly, createOrUpdate
 private $dedupeBy;//dedupeFields or idField
 private $input;//array of salesperson objects for input

 //takes an Auth object as the first argument
 public function _construct($auth, $input){
  $this->auth = $auth;
  $this->input = $input;
 }

 //constructs the json request body
 private function bodyBuilder(){
  $body = new stdClass();
  $body->input = $this->input;
  if (isset($this->action)){
   $body->action = $this->action;
  }
  if (isset($this->dedupeBy)){
   $body->dedupeBy = $this->dedupeBy;
  }
  return json_encode($body);
 }
 //submits the request to Marketo and returns the response as a string
 public function postData(){
  $url = $this->auth->getHost() . "/rest/v1/salespersons.json?access_token=" . $this->auth->getToken();
  $ch = curl_init($url);
  $requestBody = $this->bodyBuilder();
  curl_setopt($ch,  CURLOPT_RETURNTRANSFER, 1);
  curl_setopt($ch, CURLOPT_HTTPHEADER, array('accept: application/json','Content-Type: application/json'));
  curl_setopt($ch, CURLOPT_POST, 1);
  curl_setopt($ch, CURLOPT_POSTFIELDS, $requestBody);
  curl_getinfo($ch);
  $response = curl_exec($ch);
  curl_close($ch);
  return $response;
 }
 //getters and setters
 public function setAction($action){
  $this->action = $action;
 }
 public function getAction($action){
  return $this->action;
 }
 public function setDedupeBy($dedupeBy){
  $this->dedupeBy = $dedupeBy;
 }
 public function getDedupeBy($dedupeBy){
  return $this->dedupeBy;
 }
 public function addSalesPersons($input){
  if($this->input != null){
   if (is_array($input)){
    foreach($input as $item){
     array_push($this->input, $item);
    }
   }else{
    array_push($this->input, $input);
   }
  }else{
   $this->input = $input;
  }
 }
 public function getInput(){
  return $this->input;
 }

}
```

I ovanstående klass är variabeln $input en array med stdClass-objekt med möjliga medlemmar:

* externalSalesPersonId(endast giltigt vid skapande)
* id(endast som updateOnly-läget key-in)
* e-post
* fax
* firstName
* lastName
* mobilePhone
* telefon
* title

Mer detaljerad information om typer och fältlängder kan hämtas via anropet Describe SalesPerson.

### Synkroniserar leads

Här är en snabb exempelklass för synkronisering av leads som vi behöver:

```php
<?php

class Leads{
 private $auth;//auth object
 private $action;// string designating request action, createOnly, updateOnly, createOrUpdate
 private $lookupField;//dedupeFields or idField
 private $input;//array of salesperson objects for input
 private $asyncProcessing;//specify whether input should be processed asynchronously
 private $partitionName;//partition name for update if requires

 //takes an Auth object as the first argument
 public function _construct($auth, $input){
  $this->auth = $auth;
  $this->input = $input;
 }

 //constructs the json request body
 private function bodyBuilder(){
  $body = new stdClass();
  $body->input = $this->input;
  if (isset($this->action)){
   $body->action = $this->action;
  }
  if (isset($this->lookupField)){
   $body->lookupField = $this->lookupField;
  }
  return json_encode($body);
 }
 //submits the request to Marketo and returns the response as a string
 public function postData(){
  $url = $this->auth->getHost() . "/rest/v1/leads.json?access_token=" . $this->auth->getToken();
  $ch = curl_init($url);
  $requestBody = $this->bodyBuilder();
  curl_setopt($ch,  CURLOPT_RETURNTRANSFER, 1);
  curl_setopt($ch, CURLOPT_HTTPHEADER, array('accept: application/json','Content-Type: application/json'));
  curl_setopt($ch, CURLOPT_POST, 1);
  curl_setopt($ch, CURLOPT_POSTFIELDS, $requestBody);
  curl_getinfo($ch);
  $response = curl_exec($ch);
  curl_close($ch);
  return $response;
 }
 //getters and setters
 public function setAction($action){
  $this->action = $action;
 }
 public function getAction(){
  return $this->action;
 }
 public function setDedupeBy($lookupField){
  $this->dedupeBy = $lookupField;
 }
 public function getDedupeBy(){
  return $this->dedupeBy;
 }
 public function addLeads($input){
  if($this->input != null){
   if (is_array($input)){
    foreach($input as $item){
     array_push($this->input, $item);
    }
   }else{
    array_push($this->input, $input);
   }
  }else if (is_array($input)){
   $this->input = $input;
  }else{
   $this->input = [$input];
  }
 }
 public function getInput(){
  return $this->input;
 }
 public function setAsyncProcessing($asyncProcessing){
  $this->asyncProcessing = $asyncProcessing;
 }
 public function getAsyncProcessing() {
  return $this->asyncProcessing;
 }
 public function setPartitionName($partitionName){
  $this->partitionName = $partitionName;
 }
 public function getPartitionName() {
  return $this->partitionName;
 }

}
```

### Process

Här är ett exempel på hur du skapar två säljarposter och kopplar dem till två lead-poster:

```php
<?php

require 'Auth.php';
require 'SalesPerson.php';
require 'Leads.php';

//Create an Auth object for authentication
$auth = new Auth("https://284-RPR-133.mktorest.com", "7f99bd49-0e0e-4715-a72a-0c9319f75552", "O5lZHhrQDfDKRhulY89IU42PfdhRSe6m");

//Compose new SalesPerson Records
$sales1 = new stdClass();
$sales1->externalSalesPersonId = "SalesPerson 1";
$sales1->email = "sales1@example.com";
$sales1->firstName = "Jane";
$sales1->lastName = "Doe";

$sales2 = new stdClass();
$sales2->externalSalesPersonId = "SalesPerson 2";
$sales2->email = "sales2@example.com";
$sales2->firstName = "John";
$sales2->lastName = "Doe";

//Compose lead records
$lead1 = new stdClass();
$lead1->email = "marketoDev@example.com";
//Add the external id of the desired salesperson
$lead1->externalSalesPersonId = $sales1->externalSalesPersonId;

$lead2 = new stdClass();
$lead2->email = "devBlog@example.com";
$lead2->externalSalesPersonId = $sales2->externalSalesPersonId;

//Create a new instance of SalesPerson to submit records
$salesPerson = new SalesPerson($auth, [$sales1, $sales2]);
$spResponse = $salesPerson->postData();
print_r($spResponse . "rn");
$json = json_decode($spResponse);

//Check for success on synching SalesPersons
if ($json->success){
 $leads = new Leads($auth, [$lead1, $lead2]);
 $leadsResponse = $leads->postData();
 print_r($leadsResponse . "rn");
}else{
 print_r("SalesPerson request failed with errors:rn");
 foreach ($json->errors as $error){
  print_r("code: " . $error->code . ", message: " . $error->message . "rn");
 }
}
```

I våra exempelklasser skapar vi bara stdClass-objekt som representerar våra SalesPerson- och Lead-poster som måste synkroniseras, där varje önskat fält läggs till som medlem. När koden har körts har både lead <marketoDev@example.com> och <devBlog@example.com> fälten Lead Owner Email, Lead Owner First Name och Lead Owner Last Name (Leadägarens efternamn) fyllts i så att de kan använda de relevanta tokenerna för fälten och filtreras med de relevanta smarta listfiltren.

### Autentiseringsklass

```php
<?php

class Auth{
 private $host;//host of the target Marketo instance
 private $clientId;//client Id
 private $clientSecret;//client secret
 private $token;//access_token
 private $expiry;

 function _construct($host, $clientId, $clientSecret){
  $this->host = $host;
  $this->clientId = $clientId;
  $this->clientSecret = $clientSecret;
 }
 public function getToken(){
  if (!isset($this->token) || $this->expiry > time()){
   $data = $this->getData();
   $this->expiry = time() + $data->expires_in;
   $this->token = $data->access_token;
   return $this->token;
  }else{
   return $this->token;
  }
 }
 private function getData(){
  $url = $this->host . "/identity/oauth/token?grant_type=client_credentials&client_id=" . $this->clientId . "&client_secret="
     . $this->clientSecret;
  $ch = curl_init($url);
  curl_setopt($ch,  CURLOPT_RETURNTRANSFER, 1);
  curl_setopt($ch, CURLOPT_HTTPHEADER, array('accept: application/json','Content-Type: application/json'));
  $response = curl_exec($ch);
  $json = json_decode($response);
  curl_close($ch);
  return $json;
 }
 public function getHost(){
  return $this->host;
 }
}
```

Publicerad _2015-08-21_ av _Kenny_

## Bästa praxis för API-användare och anpassade tjänster

Marketo REST API:er använder anpassade tjänster för autentisering och var och en av dessa tjänster ägs av en Marketo-användare som bara använder API:er. Funktionerna för varje anpassad tjänst avgörs av behörigheterna för varje roll som tilldelas den användaren. Att tilldela enskilda användare och anpassade tjänster till era integreringar ger flera fördelar:

* Du kan finjustera de [behörigheter](/help/rest-api/custom-services.md) som ges till varje enskild tjänst genom den roll som ges till användaren.
* Du kan inaktivera enskilda webbtjänster från att ringa till din instans genom att ta bort motsvarande anpassade tjänst, utan att inaktivera andra.
* Rapportering om API-anropsanvändning delas upp efter användare, vilket gör att du kan identifiera högt och onormalt utnyttjande
* Det är enklare att avgöra vilka data varje webbtjänst får åtkomst till
* Förekomster med Workspace kan begränsa åtkomsten till vissa affärsenheter genom att endast tilldela roller till hjälpmedelsanpassade arbetsytor

### API-användning

Var och en av dina API-användare rapporteras separat i API-användningsrapporten, så om du delar upp dina webbtjänster efter användare kan du enkelt ta hänsyn till hur de olika integreringarna används. Om antalet API-anrop till din instans överstiger gränsen och leder till att efterföljande anrop misslyckas, kan du med den här metoden ta hänsyn till volymen från varje tjänst och låta dig utvärdera hur problemet kan lösas. Se din användning genom att gå till Admin -> Webbtjänster och klicka på antalet samtal under de senaste 7 dagarna.

### Inaktivera en tjänst

Om en integrering har oönskade effekter kan det vara omständligt och svårt att inaktivera om du inte har tilldelat var och en av dem en anpassad tjänst. Att dela upp en efter en gör det lika enkelt som att ta bort den felaktiga tjänsten i Admin -> Startpunkt.

### Workspace Management

För Marketo Enterprise-prenumerationer är det vanligt att en tjänst bara behöver åtkomst till en enda arbetsyta, och detta kan [genomdrivas genom rolltilldelning till API-användaren](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/administration/workspaces-and-person-partitions/allow-user-access-to-a-workspace). Varje användarroll kan tilldelas antingen globalt eller per arbetsyta, så åtkomsten kan begränsas i arbetsytor när så är lämpligt, vilket ger så få behörigheter som möjligt.

Publicerad _2015-08-28_ av _Kenny_

## Ange leadpartitioner med REST APII

**Leadpartitionering** Marketo Lead Partitions är ett bekvämt sätt att isolera leads. Partitioner kan göra det möjligt för olika marknadsföringsgrupper inom organisationen att dela en enda Marketo-instans. Mer information finns i [Arbetsytor och leadpartitioner](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/administration/workspaces-and-person-partitions/understanding-workspaces-and-person-partitions). Anta att du använder huvudpartitioner och skapar leads programmatiskt med Marketo REST API. Hur ser du till att de leads du skapar hamnar i rätt partition? Det här inlägget visar hur du gör! För det här exemplets skull använder vi Workspaces och Partitions för att isolera våra leads baserat på geografi.

Först definierar vi en arbetsyta med namnet&quot;Land&quot;. Därefter skapar vi två partitioner på arbetsytan som kallas &quot;Mexiko&quot; och &quot;Kanada&quot;.  **Skapa lead i partition** Anta nu att vi vill skapa två leads i Mexiko-partitionen. För att skapa leads ringer vi. För att kunna ange partitionen måste attributet partitionName finnas i begärandetexten. Hur vet vi vad vi ska använda för värdet partitionName? Vi kan hämta en lista över giltiga partitionsnamnvärden för instansen genom att anropa API:t [Hämta Lead-partitioner](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeProgramMemberUsingGET) enligt följande:

`GET /rest/v1/leads/partitions.json`

```json
{
    "requestId": "20ae#14f9a1a5a30",
    "result": [
        {
            "id": 1,
            "name": "Default",
            "description": "Initial system lead partition"
        },
        {
            "id": 2,
            "name": "Mexico",
            "description": "Leads that live in Mexico"
        },
        {
            "id": 3,
            "name": "Canada",
            "description": "Leads that live in Canada"
        }
    ],
    "success": true
}
```

I det här fallet är det korrekta värdet&quot;Mexico&quot;, så vi skickar det till Create/Update Leads enligt följande:

`POST /rest/v1/leads.json`

```json
{
    "input": [
        {"email":"enrique.pena-nieto@gob.mx"},
        {"email":"angelica.rivera@gob.mx"}
    ],
    "action":"createOrUpdate",
    "partitionName":"Mexico"
}
```

Här är våra nya leads i Marketo.  **Uppdatera lead i partition** Om du vill uppdatera befintliga leads i en partition anropar vi bara Create/Update Leads och anger partitionName på samma sätt som tidigare. För den här uppdateringen tilldelar vi förnamn, efternamn och titel till de leads som vi skapade ovan.

`POST /rest/v1/leads.json`

```json
{
"input": [
        {"email":"enrique.pena-nieto@gob.mx", "firstName":"Enrique", "lastName":"Pena Neito", "title": "El Presidente"},
        {"email":"angelica.rivera@gob.mx", "firstName":"Angelica", "lastName": "Rivera", "title": "Primera Dama"}
    ],
    "action":"createOrUpdate",
    "partitionName":"Mexico"
}
```

Här är våra nya leads i Marketo.
**Identifiera partition för en lead** Hur vet vi vilken partition en lead finns i? För detta använder vi API:t [Get Lead by Id](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadByIdUsingGET) och anger&quot;leadPartitionId&quot; i frågeparametern&quot;fields&quot;. I det här fallet hämtar vi informationen för lead-ID 318816 som vi skapade ovan.

`GET /rest/v1/lead/318816.json?fields=leadPartitionId,email,firstName,lastName,title`

```json
{
    "requestId": "5c57#14f9a495b1f",
    "result": [
        {
            "id": 318816,
            "lastName": "Pena Neito",
            "title": "El Presidente",
            "email": "enrique.pena-nieto@gob.mx",
            "firstName": "Enrique",
            "leadPartitionId": 2
        }
    ],
    "success": true
}
```

Observera att leadPartitionId återställs i stället för partitionName. För att hämta partitionName måste vi gå igenom utdata från API:t Get Lead Partitions ovan.

```json
{
    "id": 2,
    "name": "Mexico",
    "description": "Leads that live in Mexico"
},
```

I det här fallet mappas ett leadPartitionId-värde på 2 till partitionName för Mexico. Det är allt för tillfället. Glad partitionering!

Publicerad _2015-09-04_ av _David_

## Jämföra poängfält i Marketo e-postskript

**Obs!** Det här är en gästpost av Kateal Moran. Cathal är en lösningskonsult som arbetar på Marketo EMEA-kontor i Dublin i Irland.

### Jämföra poängfält

Många Marketo-kunder, särskilt de som fokuserar på korsförsäljning, har flera poängfält och detta används ofta för att mäta en leads intresse för en viss produkt/område. Tänk mig att jag säljer äpplen och bananer, och om en lead har poängen 50 för äpplen och 10 för bananer så är det tydligt var preferensen ligger, skulle det inte vara trevligt om mitt innehåll reflekterade denna inställning. E-postskript kan användas för att jämföra resultat och för att anpassa innehåll i ett e-postmeddelande beroende på vilken poäng som är högst (eller lägst) för just det lead som tar emot e-postmeddelandet.

### Eftersom jag vill jämföra två tal måste jag konvertera mina fältvärden till heltal

```
#set ($score1 = $math.toInteger(${lead.Apple_Score}))
#set ($score2 = $math.toInteger(${lead.Banana_Score}))
##check if the lead score is greater than feature score
#if($score1 >= $score2)
                ##if Apple score is greater
                #set($Interest = "Special offer on Apples")
##check is the feature score is higher
#elseif($score2 >= $score1)
                ##if Feature score is greater
                #set($Interest = "Special offer on Bananas")
#else
                ##otherwise
                #set($Interest = "Special offer on Fruit")
#end
##display the Interest as content
${Interest}
```

I exemplet ovan personaliserar jag bara text, men jag kan lika enkelt visa en annan bild baserat på det högre poängtalet.

Publicerad _2015-09-14_ av _Kenny_

## Skicka ett Marketo-formulär i bakgrunden

När organisationen har många olika plattformar för webbinnehåll och kunddata blir det ganska vanligt att man behöver parallella inskick från ett formulär så att de resulterande uppgifterna kan samlas in på olika plattformar. Det finns flera strategier för att göra detta, men den bästa är ofta den enklaste: Använda API:t för Forms 2 för att skicka in ett dolt Marketo-formulär. Detta fungerar med alla nya Marketo-formulär, men du bör helst skapa ett tomt formulär för detta, som inte har några fält. Detta säkerställer att formuläret inte läser in mer data än nödvändigt eftersom vi inte behöver återge någonting. Hämta nu bara [inbäddningskoden](https://experienceleague.adobe.com/sv/docs/marketo/using/home) från formuläret och lägg till den i brödtexten på den önskade sidan, vilket gör en liten ändring. Din inbäddningskod innehåller ett formulärelement som detta:

`<form id="mktoForm_1068"></form>`

Du vill lägga till &quot;style=&quot;display:none&quot; i elementet så att det inte syns, så här:

`<form id="mktoForm_1068" style="display:none"></form>`

När formuläret väl är inbäddat och dolt är koden för att skicka formuläret mycket enkel:

```javascript
var myForm = MktoForms2.allForms()[0];
myForm.addHiddenFields({
 //These are the values which will be submitted to Marketo
 "Email":"test@example.com",
 "FirstName":"John",
 "LastName":"Doe"
 });
myForm.submit();
```

Forms har lämnat in en sådan ansökan kommer att bete sig exakt som om leadet hade fyllt i och skickat in ett synligt formulär. Hur du utlöser inlämningen varierar mellan olika implementeringar eftersom var och en har olika åtgärder för att föreslå det, men du kan få det att ske för i stort sett alla åtgärder. Den viktiga delen är att du ställer in fält och värden korrekt. Se till att du använder SOAP API-namnet för dina fält som du kan hitta med [Exportera fältnamn](/help/rest-api/list-of-standard-fields.md) för att försäkra dig om att värdena skickas korrekt.

Migrera från Munchkin Associate Lead

Inlämning av bakgrundsformulär är en av de rekommenderade ersättningsmetoderna för Munchkin Associate Lead. HTML exempelsida nedan demonstrerar migrering på en hög nivå genom att återanvända samma värden som skickas till Associate Lead.

```html
<html>
<head>
    <!--
      Munchkin Code
      Replace with your own instance code
    -->
    <script type="text/javascript">
        (function() {
          var didInit = false;
          function initMunchkin() {
            if(didInit === false) {
              didInit = true;
              Munchkin.init('CHANGE ME');
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
</head>

<body>
  <!--
    Start Embed code.
    Pasted from Form Actions -> Embed Code except for addition of 'style="display:none"' to the form tag in order to hide it, and instance-specific codes redacted
    Replace with your own code for testing
  -->
  <script src="CHANGE ME"></script>
  <form id="CHANGE ME" style="display:none"></form>
  <script>MktoForms2.loadForm("CHANGE ME", "CHANGE ME", "CHANGE ME TO AN INTEGER ID");</script>
  <!--End Embed Code-->

  <!--Demo code-->
    <script>
        //The same map which is assembled for the Munchkin submission can be reused for the form submission
        let values = {
            "Email": "email@example.com",
            "FirstName": "Test",
            "LastName": "Record"
        }
        //whenReady lets us apply a callback to all mkto forms on the page
        //the callback function fires whenever a form is completely loaded
        //for most use cases this will just be used to capture a reference to the form for later usage
        //submission is done in line here for demonstration only
        MktoForms2.whenReady(function(form){

            //the addHiddenFields methods lets us add arbitrary fields to the form as well as their values
            form.addHiddenFields(values);

            //submit the form
            form.submit();


        })
    </script>
</body>
</html>
```

Publicerad _2015-09-25_ av _Kenny_

## Marketo REST API Exception and Error Handling: Part 1

Marketo REST API kan returnera ett undantag eller fel som vi bekvämt anropar här ute. Fel kan uppstå på tre olika nivåer:

* HTTP-nivå, dessa fel anges med en 4xx-kod
* Svarsnivån, dessa fel ingår i arrayen &quot;errors&quot; i JSON-svaret
* Postnivå, dessa fel inkluderas i arrayen &quot;result&quot; i JSON-svaret och anges på individuell postbasis med fältet &quot;status&quot; och arrayen &quot;reasons&quot;

### HTTP-fel

Under normala driftförhållanden bör Marketo endast returnera två HTTP-statusfel, 413 Begär enheten för stor och 414 Begär URI för lång. Båda kan återställas genom att felet identifieras, begäran ändras och nya försök görs, men med smart kodning bör du aldrig stöta på detta i verkligheten. Marketo returnerar 413 om nyttolasten för begäran överstiger 1 MB, eller 10 MB vid [Importera lead](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads). I de flesta fall är det osannolikt att de här gränserna kommer att nås, men om man lägger till en kontroll av begärans storlek och flyttar poster som gör att gränsen överskrids till en ny begäran bör det inte finnas några omständigheter som leder till att felet returneras av slutpunkterna. 414 returneras när URI:n för en GET-begäran överskrider 8KiB. För att undvika det bör du kontrollera mot längden på frågesträngen för att se om den överskrider den här gränsen. Om den ändrar din begäran till en POST-metod anger du frågesträngen som begärandebrödtext med den extra parametern&quot;_method=GET&quot;. Detta innebär att begränsningen för URI:er ignoreras. I de flesta fall är det ovanligt att den här gränsen nås, men det är något vanligt när du hämtar stora grupper med poster med långa enskilda filtervärden som GUID.

### Fel på svarsnivå

Svarsnivåfel uppstår när parametern &quot;success&quot; i svaret är inställd på false. Varje post i &quot;errors&quot; har två medlemmar, &quot;code&quot; a number, either 6xx or 7xx, och ett &quot;message&quot; som anger felets huvudorsak. 6xx-koder anger alltid att en begäran misslyckades helt och inte kördes. Ett exempel på detta är 601, &quot;Åtkomsttoken invalid&quot;, som kan återställas genom återautentisering och överföring av den nya åtkomsttoken med begäran. 7xx-fel indikerar att begäran misslyckades, antingen på grund av att inga data returnerades, eller på grund av att begäran parametriserades felaktigt, till exempel att ett ogiltigt datum inkluderades, eller att en obligatorisk parameter saknades.

### Fel på postnivå

Postnivåfel indikerar att en åtgärd inte kunde slutföras för en enskild post, men själva begäran var giltig. Dessa förekommer i en enskild post i arrayen &quot;result&quot; i ett svar. Fältet &quot;status&quot; för dessa poster hoppas över och arrayen &quot;reasons&quot; kommer att finnas. Varje orsak innehåller en&quot;kodmedlem&quot; och en&quot;meddelandemedlem&quot;. Koden blir alltid 1xxx och meddelandet anger varför posten hoppades över. Ett exempel är när en begäran om att skapa/uppdatera leads har &quot;action&quot; inställt på &quot;createOnly&quot;, men det redan finns en lead för en av nycklarna i de skickade posterna. I det här fallet returneras koden 1005 och meddelandet&quot;Lead already exists&quot; visas. I de följande inläggen ska vi ta en titt på fel som kan återställas och några exempel på hur dessa ska hanteras i koden.

Publicerad _2015-10-09_ av _Kenny_

## Gästpost - Direct SQL Connectors to the Marketo Database

**Det här är ett gästinlägg från Sumit Sarkar of Progress, Inc.**

**SQL-anslutningar till Marketo-databaser** Marketo har väldokumenterade API:er för dataåtkomst. Ibland behöver organisationer dock direkt SQL-åtkomst. Vi ser oskärpan i Marketo butiker mellan Marketing och IT, som ökar efterfrågan på standardbaserad SQL-anslutning. Direkta SQL-anslutningar till Marketo är tillgängliga via [Progress DataDirect Cloud](https://www.progress.com/connectors/cloud-data-integration). DataDirect Cloud är en dataanslutningstjänst som exponerar Marketo-data genom öppna branschstandarder för SQL-åtkomst över ODBC (&quot;Open Database Connectivity&quot;) eller JDBC (&quot;Java Database Connectivity&quot;) och REST med OData (&quot;Open Data Protocol&quot;). Nedan visas några vanliga användningsexempel för att ansluta Marketo-data direkt med DataDirect Cloud:

* Dataidentifiering och visualisering (Qlik, Tableau, SAP Lumira)
* Enterprise Reporting (SAP Business Objects, Microstrategy, Cognos)
* Dataintegrering (SQL Server Integration Services - SSIS, Oracle Data Integrator - ODI, Informatica)
* Data Federation (SQL Server Linked Server, SAP Hana SDA, Oracle Database Gateway)
* Ad hoc-fråga (Microsoft Office, DB Visualizer, Aqua Data Studio)
* Dataförberedelse (Alteryx, Trifecta, Paxata)

**Hur fungerar SQL-åtkomst till Marketo?**

* DataDirect Cloud skapar ett logiskt schema för data som exponeras av Marketo API:er för integrering.
* DataDirect Cloud bearbetar SQL-begäranden från enkla ODBC- eller JDBC-klienter med hjälp av en elastisk realtidsfrågemotor på data som hämtas från Marketo API:er.
* Anslutningen till DataDirect Cloud är direkt och i realtid utan att data behöver dupliceras.
* DataDirect Cloud Service abstraherar Marketo API så att slutanvändarna inte behöver bekymra sig om API-versionsändringar eller använda SOAP eller REST.

DataDirect har byggt upp den här typen av anslutning till SaaS-datakällor sedan 2006 från och med den första Salesforce ODBC-drivrutinen som är byggd ovanpå webbtjänstens API:er. **Komma igång med att ansluta till Marketo:**

1. [Registrera dig för en DataDirect Cloud-inloggning](https://pacific.progress.com/console/register?productName=d2c&ignoreCookie=true)
1. Klicka på Datakällor och sedan på knappen &quot;+New Data Source&quot;
1. Välj &quot;Marketo&quot; och ange anslutningsinformationen. Du kan söka efter [anslutningsinformation för SOAP-integrering](/help/soap-api/soap-api.md) hos Marketo-administratören eller inloggningen.
1. Klicka på knappen Testa anslutning. Observera att det finns en OData-flik för att producera OData från Marketo, som vi kommer att diskutera i ett framtida blogginlägg.
1. Klicka på SQL-testning om du vill inspektera det Marketo-schema som visas eller utfärda grundläggande SQL-frågor inifrån användargränssnittet.
1. Klicka på&quot;Hämtar&quot; till vänster och välj DataDirect Cloud ODBC- eller JDBC-drivrutinen för programmet och plattformen som ska installeras.
1. När ODBC- eller JDBC-drivrutinen för DataDirect har installerats kan du ansluta alla standardbaserade program till Marketo.

Här är ett videoexempel på [anslutning med DataDirect Cloud ODBC-klienten](https://www.youtube.com/watch?v=H6PHra56Iig). Här följer andra självstudiekurser för DataDirect Cloud som gäller Marketo:

* [SAP-dataanalys](http://scn.sap.com/community/lumira/blog/2015/08/05/connect-sap-lumira-to-eloqua-marketo-google-analytics)
* [Microstrategy Enterprise Reporting](https://community.microstrategy.com/t5/Tech-Corner/What-MSTR-developers-should-know-about-Cloud-Data-Sources/ba-p/253083)
* [Oracle-dataintegrering](https://www.ateam-oracle.com/post/a-universal-cloud-applications-adapter-for-odi/)

**FoU-utmaningar när det gäller att bygga upp SQL-anslutningar i molnkällor som Marketo**

Alla SaaS API:er visar inte ett standardfrågespråk. I sådana fall tittar konstruktörerna på varje objekt för sig. Varje objekt kan exponeras med olika API:er med unika regler för anrop, sökfiltrering osv. Det krävdes en stor insats för att tillhandahålla en standardupplevelsefråga för hela datamodellen.

Hanterar fullständiga kopplingsfunktioner. I de fall där SaaS API:er inte stöder ett frågespråk med JOIN-kapacitet, måste konstruktionsteamet utföra den åtgärden. Detta kräver en översättning från SQL för att effektivt anropa Marketo API:er för att returnera den minsta mängden data innan sammanfogningen utförs. När du sammanfogar två mycket stora objekt kan dataåtkomstlagret behöva stora resurser på programservern eller skrivbordet. Distributionen av dataåtkomstlagret till en elastisk molntjänst som DataDirect Cloud är därför mycket användbar av två skäl:

1. Snabbare prestanda och färre minnes-/CPU-resurser på klientprogramservern eller skrivbordet
1. Utnyttja den överlägsna bandbredden mellan DataDirect Cloud och Marketo där förkopplade datamängder byts ut.

Hur man hanterar datamodeller? Är det statiskt eller dynamiskt? Hur identifieras och kommuniceras ändringar till klienten? Alla SaaS-datakällor är olika, och i Marketo är det bättre att söka efter vissa objekt via vyer och andra via tabeller. Att hantera den här matrisen med datamodeller och objekt i alla SaaS-källor var verkligen en utmaning.

**Marketo och DataDirect Cloud Reference for Developers:**

* Marketo-anslutningsegenskaper ([länk till dokument](https://www.progress.com/output/DataDirect/DataDirectCloud/index.html))
* SQL och tillägg som stöds med Marketo ([länk till dokument](https://www.progress.com/output/DataDirect/DataDirectCloud/index.html,-hubspot,-and-marketo.html))
* Exponerade Marketo-tabeller och vyer ([länk till dokument](https://www.progress.com/output/DataDirect/DataDirectCloud/index.html))
* Vanliga felmeddelanden returneras från Marketo ([länk till dokument](https://www.progress.com/output/DataDirect/DataDirectCloud/index.html))

**Biografi:** Sumit Sarkar är Chief Data Evangelist på Progress, med över 10 års erfarenhet av att arbeta i dataanslutningsfältet. Världens ledande konsult för öppna datastandarder i anslutning till molndata, Sumits intressen omfattar prestandaoptimering av dataåtkomstskiktet för vilket han har utvecklat en patentsökt teknik för sin analys, affärsintelligens och datalagerhantering för SaaS-plattformar samt datakoppling för PaaS-miljöer, med fokus på standarder som ODBC, JDBC, ADO.NET och ODATA. Han är IBM Certified Consultant för IBM Cognos Business Intelligence och medlem i TDWI. Han har presenterat sessioner om dataanslutning på olika konferenser, bland annat Dreamforce, Oracle OpenWorld, Strata Hadoop, MongoDB World och SAP Analytics och Business Objects Conference. Twitter: @SAsInSumit LinkedIn: [www.linkedin.com/in/meetsumit](http://www.linkedin.com/in/meetsumit)

Publicerad _2015-12-07_ av _Kenny_

## Skapa en instrumentpanel för API-användning och felräkningar

Som Marketo API-kund är det här användbar information som du bör hålla ett öga på. Vad händer om ni kan få historiska användningsdata som hjälper er att identifiera trender över tid? Vad händer om du kan få en sammanfattning av API-felkoder för att mäta integreringens hälsa? Som Marketo Technology Partner, tänk om du kunde få användnings- och feldata för alla dina kundkonton på en och samma kontrollpanel? I det här inlägget beskrivs hur du besvarar frågorna ovan. Sätt igång, nu kör vi!
**Schemalagt jobb för hämtning av statistik** Låt oss skapa en app som hämtar användnings- och feldata med slutpunkterna [Hämta daglig användning](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLast7DaysErrorsUsingGET) och [Hämta dagliga fel](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getDailyErrorsUsingGET). Appen är utformad för att köras en gång om dagen. Varje gång appen körs läggs användningsuppgifter för en dag till i en fil, och en dags feldata till en annan fil. I början av varje månad skapas ett nytt filpar. De här filerna fungerar som en historik som vi kan komma åt när som helst. Här är applogiken ...

* Läs Marketo-kontoinformation (Munchkin-id och klientuppgifter) från en extern källa. Obs! Den här källan måste vara säker för att andra inte ska kunna komma åt kontodata.
* Iterera genom varje konto och..
   * Ring Hämta daglig användning för att hämta användningsdata för en dag
   * Lägg till daglig användningsinformation i en månatlig &quot;användningsfil&quot;
   * Anropa Get Daily Errors för att hämta feldata för en dag
   * Lägg till daglig felinformation i en månadsvis&quot;felfil&quot;

Utdatafilformat Formatet för utdatafilerna är JSON, som matchar den resulterande arrayen som returneras från respektive API-anrop (Användning och fel). Varje element i arrayen &quot;result&quot; är ett JSON-objekt som innehåller data för en dag. Namnge utdatafil Utdatafilerna får följande namn:

`<type>_<yyyy>_<mm>_<account>.json`

Var,

`<type>` - Datatypen (&quot;syntax&quot; eller&quot;fel&quot;) `<yyyy>` - År (4 siffror) `<mm>` - Månad (2 siffror) `<account>` - Konto-ID (Munchkin-id)

Exempel på utdatafil användning_2015_10_111-AAA-222.json

```json
[
    { "date": "2015-10-15", "total": 0, "users" : [] },
    { "date": "2015-10-16", "total": 9, "users": [ { "userId": "some.body@yahoo.com", "count": 9 } ] },
    { "date": "2015-10-17", "total": 1120, "users": [ { "userId": "some.body@yahoo.com", "count": 200 }, { "userId": "some.body@marketo.com", "count": 200 }, { "userId": "some.body@gmail.com", "count": 720 } ] },
]
```

`errors_2015_10_111-AAA-222.json:`

```json
[
    { "date": "2015-10-15", "total":80, "errors": [ { "errorCode":"1003", "count":80 } ] },
    { "date": "2015-10-16", "total":148, "errors": [ { "errorCode":"612", "count":40 }, { "errorCode":"609", "count":70 }, { "errorCode":"1008", "count":38 } ] },
    { "date": "2015-10-17", "total":73, "errors": [ { "errorCode":"604", "count":1 }, { "errorCode":"609", "count":56 }, { "errorCode":"610", "count":16 } ] },
]
```

Kod för den här appen visas i slutet av det här inlägget (Stats.java). **Stats Web Service** Så nu behöver vi ett sätt att få in dessa data i webbläsaren. Förslaget är att skapa en webbtjänst för att leverera data. Webbtjänsten använder programmets utdatafiler och returnerar sedan data till webbläsaren i en form som enkelt kan presenteras. För enkelhetens skull, och för att kringgå policybegränsningar med samma ursprung, använder vi mönstret [JSONP](https://en.wikipedia.org/wiki/JSONP). Här är den föreslagna REST-slutpunktsspecifikationen för den externa webbtjänsten: **URI**: /stats **Metod**: GET

**Parameter**

**Beskrivning**

**Exempel**

månad

Hämta data för den här månaden. Kommaavgränsad lista över månader som ska inkluderas (tvåsiffrig representation). Standard är alla månader.

10,11

år

Hämta data för det här året. Kommaavgränsad lista över år som ska inkluderas (fyrsiffrig representation). Standard till alla år.

2015

konto

Hämta data för det här kontot (Munchkin-id).

111-AAA-222

callback

Namnet på den funktion som JSON-innehåll ska kapslas med.

processStats

Exempelbegäran

`GET //localhost:8080/stats?month=10&year=2015&account=111-AAA-222&callback=processStats`

Webbtjänsten läser &quot;användnings&quot;- och &quot;felfiler&quot; och kombinerar dem och returnerar dem i följande format:

```html
**<Name of Callback here>**

<Contents of Usage file here>,

<Contents of Error file here>
```

Detta är ett JSONP-återanrop med 2 argument. Det första argumentet är att använda matrisen &quot;result&quot;. Det andra argumentet är felen &quot;result&quot;-array. Exempel på svar

```json
processStats(
    [
        { "date": "2015-10-15", "total": 0, "users" : [] },
        { "date": "2015-10-16", "total": 9, "users": [ { "userId": "some.body@yahoo.com", "count": 9 } ] },
        { "date": "2015-10-17", "total": 1120, "users": [ { "userId": "some.body@yahoo.com", "count": 200 }, { "userId": "some.body@marketo.com", "count": 200 }, { "userId": "some.body@gmail.com", "count": 720 } ] }
    ],
    [
        { "date": "2015-10-15", "total":80, "errors": [ { "errorCode":"1003", "count":80 } ] },
        { "date": "2015-10-16", "total":148, "errors": [ { "errorCode":"612", "count":40 }, { "errorCode":"609", "count":70 }, { "errorCode":"1008", "count":38 } ] },
        { "date": "2015-10-17", "total":73, "errors": [ { "errorCode":"604", "count":1 }, { "errorCode":"609", "count":56 }, { "errorCode":"610", "count":16 } ] },
    ]
);
```

Som du ser har webbtjänsten helt enkelt paketerat innehållet i de två utdatafilerna från vår app. Vi har skapat ett dummywebbtjänstsvar med [Mocky](https://designer.mocky.io). Ett exempel på webbtjänsten som modellen är [här.](http://www.mocky.io/v2/5627b2f9270000f2226eec63?month=10&year=2015&account=111-AAA-222&callback=processStats) Den här webbtjänsten skapas inte som en övning för läsaren: **Kontrollpanelens webbsida**. Nu behöver vi alltså bara en webbsida som anropar vår webbtjänst och formaterar data. För att kunna använda JSONP-mönstret behöver vi bara lägga till en `<script>`-tagg som anropar webbtjänsten:

`<script src="http: //<hostname>/stats?month=10&year=2015&account=284-RPR-133&callback=processStats"></script>`

Då läggs webbtjänstens svarsbrödtext in direkt på HTML-sidan. Sedan lägger vi till JSONP-återanropsfunktionen:

```javascript
function processStats(usage, errors) {
    var cfg = { maxDepth: 5};
    document.write("<h2>Usage</h2>");
    document.body.appendChild(prettyPrint(usage, cfg));
    document.write("<h2>Errors</h2>");
    document.body.appendChild(prettyPrint(errors, cfg));
;
```

Den här funktionen anropas automatiskt efter webbtjänstanropet. I det här exemplet anropar vi en enkel JavaScript-variabel dumper som heter [prettyPrint.js](https://github.com/padolsey-archive/prettyprint.js/tree/master) för varje array. Funktionen prettyPrint skapar helt enkelt en HTML-tabell med innehållet i arrayen. Här är en skärmbild av HTML tabeller. Voilà, det är vår instrumentpanel! Det här är inte särskilt elegant, men det borde ge dig en uppfattning om vad som är möjligt. Det finns inget som hindrar dig från att omvandla data på något sätt som du vill göra dina egna effektfulla visualiseringar. HTML-sidan är nedan (Index.html) Du kan visa tabellerna ovan direkt i webbläsaren genom att följa de här stegen:

1. Spara en lokal kopia av Index.html
1. Spara en lokal kopia av prettyPrint.js
1. Öppna Index.html i webbläsaren

Så där är det. Förhoppningsvis har det här inlägget gett dig några idéer om hur du kan övervaka din Marketo API-statistik. Glad kodning! **Stats.java**

```java
package com.marketo;

// minimal-json library (https://github.com/ralfstx/minimal-json)
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;

import java.io.\*;
import java.lang.reflect.Array;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.\*;

import java.nio.file.Files;
import java.nio.file.Paths;

import javax.net.ssl.HttpsURLConnection;

public class Stats {
    //
    // Define Marketo instance meta data here. Each row contains three elements: Account Id, Client Id, Client Secret.
    //
    // Note: that this information would typically be stored read from an external source (i.e. database)
    //
    // For example:
    //
    // private static String final CUSTOM_SERVICE_DATA[][] = {
    // {"111-AAA-222", "2f4a4435-f6fa-4bd9-3248-098754982345", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W"},
    // {"222-BBB-333", "5f4a6657-f6fa-4cd9-4356-123083238821", "gfjgfIVE9h4Jjcl59cOMAKFSk78ut12W"},
    // {"444-CCC-444", "9f4a4678-f6fa-4dd9-7735-908713247721", "xzcxvbVE9h4Jjcl59cOMAKFSk78ut12W"}
    // };
    //
    private static final String CUSTOM_SERVICE_DATA[][] = {
    };

    // Output directory for stats files
    private static final String OUTPUT_DIR = "C:stats";

public static void main(String[] args) {

    // Loop through each Marketo instance
    for (int i = 0; i < CUSTOM_SERVICE_DATA.length; i++) {

        // Compose base URL
        String baseUrl = String.format("https://%s.mktorest.com",
            CUSTOM_SERVICE_DATA[i][0]);

        // Compose Identity URL
        String identityUrl = String.format("%s/identity/oauth/token?grant_type=%s&client_id=%s&client_secret=%s",
            baseUrl, "client_credentials", CUSTOM_SERVICE_DATA[i][1], CUSTOM_SERVICE_DATA[i][2]);

        // Call Identity API
        JsonObject identityObj = JsonObject.readFrom(httpRequest("GET", identityUrl, null));
        String accessToken = identityObj.get("access_token").asString();

        // Compose Get Last 7 Days Usage URL
        String usageUrl = String.format("%s/rest/v1/stats/usage/last7days.json?access_token=%s",
            baseUrl, accessToken);

        // Compose Get Last 7 Days Errors URL
        String errorsUrl = String.format("%s/rest/v1/stats/errors/last7days.json?access_token=%s",
            baseUrl, accessToken);

        // Process usage data
        JsonObject usageObj = JsonObject.readFrom(httpRequest("GET", usageUrl, null));
        if (usageObj.get("success").asBoolean()) {
            if (usageObj.get("result") != null) {
                updateFile(usageObj, "usage", CUSTOM_SERVICE_DATA[i][0]);
            }
        }

        // Process errors data
        JsonObject errorsObj = JsonObject.readFrom(httpRequest("GET", errorsUrl, null));
        if (usageObj.get("success").asBoolean()) {
            if (errorsObj.get("result") != null) {
                updateFile(errorsObj, "errors", CUSTOM_SERVICE_DATA[i][0]);
            }
        }
    }
    System.exit(0);
}

// Write yesterday's data to output file
private static void updateFile(JsonObject usageObj, String statsType, String account){

    JsonArray usageResultAry = usageObj.get("result").asArray();
    JsonObject yesterdayUsageResultObj = usageResultAry.get(1).asObject();

    String yesterdayDate = yesterdayUsageResultObj.get("date").asString();
    String[] yesterdayDateAry = yesterdayDate.split("[-]+");

    // "C:statsstats_yyyy_mm_account.json"
    String statsFile = String.format("%s%s_%s_%s_%s.json",
        OUTPUT_DIR, statsType, yesterdayDateAry[0], yesterdayDateAry[1], account);

    // Create file
    File file = new File(statsFile);
    try {
        if (file.createNewFile()) {
            // created new file, seed with empty array
            FileWriter fw = new FileWriter(file.getAbsoluteFile());
            BufferedWriter bw = new BufferedWriter(fw);
            bw.write("[n]");
            bw.close();
        }
    } catch (IOException e) {
        e.printStackTrace();
    }

    // Read file
    String content = null;
    try {
        content = new String(Files.readAllBytes(Paths.get(statsFile)));
    } catch (IOException e) {
        e.printStackTrace();
    }

    // Remove trailing "]", append new record, append trailing "]"
    content = content.substring(0, content.length() - 1);
    content += yesterdayUsageResultObj.toString();
    content += "n]";

    // Write file
    FileWriter fw = null;
    try {
        fw = new FileWriter(file.getAbsoluteFile());
        BufferedWriter bw = new BufferedWriter(fw);
        bw.write(content);
        bw.close();
    } catch (IOException e) {
        e.printStackTrace();
    }
}

// Perform HTTP request
private static String httpRequest(String method, String endpoint, String body) {
    String data = "";
    try {
        URL url = new URL(endpoint);
        HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
        urlConn.setDoOutput(true);
        urlConn.setRequestMethod(method);
        switch (method) {
            case "GET":
                break;
            case "POST":
                urlConn.setRequestProperty("Content-type", "application/json");
                urlConn.setRequestProperty("accept", "text/json");
                OutputStreamWriter wr = new OutputStreamWriter(urlConn.getOutputStream());
                wr.write(body);
                wr.flush();
                break;
            default:
                System.out.println("Error: Invalid method.");
                return data;
        }
        int responseCode = urlConn.getResponseCode();
        if (responseCode == 200) {
            InputStream inStream = urlConn.getInputStream();
            data = convertStreamToString(inStream);
        } else {
            System.out.println(responseCode);
            data = "Status:" + responseCode;
        }
    } catch (MalformedURLException e) {
        System.out.println("URL not valid.");
    } catch (IOException e) {
        System.out.println("IOException: " + e.getMessage());
        e.printStackTrace();
    }
    return data;
}

private static String convertStreamToString(InputStream inputStream) {
    try {
        return new Scanner(inputStream).useDelimiter("A").next();
    } catch (NoSuchElementException e) {
        return "";
    }
}
}
```

**Index.html**

```html
<html>
<head>
<title>Marketo API Stats</title>
<!-- Browser JavaScript variable dumper                  -->
<!-- https://github.com/padolsey-archive/prettyprint.js  -->
<script src="prettyPrint.js"></script>
</head>
<body>

<h1>Marketo API Stats</h1>

<script>
// JSONP callback that uses prettyPrint to format API stats
function processStats(usage, errors) {
    var cfg = { maxDepth: 5};
    document.write("<h2>Usage</h2>");
    document.body.appendChild(prettyPrint(usage, cfg));
    document.write("<h2>Errors</h2>");
    document.body.appendChild(prettyPrint(errors, cfg));
};
</script>

<!-- Web service for you to implement as an exercise                                                        -->
<!-- <script src="http://localhost:8080/stats?month=10&account=111-AAA-222&callback=processStats"></script> -->

<!-- Mock web service that returns sample payload -->
<!-- http://www.mocky.io/                         -->
<script src="http://www.mocky.io/v2/5627b2f9270000f2226eec63?month=10&year=2015&account=111-AAA-222&callback=processStats"></script>"
</body>
</html>
```

Publicerad _2015-10-22_ av _David_

## Marketo REST API Exception and Error Handling: Part 3

För det mesta går det inte att automatiskt återställa fel som tas emot från Marketo REST API. Det finns emellertid ett antal fall där du kan återställa automatiskt eller se till att du aldrig ser en viss typ av fel.

### Fel i begärandestorlek

Som vi tittade på i det sista inlägget i den här serien skickar Marketo HTTP-statuskod 414 om din URI är längre än 8 kB eller 413 om din begärandetext är större än 1 MB, eller 10 MB för Importera lead. Även om 414s är ovanliga kan du se dem om du använder filtertypen Hämta leads efter för att begära poster baserade på 300 separata GUID:n eller liknande kriterier. Säg att du har följande begäran: `<https://AAA-BBB-CCC.mktorest.com/rest/v1/leads.json?filterType=customGUID&fields=email`, company...firstName, lastName> När du skickar begäran returnerar Marketo statusen 414 eftersom URI:n överstiger 8 kB.

För att kunna hantera detta måste vi ändra mönstret för den här begäran och skicka en POST i stället för en GET, lägga till &quot;_method=GET&quot; i URI:n och skicka frågesträngen i begärandetexten som en x-www-form-urlencoded-begäran i stället: URI: `<https://AAA-BBB-CCC.mktorest.com/rest/v1/leads.json?_method=GET>` Begärandetext: filterType=customGUID&amp;fields=email,company..firstName, lastName i stället för att fånga det här undantaget från HTTP-svaret kan dock bara kontrollera den totala längden på begäran vid körning och distribuera det här alternativa mönstret om URI:n överstiger 8 kB. Du kan också använda POST-metoden i alla fall för batchhämtning av poster. För 413s kan vi följa ett liknande mönster, kontrollera längden på begärandetexten när poster läggs till under serialiseringssteget och dela upp begäran i flera delar om den här gränsen överskrids.

### Autentiseringsfel

Nästa typ av fel som kan återställas är autentisering. När en tidigare giltig åtkomsttoken används efter att perioden expirres_in har gått ut returnerar den första användningen felkoden 602, &quot;Åtkomsttoken utgången&quot;. Därefter returnerar samma token 601, &quot;Åtkomsttoken invalid&quot;. All annan användning av en sträng som inte är en giltig åtkomsttoken för målprenumerationen resulterar i 601. I båda fallen kan det här felet återställas genom att den nya åtkomsttoken autentiseras igen och skickas med ett nytt försök att utföra den misslyckade begäran.

### Timeout

I mycket sällsynta fall kan ett anrop returnera 604,&quot;Timeout för begäran&quot;, efter att den 30 andra timeoutperioden har gått ut. För gruppbegäran, t.ex. Skapa/uppdatera leads, kan begäran delas upp i mindre grupper och försökas igen tills lyckad åtgärd returneras (om gruppen delas upp i färre än 100 poster och begäran fortfarande är timeout, bör du förmodligen arkivera ett supportärende). Det vanligaste andra fallet är anrop av godkännande av tillgångar, där en låsning kan hållas på den aktuella godkända posten av en annan användare eller tjänst, till exempel för en [e-post](https://developer.adobe.com/marketo-apis/api/asset/approve-email-by-id/) eller [e-postmall](https://developer.adobe.com/marketo-apis/api/asset/approve-email-template-by-id/). I dessa fall bör [exponentiell bakomliggande &#x200B;](https://en.wikipedia.org/wiki/Exponential_backoff) användas för nya försök så att befintliga lås kan lösas. Under de närmaste veckorna kommer du att få en sista del av serien där vi tar en närmare titt på några specifika, ej återställbara fel.

Publicerad _2015-10-30_ av _Kenny_

## Marketo säkerhetsförbättringar

På Marketo tar vi säkerhet på allvar. Som en del av ett **[branschövergripande initiativ](https://security.googleblog.com/2014/09/gradually-sunsetting-sha-1.html)** uppgraderar Marketo vår webbautentisering och kryptering för att förbättra våra säkerhetsskydd. Utbyggnaden planeras äga rum den 12 december 2011. **Vem påverkas?** Endast ett litet antal användare påverkas, endast de som har en integrering till Marketo från ett system som är över tio år gammalt och som inte har uppdaterats nyligen. Se **[den här listan](https://www.digicert.com/faq/sha2/sha-2-compatibility)** för mer information om vilka system och versioner som stöds. **Följande användare påverkas inte:**

* Slutanvändare som kommer åt Marketo.com via moderna webbläsare ([se lista](https://www.digicert.com/faq/sha2/sha-2-compatibility))
* Kunder som använder integreringspartners som Informatica, Dell Boomi och Scribe.
* Kunder som använder Launchpoint-partners.

Alla andra Marketo-domäner använder redan SHA2-certifikat.

Publicerad _2015-11-18_ av _Kenny_

## Avsökning efter aktiviteter med REST API

Verksamheter är ett huvudobjekt i Marketo Platform. Aktiviteter är de beteendedata som lagras för varje webbsidesbesök, öppet e-postmeddelande, webbinarium eller deltagande i mässor. Ett vanligt användningsexempel är att kombinera aktivitetsdata med data från andra delar av en organisation. Detta exempelprogram innehåller 6 steg:

1. Anropa [Hämta lead-aktiviteter](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET) om du vill generera en lista med alla aktivitetsposter som skapats vid ett visst datum/tid. Vi använder ett filter för att begränsa vilken typ av aktivitetsposter som returneras.
1. Extrahera intressefält från varje aktivitetspost.
1. Anropa [Hämta flera leads efter filtertyp](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadsByFilterUsingGET) om du vill generera en lista med lead-poster som motsvarar aktiviteter från steg 1. Vi använder fältet leadId som extraherats från aktivitetsposten i steg 2 som vårt filter för att ange vilka leads som returneras.
1. Extrahera intressefält från varje lead-post.
1. Koppla aktivitetsdata från steg 2 till huvuddata från steg 4.
1. Omvandla data från steg 5 till ett format som kan användas av ett externt system.

Som framgår av diagrammet nedan har vi valt att hämta e-postrelaterade aktiviteter.

**Programindata** Som standard går programmet tillbaka en dag från dagens datum för att söka efter ändringar. Du kan till exempel köra det här programmet vid samma tidpunkt varje dag. Om du vill gå längre tillbaka i tiden kan du ange antalet dagar som ett kommandoradsargument och på så sätt öka tidsfönstret. Programmet innehåller flera variabler som du kan ändra: CUSTOM_SERVICE_DATA - Detta innehåller dina anpassade Marketo-tjänstdata (konto-ID, klient-ID, klienthemlighet). READ_BATCH_SIZE - Det här är antalet poster som ska hämtas åt gången. Använd detta för att justera svaret på kroppsstorleken. LEAD_FIELDS - Den här listan innehåller en lista med lead-fält som vi vill samla in. ACTIVITY_TYPES - Den här listan innehåller en lista med aktivitetstyper som vi vill samla in.

**Programlogik** Först skapar vi vårt tidsfönster, skapar REST-slutpunkts-URL:er och hämtar en åtkomsttoken för autentisering. Därefter startar vi en Get Paging Token/Get Lead Activities-loop som körs tills vi tar slut på aktiviteterna. Syftet med den här slingan är att hämta aktivitetsposter och extrahera intressefält från dessa poster. Vi anger att Hämta leadaktiviteter bara ska söka efter följande aktivitetstyper:

* E-post levererad
* Avbeställ e-post
* Öppna e-post
* Klicka på E-post.

Följande intressefält extraheras från varje aktivitetspost:

* leadId
* activityType
* activityDate
* primaryAttributeValue

Du kan välja vilken kombination av aktivitetstyper och aktivitetsfält som helst för att passa ditt syfte. Därefter startar vi en Get Multiple Leads by Filter Type-loop som körs tills vi tar bort tillgången på leads. Observera att vi använder parametern &quot;filterType=id&quot; i kombination med en serie &quot;filterValues&quot;-parametrar för att begränsa antalet lead-poster som hämtas till endast de leads som är kopplade till aktiviteter som vi har hämtat tidigare. Följande intresseområden extraheras från varje lead-post:

* firstName
* lastName
* e-post

Återigen känner du att du väljer vilka huvudfält du vill. Därefter kopplar vi ihop leadfälten med aktivitetsfälten genom att använda lead-ID:t för att länka ihop dem. Slutligen gör vi en slinga genom alla data, omvandlar dem till JSON-format och skriver ut dem på konsolen. **Programutdata** Här är ett exempel på utdata från exempelprogrammet. Detta visar aktivitetsfälten och lead-fälten tillsammans som JSON-resultatobjekt. Tanken här är att du kan skicka denna JSON som nyttolast för en begäran till en extern webbtjänst.

```json
{
    "result": [
        {
            "leadId": 318581,
            "activityType": "Email Delivered",
            "activityDate": "2015-03-17T20:00:06Z",
            "primaryAttributeValue": "My Email Program",
            "firstName": "David",
            "lastName": "Everly",
            "email": "everlyd@marketo.com"
        },
        {
            "leadId":318581,
            "activityType":"Open Email",
            "activityDate":"2015-03-17T23:23:12Z",
            "primaryAttributeValue":"My Email Program - Auto Response",
            "firstName":"David",
            "lastName":"Everly",
            "email":"everlyd@marketo.com"
       },
        ... more result objects here...
    ]
}
```

**Programkod**

```java
package com.marketo;

// minimal-json library (https://github.com/ralfstx/minimal-json)
import com.eclipsesource.json.JsonArray;
import com.eclipsesource.json.JsonObject;
import com.eclipsesource.json.JsonValue;

import java.io.\*;
import java.net.MalformedURLException;
import java.net.URL;
import java.text.DateFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.\*;

import javax.net.ssl.HttpsURLConnection;

public class LeadActivities {
//
// Define Marketo REST API access credentials: Account Id, Client Id, Client Secret.  For example:
    private static Map<String, String> CUSTOM_SERVICE_DATA;
    static {
        CUSTOM_SERVICE_DATA = new HashMap<String, String>();
//        CUSTOM_SERVICE_DATA.put("accountId", "111-AAA-222");
//        CUSTOM_SERVICE_DATA.put("clientId", "2f4a4435-f6fa-4bd9-3248-098754982345");
//        CUSTOM_SERVICE_DATA.put("clientSecret", "asdf6IVE9h4Jjcl59cOMAKFSk78ut12W");
    }

    // Number of lead records to read at a time
    private static final String READ_BATCH_SIZE = "200";

    // Lookup lead records using lead id as primary key
    private static final String LEAD_FILTER_TYPE = "id";

    // Lead record lookup returns these fields
    private static final String LEAD_FIELDS = "firstName,lastName,email";

    // Lookup activity records for these activity types
    private static Map<Integer, String> ACTIVITY_TYPES;
    static {
        ACTIVITY_TYPES = new HashMap<Integer, String>();
        ACTIVITY_TYPES.put(7, "Email Delivered");
        ACTIVITY_TYPES.put(9, "Unsubscribe Email");
        ACTIVITY_TYPES.put(10, "Open Email");
        ACTIVITY_TYPES.put(11, "Click Email");
    }

    public static void main(String[] args) {
        // Command line argument to set how far back to look for lead changes (number of days)
        int lookBackNumDays = 1;
        if (args.length == 1) {
            lookBackNumDays = Integer.parseInt(args[0]);
        }

        // Establish "since date" using current timestamp minus some number of days (default is 1 day)
        Calendar cal = Calendar.getInstance();
        cal.add(Calendar.DAY_OF_MONTH, -lookBackNumDays);
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
        String sinceDateTime = sdf.format(cal.getTime());

        // Compose base URL
        String baseUrl = String.format("https://%s.mktorest.com",
            CUSTOM_SERVICE_DATA.get("accountId"));

        // Compose Identity URL
        String identityUrl = String.format("%s/identity/oauth/token?grant_type=%s&client_id=%s&client_secret=%s",
                baseUrl, "client_credentials", CUSTOM_SERVICE_DATA.get("clientId"), CUSTOM_SERVICE_DATA.get("clientSecret"));

        // Call Identity API
        JsonObject identityObj = JsonObject.readFrom(getData(identityUrl));
        String accessToken = identityObj.get("access_token").asString();

        // Compose URLs for Get Lead Changes, and Get Paging Token
        String activityTypesUrl = String.format("%s/rest/v1/activities/types.json?access_token=%s",
                baseUrl, accessToken);
        String pagingTokenUrl = String.format("%s/rest/v1/activities/pagingtoken.json?access_token=%s&sinceDatetime=%s",
                baseUrl, accessToken, sinceDateTime);

        // Use activity ids to create filter parameter
        String activityTypeIds = "";
        for (Integer id : ACTIVITY_TYPES.keySet()) {
            activityTypeIds += "&activityTypeIds=" + id.toString();
        }

        // Compose URL for Get Lead Activities
        // Only retrieve activities that match our defined activity types
        String leadActivitiesUrl = String.format("%s/rest/v1/activities.json?access_token=%s%s&batchSize=%s",
                baseUrl, accessToken, activityTypeIds, READ_BATCH_SIZE);

        Map<Integer, List> activitiesMap = new HashMap<Integer, List>();
        Set leadsSet = new HashSet();

        // Call Get Paging Token API
        JsonObject pagingTokenObj = JsonObject.readFrom(getData(pagingTokenUrl));
        if (pagingTokenObj.get("success").asBoolean()) {
            String nextPageToken = pagingTokenObj.get("nextPageToken").asString();
            boolean moreResult;
            do {
                moreResult = false;

                // Call Get Lead Activities API to retrieve activity data
                JsonObject leadActivitiesObj = JsonObject.readFrom(getData(String.format("%s&nextPageToken=%s",
                        leadActivitiesUrl, nextPageToken)));
                if (leadActivitiesObj.get("success").asBoolean()) {
                    moreResult = leadActivitiesObj.get("moreResult").asBoolean();
                    nextPageToken = leadActivitiesObj.get("nextPageToken").asString();

                    if (leadActivitiesObj.get("result") != null) {
                        JsonArray activitiesResultAry = leadActivitiesObj.get("result").asArray();
                        for (JsonValue activitiesResultObj : activitiesResultAry) {

                            // Extract activity fields of interest (leadID, activityType, activityDate, primaryAttributeValue)
                            JsonObject leadObj = new JsonObject();
                            int leadId = activitiesResultObj.asObject().get("leadId").asInt();
                            leadObj.add("activityType", ACTIVITY_TYPES.get(activitiesResultObj.asObject().get("activityTypeId").asInt()));
                            leadObj.add("activityDate", activitiesResultObj.asObject().get("activityDate").asString());
                            leadObj.add("primaryAttributeValue", activitiesResultObj.asObject().get("primaryAttributeValue").asString());

                            // Store JSON containing activity fields in a map using lead id as key
                            List leadLst = activitiesMap.get(leadId);
                            if (leadLst == null) {
                                activitiesMap.put(leadId, new ArrayList());
                                leadLst = activitiesMap.get(leadId);
                            }
                            leadLst.add(leadObj);

                            // Store unique lead ids for use as lead filter value below
                            leadsSet.add(leadId);
                        }
                    }
                }
            } while (moreResult);
        }

        // Use unique lead id values to create filter parameter
        String filterValues = "";
        for (Object object : leadsSet) {
            if (filterValues.length() > 0) {
                filterValues += ",";
            }
            filterValues += String.format("%s", object);
        }

        // Compose Get Multiple Leads by Filter Type URL
        // Only retrieve leads that match the list of lead ids that was accumulated during activity query
        String getMultipleLeadsUrl = String.format("%s/rest/v1/leads.json?access_token=%s&filterType=%s&fields=%s&filterValues=%s&batchSize=%s",
                baseUrl, accessToken, LEAD_FILTER_TYPE, LEAD_FIELDS, filterValues, READ_BATCH_SIZE);
        String nextPageToken = "";

        do {
            String gmlUrl = getMultipleLeadsUrl;

            // Append paging token to Get Multiple Leads by Filter Type URL
            if (nextPageToken.length() > 0) {
                gmlUrl = String.format("%s&nextPageToken=%s", getMultipleLeadsUrl, nextPageToken);
            }

            // Call Get Multiple Leads by Filter Type API to retrieve lead data
            JsonObject multipleLeadsObj = JsonObject.readFrom(getData(gmlUrl));
            if (multipleLeadsObj.get("success").asBoolean()) {
                if (multipleLeadsObj.get("result") != null) {
                    JsonArray multipleLeadsResultAry = multipleLeadsObj.get("result").asArray();

                    // Iterate through lead data
                    for (JsonValue leadResultObj : multipleLeadsResultAry) {
                        int leadId = leadResultObj.asObject().get("id").asInt();

                        // Join activity data with lead fields of interest (firstName, lastName, email)
                        List leadLst = activitiesMap.get(leadId);
                        for (JsonObject leadObj : leadLst) {
                            leadObj.add("firstName", leadResultObj.asObject().get("firstName").asString());
                            leadObj.add("lastName", leadResultObj.asObject().get("lastName").asString());
                            leadObj.add("email", leadResultObj.asObject().get("email").asString());
                        }
                    }
                }
            }

            nextPageToken = "";
            if (multipleLeadsObj.asObject().get("nextPageToken") != null) {
                nextPageToken = multipleLeadsObj.get("nextPageToken").asString();
            }
        } while (nextPageToken.length() > 0);

        // Now place activity data into an array of JSON objects
        JsonArray activitiesAry = new JsonArray();
        for (Map.Entry<Integer, List> activity : activitiesMap.entrySet()) {
            int leadId = activity.getKey();
            for (JsonObject leadObj : activity.getValue()) {
                // do something with leadId and each leadObj
                leadObj.add("leadId", leadId);
                activitiesAry.add(leadObj);
            }
        }

        // Print out result objects
        JsonObject result = new JsonObject();
        result.add("result", activitiesAry);
        System.out.println(result);

        System.exit(0);
    }

    // Perform HTTP GET request
    private static String getData(String endpoint) {
        String data = "";
        try {
            URL url = new URL(endpoint);
            HttpsURLConnection urlConn = (HttpsURLConnection) url.openConnection();
            urlConn.setRequestMethod("GET");
            urlConn.setAllowUserInteraction(false);
            urlConn.setDoOutput(true);
            int responseCode = urlConn.getResponseCode();
            if (responseCode == 200) {
                InputStream inStream = urlConn.getInputStream();
                data = convertStreamToString(inStream);
            } else {
                System.out.println(responseCode);
                data = "Status:" + responseCode;
            }
        } catch (MalformedURLException e) {
            System.out.println("URL not valid.");
        } catch (IOException e) {
            System.out.println("IOException: " + e.getMessage());
            e.printStackTrace();
        }
        return data;
    }

    private static String convertStreamToString(InputStream inputStream) {
        try {
            return new Scanner(inputStream).useDelimiter("A").next();
        } catch (NoSuchElementException e) {
            return "";
        }
    }
}
```

Så där ja. Glad kodning!

Publicerad _2015-11-20_ av _David_

## Integrera SoundCloud-spelaren med Munchkin API

SoundCloud är en fantastisk hostingplattform för ljud, med omfattande analyser och funktionalitet för allt från att sträva efter indie-grejer, till EDM-artister högst upp i musikbranschen, till berättande poddsändningar. Tillsammans med plattformens fantastiska inbyggda funktioner finns ett API-program i världsklass för att flytta data och spåra avlyssningsbeteende. Detta är särskilt användbart för poddsändningar och kan göra att du kan korrelera specifika avlyssningsåtgärder, som uppspelningar, pauser och delningar med specifikt innehåll i skriptet och ljudet. Idag ska vi titta på hur vi kan använda [SoundClouds widget-API](https://developers.soundcloud.com/docs/api/html5-widget) för att skicka och spåra dessa aktiviteter i Marketo. Först ska vi titta på hur man skapar en Munchkin-aktivitet som spelas in i en leds aktivitetsinloggning Marketo. Vi anropar som mest grundläggande Munchkin.munchkinFunction och skickar&quot;visitWebPage&quot; som första argument. Detta loggar en webbplatsaktivitet för besök med Marketo och registrerar alla godtyckliga URL- och frågesträngsdata som skickas till metoden. Det andra argumentet tar emot ett JavaScript-objekt med våra data, som har två medlemmar, &quot;url&quot; och&quot;params&quot;, båda strängarna. URL-medlemmen motsvarar webbsidan för aktiviteten i Marketo, medan parametrarna motsvarar frågesträngen. För våra syften använder vi URL:en som en identifierare för SoundCloud-relaterade åtgärder,&quot;soundCloudInteraction&quot;, medan parametrar innehåller ytterligare data om den aktuella aktiviteten. Här är funktionen som vi använder för att spåra varje åtgärd:

```javascript
var trackActivity = function(action){

    //set action param to be the string passed to the function
    var qs = "action=" + action;

    //use getCurrentSound callback to get the name of the current track
    soundCloudMunchkin.widget.getCurrentSound(function(currentSound){

        //add it to our querystring
        qs = qs + "&sound=" + currentSound.title;

        //use the getPosition callback to get the position of the track in ms
        soundCloudMunchkin.widget.getPosition(function(position){

            //add it to the querystring
            qs = qs + "&position=" + position;

            //assemble our data object for the munchkin activity
            var dataObject = {
                "url": "soundCloudInteraction",
                "params": qs
            }

            //call the munchkinFunction to submit the activity
            Munchkin.munchkinFunction("visitWebPage", dataObject);
        });
    });
}
```

Eftersom standardwidgeten SoundCloud är inbäddad i en iframe används postmeddelanden i widgeten för att kommunicera och återanrop måste användas för att hämta data, vilket du kan se med metoderna currentSound och getPosition. API:t för SoundCloud-widgeten innehåller en uppsättning JavaScript-återanrop som vi kan använda för att svara på enskilda händelser i spelaren och skicka dessa till Marketo. De händelser som vi är intresserade av är vad användaren lyssnar på, hur länge användaren lyssnar och hur interaktioner användaren gör med spelaren, så vi tittar på följande händelser:

* SPELA
* PAUS
* FINISH
* SEEK
* CLICK_DOWNLOAD
* CLICK_BUY
* OPEN_SHARE_PANEL

Vi måste också använda metoden bind() från widgeten för att lägga till återanrop till var och en av dessa händelser. Låt oss titta på ett exempel:

```javascript
widget.bind(SC.Widget.Events.PLAY, function(){
    soundCloudMunchkin.trackPlay();
});
```

Detta gör att när ett spår spelas upp, startar vi metoden trackPlay för att skicka en händelse till Marketo med data om det aktuella spåret. Det fullständiga skriptet [finns här](https://gist.github.com/kelkingtron/6750bb07c1397d93d9c7#file-soundcloudmunchkin-js). SoundCloudMunchkin-objektet har en init-metod som accepterar ett SoundCloud-widget-objekt som dess enda argument, som binder spårningsmetoderna till relevanta återanrop och konfigurerar widgeten så att den kan spåra aktiviteter ned till Marketo. Din sida måste ha din [Munchkin-kod](/help/javascript-api/lead-tracking.md) inläst, samt [SoundCloud API-biblioteket](https://w.soundcloud.com/player/api.js). Du måste också initiera allt, förutom att bädda in din faktiska SoundCloud-widget:

```javascript
window.onload=function(){
var iframe = document.getElementById(iframeId);
    if(iframe) {
        widget = SC.Widget(iframe);
        soundCloudMunchkin.init(widget);
    };
};
```

Publicerad _2015-12-21_ av _Kenny_

## RTP och EU:s direktiv om integritetsskydd

I det här inlägget beskrivs hur du kan använda RTP för att meddela webbplatsbesökare att de spåras eller automatiskt inaktivera spårning för europeiska besökare. Sedan 2012 måste alla webbplatser som är tillgängliga för europeiska besökare följa EU:s direktiv om e-sekretess. Nya lagar trädde i kraft 2011 som förhindrar att identifieringsinformation lagras på användarens dator utan deras vetskap och medgivande. Om du använder cookies eller någon annan teknik för icke nödvändig spårning måste du:

1. Meddela användarna att spårningstekniker används.
1. Förklara skälen till att använda dessa tekniker.
1. Inhämta användarens samtycke innan den tekniken används och ge denne tillstånd att återkalla tillstånd när som helst.

Publicerad _1970-01-01_ av _Yanir_

## Uppdatera information om kunder och potentiella kunder i Marketo med åtkomstpunkten

Det finns scenarier där egenutvecklade system används för att uppdatera information om kunder och potentiella kunder. Marknadsföringsteamet vill att uppdateringarna ska återspeglas i Marketo så att de får det mest korrekta registersystemet att använda i sina marknadsföringskampanjer. Med hjälp av nedanstående metod kan du ställa in periodiska överföringar till Marketo för att hålla kontaktinformationen för Marketo uppdaterad med data som ändrats i det egna systemet. Diagrammet nedan visar de API-anrop som görs för en angiven periodisk timer. När den periodiska timern aktiveras hämtar klientlogiken först uppdaterade kontakter från det egna systemet. Hur detta görs skiljer sig från system till system med antingen API:er eller dataexport från det egna systemet. Vi detaljerar de Marketo-API:er som körs när den uppdaterade kontaktinformationen hämtas. SOAP Request for syncMultipleLeads:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:paramsSyncMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <leadRecordList>
    <leadRecord>
      <Email>henry@superstar.com</Email>
      <ns2:leadAttributeList>
        <attribute>
          <attrName>FirstName</attrName>
          <attrValue>Henry</attrValue>
        </attribute>
        <attribute>
          <attrName>LastName</attrName>
          <attrValue>Adams</attrValue>
        </attribute>
        <attribute>
          <attrName>Title</attrName>
          <attrValue>Director of Demand Generation</attrValue>
        </attribute>
      </ns2:leadAttributeList>
    </leadRecord>
    <leadRecord>
      <Email>ssmith@gmail.com</Email>
      <ns2:leadAttributeList>
        <attribute>
          <attrName>FirstName</attrName>
          <attrValue>Suzie</attrValue>
        </attribute>
        <attribute>
          <attrName>LastName</attrName>
          <attrValue>Smith</attrValue>
        </attribute>
        <attribute>
          <attrName>Title</attrName>
          <attrValue>VP Marketing</attrValue>
        </attribute>
      </ns2:leadAttributeList>
    </leadRecord>
  </leadRecordList>
  <dedupEnabled>true</dedupEnabled>
</ns2:paramsSyncMultipleLeads>
```

SOAP Response från syncMultilpeLeads:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ns2:successSyncMultipleLeads xmlns:ns2="<http://www.marketo.com/mktows/">
  <result>
    <syncStatusList>
      <syncStatus>
        <leadId>1094593</leadId>
        <status>UPDATED</status>
        <error xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
      </syncStatus>
      <syncStatus>
        <leadId>1094594</leadId>
        <status>UPDATED</status>
        <error xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true" />
      </syncStatus>
    </syncStatusList>
  </result>
</ns2:successSyncMultipleLeads>
```

`syncMultipleLeads` utför en UPSERT-åtgärd. Om det redan finns en kontakt inom Marketo baserat på den e-postadress som skickats uppdateras attributen. Om en kontakt inte finns skapas den. Svaret från `syncMultipleLeads` returnerar status för var och en av de skickade kontakterna. `<attrName/>`-värdena i `<leadAttributeList/>` måste matcha SOAP API Name som definierats för den Marketo-prenumerationen. Du kan identifiera SOAP API-namn i avsnittet för fälthantering på Marketo administratörspanel genom att exportera fältnamnen.

Se exemplet nedan på Java-programmet som kör det scenario som beskrivs ovan:

```java
 import com.marketo.mktows.\*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;
import java.util.\*;

public class SyncMultipleLeadsExample {

  public static void main(String[] args) {

    try {
      URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
      String marketoUserId = "CHANGE ME";
      String marketoSecretKey = "CHANGE ME";

      QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
      MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
      MktowsPort port = service.getMktowsApiSoapPort();

      // Create Signature
      DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
      String text = df.format(new Date());
      String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
      String encryptString = requestTimestamp + marketoUserId ;

      SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
      Mac mac = Mac.getInstance("HmacSHA1");
      mac.init(secretKey);
      byte[] rawHmac = mac.doFinal(encryptString.getBytes());
      char[] hexChars = Hex.encodeHex(rawHmac);
      String signature = new String(hexChars);

      // Set Authentication Header
      AuthenticationHeader header = new AuthenticationHeader();
      header.setMktowsUserId(marketoUserId);
      header.setRequestTimestamp(requestTimestamp);
      header.setRequestSignature(signature);

      // Create Request
      ParamsSyncMultipleLeads request = new ParamsSyncMultipleLeads();

      ObjectFactory objectFactory = new ObjectFactory();

      JAXBElement dedup = objectFactory.createParamsSyncMultipleLeadsDedupEnabled(true);
      request.setDedupEnabled(dedup);

      // The list of contacts defined here would be retrieved from the proprietary system
      Contact contact = new Contact("Henry","Adams","henry@superstar.com", "Director of Demand Generation");
      Contact contact2 = new Contact("Suzie","Smith","ssmith@gmail.com", "VP Marketing");

      ArrayList updatedContacts = new ArrayList();
      updatedContacts.add(contact);
      updatedContacts.add(contact2);

      ArrayOfLeadRecord arrayOfLeadRecords = new ArrayOfLeadRecord();

      Iterator it = updatedContacts.iterator();
      while(it.hasNext())
      {
        Contact c = it.next();

        LeadRecord leadRec = new LeadRecord();

        JAXBElement email = objectFactory.createLeadRecordEmail(c.email);
        leadRec.setEmail(email);

        Attribute attr1 = new Attribute();
        attr1.setAttrName("FirstName");
        attr1.setAttrValue(c.fname);

        Attribute attr2 = new Attribute();
        attr2.setAttrName("LastName");
        attr2.setAttrValue(c.lname);

        Attribute attr3 = new Attribute();
        attr3.setAttrName("Title");
        attr3.setAttrValue(c.title);

        ArrayOfAttribute aoa = new ArrayOfAttribute();
        aoa.getAttributes().add(attr1);
        aoa.getAttributes().add(attr2);
        aoa.getAttributes().add(attr3);

        QName qname = new QName("http://www.marketo.com/mktows/", "leadAttributeList");
        JAXBElement attrList = new JAXBElement(qname, ArrayOfAttribute.class, aoa);

        leadRec.setLeadAttributeList(attrList);
        arrayOfLeadRecords.getLeadRecords().add(leadRec);

      }

      request.setLeadRecordList(arrayOfLeadRecords);

      JAXBContext context = JAXBContext.newInstance(SuccessSyncMultipleLeads.class);
      Marshaller m = context.createMarshaller();
      m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
      m.marshal(request, System.out);

      SuccessSyncMultipleLeads result = port.syncMultipleLeads(request, header);

      m.marshal(result, System.out);
    }

    catch(Exception e) {
      e.printStackTrace();
    }
  }

  public static class Contact {
    public String fname, lname, email, title;

      public Contact(String fname, String lname, String email, String title) {
          this.fname = fname;
          this.lname = lname;
          this.email = email;
          this.title = title;
      }
  }
}
```

Den här artikeln innehåller kod som används för att implementera anpassade integreringar. På grund av den anpassade karaktären kan Marketo tekniska support inte felsöka skräddarsytt arbete. Försök inte att implementera följande kodexempel utan lämplig teknisk erfarenhet, eller använd en erfaren utvecklare.

Publicerad _2014-03-24_ av _Travis Kaufman_

## Skicka ett transaktionsmejl från Marketo med API:t

Det kräver att en befintlig Smart Campaign skapas med Marketo-gränssnittet. E-postmottagaren måste också finnas i Marketo. Innan du anropar API:t requestCampaign använder du [getLead API]&#x200B;(/help/soap-api/getlead.md) för att kontrollera om e-postmeddelandet finns i Marketo. När du har gjort ett anrop via API:t requestCampaign kan du bekräfta det genom att kontrollera om Smart Campaign har körts i Marketo. Vi visar först hur du skapar en smart kampanj, därefter hur du ställer in en utlösare för att skicka en kampanj via API:t, dels hur du definierar ett e-postmeddelande som en del av en flödesåtgärd, och slutligen hur du använder ett kodexempel för att köra kampanjen.
**Så här skapar du en ny smart kampanj i Marketo** Smarta kampanjer i Marketo och utför alla dina marknadsföringsaktiviteter. Du kan konfigurera en serie automatiska åtgärder som ska vidtas i en smart lista med kontakter. När det gäller att skicka transaktionsmeddelanden ställer du in en utlösare i kampanjen, som visas nedan, för att skicka e-post med API:t. Först ställer vi in Smart Campaign. 1. I marknadsföringsaktiviteter väljer du ett program och klickar sedan på Ny lokal resurs i listrutan Nytt.

1. Klicka på Smart Campaign
1. Ange ett smart kampanjnamn och klicka på Skapa

**Lägg till utlösare i en smart kampanj** Genom att lägga till utlösare i en smart kampanj kan du göra så att en smart kampanj körs på en person i taget baserat på en live-händelse, vilket i det här fallet är en begäran via [requestCampaign API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/triggerCampaignUsingPOST) . 1. Sök efter utlösaren&quot;Campaign is Requested&quot; och dra och släpp den sedan på arbetsytan.

1. I utlösaren väljer du&quot;is&quot; och&quot;Web Service API&quot;.

**Så här skapar du e-postflödesåtgärd för en kampanj** Genom att associera ett e-postmeddelande med en smart kampanj kan marknadsförarna hantera hur de vill att ett e-postmeddelande ska se ut, och tredjepartsprogrammet kan avgöra vem som tar emot det och när. När du har skapat ett e-postmeddelande som en ny lokal resurs kan du ange den som en flödesåtgärd i en kampanj.  Sök efter och välj det e-postmeddelande som du vill skicka.

**Kodexempel som anropar API:t requestCampaign** När du har konfigurerat kampanjen och utlösarna i Marketo-gränssnittet visar vi hur du använder API:t för att skicka ett e-postmeddelande. Det första exemplet är en XML-begäran, det andra är ett XML-svar och det sista är ett Java-kodexempel som kan användas för att generera XML-begäran. Vi visar även hur du hittar det kampanj-ID som används vid anrop till API:t `requestCampaign`.
API-anropet kräver även att du känner till ID:t för Marketo-kampanjen i förväg. Du kan bestämma kampanj-ID med någon av följande metoder: 1. Använd [getCampaignsForSource](/help/soap-api/getcampaignsforsource.md) API 1. Öppna Marketo-kampanjen i en webbläsare och se URL-adressfältet. Kampanj-ID (representerat som ett fyrsiffrigt heltal) finns omedelbart efter SC. Exempel: `<https://app-stage.marketo.com/#SC**1025**A1>`. Den fetstilta delen är kampanj-ID -&quot;1025&quot;. SOAP-begäran för `requestCampaign`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809939944BFABAE58E5D27</mktowsUserId><requestSignature>48397ad47b71a1439f13a51eea3137df46441979</requestSignature><requestTimestamp>2013-08-01T12:31:14-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsRequestCampaign>
      <source>MKTOWS</source>
      <campaignId>4496</campaignId>
      <leadList>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>lead@company.com</keyValue>
        </leadKey>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>anotherlead@company.com</keyValue>
        </leadKey>
      </leadList>
    </ns1:paramsRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

SOAP-svar för requestCampaign

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successRequestCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

Nedan visas ett exempel på ett Java-program som kör det scenario som beskrivs ovan.

```java
import com.marketo.mktows.*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;


public class RequestCampaign {

    public static void main(String[] args) {
        System.out.println("Executing Request Campaign");
        try {
            URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
            String marketoUserId = "CHANGE ME";
            String marketoSecretKey = "CHANGE ME";

            QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
            MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
            MktowsPort port = service.getMktowsApiSoapPort();

            // Create Signature
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
            String text = df.format(new Date());
            String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
            String encryptString = requestTimestamp + marketoUserId ;

            SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
            Mac mac = Mac.getInstance("HmacSHA1");
            mac.init(secretKey);
            byte[] rawHmac = mac.doFinal(encryptString.getBytes());
            char[] hexChars = Hex.encodeHex(rawHmac);
            String signature = new String(hexChars);

            // Set Authentication Header
            AuthenticationHeader header = new AuthenticationHeader();
            header.setMktowsUserId(marketoUserId);
            header.setRequestTimestamp(requestTimestamp);
            header.setRequestSignature(signature);

            // Create Request
            ParamsRequestCampaign request = new ParamsRequestCampaign();

            request.setSource(ReqCampSourceType.MKTOWS);

            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Integer> campaignId = objectFactory.createParamsRequestCampaignCampaignId(4496);
            request.setCampaignId(campaignId);

            ArrayOfLeadKey leadKeyList = new ArrayOfLeadKey();
            LeadKey key = new LeadKey();
            key.setKeyType(LeadKeyRef.EMAIL);
            key.setKeyValue("lead@company.com");

            LeadKey key2 = new LeadKey();
            key2.setKeyType(LeadKeyRef.EMAIL);
            key2.setKeyValue("anotherlead@company.com");

            leadKeyList.getLeadKeies().add(key);
            leadKeyList.getLeadKeies().add(key2);

            JAXBElement<ArrayOfLeadKey> arrayOfLeadKey = objectFactory.createParamsRequestCampaignLeadList(leadKeyList);
            request.setLeadList(arrayOfLeadKey);

            SuccessRequestCampaign result = port.requestCampaign(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessRequestCampaign.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            m.marshal(result, System.out);

        }
        catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

Den här artikeln innehåller kod som används för att implementera anpassade integreringar. På grund av den anpassade karaktären kan Marketo tekniska support inte felsöka skräddarsytt arbete. Försök inte att implementera följande kodexempel utan lämplig teknisk erfarenhet, eller använd en erfaren utvecklare.

Publicerad _2014-03-27_ av _Murta_

## Skicka ett e-postmeddelande med dynamiskt innehåll från Marketo med hjälp av AP-

Föreställ dig att du vill automatisera e-postmeddelanden till kundtjänst. När din supporttekniker har talat med en kund vill du automatiskt skicka ett e-postmeddelande som tackar dem för att de har kontaktat ditt företag. Låt oss gå ett steg längre och säga att du vill ta med det specifika samtalsämnet som diskuterades med kunden som du spårar i CRM. Du kan göra detta från Marketo med hjälp av API:t requestCampaign i SOAP för att skicka ett e-postmeddelande med dynamiskt innehåll. API:t requestCampaign gör att du kan skicka in en lead eller leads. Ni kan också skicka in programtoken som kan användas med en befintlig kampanj för att skicka dynamiskt innehåll. API:t requestCampaign för SOAP kräver att e-postmottagaren finns i Marketo. Innan du anropar API:t requestCampaign bör du därför använda API:t [getLead](/help/soap-api/getlead.md) för att kontrollera om e-postmeddelandet finns i Marketo. Vi visar först hur du skapar en smart kampanj, och sedan hur du ställer in en utlösare för att skicka en kampanj via API:t, för det tredje hur du skapar ett e-postmeddelande som accepterar dynamiskt innehåll via programtoken, för det fjärde hur du definierar ett e-postmeddelande som en del av en flödesåtgärd och för det femte ett kodexempel som skulle användas för att köra den här kampanjen. **Så här skapar du en ny smart kampanj i Marketo** Smarta kampanjer i Marketo och utför alla dina marknadsföringsaktiviteter. Du kan konfigurera en serie automatiska åtgärder som ska vidtas i en smart lista med kontakter. När det gäller att skicka transaktionsmeddelanden ställer du in en utlösare i kampanjen, som visas nedan, för att skicka e-post med API:t. Först ställer vi in Smart Campaign. 1. I marknadsföringsaktiviteter väljer du ett program och klickar sedan på Ny lokal resurs i listrutan Nytt.

1. Klicka på Smart Campaign
1. Ange namnet på den smarta kampanjen och klicka på Skapa **Lägg till utlösare i en smart kampanj** Om du lägger till utlösare i en smart kampanj kan du göra så att en smart kampanj körs på en person i taget baserat på en live-händelse, vilket i det här fallet är en begäran via [requestCampaign API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/triggerCampaignUsingPOST) .
1. Sök efter utlösaren&quot;Campaign is Requested&quot; och dra och släpp den sedan på arbetsytan.
1. I utlösaren väljer du&quot;is&quot; och&quot;Web Service API&quot;.

**Så här skickar du in dynamiskt innehåll med API:t** i Marketo. Mina token är variabler som du kan använda i ditt program. Med Mina token kan du ange information som gäller ditt program på ett ställe, ersätta den informationen med ett värde som du anger och hämta informationen i andra delar av programmet, till exempel en e-postmall. Med API:t requestCampaign SOAP kan du skicka en array med programtoken, som åsidosätter befintliga token. När kampanjen har körts ignoreras token. Du skapar Mina token på mappnivå Campaign eller på programnivå. Mina token på Campaign-mappnivå ärver ned till alla program som finns i Campaign-mappen. Om du skapar Mina token på mappnivå för Campaign kan du skriva över det ärvda värdet på programnivå. Om du till exempel definierar variabler för Programdatum och Programbeskrivning på mappnivå för Campaign kan du skriva över dessa värden på den enskilda programnivån.

Så här gör du. 1. I trädet Marknadsföringsaktiviteter väljer du den Campaign-mapp eller det Program där du vill skapa token. Välj Mina token på den övre menyraden. Sedan visas arbetsytan Mina token. Dra en tokentyp från det högra trädet till arbetsytan, som i det här fallet är &quot;Text&quot;. I fältet Tokennamn markerar du Min token och anger ett unikt tokennamn, som i det här fallet är &quot;my.conversationtopic&quot;. I fältet Värde anger du ett relevant värde för token, som i det här fallet är&quot;Tack för att du ringer oss idag&quot;. Observera att om vi använder API åsidosätter vi standardvärdet för Min token. Klicka på Spara för att spara den anpassade token.  1. Skapa ett nytt e-postmeddelande genom att klicka på Nytt. Klicka sedan på Ny lokal Assets och välj E-post. Fyll sedan i fälten för att namnge din e-post. När du skriver ditt e-postmeddelande klickar du på ikonen Token för att inkludera tokens i ditt e-postmeddelande. Nu när du har skapat e-postmallen med Tokens lägger vi till e-postmeddelandet som en flödesåtgärd för Campaign i det följande steget. Så när ni anropar kampanjen via API:t skickas e-postmeddelandet ut.
**Så här skapar du e-postflödesåtgärd för en kampanj** Genom att associera ett e-postmeddelande med en smart kampanj kan marknadsförarna hantera hur de vill att ett e-postmeddelande ska se ut, och tredjepartsprogrammet kan avgöra vem som tar emot det och när. När du har skapat ett e-postmeddelande som en ny lokal resurs kan du ange den som en flödesåtgärd i en kampanj. Sök efter och välj det e-postmeddelande som du vill skicka.
**Kodexempel som anropar API:t requestCampaign** När du har konfigurerat kampanjen och utlösarna i Marketo-gränssnittet visar vi hur du använder API:t för att skicka ett e-postmeddelande. Det första exemplet är en XML-begäran, det andra är ett XML-svar och det sista är ett Java-kodexempel som kan användas för att generera XML-begäran. Vi visar även hur du hittar det kampanj-ID som används för att anropa API:t requestCampaign. API-anropet kräver även att du känner till ID:t för Marketo-kampanjen i förväg. Du kan bestämma kampanj-ID med någon av följande metoder: 1. Använd [getCampaignsForSource](/help/soap-api/getcampaignsforsource.md) API 1. Öppna Marketo-kampanjen i en webbläsare och se URL-adressfältet. Kampanj-ID (representerat som ett fyrsiffrigt heltal) finns omedelbart efter SC. Exempel: `<https://app-stage.marketo.com/#SC&#x200B;**1025**&#x200B;A1>`. Den fetstilta delen är kampanj-ID -&quot;1025&quot;. SOAP Request for requestCampaign

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809939944BFABAE58E5D27</mktowsUserId><requestSignature>48397ad47b71a1439f13a51eea3137df46441979</requestSignature><requestTimestamp>2013-08-01T12:31:14-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsRequestCampaign>
      <source>MKTOWS</source>
      <campaignId>4496</campaignId>
      <leadList>
        <leadKey>
          <keyType>EMAIL</keyType>
          <keyValue>lead@company.com</keyValue>
        </leadKey>
      </leadList>
      <programTokenList>
        <attrib>
          <name>{{my.conversationtopic}}</name>
          <value>Thank you for calling about adding a line of service to your current plan.</value>
        </attrib>
      </programTokenList>
    </ns1:paramsRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

SOAP-svar för requestCampaign

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="<http://schemas.xmlsoap.org/soap/envelope/>" xmlns:ns1="<http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successRequestCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successRequestCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

Nedan visas ett exempel på ett Java-program som kör det scenario som beskrivs ovan.

```java
import com.marketo.mktows.\*;
import java.net.URL;
import javax.xml.namespace.QName;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;


public class RequestCampaign {

    public static void main(String[] args) {
        System.out.println("Executing Request Campaign");
        try {
            URL marketoSoapEndPoint = new URL("CHANGE ME" + "?WSDL");
            String marketoUserId = "CHANGE ME";
            String marketoSecretKey = "CHANGE ME";

            QName serviceName = new QName("http://www.marketo.com/mktows/", "MktMktowsApiService");
            MktMktowsApiService service = new MktMktowsApiService(marketoSoapEndPoint, serviceName);
            MktowsPort port = service.getMktowsApiSoapPort();

            // Create Signature
            DateFormat df = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
            String text = df.format(new Date());
            String requestTimestamp = text.substring(0, 22) + ":" + text.substring(22);
            String encryptString = requestTimestamp + marketoUserId ;

            SecretKeySpec secretKey = new SecretKeySpec(marketoSecretKey.getBytes(), "HmacSHA1");
            Mac mac = Mac.getInstance("HmacSHA1");
            mac.init(secretKey);
            byte[] rawHmac = mac.doFinal(encryptString.getBytes());
            char[] hexChars = Hex.encodeHex(rawHmac);
            String signature = new String(hexChars);

            // Set Authentication Header
            AuthenticationHeader header = new AuthenticationHeader();
            header.setMktowsUserId(marketoUserId);
            header.setRequestTimestamp(requestTimestamp);
            header.setRequestSignature(signature);

            // Create Request
            ParamsRequestCampaign request = new ParamsRequestCampaign();

            request.setSource(ReqCampSourceType.MKTOWS);

            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Integer> campaignId = objectFactory.createParamsRequestCampaignCampaignId(4496);
            request.setCampaignId(campaignId);

            ArrayOfLeadKey leadKeyList = new ArrayOfLeadKey();
            LeadKey key = new LeadKey();
            key.setKeyType(LeadKeyRef.EMAIL);
            key.setKeyValue("lead@company.com");

            leadKeyList.getLeadKeies().add(key);

            JAXBElement<ArrayOfLeadKey> arrayOfLeadKey = objectFactory.createParamsRequestCampaignLeadList(leadKeyList);
            request.setLeadList(arrayOfLeadKey);

            ArrayOfAttrib aoa = new ArrayOfAttrib();

            Attrib attrib = new Attrib();
            attrib.setName("{{my.conversationtopic}}");
            attrib.setValue("Thank you for calling about adding a line of service to your current plan.");

            aoa.getAttribs().add(attrib);

            JAXBElement<ArrayOfAttrib> arrayOfAttrib = objectFactory.createParamsRequestCampaignProgramTokenList(aoa);
            request.setProgramTokenList(arrayOfAttrib);

            SuccessRequestCampaign result = port.requestCampaign(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessRequestCampaign.class);
            Marshaller m = context.createMarshaller();
            m.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            m.marshal(result, System.out);

        }
        catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```

När du har gjort ett anrop via API:t requestCampaign kan du bekräfta det genom att kontrollera om Smart Campaign har körts i Marketo.

Den här artikeln innehåller kod som används för att implementera anpassade integreringar. På grund av den anpassade karaktären kan Marketo tekniska support inte felsöka skräddarsytt arbete. Försök inte att implementera följande kodexempel utan lämplig teknisk erfarenhet, eller använd en erfaren utvecklare.

Publicerad _2014-04-03_ av _Murta_

## Samla in anonym besöksaktivitet baserat på affärslogik

Tänk dig att du vill spåra användare som besöker ett visst inlägg på företagets blogg. Låt oss säga av det totala antalet användare som besöker ett inlägg, att du bara vill spåra användare som signalerar sitt intresse genom att spendera minst 5 sekunder och rulla nedåt på sidan. För anonyma användare vill du skapa en ny lead i Marketo med den här händelsen och för kända användare vill du uppdatera deras lead-aktivitet med den här händelsen. Du kan uppnå detta genom att använda [Munchkin spårningskod](/help/javascript-api/lead-tracking.md) på webbplatsen. När en icke-cookie-användare går till en sida med spårningskoden för Munchkin skapas en ny cookie i användarens webbläsare och en ny anonym lead skapas i Marketo. Om användaren redan är cookie och om användaren redan är en lead i Marketo, registreras besöket på sidan i aktivitetsloggen för användaren i Marketo. Vi visar först hur du genererar spårningskod för Munchkin i Marketo, och sedan hur du ändrar Munchkin exempelkod så att den bara aktiveras om vissa villkor uppfylls, och för det tredje hur du verifierar att ett sidbesök från en anonym användare spelades in i Marketo.

**Så här genererar du spårningskod för Munchkin** Med spårningskoden för Munchkin kan du spåra besök på din webbplats. Det finns tre typer av Munchkin-kod som beskrivs nedan, men i det här exemplet använder vi den asynkrona Munchkin-spårningskoden. A) Enkel: har minst antal kodrader, men optimerar inte för webbsidans inläsningstid. Den här koden läser in jQuery-biblioteket varje gång en webbsida läses in. B) Asynkron: minskar inläsningstiden för webbsidor. Den här koden kontrollerar om jQuery-biblioteket redan finns, läser in det om det saknas och använder det för att köra spårningskod när resten av webbsidan har lästs in. C) Asynkron jQuery: minskar inläsningstiden för webbsidor och förbättrar även systemprestanda. Den här koden förutsätter att du redan har jQuery och inte kontrollerar att den har lästs in. 1. Klicka på Admin längst upp till höger i programmet.  1. Klicka på Munchkin i trädet till vänster.  1. Välj Asynkron som spårningskodtyp. 1. Klicka på och kopiera spårningskoden för JavaScript för att lägga ut den på webbplatsen.
**Kodexempel till cookie-användare och spårningshändelse** Placera spårningskoden på dina webbsidor precis före `</body>` -taggen. Landningssidor som skapats i Marketo innehåller automatiskt spårningskod, så du behöver inte placera den här koden på dem. Detta kodexempel anropar Munchkin API när skriptet har lästs in:

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      Munchkin.init('XXX-XXX-XXX');
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

Det här kodexemplet anropar Munchkin API efter att användaren har varit på sidan i 5 sekunder och har även rullat 500 pixlar nedåt på sidan:

```javascript
<script src="https://code.jquery.com/jquery-2.1.0.min.js"></script>
<script type="text/javascript">
$(function(){
 setTimeout(function(){
  $(window).scroll(function() {
      var y_scroll_position = window.pageYOffset;
      var scroll_position = 500; //Sets number of pixels user must scroll to be tracked

  if(y_scroll_position > scroll_position) {
  //Munchkin tracking code
   (function() {
     var didInit = false;
     function initMunchkin() {
      if(didInit === false) {
        didInit = true;
        Munchkin.init('XXX-XXX-XXX');
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
   }
 },5000); //Sets time delay before tracking user
});
</script>
```

**Hur man verifierar sidbesök av anonyma användare spelades in i Marketo**

1. Klicka på Analytics (Analyser) i den övre menyn och klicka sedan på New Report (Ny rapport). Välj Webbsidesaktivitet som rapporttyp och ge sedan rapporten ett namn.
1. När du har skapat en rapport klickar du på Smart lista. Välj sedan filtret Besök webbsida i rutan till höger. Ange webbsidan där du placerar spårningskoden för Munchkin.
1. Klicka på Inställningar. Välj anonyma besökare från Internet-leverantörer och ändra alternativet till Visa.
1. Klicka på Rapport. Aktiviteten visas nu på den webbsida du valde.
1. Dubbelklicka på lead-posten, som visar aktivitetsloggen där du kan se den specifika sida som den anonyma användaren besökte.

Den här artikeln innehåller kod som används för att implementera anpassade integreringar. På grund av den anpassade karaktären kan Marketo tekniska support inte felsöka skräddarsytt arbete. Försök inte att implementera följande kodexempel utan lämplig teknisk erfarenhet, eller använd en erfaren utvecklare.

Publicerad _2014-04-17_ av _Murta_

## Ändra lokalt telefonnummer dynamiskt med RTP

Personalization är allt - vi kom på det här för länge sedan. Med detta sagt är det fortfarande överraskande för mig att varje gång jag behöver omedelbar hjälp är det så svårt att hitta relevanta lokala telefonnummer på en webbplats. Vi har [Marketo Real-Time Personalization](https://business.adobe.com/se/products/marketo/content-personalization.html) (RTP) installerat på <https://business.adobe.com/se/products/marketo/adobe-marketo.html>. Vi kan använda [RTP Visitor API](/help/javascript-api/web-personalization.md) för att dynamiskt ändra det telefonnummer som en webbbesökare ser i olika delar av webbplatsen. Wow! Kan du tro det här? Hur fungerar den här magin? Först måste du ha RTP installerat på din webbplats enligt beskrivningen [här](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/web-personalization/rtp-tag-implementation/deploy-the-rtp-javascript). Följ sedan instruktionerna nedan och implementera JavaScript-koden på din webbplats:

1. Infoga ditt internationella telefonnummer i konfigurationen **defaultPhone**
1. Infoga HTML-element-ID:n i konfigurationen **divIds**
1. Om du vill göra telefonnumret till en länk för att klicka-och-anropa för webbläsare i mobilen anger du **mobileLink**-konfigurationen till **true**.
1. Mappa olika platser med deras telefonnummer i konfigurationerna **cityPhone**, **statePhone** och **countryPhone**

Här följer exempel på värden för konfigurationsinställningar:

```json
  defaultPhone:"+1.503.608.4679", // Optional
  divIds:["phoneId1","phoneId2"],
  mobileLink: true,
  cityPhone: {
    "<a href="#">yanir</a>": ["San Mateo", "San Francisco"],
    "+353.1.242.3000": ["tel-aviv"]
  },
  statePhone: {
    "+1.650.376.2300": ["CA"],
    "+1.650.376.2302": ["OR"]
  },
  countryPhone: {
    "+1.650.376.2300": ["United States"],
    "+353.1.242.3000": ["Israel"]
  }
```

Infoga slutligen en HTML-ankartagg som innehåller ett ID som matchar ett av ID:na i **divIds** (från steg 2 ovan). Om du till exempel angav &quot;phoneId1&quot; i **divIds** skulle HTML-ankartaggen se ut så här:

`<a href="tel:+1800229933" id="phoneId1">+1800229933</a>`

Skriptet kontrollerar om det finns en matchning i den här ordningen: cityPhone > statePhone > countryPhone > defaultPhone Du kan också ersätta telefonnumren med text (Exempel: &quot;Gå med i vår San Francisco-användargrupp!&quot;) eller HTML-kod och ändra dynamiskt innehållet baserat på geografisk plats. Mycket nöje!

```html
<a href="tel:+1800229933" id="phoneId1">+1800229933</a>
<script>
(function(a){
    rtp('get','visitor',function(yc){
        var location = yc.results.location;
        var loop = true;
        var phoneChanged = false;
        console.log(yc.results);
        function checkObj(obj){
            return Object.getOwnPropertyNames(obj).length >0;
        }
        function changePhone(p){
            d=a.divIds;
            for(i=0;i<d.length;i++){
                if(document.getElementById(d[i]) !== null){
                    document.getElementById(d[i]).innerHTML = p;
                    if(a.mobileLink){
                        document.getElementById(d[i]).href= "tel:" + p;
                    }
                    console.log(p);
                }
            }
            loop = false;
            phoneChanged = true;
        }
        function matchLocation(loc,objc){
            for (var key in objc) {
                for(i=0;i<objc[key].length && loop;i++){
                    if (!loop) { return true;};
                    val = objc[key][i];
                    //console.log(loc + location[loc] + " ? " + val);
                    if(location[loc].toLowerCase() === val.toLowerCase()){
                        changePhone(key);
                    }
                }
            }
        }
        if(checkObj(a.cityPhone)){
            matchLocation("city",a.cityPhone);
        }else if(checkObj(a.statePhone)){
            matchLocation("state",a.statePhone);
        }else if(checkObj(a.countryPhone)){
            matchLocation("country", a.countryPhone);
        }else if(!phoneChanged && a.defaultPhone.length > 0 ){
            changePhone(a.divId,a.defaultPhone);
        }
    });
})({
    defaultPhone:"",  //  [Optional] the number to show if visitor does not match the mapping below
    divIds:["phoneId1","Floater"],  //the phone HTML element ID, can be <div>, <a>, <span>, <p> etc.
    mobileLink: true,  //if you use click to call link (with href="tel:") you can also change its number

    cityPhone: {
        "<a href='#'>yanir</a>": ["San Mateo", "San Francisco"],
        "+353.1.242.3000": ["tel-aviv"]
    },
    statePhone: {
        "+1.650.376.2300": ["CA"],
        "+1.650.376.2302": ["OR"]
    },
    countryPhone: {
        "+1.650.376.2300": ["United States"],
        "+353.1.242.3000": ["Israel"]
    }
});
</script>
```

Publicerad _2016-02-02_ av _Yanir_

## Vintern 2016 - uppdateringar

### Anpassade objekt

* [Anpassade objekt N:N-relationer stöds nu](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/administration/marketo-custom-objects/create-marketo-custom-objects)
   * Lead- eller kontoposter kan nu ha många-till-många-relationer genom anpassade objekt via definitionen av mellanliggande objekt. När du har skapat en fristående anpassad objekttyp kan du skapa en mellanliggande objekttyp med länkfält till både det fristående objektet och antingen leads eller konton.
   * Det finns inga nya API-anrop för den här funktionen, men objektdefinitionerna måste vara korrekt konfigurerade för att kunna utnyttja dessa relationer via API:t.
* `getLeadActivities` och `getLeadChanges` returnerar inte längre aktiviteter för anonyma leads. Mer information finns i [Nästa generations vanliga frågor om Munchkin-spårning](https://experienceleague.adobe.com/sv/docs/marketo/using/home)

Publicerad _2016-02-05_ av _Kenny_

## Hämta aktiviteter för ett enskilt lead med REST API

Här är en fråga som vi flera gånger får frågan från vår utvecklarcommunity:

&quot;Hur får jag en lista över tidigare aktiviteter för en enskild lead?&quot;

Fram tills helt nyligen fanns det inget enkelt sätt att uppnå detta med REST API. Men nu finns det! Vintern 2016-utgåvan av REST API innehåller en liten förbättring. [Hämta lead-aktiviteter](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET) accepterar nu parametern **leadIds** som kan användas för att ange ett lead-ID. När parametern **leadIds** anges returneras endast aktiviteter för detta lead-ID. Du kan tänka dig detta som ett lead-id-filter. Observera att parametern **leadIds** kan ha en kommaavgränsad lista med lead-ID om du vill filtrera resultaten på mer än en lead (upp till 30). Detta kan till exempel vara praktiskt när aktiviteter begränsas till leads för ett visst företag. **Exempel** nedan är en exempelbegäran till [Hämta lead-aktiviteter](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadActivitiesUsingGET) som innehåller parametern **leadIds**. Jag har angett värdet 50 för parametern **leadIds** som motsvarar ett godtyckligt lead i min Marketo-instans. Jag har angett värdet 129 för parametern activityTypeIds, vilket motsvarar aktiviteten Mobile App Session i min Marketo-instans.

`<https://123-abc-456.mktorest.com/rest/v1/activities.json?leadIds=50&activityTypeIds=129&nextPageToken=WQV2VQVPPCKHC6AQYVK7JDSA3J4SMAZRQO4RKIXCEMLFCM2APRSQ====>`

Nedan visas ett utdrag av svaret från den begäran som anges ovan. Som du ser innehåller den bara resultatobjekt med leadId: 50 och activityTypeId: 129.

```json
{
    "id": 846,
    "leadId": 50,
    "activityDate": "2015-04-06T21:58:59Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 7
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
{
    "id": 879,
    "leadId": 50,
    "activityDate": "2015-04-07T00:45:11Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 5
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
{
    "id": 1114,
    "leadId": 50,
    "activityDate": "2015-04-08T00:02:41Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 241
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
{
    "id": 1551,
    "leadId": 50,
    "activityDate": "2015-04-09T23:31:56Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 223
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
{
    "id": 1716,
    "leadId": 50,
    "activityDate": "2015-04-15T22:44:19Z",
    "activityTypeId": 129,
    "primaryAttributeValueId": 13,
    "primaryAttributeValue": "Sample App",
    "attributes": [
        {
            "name": "Device Type",
            "value": "iPhone"
        },
        {
            "name": "Mobile App Session Length",
            "value": 223
        },
        {
            "name": "Platform",
            "value": "ios"
        }
    ]
}
```

## Smidig integrering med Marketo och över 500 appar med Zapier

Det här är ett inlägg från Philippe Dele Case, Principal Solutions Consultant, Marketo.

### Mål

I den här artikeln beskrivs i detalj hur du kan integrera Marketo med över 500 molnappar, tack vare Zapier. Därför ska vi bygga en Zapier-anslutning från grunden för Marketo och implementera två praktiska integreringsexempel: Fall 1: Unidirectional Leads integration from FullContact Card Reader to Marketo

* Skanna in vilken kontakts visitkort som helst med mobilappen FullContact Reader och få en lead som skapas automatiskt i Marketo.
* Lägg till en befintlig lead i en statisk lista i Marketo och hitta den lead som automatiskt läggs till i din Google-mall.
* Ändra vilket lead som helst i Google Sheet och se hur ändringen slår tillbaka till Marketo.

### Förutsättningar

**Registrera dig för ett kostnadsfritt konto hos Zapier** [Zapier](https://zapier.com/) är en webbprogramautomatiseringstjänst som gör att du enkelt kan automatisera uppgifter mellan andra onlineprogram utan att behöva använda programmerare eller IT-resurser. En kort introduktion finns [här](https://zapier.com/api/v4/helpdocs/category/redirect/what-is-zapier). Idag stöder Zapier över 500 appar i många olika domäner, t.ex. Marketing, CRM, CMS, Customer Support, Electronic Signature, Forms. En enda integrering mellan ett program och ett annat kallas för en Zap. Kontrollera Zapiers [zapbook](https://zapier.com/apps) för en fullständig lista över webbprogram som stöds.  Registrera ett kostnadsfritt konto [här](https://zapier.com/sign-up/). Du får tillgång till upp till 100 uppgifter/månad, 5 zaps och zaps var 15:e minut. Du kan förstås få mycket mer genom att prenumerera på Zapiers betalda planer (grundläggande, affärsplan, företagsavtal osv.)

**Åtkomst till en Marketo-instans som administratör eller med ett angivet API-användarkonto** Vår Zapier-anslutning använder Marketo REST API för att skicka lead-data till Marketo. För att kunna använda detta API behöver du en API-användare och en anpassad tjänst som du kan skapa själv om du är administratör för din Marketo-instans. Annars måste administratören ge dig dem. Det finns också en webkrok att skapa som bara är tillgänglig för en Marketo-administratör. En stegvis förklaring om hur du skapar Marketo API-användaren och den anpassade tjänsten finns [här](http://rest-api/custom-services/). När du är klar bör du ha följande autentiseringsuppgifter för att anropa Marketo REST API: Klient-ID, Klienthemlighet, Munchkin konto-ID, Munchkin konto-ID

Du kan hämta Munchkin konto-ID från Munchkin eller administrationsskärmarna för webbtjänster. Dess mönster ser ut så här: `000-XXX-000`.  Du behöver inte hämta en åtkomsttoken eftersom den bara skulle vara giltig för en timme. Kopplingen genererar automatiskt tokens åt dig.
**Registrera dig för ett kostnadsfritt konto hos Google Docs, Sheets och Slides är produktivitetsprogram där du kan skapa olika typer av onlinedokument, arbeta med dem i realtid med andra och lagra dem i Google Drive online. Vi behöver ett Google Sheet. Olika funktioner i Google Docs och att skapa ett konto hos Google finns [här](https://workspace.google.com/products/docs/).
**Registrera dig för ett kostnadsfritt konto med FullContact** FullContact håller dig helt ansluten till de personer som betyder mest genom att dra in alla dina kontakter och kontinuerligt synkronisera dem med ändringar i sociala profiler, foton, e-postsignaturer, företagsinformation med mera. De har en mobil visitkortsläsare som kan skanna in kort till över 250 webbappar, inklusive Zapier. Du kan registrera ett kostnadsfritt konto [här](https://app.fullcontact.com/login). Du kan också prenumerera på ett betalkonto med extra funktioner och kapacitet. Mobilappen kan hämtas från [Apple AppStore](https://apps.apple.com/us/app/fullcontact-business-card/id576780807) eller från [Google Play](https://play.google.com/store/apps/details?id=com.fullcontact.cardreader). FullContact-grupperna dokumenteras [här](https://zapier.com/apps/contacts-plus/integrations).

### Implementering av Marketo Connector for Zapier

**Skapa Marketo-appen** Gå till utvecklarportalen från Zapier-webbgränssnittet. Klicka på **Lägg till ny app** och fyll i åtminstone rubriken (till exempel Marketo) och beskrivningen. Logotypen är valfri, men trevligt att ha.\ **Autentisering** I det här avsnittet deklarerar vi de olika fält som används för Marketo REST API-autentisering och autentiseringsinställningarna. Skapa först följande fält:

Redigera &#39;Autentiseringsinställningar&#39;:

* Autentiseringstyp: Sessionsautentisering
* Autentiseringsmappning:

  `{"access_token":"{{access_token}}"}`

* Åtkomsttokenplacering&#x200B;**:** Token i frågesträng

När en anpassad Marketo-tjänst har skapats blir klient-ID och klienthemlighet tillgängliga. Klient-ID och klienthemlighet används för att generera en åtkomsttoken via REST API-slutpunkten [Authentication](/help/rest-api/authentication.md). Vi kan sedan använda denna åtkomsttoken för att göra efterföljande förfrågningar till REST API. Token upphör att gälla efter en timme och måste genereras igen för att du ska kunna fortsätta anropa REST API. Vi valde autentiseringstyp = &#39;Sessionsautentisering&#39; eftersom det gör att vi kan köra ett anpassat autentiseringsskript varje gång som sessionstoken upphör att gälla. I avsnittet Skriptprogrammeringsgränssnitt får du se hur du implementerar den här funktionen som bara kan fungera med den här typen av autentisering.
**Utlösare** Zapier-utlösare finns där för att hämta data till Zapier. Vi behöver ingen för våra användningsområden eftersom vi använder en Marketo Webkrok istället. Vi måste dock fortfarande skriva en provutlösare som ett obligatoriskt test för vår Marketo-kontakt. Vi kommer att skapa en testutlösare som anropar slutpunkten för Marketo REST API [Get Daily Usage](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getDailyUsageUsingGET) . Klicka på **Lägg till ny utlösare** för att starta guiden och fylla i följande fält (fält som inte omnämns kan lämnas tomma): Namn och beskrivning

* Namn: Testutlösare
* Nyckel: test_trigger
* Beskrivning: Testutlösaren för Marketo App
* Viktigt? Inte markerad
* Dölj? Markerad

Utlösarfält

* Ingen

Varifrån data kommer

* Data Source: Avsökning
* Avsöknings-URL: `https://{{munchkin_account_id}}.mktorest.com/rest/v1/stats/usage.json`

Provresultat

* Lämna tomt

Klicka på **Hantera utlösarinställningar** och ange att Testutlösaren ska vara den som vi använder för att verifiera användarens inloggningsuppgifter. **Åtgärder** Zapier-åtgärder finns för att skicka data från Zapier. Vi implementerar åtgärden Create_Update Lead som anropar Marketo REST API. Med den här åtgärden kan vi skapa en ny lead i Marketo, eller om leadet redan finns, uppdateras den med de inskickade värdena. Vi använder fältet&quot;e-post&quot; för borttagning av dubbletter. Klicka på **Lägg till ny åtgärd** för att starta guiden och fylla i följande fält (fält som inte omnämns kan lämnas tomma): Namn och beskrivning

* Namn: Create_Update Lead
* Antal: Lead
* Nyckel: create-update-lead
* Beskrivning: Skapa en ny lead i Marketo eller uppdatera den med inskickade värden om leadet redan finns
* Viktigt? Markerad
* Dölj? Inte markerad

Åtgärdsfält Åtgärdsfält är de fält som användarna ska mappa data till. Välj dem noga efter dina egna behov eftersom de representerar alla data som du kan uppdatera i Marketo. Det finns ett alternativ i Zapier för att erbjuda slutanvändaren alla fält som är tillgängliga i Marketo, men det skulle leda till mer kod och komplexitet, vilket inte krävs för en engångsanslutning. Vi har t.ex. valt följande fält.

Partitionsnamn är obligatoriskt i vårt fall eftersom vår Marketo-instans har Lead Partitions i tjänst. Det kan uteslutas annars. Vi separerade den från indatagruppen så att slutanvändaren förstår att detta inte är ett fält som ska synkroniseras. Fältet Anteckningar kommer från en synkronisering mellan Marketo och Salesforce, använd det inte om du inte har det i din Marketo-instans. Fältet&quot;Anropat&quot; har skapats i vår Marketo-instans, använd det inte om du inte har det i din Marketo-instans. Målet är förstås att du ska kunna välja de fält du behöver från Marketo. Vi rekommenderar att du börjar med small och lägger till de extra fälten senare. Var ska data skickas?

* URL för åtgärdsslutpunkt: `https://{{munchkin_account_id}}.mktorest.com/rest/v1/leads.json`

Provresultat

* Lämna tomt

### Zapier skriptnings-API

Zapiers skriptfunktion gör att du kan hantera förfrågningar och svar som utbyts mellan appens API och Zapier. Du kan ändra HTTP-begäranden precis innan de skickas och kan analysera svaren innan Zapier gör något med dem. Vi behöver den för att slutföra vår anpassade autentisering av sessionsautentisering så att den fungerar med Marketo. Mer information finns [här](https://zapier.com/developer/documentation/scripting/). Kopiera följande kod så går vi igenom några förklaringar senare:

```javascript
var Zap = {

    get_session_info: function(bundle) {

       console.log('Entering get_session_info method ...');

         var access_token,
            access_token_request_payload,
            access_token_response;


        // Assemble the meta data for our Access Token swap request
         console.log('building Request with client_id=' + bundle.auth_fields.client_id + ', and client_secret=' + bundle.auth_fields.client_secret);
        access_token_request_payload = {
            method: 'POST',
            url: 'https://' + bundle.auth_fields.munchkin_account_id + '.mktorest.com/identity/oauth/token',
            params: {
                'grant_type' : 'client_credentials',
                'client_id' : bundle.auth_fields.client_id,
                'client_secret' : bundle.auth_fields.client_secret
            },
            headers: {
                'Content-Type': 'application/json',  // Could be anything.
                Accept: 'application/json'
            }
        };

        // Fire off the Access Token request.
        access_token_response = z.request(access_token_request_payload);

        // Extract the Access Token from returned JSON.
        access_token = JSON.parse(access_token_response.content).access_token;
        console.log('New Access_Token=' + access_token);

        // This will be mixed into bundle.auth_fields in future calls.
        //bundle.auth_fields.access_token=access_token;
        return {'access_token': access_token};
    },


    test_trigger_pre_poll: function(bundle) {

         console.log('Entering test_trigger_pre_poll method ...');

         bundle.request.params = {
         'access_token':bundle.auth_fields.access_token
         };

         return bundle.request;

    },


    test_trigger_post_poll: function(bundle) {

        console.log('Entering test_trigger_post_poll method ...');

        var data = JSON.parse(bundle.response.content);
        if ((!data.success)&&((data.errors[0].code=="601")||(data.errors[0].code=="600"))){
            console.log('Access Token expired or invalid, requesting new one - data.success=' + data.success + ', data.errors[0].code=' + data.errors[0].code);

           throw new InvalidSessionException(); // Calling get_session_info() to regenerate Access Token
        }

        return JSON.parse(bundle.response.content);
    },

    create_update_lead_pre_write: function(bundle) {

       bundle.request.params = {'access_token':bundle.auth_fields.access_token};
       return bundle.request;
    },

    create_update_lead_post_write: function(bundle) {

         var data = JSON.parse(bundle.response.content);
         if ((!data.success)&&((data.errors[0].code=="601")||(data.errors[0].code=="600"))){
            console.log('Access Token expired or invalid, requesting new one - data.success=' + data.success + ', data.errors[0].code=' + data.errors[0].code);
            throw new InvalidSessionException(); // Calling get_session_info() to regenerate Access Token
        }
        return JSON.parse(bundle.response.content);
    }
};
```

**Metoder** **get_session_info**

* Den här metoden genererar eller återskapar en åtkomsttoken som anropar Marketo REST API [Authentication](/help/rest-api/authentication.md) -slutpunkten.
* Den anropas varje gång någon post_poll-metod påträffar ett fel av typen Access Token Expired. En åtkomsttoken har schemalagts att upphöra var 1 timme så detta är förväntat.
* URL för åtgärdsslutpunkt: https://{{munchkin_account_id}}.mktorest.com/identity/oauth/token

**pre_poll, pre_write**

* Vi måste skapa en pre-poll-metod för alla utlösare vi har skapat, för att ändra HTTP-begäran precis innan den skickas, så att vi kan lägga till Marketo Access Token i dess parametrar.
* Vi måste skapa en&quot;pre-write&quot;-metod för alla åtgärder vi har skapat, av samma anledning.

**post_poll, post_write**

* Vi måste skapa en post-poll-metod för alla utlösare som vi har skapat, för att analysera svaren innan Zapier gör något med dem och så småningom avbryta felet &quot;Åtkomsttoken har upphört&quot;.
* Vi måste skapa en post-write-metod för alla åtgärder vi har skapat, av samma anledning.
* Om ett sådant fel har inträffat genererar vi ett InvalidSessionException som instruerar Zapier att spela upp autentiseringen och köra metoden get_session_info igen.

Observera att du kan komma åt paketloggarna från skript-API:t på snabblänksmenyn i skärmens övre högra hörn. Det här är användbart om du vill felsöka skripten.

Användningsfall 1: Integrering av Marketo med FullContact Card Reader

För den här integreringen skapar vi en enda Zap från FullContact till Marketo. Med den här Zap-funktionen kan du skanna visitkort med FullContact Mobile Card Reader och skicka leads till Marketo.   **Zap FullContact -> Marketo** Klicka på knappen Skapa en ny Zap på kontrollpanelen i Zapier.

**Utlösare i Zapier**

* Välj App FullContact
* Välj FullContact-utlösare &#39;Nytt visitkort&#39;
* Anslut till ditt FullContact-konto
* Testa FullContact-appen

**Åtgärd i Zapier**

* Välj den app som Marketo skapade tidigare, den ska visas i Beta
* Välj Marketo-åtgärd &#39;Create_Update Lead&#39;
* Anslut till ditt Marketo-konto och fyll i autentiseringsparametrarna (Munchkin konto-ID, klient-ID, Klienthemlighet)
* Mappa fälten från FullContact till Marketo
* Fyll i så småningom i ett partitionsnamn där dina nya leads ska placeras (endast om det finns partitioner i din Marketo-instans)
* Testa Marketo App
* Aktivera din Zap

Obs! Se till att du hämtar visitkorten Reader från FullContact och aktiverar Zapier Integration direkt från din mobila enhet.

Användningsfall 2: Integrering av Marketo med Google Sheets-

För den här integreringen skapar vi två Zaps. En från Marketo till Google Sheets och en annan från Google Sheets till Marketo. Med den här Zap-funktionen kan du synkronisera några av dina leads eller kontakter mellan Marketo och ett Google-blad.   **Zap Marketo Webkrok -> Google Sheets**
För första Zap är vi inte beroende av en anpassad anslutning för Marketo, men vi använder Marketo webbhooks och utlösaren&quot;Webhooks av Zapier&quot;. Klicka på knappen Skapa en ny Zap på Zapier-kontrollpanelen. **Utlös del 1 i Zapier**

* Välj utlösarappen &#39;Webhooks by Zapier&#39;
* Kontrollera &#39;Catch Hook&#39; som gör att du kan vänta på en POST eller GET till en Zapier-URL
* Du behöver inte välja en underordnad nyckel
* Zapier genererade en anpassad **webkrok-URL** som du kan skicka begäranden till och kopiera den till Urklipp

**Webkrok i Marketo (steg som ska utföras av en administratör)**

* Gå till Admin -> Webbhooks
* Skapa en ny webkrok med namnet&quot;Push Lead to Zapier&quot; och redigera Webkroks-formuläret.

I mallens fält deklarerar du alla Lead-fält som du vill överföra till Zapier och använder Marketo-tokens. I våra användningsfall använder vi samma fält som vi har definierat för den anpassade Zapier-kontakten som skickar leads till Marketo:

```json
{
"firstName":"{{lead.First Name}}",
"lastName":"{{lead.Last Name}}",
"email":"{{lead.Email Address}}",
"phone":"{{lead.Phone Number}}",
"leadOwner":"{{lead.Lead Owner First Name}} {{lead.Lead Owner Last Name}}",
"leadOwnerEmail":"{{lead.Lead Owner Email Address}}",
"leadNotes":"{{lead.Lead Notes:default=edit me}}",
"called":"{{lead.Called}}"
}
```

* Spara formuläret
* Ingen svarsmappning behövs, så du är klar med webkroken

**Testa kampanj i Marketo (steg som ska utföras av en marknadsförare eller en administratör)**

* Skapa en ny smart kampanj från marknadsföringsaktiviteterna

Vi ska skapa en kampanj som triggar vår webkrok varje gång en lead-status ändras till MQL. Du kan förstås använda Webkroken för andra syften.

* Redigera den smarta listan
* Ring webbkroken i flödet
* Schemalägg kampanjen
* Se till att varje lead kan gå igenom flödet varje gång
* Aktivera den smarta kampanjen

**Utlös del 2 i Zapier**

* För att slutföra utlösarappen&quot;Webhooks by Zapier&quot; måste vi avfyra Marketo Smart Campaign en gång och fånga webkroken i Zapier
* I testfallet behöver vi bara gå till Marketo Lead Database, öppna en lead och ändra dess status till MQL

**Skapa kalkylbladet i Google-blad**

* Skapa ett nytt kalkylblad
* Skapa ett kalkylblad eller använd ett standardkalkylblad
* Lägg till en kolumn för varje fält som du vill synkronisera från Marketo (de som deklareras på Marketo-webkroken)

  **Åtgärd i Zapier**

* Välj appen Google Sheets
* Markera alternativet Skapa kalkylbladsrad
* Anslut till ditt Google Sheets-konto
* Välj Google-kalkylblad
* Markera kalkylbladet
* Mappa alla fält mellan utlösarappen för Webhooks av Zapier och Google Sheets.

* Testa appen Google Sheets
* Aktivera din Zap

**Zap Google Sheets -> Marketo**

Klicka på knappen Skapa en ny Zap på Zapier-kontrollpanelen.

**Utlösare i Zapier**

* Välj utlösarappen Google Sheets
* Markera den uppdaterade tabellrad som utlöses när en ny rad läggs till eller ändras i ett kalkylblad
* Anslut till ditt Google-konto
* Markera det kalkylblad som du vill utlösa från (ska vara det som användes i föregående Zap) och kalkylbladet
* Ange utlösarkolumnen till &#39;any_column&#39;
* Testa appen Google Sheets

**Åtgärd i Zapier**

* Välj den app som Marketo skapade tidigare, den ska visas i Beta
* Välj Marketo-åtgärd &#39;Create_Update Lead&#39;
* Anslut till ditt Marketo-konto och fyll i autentiseringsparametrarna (Munchkin konto-ID, klient-ID, Klienthemlighet)
* Mappa fälten från Google Sheets till Marketo
* Fyll i så småningom i ett partitionsnamn där dina nya leads ska placeras (endast om det finns partitioner i din Marketo-instans)
* Testa Marketo App
* Aktivera din Zap

### Slutsats

Här är några idéer om hur vi kan förbättra vår Marketo-kontakt för Zapier:

* Lägga till andra utlösare och åtgärder som rör olika Marketo-objekt (listor, anpassade objekt osv.)
* I stället för att hårdkoda fälten från Marketo kan du dynamiskt hämta fälten från Marketo, men det skulle kräva en viss teknisk översättning mellan Marketo och Zapier.
* Dela kontakten med utvecklingsteamet och gör den allmänt tillgänglig.

Det är möjligt att Zapier kommer att driftsätta ett Premium Marketo-kort, vilket gör det mycket enklare att implementera våra användningsexempel. I vilket fall som helst kan den här artikeln alltid användas för att integrera Marketo med Zapier med en kostnadsfri Zapier-plan och även för att skapa anpassade användningsfall som kanske inte stöds av en premiumadapter. Vi hoppas att du tyckte om den här artikeln och att den kommer att hjälpa dig att bli ännu bättre med Marketo och Zapier. Tack!

Publicerad _2016-04-17_ av _David_

## Uppdateringar våren 2016

**REST API**

* Resurs-API - webbsidor
   * **Landningssidor** visas nu med femton nya slutpunkter som gör det möjligt att skapa, uppdatera, ta bort, klona och hantera utkast för landningssidor. Mallar för landningssidor har nu även slutpunkter för utkastshantering exponerade
      * Hämta landningssidor
      * Hämta landningssida efter ID
      * Hämta landningssida efter namn
      * Skapa landningssida
      * Uppdatera landningssidans metadata
      * Hämta innehåll för landningssida
      * Lägg till avsnitt för startsidans innehåll
      * Uppdatera avsnitt för startsidans innehåll
      * Ta bort avsnitt för startsidans innehåll
      * Hämta avsnitt med dynamiskt innehåll
      * Uppdatera avsnitt med dynamiskt innehåll
      * Ignorera startsidesutkast
      * Godkänn landningssida
      * Ta bort landningssidesutkast
      * Ta bort landningssida
   * **Mallar för landningssidor**
      * Ignorera utkast av landningssidmall
      * Godkänn mall för landningssida
      * Ta bort landningssidmall
      * Ta bort mall för landningssida
   * **Forms** har 21 nya slutpunkter som ger dig full tillgång till funktioner för att skapa, redigera och hantera via API:t. API:erna stöder inte ändringar i Forms 1.0-formulär.
      * Skaffa Forms
      * Hämta formulär efter ID
      * Hämta formulär efter namn
      * Hämta formulärfältslista
      * Uppdatera formulärfältslista
      * Skapa formulär
      * Formulärets tacksida
      * Uppdatera formulärtack
      * Uppdatera formulär
      * Ignorera formulärutkast
      * Godkänn formulär
      * Ogodkänt formulär
      * Klona formulär
      * Ta bort formulär
      * Uppdatera formulärfält
      * Ta bort formulärfält
      * Uppdatera visningsregler för formulärfält
      * Lägg till textformulärfält
      * Lägg till fältuppsättning
      * Ta bort fält från fältuppsättning
      * Hämta tillgängliga formulärfält
      * Ändra formulärfältspositioner
      * Uppdatera Skicka-knapp
   * När du använder **Get eller Browse Programs** returneras SFDC Campaign-ID för program som är länkade till en SFDC Campaign

**Egna objekt** Anpassade objekt har nu stöd för datatyper i textområdet, vilket gör att strängfält med upp till 2 000 tecken kan lagras i anpassade objektfält av den här typen. **Vitlista över IP-adresser** Administratörsanvändare kan nu hantera en vitlista över IP-adresser för att förhindra obehörig åtkomst via API:erna. [Du kan läsa mer om funktionen här](https://experienceleague.adobe.com/sv/docs/marketo/using/home). **Anpassat aktivitetsgränssnitt** Administratörsanvändare kan nu definiera anpassade aktivitetstyper på sin administratörsmeny och lägga till poster till leads via API:t [Lägg till anpassade aktiviteter](https://developer.adobe.com/marketo-apis/api/mapi/#operation/addCustomActivityUsingPOST) . [Du kan läsa om hur du definierar anpassade aktivitetstyper här](https://experienceleague.adobe.com/sv/docs/marketo/using/home).

Publicerad _2016-06-01_ av _Kenny_

## Uppdateringar sommaren 2016

För sommarversionen 2016 den 23 september finns det tre utvecklarorienterade funktioner som släpps.

### Stöd för e-post 2.0 i REST API

Alla [befintliga resurs-API:er](/help/rest-api/assets.md) som bara var kompatibla med e-post och mallar för version 1.0 har nu aktiverats för användning med e-postresurser för version 2.0.

### Push Lead to Marketo

[Push Lead](/help/rest-api/leads.md) är en alternativ metod för leadsynkronisering som är utformad för att göra det enklare att aktivera i smarta kampanjer. Du kan skapa ett enda aktivitetsloggsobjekt, associera ett lead och uppdatera lead-posten i ett samtal. Detta fungerar på samma sätt som en enstaka formulärfyllning från en lead, och kan enklare användas som en proxymetod för att skicka formulär i stället för att använda den befintliga metoden Synkronisera leads.

### HTTP-komprimering

REST API kan nu komprimera svar med den standard som definieras av specifikationen HTTP 1.1. Detta kan bidra till att minska svarsstorleken, vilket ökar överföringshastigheten och minimerar bandbreddsanvändningen.  

Publicerad _2016-09-23_ av _Kenny_

## Använda swagger-codegen med Marketo

[Swagger-codegen](https://github.com/swagger-api/swagger-codegen) är ett kraftfullt Java-bibliotek som kan generera både serverstubbar och API-klienter från swagger-definitioner. Detta kan avsevärt minska svårigheterna och kostnaderna för att generera kunder för ett visst språk. För att komma igång och generera din första klient behöver du först hämta en instansspecifik kopia av en av Marketo Swagger-definitionerna. Ange Munchkin-id:t från den instans du vill testa med. Börja med identitetsdefinitionen. Nu när du har en definition som är specifik för din instans måste du hämta och installera swagger-codegen. Processen är specifik för ditt operativsystem och du kan hämta instruktionerna [här](https://github.com/swagger-api/swagger-codegen#prerequisites) Med standardinställningarna kommer kodegenen att generera en klient som täcker alla angivna slutpunkter och modeller. Dessa hanteras vanligtvis via en klass som heter DefaultApi som innehåller metoder för att anropa tillgängliga slutpunkter med exempel i en docs-mapp (alla språk innehåller inte mallar för detta som standard). Nu ska vi bygga den första klienten. Skapa en mapp där du vill att koden ska finnas och gå dit i terminalsessionen och använd kommandot Generera för att skapa en klient på det språk du vill ha (vi antar att du har använt installationsmetoden för homebrew):

swagger-codegen generate -i $definitionLocation -l $yourLanguage -o $yourLocation

Klientkoden kommer då att skrivas ut på den önskade platsen. Nu ska vi titta på hur du använder det här för att anropa identitetsslutpunkten och få en åtkomsttoken:

```java
 public static void main(String[] args){
  String client_id = args[0];
  String client_secret = args[1];
  ApiClient client = new ApiClient();
  DefaultApi id = new DefaultApi();
  try {
   String token = id.identityOauthTokenGet(client_id, client_secret, "client_credentials").getAccessToken();
   System.out.println(token);
  } catch (ApiException e) {
   e.printStackTrace();
  }
 }
```

```php
<?php
require_once(_DIR_ . '/vendor/autoload.php');

$api_instance = new Swagger\\Client\\Api\\DefaultApi();
$client_id = "client_id_example";
$client_secret = "client_secret_example";
$grant_type = "grant_type_example";

try {
    $result = $api_instance->identityOauthTokenGet($client_id, $client_secret, $grant_type);
    print_r($result->getAccessToken);
} catch (Exception $e) {
    echo 'Exception when calling DefaultApi->identityOauthTokenGet: ', $e->getMessage(), "\\n";
}
?>
```

```c#
  public static void Main(string[] args)
  {
      string clientId = "CHANGE ME";
      string clientSecret = "CHANGE ME";

      IdentityApi instance = new IdentityApi();
      ResponseOfIdentity response = instance.IdentityUsingGET(clientId, clientSecret, "client_credentials");

      string message = string.Format("Access Token: {0}, Expires In: {1}, Scope: {2}, Token Type: {3}",
          response.AccessToken, response.ExpiresIn, response.Scope, response.TokenType);
      Console.WriteLine(message);
  }
```

Nu när vi vet hur vi kan få tillstånd kommer vi att undersöka mer avancerade användningsfall för autogenererade klienter under de kommande veckorna.

Publicerad _2016-10-10_ av _Kenny_

## Excel-integreringsdel 1: Extrahera och forma Marketo-data med Power Query

Det här är den första av en serie artiklar som förklarar hur man utnyttjar Power BI-tekniken som är inbyggd i Microsoft Excel för att skapa en verkligt självbetjänad affärsanalysupplevelse med Marketo. Med de koncept som beskrivs i dessa artiklar kan du:

* Importera data från Marketo till Excel
* Importera och kombinera data från andra källor (SaaS-program, databaser, platta filer etc.)
* Formdata för affärsbehov och analyssyfte
* Uppdatera data från Excel vid behov
* Skapa beräknade kolumner och mått med hjälp av formler
* Skapa relationer mellan heterogena data
* Analysera data och skapa avancerade rapporter med pivottabeller och pivotdiagram
* Skapa enastående datavisualiseringar

### Power Query för Excel

Den här första artikeln behandlar import och formning av data med Power Query-teknik. Power Query är en dataanslutningsteknik som gör att du kan identifiera, ansluta, kombinera och förfina datakällor för att uppfylla dina analysbehov. Funktioner i Power Query finns i Excel och Power BI Desktop. Power Query kan ansluta till många datakällor, t.ex. databaser, Facebook, Salesforce, MS Dynamics CRM osv. Marketo stöds inte direkt, men som tur är kan vi använda Marketo REST API:er för fjärrexekvering av många av systemets funktioner, och Power Query innehåller en mängd formler (som vanligen kallas &quot;M&quot;) som gör att du kan skripta en anpassad datakälla.

### Anpassad koppling

Skriptning av ett enda REST API-anrop är enkelt med Power Query, men det blir svårare att hantera följande krav:

* Åtkomst till tokenhantering inklusive autentiseringsmekanism och periodisk tokenuppdatering
* Sidnumreringsmekanism för stora datamängder
* Felhantering

I den här artikeln beskrivs hur du skapar en robust, anpassad anslutning som kan använda REST-API:er för Marketo för att hämta alla typer av data (leads, aktiviteter, anpassade objekt, program osv.). Din enda begränsning är begränsad till din dagliga gräns för Marketo API-begäranden. De koncept som förklaras här fokuserar på Marketo, men de kan också användas för att integrera andra SaaS-lösningar som tillhandahåller ett REST API.

### Förutsättningar

#### Power Query

Innan Excel 2016 släpptes fungerade Microsoft Power Query för Excel som ett Excel-tillägg som hämtades och installerades i Excel 2010 eller Excel 2011. Från Excel 2016 är den här tekniken en inbyggd funktion som är integrerad i menyfliksområdet Data under Get &amp; Transform. Alla skript som skapats för den här artikeln har testats i Excel 2016 för Windows. Begreppen bör vara desamma för Excel 2013 och Excel 2010, men vissa anpassningar kan behövas.  Power Query finns för närvarande endast i Microsoft Windows-versionen av Excel. Mac-versionen stöds tyvärr inte.

#### Marketo

Power Query kommer att använda Marketo REST API:er för att komma åt data från Marketo. För att kunna använda dessa API:er behöver du en API-användare och en anpassad tjänst som du kan skapa själv om du är administratör för din Marketo-instans. Annars måste administratören ge dig dem. En stegvis förklaring om hur du skapar Marketo API-användaren och den anpassade tjänsten finns [här](/help/rest-api/custom-services.md). När du är klar bör du ha följande autentiseringsuppgifter för att anropa Marketo REST API:er: **Klient-ID** och **Klienthemlighet**. **REST API-slutpunkten** finns i REST API-avsnittet i Web Services Admin i Marketo och bör ha följande mönster:

`<https://XXX-XXX-XXX.mktorest.com/rest>`

Marketo har en gräns för antalet begäranden per dag för API:t och denna gräns finns i Web Services Admin tillsammans med en förbrukningsrapport. **Kontrollera att du aldrig överskrider din dagliga gräns när du utformar dina frågor eftersom du kanske saknar vissa data i dina rapporter**.

### Skapa Power Query Workbook

Börja med en ny Excel-arbetsbok. Vi skapar ett specifikt konfigurationskalkylblad för att deklarera alla Marketo REST API-inställningar. I det här kalkylbladet skapar vi tre tabeller:

Tabellen **REST_API_Authentication** med kolumnerna **URL**: Marketo REST API Endpoint. **Klient-ID**: från dina Marketo REST API OAuth2.0-autentiseringsuppgifter. **Klienthemlighet**: från dina Marketo REST API OAuth2.0-autentiseringsuppgifter.
Tabell **Omfång** med kolumnerna: **Paging Token SinceDateTime**: ett datum som följer ISO 8601-standarddatumnotation (till exempel &quot;2016-10-06T13:22:17-08:00&quot;, &quot;2016-10 -06 är ett giltigt datum/tid) som används för att hämta Marketo-aktiviteter sedan en viss period, tack vare en inledande datumbaserad sidtoken. Det här datumet används främst för att begränsa mängden data som ska importeras till arbetsboken. **List-ID**: ID:t för en statisk lista i Marketo som refererar till alla leads/kontakter som vi hanterar. Denna statiska lista kan hanteras fritt i Marketo (en smart kampanj kan t.ex. mata in den regelbundet eller i realtid med leads och kontakter).
Om du vill hämta ID:t för en statisk lista öppnar du den i Marketo och hämtar dess numeriska ID från URL:en, t.ex. `<https://myorg.marketo.com/#ST3517A1LA1>`, List ID=3511. **Max Records Pages**: Detta används för våra pseudo-rekursiva algoritmer som itererar genom Marketo-utdata, med &#39;positionsbaserade&#39; paging-tokens, med en kapacitet på högst 300 poster per sida. Eftersom det är vårt intresse att få så många register per sida som möjligt håller vi oss till 300. Vanligtvis innebär ett maximalt antal postsidor på 33,333 en kapacitet på 33,333 × 300 = 9,9999 miljoner poster, men det innebär även 33,333 kB på din Marketo API-gräns för daglig begäran. Algoritmerna avbryts i alla fall så snart alla data från frågorna har hämtats, så den här parametern är bara en säkerhetsgräns för en slinga.

Tabell `Leads` med kolumnen **Leadfält**: kommaseparerade lead-fält som ska samlas in från Marketo när leads och kontakter efterfrågas. Det är enkelt att deklarera en tabell i Excel. Ange två rader i kalkylbladet med kolumnnamnen och kolumnvärdena, markera med musen tabellens omkrets, markera ikonen Tabell på menyn Infoga och ge den ett namn. Namnen på tabellerna och deras kolumner är viktiga eftersom de anropas direkt av våra skript.

## Autentiserings- och åtkomsttoken

### Om Marketo REST API-autentisering

Marketo REST API:er autentiseras med 2-legged OAuth 2.0. Klient-ID:n och kundhemligheter tillhandahålls av anpassade tjänster som du definierar. Varje anpassad tjänst ägs av en användare med endast API:er som har en uppsättning roller och behörigheter som tillåter tjänsten att utföra specifika åtgärder. En åtkomsttoken är associerad med en enda anpassad tjänst.
Den fullständiga autentiseringsmekanismen beskrivs [här](/help/rest-api/authentication.md) på Marketo Developer-webbplatsen. När en åtkomsttoken ursprungligen skapades är dess livslängd 3 600 sekunder eller 1 timme. Varje efterföljande autentiseringsanrop för samma anpassade tjänst returnerar den aktuella åtkomsttoken med dess återstående livslängd. När token har upphört att gälla returnerar autentiseringen en helt ny åtkomsttoken. Det är viktigt att hantera utgångsdatum för åtkomsttoken så att integreringen fungerar smidigt och förhindra att oväntade autentiseringsfel uppstår under normala operationer.

#### Skapa fråga

Skapa en ny fråga genom att klicka på ikonen Ny fråga i avsnittet Get&amp;Transform på Data-menyn. Välj en tom fråga att börja med och ge den ett namn som MktoAccessToken. Starta den avancerade redigeraren i Frågeredigeraren, så att du kan skripta vissa Power Query-formler manuellt. Ange följande kod i den avancerade redigeraren:

```
let
    // Get url and credentials from config worksheet - Table REST_API_Authentication
    mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
    clientIdStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[Client ID],
    clientSecretStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[Client Secret],

    // Calling Marketo API Get Access Token
    getAccessTokenUrl = mktoUrlStr & "/identity/oauth/token?grant_type=client_credentials&client_id=" & clientIdStr & "&client_secret=" & clientSecretStr,
    TokenJson = try Json.Document(Web.Contents(getAccessTokenUrl)) otherwise "Marketo REST API Authentication failed, please check your credentials",

    // Parsing access token
    accessTokenStr = TokenJson [access_token]

in
    accessTokenStr
```

Kommentarerna som är inbäddade i källkoden, föregångna av&quot;//&quot;, gör koden självförklarande. Om du behöver en funktionsreferens kan du kolla in länkarna i referensavsnittet i den här artikeln. Klicka på knappen &quot;Klar&quot;. Kontrollera att åtkomsttoken visas korrekt i utdata för det sista steget accessTokenStr.  En snabb kommentar om säkerheten i Excel. Du kan tillfrågas ibland om du vill aktivera externa dataanslutningar från den gula banderollen. Detta krävs för att frågorna ska fungera korrekt.

#### Konvertera en fråga till en funktion

Återgå till den avancerade redigeraren och omsluta koden med följande funktionsdeklaration:

```
let
    FnMktoGetAccessToken =()=>

        let
            // Get url and credentials from config worksheet - Table REST_API_Authentication
            mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
            clientIdStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[Client ID],
            clientSecretStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[Client Secret],

            // Calling Marketo API Get Access Token
           getAccessTokenUrl = mktoUrlStr & "/identity/oauth/token?grant_type=client_credentials&client_id=" & clientIdStr & "&client_secret=" & clientSecretStr,
           TokenJson = try Json.Document(Web.Contents(getAccessTokenUrl)) otherwise "Marketo REST API Authentication failed, please check your credentials",

            // Parsing access token from Json
           accessTokenStr = TokenJson [access_token]

        in
            accessTokenStr

in FnMktoGetAccessToken
```

Funktionen tar inga parametrar i indata utan hämtar dem från konfigurationskalkylbladet. Den skapar åtkomsttoken som utdata. Byt namn på frågan `FnMktoGetAccessToken` och spara den. Observera att du kan se alla dina frågor när som helst i Excel genom att klicka på knappen Visa frågor i delen Hämta och omforma på Data-menyn. Funktionen ska nu markeras med funktionsikonen Fx.

### Läs in medlemmar i statisk lista

#### Hämta leads

Marketo Lead-API:t tillhandahåller enkla CRUD-åtgärder mot lead-poster, möjlighet att ändra ett leads medlemskap i statiska listor och program samt initiera Smart Campaign-bearbetning för leads. Alla dessa funktioner dokumenteras [här](/help/rest-api/leads.md). En stor uppsättning lead-poster kan hämtas baserat på medlemskap i en statisk lista eller ett program. Med ID:t för en statisk lista kan du hämta alla lead-poster som är medlemmar i den statiska listan. Listans ID är en sökvägsparameter i anropet. Mer information finns i kapitlet&quot;Lista och programmedlemskap&quot; i dokumentationen för Marketo-utvecklare. Det högsta antalet lead-poster som kan hämtas per API-anrop är 300, så vi måste utnyttja växlingstoken för att samla in poster per sida med 300 poster. Vi får sidindelningstoken i Json-svaret efter det första samtalet och vi vet att vi är klara när sidindelningstoken inte längre finns i utdata.

### Enkel fråga

Låt oss komma igång med en fullt fungerande fråga som syftar till att hämta alla leads från en statisk lista. Skapa en ny tom fråga med namnet&quot;MktoLeads&quot; och ange följande kod i den avancerade redigeraren:

```
let
    // Get Url from config worksheet - Table REST_API_Authentication
    mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
    // Get the number of iterations (pages of 300 records) - Table Scoping
    iterationsNum = Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[Max Records Pages],
    // Get the List id - Table Scoping
    listIdStr = Number.ToText(Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[List ID], "D", ""),
    // Get the Lead fields to extract - Table Leads
    LeadFieldsStr = Excel.CurrentWorkbook(){[Name="Leads"]}[Content]{0}[Lead Fields],


    // Build Multiple Leads by List Id URL
    getMultipleLeadsByListIdUrl = mktoUrlStr & "/rest/v1/list/" & listIdStr & "/leads.json?fields=" & LeadFieldsStr,

    // Build Marketo Access Token URL parameter
    accessTokenParamStr = "&access_token=" & FnMktoGetAccessToken(),

    pagingTokenParamStr = "",

    // Function iterating though the pages
    FnProcessOnePage =
    (accessTokenParamStr, pagingTokenParamStr) as record =>
        let

            // Send REST API Request
            content = Web.Contents(getMultipleLeadsByListIdUrl & accessTokenParamStr & pagingTokenParamStr),

            // Recover Json output and watch if token is expired, in that case, regenerate access token
            newAccessTokenParamStr = if Json.Document(content)[success]=true then accessTokenParamStr else "?access_token=" & FnMktoGetAccessToken(),
            getMultipleLeadsByListIdJson = if Json.Document(content)[success]=true then Json.Document(content) else Json.Document(Web.Contents(getMultipleLeadsByListIdUrl & newAccessTokenParamStr & pagingTokenParamStr)),

            // Parse Json outputs: data and next page token
            data = try getMultipleLeadsByListIdJson[result] otherwise null,
            next  = try  "&nextPageToken=" & getMultipleLeadsByListIdJson[nextPageToken] otherwise null,
            res = [Data=data, Next=next, Access=newAccessTokenParamStr]
        in
            res,

    // Generates a list of values given four functions that generate the initial value initial, test against a condition, and if successful select the result and generate the next value next. An optional parameter, selector, may also be specified
    GeneratedList =
        List.Generate(
            ()=>[i=0, res = FnProcessOnePage(accessTokenParamStr, pagingTokenParamStr)],
            each [i]<iterationsNum and [res][Data]<>null,
            each [i=[i]+1, res = FnProcessOnePage([res][Access],[res][Next])],
            each [res][Data])
in
    GeneratedList
```

Power Query har inga traditionella slingfunktioner (t.ex. For-loop, While-loop) och stöder inte rekursion. En bra lösning är att implementera en For-slinga med List.Generate. Den här funktionen dokumenteras [här](http://msdn.microsoft.com/query-bi/m/list-generate). Med lista. Generera det går att iterera över sidorna. I varje steg av iterationen extraherar vi en sida med data och behåller URL:en som innehåller sidbläddringstoken för nästa sida, och sparar resultaten i nästa objekt i den genererade listan. Bloggen från [Datachant](https://datachant.com/2016/06/27/cursor-based-pagination-power-query/) var en bra resurs för att lösa detta. Vår parameter &#39;Max Records Pages&#39; är här för att begränsa antalet sidor och begränsa det till ett realistiskt intervall för att undvika en oändlig slinga. En annan utmaning är att se till att åtkomsttoken aldrig upphör att gälla. Att spåra dess återstående livslängd skulle vara för komplext med Power Query. Alla anrop till REST API säkerhetskopieras med en felkontroll. Om ett fel inträffar antar vi att token har upphört att gälla och att vi förnyar den först och spelar upp anropet igen. Om det andra anropet misslyckas meddelas Excel det andra felet (i värsta fall får du inga data som resultat). Starta frågan när du har sparat den eller genom att klicka på Uppdatera när du vill.  I vårt fall extraherades 1364 lead-poster som passade in 5 sidor med data i 5 listor.

### Forma data

Vi måste utforma data så att alla dessa poster finns i en enda lista över alla register. Det finns två sätt att göra detta:

* Använda mer kod
* Utnyttja Power Query-gränssnittet

Högerklicka på utdatarutnätet och välj Till tabell på snabbmenyn för att konvertera det till en lista.  Lämna standardvärdena i de två listrutorna i popup-fönstret Till tabell.  Expandera nu den resulterande tabellen med listor. Nu har vi alla register i en enda lista. Posterna som är kodade i Json-format innehåller fälten och deras associerade värden. Expandera igen. Markera i popup-fönstret alla fält som du vill behålla och avmarkera kryssrutan Använd det ursprungliga kolumnnamnet som prefix.  Et voila! Alla poster visas bra i vår tabell. Om vi öppnar den avancerade redigeraren igen ser vi att tre kodrader har lagts till för att forma våra data:

```
# "Converted to Table" = Table.FromList(GeneratedList, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
# "Expanded Column1" = Table.ExpandListColumn(#"Converted to Table", "Column1"),
# "Expanded Column2" = Table.ExpandRecordColumn(#"Expanded Column1", "Column1", {"id", "updatedAt", "lastName", "email", "createdAt", "firstName"}, {"id", "updatedAt", "lastName", "email", "createdAt", "firstName"})
```

Du kan göra mycket mer med Power Query, som att skapa extra kolumner med beräknade värden, så kommer vi att se fler möjligheter senare. Låt oss spara och stänga den här frågan. Den kan nu uppdateras manuellt när som helst eller automatiskt via bakgrundsuppdateringar.

### Omdirigera resultaten

Frågan är nu var vi ska skicka resultatdata? Håll muspekaren över frågan och välj menyn Läs in till på snabbmenyn... På popup-menyn kan du välja:

* &#39;Tabell&#39; om du vill skicka alla formade data till ett kalkylblad (nytt eller befintligt),
* &quot;Skapa bara anslutning&quot; om ditt mål är att utföra ytterligare analys i Power Pivot.

Kryssrutan Lägg till detta i datamodellen gör att du kan utnyttja data i Power Pivot. Det är vad vi vill ha för den andra delen av den här artikeln.

### Hantera sidnumrering

Eftersom målet för vårt projekt är att skapa många fler frågor, kan vi göra lite omfaktorisering och extrahera en återanvändbar funktion som hanterar sidnumreringen. Skapa en ny tom fråga med namnet **FnMktoGetPagedData** och ange följande kod i den avancerade redigeraren:

```
let
    FnMktoGetPagedData =(url, accessTokenParamStr, pagingTokenParamStr)=>

    let

        // Get the number of iterations (pages of 300 records) - Table Scoping
        iterationsNum = Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[Max Records Pages],

        // Sub-function iterating though the REST API service result pages
        FnProcessOnePage =
        (accessTokenParamStr, pagingTokenParamStr) as record =>
            let

                // Send REST API Request
                content = Web.Contents(url& accessTokenParamStr & pagingTokenParamStr),

                // Recover Json output and watch if token is expired, in that case, regenerate access token
                newAccessTokenParamStr = if Json.Document(content)[success]=true then accessTokenParamStr else "?access_token=" & FnMktoGetAccessToken(),
                contentJson = if Json.Document(content)[success]=true then Json.Document(content) else Json.Document(Web.Contents(url & newAccessTokenParamStr & pagingTokenParamStr)),

                // Parse Json outputs: data and next page token
                data = try contentJson[result] otherwise null,
                next  = try  "&nextPageToken=" & contentJson[nextPageToken] otherwise null,
                res = [Data=data, Next=next, Access=newAccessTokenParamStr]
            in
                res,

        // Generates a list of values given four functions that generate the initial value initial, test against a condition, and if successful select the result and generate the next value next. An optional parameter, selector, may also be specified
        GeneratedList =
            List.Generate(
                ()=>[i=0, res = FnProcessOnePage(accessTokenParamStr, pagingTokenParamStr)],
                each [i]<iterationsNum and [res][Data]<>null,
                each [i=[i]+1, res = FnProcessOnePage([res][Access],[res][Next])],
                each [res][Data])
    in
        GeneratedList

in FnMktoGetPagedData
```

Spara frågan. Vi kommer att använda den härnäst.

### Förenklad fråga

Låt oss skriva om frågan&quot;MktoLeads&quot; som anropar funktionen **FnMktoGetPagedData** igen.

```
let
    // Get Url from config worksheet - Table REST_API_Authentication
    mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
    // Get the List id - Table Scoping
    listIdStr = Number.ToText(Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[List ID], "D", ""),
    // Get the Lead fields to extract - Table Leads
    LeadFieldsStr = Excel.CurrentWorkbook(){[Name="Leads"]}[Content]{0}[Lead Fields],


    // Build Multiple Leads by List Id URL
    getMultipleLeadsByListIdUrl = mktoUrlStr & "/rest/v1/list/" & listIdStr & "/leads.json?fields=" & LeadFieldsStr,

    // Build Marketo Access Token URL parameter
    accessTokenParamStr = "&access_token=" & FnMktoGetAccessToken(),

    // No initial paging token required for this call
    pagingTokenParamStr = "",

    // Invoke the multiple REST API calls through the FnMktoGetPagedData function
    result = FnMktoGetPagedData (getMultipleLeadsByListIdUrl , accessTokenParamStr, pagingTokenParamStr)

in
    result
```

Som du ser är frågan nu väldigt enkel att läsa och underhålla. Vi kommer att återanvända funktionen **FnMktoGetPagedData** för de andra frågorna.

### Läs in specifika aktiviteter från en angiven tidsperiod

#### hämta aktiviteter med sidnumrering

Marketo tillåter ett stort antal aktivitetstyper som hör ihop med lead-register. Nästan varje ändring, åtgärd eller flödessteg registreras mot en leads aktivitetsloggar och kan hämtas via API:t eller utnyttjas i Smart List och Smart Campaign-filter och -utlösare. Aktiviteter är alltid relaterade tillbaka till lead-posten via leadId, som motsvarar postens ID, och har också ett unikt heltals-ID. Den fullständiga REST API-dokumentationen [finns här](/help/rest-api/activities.md).

Det finns ett mycket stort antal möjliga aktivitetstyper, som kan variera från prenumeration till prenumeration, och som har unika definitioner för varje. Alla aktiviteter har ett eget unikt ID, leadId och activityDate, men primärAttributeValueId och primärAttributeValue är olika i sin mening. Vi kommer att fokusera på intressanta ögonblick, en typ av Marketo spårade aktiviteter med ID 41. De nya utmaningar som vi kommer att lösa är:

* Vi måste initiera en datumbaserad sidindelningstoken för att definiera tidsperioden när aktiviteterna inträffade,
* Att formge data är lite svårare eftersom en lista med aktivitetsspecifika attribut tillhandahålls i Json och måste analyseras och förenklas för att underlätta analysen, beroende på aktivitetstyperna.

#### Datumbaserad sidtoken

Vi måste först skapa den här funktionen för att generera den initiala datumbaserade sidtoken, som krävs för att ange tidsperioden för våra aktivitetsfrågor. Dokumentationen om sidindelningstoken [finns här](/help/rest-api/paging-tokens.md). Skapa en ny tom fråga med namnet **FnMktoGetPagingToken** och ange följande kod i den avancerade redigeraren:

```
let
    FnMktoGetPagingToken =(accessTokenStr)=>

        let
            // Get url from config worksheet - Table REST_API_Authentication
            mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],

            // Get Paging Token SinceDatetime from config worksheet - Table Scoping
            mktoPTSinceDatetimeStr = DateTime.ToText(Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[Paging Token SinceDatetime], "yyyy-MM-ddThh:mm:ss"),

            // Building URL for API Call
            getPagingTokenUrl = mktoUrlStr & "/rest/v1/activities/pagingtoken.json?access_token=" & accessTokenStr & "&sinceDatetime=" & mktoPTSinceDatetimeStr,

            // Calling Marketo API Get Paging Token
            content = Web.Contents(getPagingTokenUrl),

            // Recover Json output and watch if access token is expired, in that case, regenerate it
            newAccessTokenStr = if Json.Document(content)[success]=true then accessTokenStr else "?access_token=" & FnMktoGetAccessToken(),
            pagingTokenJson = if Json.Document(content)[success]=true then Json.Document(content) else Json.Document(Web.Contents(mktoUrlStr & "/rest/v1/activities/pagingtoken.json?access_token=" & newAccessTokenStr & "&sinceDatetime=" & mktoPTSinceDatetimeStr)),

            // Parsing Paging Token
            pagingTokenStr = pagingTokenJson[nextPageToken]

        in
            pagingTokenStr

in FnMktoGetPagingToken
```

Spara funktionen. Vi kommer att använda den härnäst.

#### Intressanta ögonblick

Nu skriver vi frågan&quot;MktoInterestingMomentsActivities&quot; som anropar funktionerna **FnMktoGetPagedData** och **FnMktoGetPagingToken** .

```
let

    // Get Url from config worksheet - Table REST_API_Authentication
    mktoUrlStr = Excel.CurrentWorkbook(){[Name="REST_API_Authentication"]}[Content]{0}[URL],
    // Get the List id - Table Scoping
    listIdStr = Number.ToText(Excel.CurrentWorkbook(){[Name="Scoping"]}[Content]{0}[List ID], "D", ""),

    // Build Get Activities URL
    getActivitiesUrl = mktoUrlStr & "/rest/v1/activities.json?ListId=" & listIdStr & "&activityTypeIds=46",

    // Build Marketo Access Token URL parameter
    accessTokenStr = FnMktoGetAccessToken(),
    accessTokenParamStr = "&access_token=" & accessTokenStr,

    // Obtain date-based paging token used to scope in time the activities
    pagingTokenParamStr = "&nextPageToken=" & FnMktoGetPagingToken(accessTokenStr),

    // Invoke the multiple REST API calls through the FnMktoGetPagedData function
    result = FnMktoGetPagedData (getActivitiesUrl , accessTokenParamStr, pagingTokenParamStr)

in
    result
```

Resultatet av den här frågan är en lista med listor igen, så ytterligare databearbetning krävs för att kunna användas för analys.

### Forma data

Låt oss göra samma formningsåtgärder som vi gjorde för Leads:

* Högerklicka på utdatarutnätet och välj &#39;Till tabell&#39; på snabbmenyn för att konvertera det till en lista.
* Expandera den resulterande tabellen med listor,
* Expandera en gång till och markera alla fält som du vill behålla i popup-fönstret (avmarkera kryssrutan Använd det ursprungliga kolumnnamnet som prefix).

Du kan se kolumnerna med deras värden, förutom kolumnen &quot;attribut&quot; som fortfarande innehåller en lista med specifika attribut som är kopplade till de intressanta stunderna. Vi expanderar dessa attribut. Nu när listan har expanderat till poster expanderar vi igen, markerar de fält vi vill ha (namn och värde för varje attribut) och avmarkerar kryssrutan Använd det ursprungliga kolumnnamnet som prefix.  Därför är alla våra data synliga, inklusive attribut, men varje intressant ögonblick som aktiviteten sträcker sig över tre rader. Detta kommer att bli svårt att använda för vår analys.  Helst vill vi bara ha en rad per aktivitet, med alla attribut som extra kolumner. Det kan vi enkelt göra genom att pivotera de tre attributen från vår tabell. Markera de två kolumnerna Namn och Värde i aktivitetsattributen och klicka på Pivottolumn på menyn Omforma. Fråga efter alternativ för framsteg i popup-fönstret och välj Värdekolumn = värde och Sammanfoga inte värdefunktion.  Klicka på OK så måste du skriva ut en enda rad med data per aktivitet.  Följande kodrader för dataformning ska automatiskt ha lagts till i skriptet för din fråga:

```
  #"Converted to Table" = Table.FromList(result, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandListColumn(#"Converted to Table", "Column1"),
    #"Expanded Column2" = Table.ExpandRecordColumn(#"Expanded Column1", "Column1", {"id", "leadId", "activityDate", "activityTypeId", "campaignId", "primaryAttributeValue", "attributes"}, {"id", "leadId", "activityDate", "activityTypeId", "campaignId", "primaryAttributeValue", "attributes"}),
    #"Expanded attributes" = Table.ExpandListColumn(#"Expanded Column2", "attributes"),
    #"Expanded attributes1" = Table.ExpandRecordColumn(#"Expanded attributes", "attributes", {"name", "value"}, {"name", "value"}),
    #"Pivoted Column" = Table.Pivot(#"Expanded attributes1", List.Distinct(#"Expanded attributes1"[name]), "name", "value")
in
    #"Pivoted Column"
```

### Nästa steg

Du bör nu kunna utforma alla frågor du behöver för att komma åt specifika Marketo-data som är tillgängliga via dess REST API:er. Vi hoppas att du tyckte om den här artikeln och att den hjälpte dig att utnyttja fördelarna med Excel och Marketo tillsammans. Ett exempel på en arbetsbok med alla frågor finns också i den andra artikeln.

### Referenser

#### Power Query

* [Power Query - översikt och inlärning](https://support.microsoft.com/en-us/article/Power-Query-Overview-and-Learning-ed614c81-4b00-4291-bd3a-55d80767f81d)
* [Power Query-formelreferens](http://msdn.microsoft.com/query-bi/m/power-query-m-reference)
* [Matt Masson Blog](http://www.mattmasson.com/tag/power-query/) innehåller bra resurser om Power Query
* [DataChant-blogg](https://datachant.com/2016/06/27/cursor-based-pagination-power-query/) är mycket användbar för implementering av sidnumreringsmekanismen

Anslaget _2016-10-18_ av _Philippe_

## Uppdateringar hösten 2016

I hösten 2016 lägger vi till CRUD-stöd för e-postvariabler och -moduler och CRUD-stöd för namngivna konton. Nu kan du läsa och redigera innehållet lokalt med Marketo REST API:er, samt flytta, ordna om och ta bort dem. Se hela listan med uppdateringar nedan:

### API:er för lead-databas

* [**Namngivna konton**](/help/rest-api/named-accounts.md)
   * Nya slutpunkter för att läsa, uppdatera och ta bort namngivna konton
   * Kända fel:
      * Från och med hösten 2016 kan leads inte kopplas till namngivna konton via API:t

### Resurs-API:er

* [**E-post**](https://developer.adobe.com/marketo-apis/api/asset/#operation/describeUsingGET_5)
   * Nya slutpunkter för manipulering av e-postvariabler v2
   * Nya slutpunkter för manipulering av e-postmoduler v2
   * Kända fel:
      * Frågor och uppdateringar för avsnitt som innehåller prediktiva tokens returnerar ett fel
      * E-postmeddelanden med innehållsavsnitt som innehåller prediktiva token kanske inte godkänns med API:t

Publicerad _2016-12-07_ av _Kenny_

## Varför vi tog tillbaka @MarketoDev Twitter-handtag3

Vi har bestämt oss för att ta bort @MarketoDev-referensen på Twitter. Kontot inaktiveras den 9 december 2011. Gärna varför? **Spola tillbaka till början av 2014..** Vi hade just startat Marketo Developers Site för att hjälpa utvecklare att bygga mot våra API:er. Vi ville öka medvetenheten om Marketo-plattformen och minska hindren för utvecklare. Tillsammans med den satsningen skapade vi @MarketoDev för att interagera med våra teknikpartners och kunder som var villiga att bygga integrerade lösningar med Marketo. Som vilket modigt nytt företag som helst, var vi inte säkra på hur det skulle gå till. Vi interpolerade först nya blogginlägg och nya API-versioner. Det var bra; trafiken till utvecklarsajten gick upp! Vi har också börjat få ett antal frågor, som vi besvarat noggrant. **Snabbspolning framåt till slutet av 2016..** I takt med att ekosystemet för [LaunchPoint-partnern](https://exchange.adobe.com/apps/browse/ec?product=MRKTO) ökade antalet tweet-aktiviteter. Att svara på den stora mängd frågor som ställts till @MarketoDev har blivit ett stort jobb för vårt lilla team. Vi fick allt fler frågor om den allmänna produkten och försäljningen, som vi vidarebefordrade till @Marketo eller @MarketoCares. Vi hade också funnit att 140 tecken helt enkelt inte räckte för utvecklingsrelaterade frågor. Att svara på dessa ledde ofta till långa och involverade konversationstrådar, en strategi som inte skalades. Slutligen analyserade vi trafikkällor för besökare på webbplatsen för utvecklare och fann att majoriteten kom från organisk sökning och funktionen&quot;Prenumerera nu&quot; på vår blogg. Därför bestämde vi oss för att dra in plugin-programmet på @MarketoDev. **Härifrån och ut..** Om du är Twitter-fan (och inte är det) är du inte rädd! Vår Twitter-hantering av @Marketo fortsätter; precis som vår kundsupport hanterar @MarketoCares.

Publicerad _2016-12-02_ av _David_

## Excel-integrering, del 2: Skapa avancerade Marketo-rapporter och datavisualiseringar med Power Pivot- och Power View-

Detta är den andra i en serie av två artiklar som förklarar hur man utnyttjar Power BI-tekniken som är inbyggd i Microsoft Excel för att skapa en verkligt självbetjäningsbaserad affärsanalysupplevelse med Marketo. Med de koncept som beskrivs i dessa artiklar kan du:

* Importera data från Marketo till Excel
* Importera och kombinera data från andra källor (SaaS-program, databaser, platta filer etc.)
* Formdata för affärsbehov och analyssyften
* Uppdatera data on demand i Excel
* Skapa beräknade kolumner och mått med hjälp av formler
* Skapa relationer mellan heterogena data
* Analysera data och skapa avancerade rapporter med pivottabeller och pivotdiagram
* Skapa enastående datavisualiseringar

### Power Pivot och Power View för Excel

I den här artikeln finns exempel på hur du skapar följande:

* Avancerade Marketo-rapporter som utnyttjar relationer mellan olika samlingar av Marketo-data med Power Pivot
* Häftiga statiska och animerade visualiseringar med Power View

**Power Pivot** är ett Excel-tillägg som redan ingår i Excel 2016 och som du kan använda för att utföra kraftfull dataanalys och skapa avancerade datamodeller. Med Power Pivot kan du snabbt kombinera stora datavolymer från olika källor, utföra informationsanalyser och enkelt dela insikter. Data som extraheras från olika datakällor med Power Query kan skickas till datamodellen, Excel-kalkylbladet eller till båda. I den första artikeln importerade och formade vi data från Marketo och skickade dem till datamodellen för att utföra mer avancerade analyser innan de blev tillgängliga i kalkylbladet. **Power View** är ett alternativ till Excel-visualiseringslagret. Det är en interaktiv upplevelse av datautforskande, visualisering och presentation som uppmuntrar till intuitiv ad hoc-rapportering och instrumentbordning.

Alla steg som beskrivs i den här artikeln har testats i Excel 2016 för Windows. Begreppen bör vara desamma för Excel 2013 och Excel 2010 (utan Power View), men vissa anpassningar kan behövas. Power Pivot- och Power View är för närvarande bara tillgängligt i Microsoft Windows-versionen av Excel 2016. Office 365-versionen av Excel 2016 stöder inte Power Pivot- och Power View.

### Marketo Power Workbook

#### Hämta arbetsbok

I den första artikeln tog vi upp import och formning av data med Power Query-teknik. Vi lärde oss att implementera några avancerade kraftfrågor för att extrahera leads och aktiviteter från Marketo. För en del av er skulle vilja gå direkt till den punkt där de skapar rapporter och visualiseringar, utan att skriva kod. Den här arbetsboken innehåller alla frågor som beskrivs i den första artikeln och några till. Vi förbättrade felhanteringen och lade till några extra parametrar i konfigurationskalkylbladet. Om du gick igenom den första artikeln rekommenderar vi fortfarande att du laddar ned Marketo Power Workbook och ser vad som har lagts till.

Ansvarsfriskrivning: Marketo Power Workbook är inte en officiell Marketo-produkt och stöds därför inte av Marketo. Du får gärna använda och utöka för dina egna affärsbehov, men gör det på egen risk.

#### Konfigurera arbetsbok

Fyll i all nödvändig information från Marketo konfigurationsblad:

* **Marketo REST API-autentisering:** krävs
* **Omfång:** anger Paging Token SinceDateTime och ID:t för din statiska Marketo-lista som innehåller alla leads som du vill analysera
* **Leads:** För att rapporterna ska komma måste du åtminstone ange följande Lead-fält: `id`, `firstName`, `lastName`, `email`, create`edAt`, `updatedAt`, `title`, `company`, `industry`, `inferredCountry`, `inferredCity`
   * Om stadsinformationen är mer korrekt i ett av dina anpassade fält kan du använda ditt eget fält i stället
* **Aktiviteter:** Aktivitetstyper som ska hämtas från Marketo-databasen anges här för varje aktivitetsuppsättning. Du behöver inte ändra detta nu.
   * Observera att vi tillhandahöll en verktygsfråga i arbetsboken där alla befintliga aktivitetstyper listas direkt i Excel-arbetsboken om du vill justera informationen senare den

Observera att vissa säkerhetsrelaterade popup-fönster kan visas. Lita på externa anslutningar och ange dem som&quot;Public&quot;. Om popup-fönstret nedan visas kan du stanna kvar med anonym webbåtkomst. Autentiseringen till Marketo hanteras direkt av våra anpassade frågor, så du behöver inte aktivera någon annan typ av åtkomst.

#### Hämta Marketo-data

Kontrollera först att parametern som du definierar i omfånget av Marketo-konfigurationsbladet inte leder till att för mycket data hämtas, vilket överskrider gränsen för Marketo API-begäranden per dag. När du är klar klickar du på knappen Uppdatera alla på menyn Data och väntar tills alla data har hämtats till arbetsboken. Om formateringsfelmeddelanden visas när data hämtas, ungefär som &quot;column1 not found&quot;, innebär det att en eller flera frågor inte kan hämta data, så formateringen misslyckas också. Om felet kvarstår kan du försöka igen senare och kontrollera vilken version av Excel du har (använd inte Excel 2016 från Office 365). Det är också viktigt att respektera latensen från Marketo. Om du gör några ändringar i en statisk lista eller i dina lead-data bör du vänta innan du startar Power Queries.

### Datamodellering i Power Pivot

Öppna Power Pivot genom att klicka på knappen Hantera på Power Pivot-menyn, som är tillgänglig på den övre menyraden (om inte den här versionen av Excel är tillgänglig kan Power Pivot installeras som ett tillägg i vissa versioner av Excel). Alla data som hämtas från Marketo och skickas till datamodellen bör vara tillgängliga från de olika flikarna längst ned i Power Pivot-fönstret.

### Dataanalysuttryck (DAX)

Vi måste förbättra eller formatera om data för vissa rapporter. Låt oss använda Power Pivot Data Analysis Expressions (DAX) för att definiera några anpassade beräkningar som beräknade kolumner och mått (kallas även beräkningsfält). Läs mer om DAX i Power Pivot-länken i avsnittet Referenser om du vill veta mer om DAX. Kontrollera att beräkningsområdet visas i Power Pivot-fönstret. Om inte, aktivera det från Power Pivot-hemmenyn.  Markera fliken **MktoLeads** och lägg till måttet **Antal leads** någonstans i beräkningsområdet för leads: **Antal leads:=**&#x200B;**DISTINCTCOUNT**&#x200B;**([id])**. Den här åtgärden räknar de distinkta leads som finns i listan utifrån deras ID. Den skulle också ta hänsyn till de eventuella filter som finns i samband med en rapport. Den här åtgärden är inte särskilt nödvändig eftersom rapporterna kan summera antalet leads, men vi gjorde det för att ha ett antal leads med ett finare namn än summan av MktoLeads. Det är också ett enkelt exempel som gör att du enkelt kan föreställa dig mer komplexa mått som gör medelvärden, min, max för en viss typ av datainmatning (t.ex. alla leads med en poäng som är högre än 50, medelpoäng osv.). ...).  Nu ska vi välja fliken **MktoWebActivities** och skapa tre beräknade kolumner. Infoga följande beräknade kolumner genom att bläddra längst till höger i tabellen och klicka på kolumnen Lägg till kolumn. **Aktivitet:** Hämta den användarvänliga aktivitetsetiketten genom att leta upp aktivitets-ID:t i registret MktoActivityTypes. **\=**&#x200B;**LOOKUPVALUE**&#x200B;**(MKTOActivityTypes[name],MKTOActivityTypes[id],[activityTypeId])** **Year-Month:** formatera om aktivitetsdatumet med mönstret YYYmm som passar bättre för vissa rapporter. **\=**&#x200B;**LEFT**&#x200B;**([activityDate],4)&amp;**&#x200B;**MID**&#x200B;**([activityDate],6,2)** **Date:** Activity Date är bara en String från vår ursprungliga fråga, omvandla den till ett korrekt datum. **\=**&#x200B;**DATE**&#x200B;**(**&#x200B;**LEFT**&#x200B;**([activityDate],4),**&#x200B;**MID**&#x200B;**([activityDate],6,2),**&#x200B;**MID**&#x200B;**([activityDate],9,2)** Låt oss skapa samma mått för fliken **MktoEmailActivities** och ytterligare två: **Campaign:** Hämta det användarvänliga Campaign-namnet genom att leta upp Campaign-ID:t i tabellen MktoCampaigns. **\=**&#x200B;**LOOKUPVALUE**&#x200B;**(MKTOCampaigns[name],MKTOCampaigns[id],[campaignId])** **Program:** Hämta det användarvänliga programnamnet genom att leta upp kampanj-ID:t i tabellen MKtoCampaign gns. Tabellen MKTOPrograms kan innehålla mer information om programmet, till exempel mapp, arbetsyta osv. **\=**&#x200B;**LOOKUPVALUE**&#x200B;**(MKTOCampaigns[programName],MKTOCampaigns[id],[campaignId])**

### Enhetsrelationer

Vi såg tidigare ett sätt att söka efter information från en annan tabell i modellen för att slutföra en del information som saknas. Power Pivot erbjuder ett kraftfullare alternativ för att definiera relationerna mellan vissa tabeller i datamodellen, så att vi kan utnyttja dessa relationer direkt från rapporterna. Låt oss definiera de viktigaste relationerna för våra rapporter. Välj diagramvyn i Power Pivot-fönstret. Spåra följande relationer i datamodelldiagrammet:

* **MKtoInterestingMomentActivities:leadId →** **MKtoLeads:id**
* **MKtoScoringActivities:leadId →** **MKtoLeads:id**
* **MktoRevenueStageActivities:leadId →** **MktoLeads:id**
* **MKtoWebActivities:leadId →** **MKtoLeads:id**
* **MktoEmailActivities:leadId →** **MktoLeads: id**

Vi kommer inte att använda alla dessa relationer och objekt i våra rapporter, bara leads, webbaktiviteter och e-postaktiviteter. Nu är det dags att ta fram rapporter.

### Pivottabellen E-postprestanda

Den första rapporten visar nyckeltal för e-postprestanda baserat på ett vanligt Excel-pivotdiagram. Det gör att vi kan filtrera data efter bransch och/eller kampanj. Du kan skapa ett pivotdiagram direkt från Power Pivot-menyn genom att välja Pivotdiagram i pivottabellväljaren.  Ett alternativ är att skapa ett pivotdiagram direkt från Excel-kalkylbladet genom att markera alternativet Använd arbetsbokens datamodell.  Dra och släpp fälten från tabellerna **MktoEmailActivities** och **MktoLeads**, som figuren nedan: **MktoEmailActivities.Activity →** **Legend** (den här använder den DAX-beräknade kolumnen som vi implementerade på **MktoEmailActivities** tidigare) **Mkkt toEmailActivities.Date →** **Axis** (den här använder den beräknade DAX-kolumnen som vi implementerade **MktoEmailActivities** tidigare) **MktoEmailActivities.Id →** **¥ Values** **MktoEmailActivities .Campaign →** **Filter** **MktoLeads.industry →** **Filter**

Du kan skapa ett eget namn genom att välja Värdefältsinställningar för varje släppt fält. I det här fallet utelämnade vi fältet för e-postaktivitet i avsnittet &#39;¢Values&#39; och redigerade det anpassade namnet som &#39;Number of Activities&#39;. Nu ska vi konfigurera pivotdiagrammet. Högerklicka direkt på diagrammet och välj alternativet Ändra diagramtyp på snabbmenyn. Så här valde vi olika diagramtyper för alla dataserier.

### Leadkarta med Power View

I den andra rapporten visas era leads och kontakter efter geografiskt läge på en världskarta och efter bransch. Vi behöver Power View för den här rapporten. Aktivera menyn i Excel genom att följa referenslänken nedan. Du kan också bara skriva&quot;Power view&quot; i sökrutan i Excel. Välj Infoga en Power View-rapport.  I den tomma Power View-rapporten markerar du tabellen **MktoLeads** på den högra panelen och drar och släpper lead-platsfältet (t.ex. **instedCity**). Menyn Design visas nu på huvudmenyn.

Växla till kartvisualisering genom att välja Karta på Power View-menyn Design. Dra och släpp fälten från tabellen **MktoLeads**, som figuren nedan: **MktoLeads.industry → →** **Color** **MktoLeads.instedCity →** **Platser** **MktoLeads.Leads Count →** **&#x200B;**&#x200B;Ã (här används DAX-måttet som vi implementerade på **MktoLeads** tidigare) Och din lead-karta är klar! Du behöver bara justera storleken på kartan, anpassa titeln och teckenförklaringen. Med Power View kan du skapa avancerade instrumentpaneler med flera diagram på ett enda kalkylblad. Titta på den refererade självstudiekursen nedan [Skapa fantastiska Power View-rapporter](https://support.microsoft.com/en-us/article/Tutorial-Create-Amazing-Power-View-Reports-Part-1-e2842c8f-585f-4a07-bcbd-5bf8ff2243a7) om du vill se hur du fortsätter med fler instrumentpanelskomponenter med Power View.

### Webbaktiviteter animerade på en 3D-karta

I den tredje rapporten visas dina webbaktiviteter på webben, per bransch, på en 3D-världskarta. Vi behöver en 3D-karta för den här rapporten. Skriv bara&quot;3D&quot; i sökrutan i Excel och välj&quot;3D Map&quot;. Skapa en ny demo i popup-fönstret.  Markera bubbeldiagrammet på den högra panelen. Dra och släpp fälten från tabellerna **MktoLeads** och **MktoWebActivities**, enligt figuren nedan: **MktoLeads.industry →** **Kategori** **MktoLeads.instedCity →** **Plats** **MMKat ktoWebActivities.Activity →** **Time** (här används den beräknade DAX-kolumnen som vi implementerade **MktoWebActivities** tidigare. ID-fältet kan också användas för att räkna aktiviteter.) **MktoWebActivities.Date →** **Time** (den här typen använder den DAX-beräknade kolumnen som vi implementerade på **MktoWebActivities** tidigare) **MktoWebActivities.Activity** kan också användas som ett filter för att filtrera bort olika typer av webbaktiviteter.

Använd knappen Teman för att ändra färgschemat för 3D-kartan. Öppna Scenalternativ för att anpassa dina animeringar.
Och du är klar med 3D World Map, nu kan du ha kul när du animerar världen och skapar video utifrån den.

### Nästa steg

Vi skrapade just ytan av vad som är möjligt att göra med Excel Power BI-verktygen. Vi rekommenderar att du söker på webben efter andra bra artiklar och självstudiekurser för att utöka dina Excel-kunskaper och utforma de rapporter du behöver för att nå dina affärsmål. Vi hoppas att du tyckte om de här artiklarna och att de hjälpte dig att utnyttja fördelarna med Excel och Marketo tillsammans.

### Referenser

#### Power Pivot

* [Power Pivot: Kraftfull dataanalys och datamodellering i Excel](https://support.microsoft.com/en-us/article/Power-Pivot-Powerful-data-analysis-and-data-modeling-in-Excel-d7b119ed-1b3b-4f23-b634-445ab141b59b)
* [Dataanalysuttryck (DAX) i Power Pivot](https://support.microsoft.com/en-us/article/Data-Analysis-Expressions-DAX-in-Power-Pivot-bab3fbe3-2385-485a-980b-5f64d3b0f730)

#### Power View

* [Aktivera Power View i Excel 2016](https://support.microsoft.com/en-us/article/Turn-on-Power-View-in-Excel-2016-for-Windows-f8fc21a6-08fc-407a-8a91-643fa848729a)
* [Självstudiekurs: Skapa fantastiska Power View-rapporter](https://support.microsoft.com/en-us/article/Tutorial-Create-Amazing-Power-View-Reports-Part-1-e2842c8f-585f-4a07-bcbd-5bf8ff2243a7)

Anslaget _2017-02-02_ av _Philippe_

## Viktig ändring av aktivitetsposter i Marketo API

**Obs! Det här inlägget uppdateras för att återspegla ändringar som gjorts i aktivitetsposter som returnerats av API på grund av migrering till ny infrastruktur.** **Senaste uppdatering: 13 september 2018** I och med lanseringen av nästa generations aktivitetstjänst från Marketo i september 2017 kan vi inte framtvinga det unika eller unika heltalsfältet&quot;id&quot; i aktiviteter, ändringar av datavärden eller poster för borttagning av leads som returneras av Marketo API:er. För att undvika tjänstavbrott för integreringar som hämtar aktivitetsposter bör ID-fältet behandlas som valfritt. Överlappningen av den här ändringen kommer att börja påverka prenumerationer och kommande versioner. Den här ändringen påverkar följande slutpunkter: REST API

De berörda SOAP-typerna är `ActivityRecord` och `LeadChangeRecord`.

### Exempel

I följande exempel visas de posttyper som kommer att påverkas. I båda exemplen kallas fältet&quot;id&quot;.

**Exempel på REST-fält: id**

```json
  {
      "id" : 102988,
      "leadId" : 1,
      "activityDate" : "2015-01-16T23:32:19Z",
      "activityTypeId" : 1,
      "primaryAttributeValueId" : 71,
      "primaryAttributeValue" : "localhost/munchkintest2.html",
      "attributes" : [
          {
              "name" : "Client IP Address",
              "value" : "10.0.19.252"
          },
          {
              "name" : "Query Parameters",
              "value" : ""
          },
          {
              "name" : "Referrer URL",
              "value" : ""
          },
          {
              "name" : "User Agent",
              "value" : "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.95 Safari/537.36"
          },
          {
              "name" : "Webpage URL",
              "value" : "/munchkintest2.html"
          }
      ]
  }
```

**Exempel på SOAP-fält: id**

```xml
  <activityRecord>
    <id>1030680</id>
    <activityDateTime>2013-07-09T16:44:28-05:00</activityDateTime>
    <activityType>Visit Webpage</activityType>
    <mktgAssetName>ClickDemo</mktgAssetName>
    <activityAttributes>
      <attribute>
        <attrName>Webpage ID</attrName>
        <attrType xsi:nil="true" />
        <attrValue>2547</attrValue>
      </attribute>
      <attribute>
        <attrName>Webpage URL</attrName>
        <attrType xsi:nil="true" />
        <attrValue>/ClickDemo.html</attrValue>
      </attribute>
    </activityAttributes>
    <campaign />
    <personName xsi:nil="true" />
    <mktPersonId>1089965</mktPersonId>
    <foreignSysId xsi:nil="true" />
    <orgName xsi:nil="true" />
    <foreignSysOrgId xsi:nil="true" />
  </activityRecord>
```

Publicerad _2017-03-01_ av _Kenny_

## Vintern 2017 - uppdateringar

I vinterversionen 2017 lägger vi till möjligheten att importera anpassade objektdata asynkront gruppvis och att ändra variabler på guidade landningssidor. Se hela listan med uppdateringar nedan.

### API:er för lead-databas

#### Massimport av anpassade objekt

Nya slutpunkter som stöder massimport av anpassade objekt. Information finns [här](/help/rest-api/custom-objects.md).

#### Meddelande om kommande ändringar av aktiviteter

Observera en betydande förändring som inträffar när Marketo släpper sin nästa generations aktivitetstjänst. Den här ändringen påverkar följande slutpunkter:

SOAP

[getLeadActivity](/help/soap-api/getleadactivity.md), [getLeadChanges](/help/soap-api/getleadchanges.md)

Fältet för heltalet &quot;id&quot; i poster som returneras av dessa slutpunkter är inte längre garanterat unikt. Detta påverkar aktivitet, ändring av datavärde och posttyper för borttagning av lead. För att undvika tjänstavbrott för integreringar som hämtar dessa posttyper bör ID-fältet behandlas som valfritt.

#### Borttagning av push-token

Lagt till möjlighet att ta bort push-token via SDK API. Mer information finns här: [iOS](/help/mobile/push-notifications.md), [Android](/help/mobile/push-notifications.md).

Publicerad _2017-03-01_ av _David_

## Uppdateringar våren 2017

I vårutgåvan 2017 lägger vi till möjligheten att gruppextrahera lead- och aktivitetsobjektdata asynkront och att ändra namngivna kontolistor. Se hela listan med uppdateringar nedan.

### Massextrahering av leads

Nya slutpunkter som stöder extrahering av leads i bulk. Ange kriterier för posturval med hjälp av en mängd olika alternativ. Information finns [här](/help/rest-api/bulk-lead-extract.md).

### Massextrahering av verksamheter

Nya slutpunkter som stöder extrahering av gruppaktiviteter. Ange villkor för postval med datumintervall och aktivitetslista. Information finns [här](/help/rest-api/bulk-extract.md).

### Namngivna kontolistor

Nya slutpunkter som stöder CRUD-åtgärder på namngivna kontolistor. Information finns [här](/help/rest-api/named-account-lists.md).

### Andra förbättringar

* Med slutpunkten [Hämta kampanjer](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getCampaignByIdUsingGET) kan du nu filtrera utlösbara kampanjer. Detta uppnås genom att skicka&quot;isTriggerable=true&quot; som en frågeparameter.
* Slutpunkten för [klonprogrammet](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST) har nu stöd för program som innehåller alla resurstyper förutom SMS.

### Ionic

Nu kan du använda Marketo Mobile MME och med programramverket [Ionic](https://ionicframework.com/). Information finns [här](/help/mobile/ionic.md).

### Avregistrera push-meddelandetoken

En ny metod har lagts till i SDK som gör att du kan avregistrera push-meddelandetoken med Marketo. Detta är praktiskt när en användare loggar ut från ditt program. Mer information finns i `unregisterPushDeviceToken` (iOS) eller `uninitializeMarketoPush` (Android) [här](/help/mobile/push-notifications.md).

Publicerad _2017-06-16_ av _David_

## Sakernas internet för marknadsförare med IFTTT och Zapier

Sakernas internet (IoT) är ett nätverk av anslutna enheter, apparater, kroppsburna produkter, fordon osv. med inbyggd elektronik, programvara, sensorer och nätverksanslutning som gör det möjligt för dessa objekt att samla in och utbyta data med molninformationssystem. Dessa tekniker växer och trendar så snabbt att de påverkar hur vi lever, hur vi arbetar och hur vi gör affärer på nolltid. Marketo, ledande plattform för marknadsföringsengagemang, är redo för sakernas internet med möjlighet att skala och interagera med alla typer av kommunikationskanaler. Marketo kan spåra över 70 typer av aktiviteter som rör e-post, webben, mobiler, CRM osv... och stöder även [anpassade aktiviteter](https://experienceleague.adobe.com/docs/marketo/using/product-docs/administration/marketo-custom-activities/create-a-custom-activity.html?lang=sv-SE) som kan matas av andra system från tredje part. Marketo [anpassade objekt](https://experienceleague.adobe.com/docs/marketo/using/product-docs/administration/marketo-custom-objects/understanding-marketo-custom-objects.html?lang=sv-SE) gör det möjligt att spåra alla typer av tredjepartsmått som är relaterade till din verksamhet, och gör det möjligt för marknadsförare att utnyttja dessa mätvärden direkt från Marketo smarta kampanjfilter och utlösare. För att implementera IoT för konsumenter krävs en central server för interaktion med konsumentenheter, och den här servern skulle utbyta data med den öppna Marketo-plattformen, med funktioner som REST API, anpassade objekt, anpassade aktiviteter osv. - dokumenterade [här](http://eto.com/). Det är inte lätt att demonstrera via ett blogginlägg. Istället kommer vi att integrera IFTTT-tjänsten med Marketo för att implementera några coola IoT-användningsexempel för marknadsförarna:

* Ryck upp marknadsföringsteamet varje gång ett lead registreras för en utställning genom att blinka med ett färgat ljus på kontoret
* Ryck upp säljarna varje gång ett avtal vinner genom att automatiskt sätta igång en klocka som är ansluten till en ansluten strömkontakt
* Lägg automatiskt upp milstolpar för marknadsföringsframgångar i sociala nätverk som LinkedIn, Facebook, Slack osv. ...
* Starta en marknadsföringskampanj automatiskt baserat på:
   * när en vädervarning inträffar (vind, temperatur, regn osv.)
   * när en ny artikel publiceras av en tidning som New York Times, som matchar vissa specifika kriterier
   * när den amerikanska senaten eller representanshuset röstar
   * när den internationella rymdstationen passerar över en viss plats
   * osv. ...

Du kanske tycker att de här scenarierna är roliga men värdelösa, men de är här för att demonstrera ett nytt konceptuellt sätt att göra marknadsföring inte bara med människor, utan även med saker i vår uppkopplade värld. En annan intressant punkt som behandlas i den här artikeln är hur man utnyttjar en öppen webbintegrationsplattform som Zapier som en &quot;servitris&quot; mellan ett system från tredje part och Marketo för att hantera till exempel autentiseringen.

### IFTTT-tjänsten

IFTTT är en förkortning av&quot;IF This then That That&quot;. Det är en kostnadsfri webbaserad tjänst som man använder för att skapa kedjor med enkla villkorssatser, så kallade applets. Ett miniprogram aktiveras av ändringar som görs i vissa partnerwebbtjänster och som ett resultat av detta skickas åtgärder till andra partnerwebbtjänster. IFTTT startades 2011 av Linden Tibbets, Jesse Tane, Scott Tong och Alexander Tibbets i San Francisco. Vid första anblicken ser IFTTT ut ungefär som en tjänst som [Zapier](https://zapier.com/), till exempel, har den ett mycket starkare fokus på konsumenter och IoT-enheter (fjärrkontroller, larm, ljus, termostater, bilar, skrivare, mobiltelefoner och mycket annat).  Först och främst måste du skapa ett konto för IFTTT från [IFTTT-webbplatsen](https://ifttt.com/explore). Upptäck gärna alla häftiga appleter som redan finns eftersom det ger dig några andra scenarioidéer!

### Maker Channel

Ett webbprogram som inte har någon kanal, det vill säga ett partnerskap med IFTTT, måste använda Maker Channel. Med Maker Channel kan du skapa tillägg som fungerar med alla enheter och appar som kan göra eller ta emot en webbförfrågan. Det erbjuder följande integreringar:

* Inkommande utlösare för att ta emot en webbförfrågan från ett system från tredje part för att utlösa en åtgärd
* Utgående åtgärder för att göra en webbförfrågan till ett system från tredje part tillgänglig för allmänheten på Internet

I IFTTT söker du efter&quot;Maker&quot;-tjänsten och klickar på den.  Första gången måste du aktivera den genom att klicka på knappen &quot;Anslut&quot;.  Nu är Maker Channel aktivt. Du kan få tillgång till din hemliga nyckel genom att klicka på knappen Makerinställningar: Kopiera och klistra in den angivna URL-adressen i webbläsaren för mer information.

### Direkt utlösa en IFTTT-åtgärd från Market

För det första kommer vi att fokusera på att utlösa alla typer av webbtjänståtgärder från Marketo från tredje part. Därför ska vi använda en [Marketo Webkrok](https://experienceleague.adobe.com/docs/marketo/using/product-docs/administration/additional-integrations/create-a-webhook.html?lang=sv-SE). Vi börjar med ett push-meddelande på mobiltelefonen eller surfplattan via IFTTT-mobilappen och sedan implementerar vi ett IoT-scenario som blinkar med Philips Hue-lampan.

### Marketo Webkrok

Det är enkelt att utlösa en händelse från Marketo som fungerar som if för IFTTT. Allt du behöver göra är att skicka en POST-webbförfrågan till IFTTT med ett händelsenamn och din hemliga nyckel, enligt följande mönster-URL:

`<https://maker.ifttt.com/trigger/{event_name}/with/key/{secret_key}>`

Med Maker kan du också kommunicera upp till tre parametrar via webben. Detta kan göras med hjälp av frågeparametrar,

`<https://maker.ifttt.com/trigger/{event_name}/with/key/{secret_key}?value1={value1}&value2={value2}&value3={value3}>`

eller med ett JSON-organ som består av upp till tre värden:

`{ "value1" : "{value1}", "value2" : "{value2}", "value3" : "{value3}" }`

I Marketo skapar du en ny webkrok från administratörsgränssnittet.  Ange följande information för din nya Webkrok:

**Webkrok-namn:** IFTTT-programmet lyckades

**Beskrivning:** Utlös en händelse på IFTT från en smart kampanj för ett lyckat program

**URL:** `<https://maker.ifttt.com/trigger/{event_name}/with/key/{secret_key}?value1={{program.name}}&value2={{lead.Email> Address}}&value3={{lead.Full Name}}`

event_name, använd MarketoProgramSuccess till exempel

hemlig_nyckel, använd den hemliga nyckeln från IFTT Maker-tjänsten

Använd statisk text eller Marketo-tokens för de tre tillgängliga värdena. Du kan skicka mer interaktiva meddelanden genom att definiera egna tokens på programnivå och skicka dem via dessa värden.

**Typ av begäran:** POST

**Mall:** Lämna tomt

**Begär tokenkodning:** Formulär/URL

**Svarstyp:** Ingen

Ingen svarsmappning behöver definieras.

### IFTTT-miniprogram

På IFTTT-webbportalen väljer du&quot;Mina appar&quot; på huvudmenyn.  Klicka på knappen Ny applet och klicka på avsnittet **+this**.  Sök efter Maker-tjänsten.  Skapa den utlösare som ska utlösas varje gång Maker-tjänsten tar emot en webbförfrågan för att meddela den om en händelse. Använd samma händelsenamn som det som anges i URL:en för din Marketo-webkrok, till exempel&quot;MarketoProgramSuccess&quot; och klicka på knappen&quot;Skapa utlösare&quot;.  Nu är det dags att ange åtgärdstjänsten genom att klicka på avsnittet **+that**.  Vi börjar med en enkel åtgärdstjänst som alla skulle kunna testa utan att behöva investera i någon IoT-enhet, Notifications Service. Sök efter och välj meddelandetjänsten.
Välj åtgärden&quot;Skicka ett meddelande&quot; som skickar ett meddelande till dina enheter.  Du kan utnyttja de tre värden som du har skickat från Marketo genom att lägga till dem som komponenter för att skicka ett meningsfullt meddelande till användaren, precis som i exemplet nedan ... och sedan klicka på knappen&quot;Skapa åtgärd&quot;. Granska och avsluta IFTTT-appleten. Kontrollera att det är aktiverat.

### Testa IFTTT-appleten

Om du vill få ett meddelande på din mobiltelefon måste du först hämta IFTTT-appen för din enhet.  Du kan utlösa en Marketo Program Success-händelse genom att använda webkroken i ett Marketo Smart Campaign-flöde. Kom ihåg att Marketo Webhooks fungerar enbart med utlösta smarta kampanjer (t.ex. utlösare när ett ifyllt kontaktformulär har lagts till i en lista).  Här är ett exempel på ett IFTTT-meddelande på mobiltelefonen.

### Låt oss skaffa Creative med IFTTT

IFTTT erbjuder Applet Actions med över 300 partners, så din portfölj med appar och enheter tillsammans med din fantasi är gränserna ... Låt oss ta ett exempel med [Hue Light från Philips](https://www.philips-hue.com/en-us) som du kan köpa var som helst i elektronikbutiker eller online. Följande miniprogram skulle blinka med den aktuella tilldelade färgen när Marketo lyckas med ett program som skulle kunna lyfta marknadsföringsteamet på kontoret. Vi skapar en ny applet, som följer samma steg som tidigare, där Marketo utlöses med en webkrok, men den här gången väljer vi åtgärden från Philips Hue-tjänsten.

Låt oss välja åtgärden &quot;Blinka ljus&quot;. Programmet begär allt tillgängligt ljus från Philips Hue så att du kan välja det som ska blinka. Du måste skapa ett konto hos Philips Hue först, Nyansbron och naturligtvis minst en nyansglödlampa, ljusremsa, projektor eller lampa.  Vi har just lagt till en ny applet som blinkar med färgat ljus varje gång en lead registreras för ett webbprogram eller webbinarium. Marknadsföringsteamet kommer att bli bra varje dag med de inställningarna på kontoret.

### Utföra en Marketo-åtgärd från IFTTT, via Zapie

Nu kommer vi att utlösa en Marketo Smart Campaign från IFTTT-plattformen. Därför ska vi använda [Marketo REST API](/help/rest-api/rest-api.md). Eftersom detta API är skyddat och kräver en OAuth2-autentisering innan någonting anropas, måste vi hantera autentiseringen via en annan plattform som Zapier, eftersom IFTTT inte tillåter att två anrop i följd kopplas till ett API med Maker Channel. Vi valde [Zapier](https://zapier.com/) Automation Service för webbprogram eftersom vi redan har publicerat en introduktion till Zapier och förklarar steg för steg hur vi implementerar en anpassad Marketo-anslutning för Zapier. Andra plattformar som [Workato](https://www.workato.com/integrations/marketo) kan också vara en lösning.

### Marketo Campaign

Skapa ditt Marketo-program med en schemalagd Smart Campaign. I testsyfte kan du skapa följande smarta kampanj som exempel: **Smart lista** Använd bara filter, inte utlösare. Se till att du åtminstone är berättigad. **Flöde** Skicka dig ett e-postmeddelande eller någon annan typ av meddelande. **Schema** Kontrollera att du kan köra igenom flödet varje gång för att hantera flera tester. Du kan hämta det smarta kampanj-ID:t från URL:en. Exempel: _https://{{marketo_url}}/#SC4289A1_ - ID:t för den smarta kampanjen är 4289. Du kan utlösa den här kampanjen via Marketo REST API. Du kan till exempel använda plugin-programmet [Postman](https://www.postman.com/) för Chrome och skicka följande två HTTPS-anrop i följd: **Autentiseringssteg:**

`https://{{Your Munchkin_Account_id}}.mktorest.com/identity/oauth/token?grant_type=client_credentials&client_id={{Your_Client_Id}}&client_secret={{Your_Client_Secret}}`

Återställ åtkomsttoken från JSON-svaret. **Kampanjens startsteg:**

`https://{{Your_Munchkin_Account_id}}.mktorest.com/rest/v1/campaigns/{{Campaign_Id}}/schedule.json?access_token={{access_token}}`

### Mellanliggande Zapier Custom Connector för att starta Marketo Campaign

Vi måste bygga en anpassad Zapier-anslutning som autentiserar med Marketo REST API och startar vår Smart Campaign.

* Förutsättningar
* Implementering av Marketo Connector for Zapier
* Använd en annan titel som&quot;Marketo Campaign&quot;
   * Gör autentiseringssteget
   * Utför steget&quot;Utlösare&quot; (krävs för Zapier-testning)
   * Gör följande specifika&quot;funktionsmakron&quot;-steg, som startar en Marketo-kampanj, som förklaras nedan:

URL för Skicka dataåtgärdsslutpunkt:

`https://{{munchkin_account_id}}.mktorest.com/rest/v1/campaigns/{{CampaignId}}/schedule.json`

Lämna övriga valfria fält tomma.

#### Skript-API

Zapiers skriptfunktion gör att du kan hantera förfrågningar och svar som utbyts mellan appens API och Zapier. Du kan ändra HTTP-begäranden precis innan de skickas och kan analysera svaren innan Zapier gör något med dem. Vi behöver det för att slutföra vår anpassade autentisering av sessionsautentisering. Mer information finns i den ursprungliga artikeln. Kopiera följande kod som är mycket lik originalet, vi har precis ändrat åtgärdsmetoderna:

```javascript
var Zap = {

 get_session_info: function(bundle) {

 console.log('Entering get_session_info method ...');

 var access_token,
 access_token_request_payload,
 access_token_response;


 // Assemble the meta data for our Access Token swap request
 console.log('building Request with client_id=' + bundle.auth_fields.client_id + ', and client_secret=' + bundle.auth_fields.client_secret);
 access_token_request_payload = {
 method: 'POST',
 url: 'https://' + bundle.auth_fields.munchkin_account_id + '.mktorest.com/identity/oauth/token',
 params: {
 'grant_type' : 'client_credentials',
 'client_id' : bundle.auth_fields.client_id,
 'client_secret' : bundle.auth_fields.client_secret
 },
 headers: {
 'Content-Type': 'application/json', // Could be anything.
 Accept: 'application/json'
 }
 };

 // Fire off the Access Token request.
 access_token_response = z.request(access_token_request_payload);

 // Extract the Access Token from returned JSON.
 access_token = JSON.parse(access_token_response.content).access_token;
 console.log('New Access_Token=' + access_token);

 // This will be mixed into bundle.auth_fields in future calls.
 //bundle.auth_fields.access_token=access_token;
 return {'access_token': access_token};
 },

 test_trigger_pre_poll: function(bundle) {

 console.log('Entering test_trigger_pre_poll method ...');

 bundle.request.params = {
 'access_token':bundle.auth_fields.access_token
 };

 return bundle.request;

 },

 test_trigger_post_poll: function(bundle) {

 console.log('Entering test_trigger_post_poll method ...');

 var data = JSON.parse(bundle.response.content);
 if ((!data.success)&&((data.errors[0].code=="601")||(data.errors[0].code=="600"))){
 console.log('Access Token expired or invalid, requesting new one - data.success=' + data.success + ', data.errors[0].code=' + data.errors[0].code);

 throw new InvalidSessionException(); // Calling get_session_info() to regenerate Access Token
 }

 return JSON.parse(bundle.response.content);
 },

 launch_campaign_pre_write: function(bundle) {

 bundle.request.params = {'access_token':bundle.auth_fields.access_token};
 return bundle.request;
 },

 launch_campaign_post_write: function(bundle) {

 var data = JSON.parse(bundle.response.content);
 if ((!data.success)&&((data.errors[0].code=="601")||(data.errors[0].code=="600"))){
 console.log('Access Token expired or invalid, requesting new one - data.success=' + data.success + ', data.errors[0].code=' + data.errors[0].code);
 throw new InvalidSessionException(); // Calling get_session_info() to regenerate Access Token
 }
 return JSON.parse(bundle.response.content);
 }

};
```

##### Ny Zap

Klicka på knappen &quot;Make a new Zap&quot; på Zapier-kontrollpanelen. **Utlösare**

* Välj utlösarappen &quot;Webhooks by Zapier&quot;
* Kontrollera &quot;Catch Hook&quot;
* Du behöver inte välja en underordnad nyckel
* Zapier genererade en anpassad webkrok-URL som du kan skicka begäranden till, och som du kan skydda någonstans
* Testa webkroks-URL:en genom att starta scenariot &quot;IFTTT-applet som anropar Zapier Webkrok&quot; nedan. På så sätt kan Zapier lära sig mer om webkrocknyttolasten och tilldela kampanj-ID:t till åtgärden

**Åtgärd**

* Välj den Marketo Campaign-anslutning som skapats tidigare
* Välj den enda tillgängliga åtgärden: **Starta kampanj**
* Anslut till ditt Marketo-konto och fyll i autentiseringsparametrarna (Munchkin konto-ID, klient-ID, Klienthemlighet)
* Redigera mallen och associera kampanj-ID från utlösaren till parametern&quot;Starta kampanj-ID&quot;
* Testa steget och kontrollera att Marketo Campaign startas

### IFTTT-applet som anropar Zapier-webkroken

Vi börjar med ett enkelt scenario som är enkelt att testa. Vi väljer en utlösare för datum och tid i IFTTT som startar Marketo Campaign varje timme. Åtgärden är en webbförfrågan som publiceras på Zapier-webkroks-URL:en och skickas över Smart Campaign-ID:t. Kontrollera att Zapier Zap och IFTTT Applet är både aktiva och att allt fungerar som det ska.

### Låt oss skaffa Creative med IFTTT

IFTTT erbjuder Applet Triggers med över 300 partners, så återigen är din portfölj med program och enheter tillsammans med din fantasi gränserna ... Låt oss ta ett exempel med tjänsten [Weather Underground](https://www.wunderground.com/) som vi kommer att använda för att starta vår Marketo-kampanj om vädervarningar. Följande utlösare kommer att aktiveras när ett Regn-villkor tillkännages. Koppla sedan utlösaren till Maker Webkroch Action och fyll precis som tidigare i Zapiers webbkrokparametrar.  Et voila, nu behöver du bara regna för att dubbelkolla att det här faktiskt fungerar.

Vi hoppas att du har många kul när du tillämpar koncepten i den här artikeln. Men viktigast av allt är att vi tror att det kommer att hjälpa alla som vill integrera Marketo med andra system från tredje part, tack vare de viktigaste begreppen i den här artikeln:

* MARKETO REST API
* Marketo Webhooks
* Så här utnyttjar du en öppen webbintegrationsplattform som Zapier som en&quot;tjänsteflocka&quot; mellan ett system från tredje part och Marketo för att hantera autentiseringen, till exempel

Anslaget _2017-06-20_ av _Philippe_

## Uppdateringar sommaren 2017

I sommarversionen 2017 släpper vi några mindre förbättringar av våra API:er för program.

### Bläddra bland program

Vi lägger till möjligheten att hämta program efter datumintervall till vår [Get Programs](https://developer.adobe.com/marketo-apis/api/asset/#operation/browseProgramsUsingGET) -slutpunkt, genom att lägga till parametrarna firstUpdatedAt och latestUpdatedAt. Du kan ställa in någon av eller båda parametrarna med valfritt datetime för att returnera endast program som har skapats eller uppdaterats mellan de två datumtiderna. Detta är användbart för att hämta nya och uppdaterade uppsättningar av marknadsföringsmaterial, och viktigast av allt för översättnings- och affärsintelligens.

Publicerad _1970-01-01_ av _Kenny_

## Utvidga Marketo Business Logic med Google Cloud Function-

I den här artikeln föreslås en lösning för att utöka Marketo med vissa logiska funktioner med Google Cloud Platform (GCP), baserat på följande enkla exempel: 3 anpassade fält på Marketo Lead-posten:

* **OnLinePreference**: Ett inkrementellt poängvärde som anger att en potentiell kund/kund är närvarande vid onlinekommunikation.
* **OfflinePreference**: Ett inkrementellt poängvärde som anger att en potentiell kund/kund är närvarande för offlinekommunikation.
* **Inställningar**: Ett fält som beräknas av GCP och som visar &quot;offline&quot; om offlinepoängen är högre än onlinepoängen, och &quot;online&quot; på andra sättet

Den här tekniken öppnar vägen för mer avancerad affärslogik och till slut för att ringa ut externa webbtjänster, omvandla och konsolidera resultaten i Marketo.  

### Om Google Cloud Platform och funktioner

[Google Cloud Platform](https://cloud.google.com/) (GCP) är en serie molntjänster som körs på samma infrastruktur som Google använder internt för sina slutanvändarprodukter, som Google Search och YouTube. Tillsammans med en uppsättning hanteringsverktyg tillhandahåller den en serie modulära molntjänster, inklusive datoranvändning, datalagring, dataanalys, maskininlärning, big data och mycket annat. Vi kunde ha använt många olika GCP-tjänster för våra behov, till exempel Compute Engine, App Engine eller Kubernetes Engine, men vi valde [Cloud-funktioner](https://cloud.google.com/functions) (fortfarande i Beta) för följande huvudfördelar:

* Molnbaserad databearbetning utan server är där logik kan köras på begäran som svar på händelser som HTTP-anrop.
* Minskar det mesta av den smärta som serverunderhåll och driftsättningar orsakar.
* Kostnadseffektivt eftersom du bara betalar GCP för varje funktionsanrop och inte för att hålla en server igång.
* Enkelt och snabbt att implementera när du bara fokuserar på programlogiken.
* Automatisk skalning, redo för mycket hög arbetsbelastning.

Mer information om den här tekniken och dess priser finns på [GCP-webbplatsen](https://cloud.google.com/). Den här självstudiekursen bör vanligtvis inte medföra några större kostnader och passar perfekt in i den kostnadsfria GCP-testversionen.  

### Förberedelser för din Google Cloud-miljö

Du behöver ett Google Cloud-konto. Du kan testa GCP utan kostnad med en kredit som är mer än tillräcklig för att köra den här självstudiekursen. Klicka bara på knappen&quot;Prova utan kostnad&quot; på [GCP-webbplatsen](https://cloud.google.com/). Följ alla steg i avsnittet Innan du börjar i [HTTP-självstudiekursen](https://cloud.google.com/functions/docs/calling) från Google:

1. Skapa ett Cloud Platform-projekt: GÅ TILL SIDAN HANTERA RESURSER
1. Aktivera fakturering för ditt projekt: [AKTIVERA FAKTURERING](https://cloud.google.com/billing/docs/how-to/modify-project?visit_id=638816637273392093-1926929734&rd=1)
1. Aktivera API:t för molnfunktioner: [AKTIVERA API](https://accounts.google.com/InteractiveLogin?continue=https://console.cloud.google.com/flows/enableapi?apiid%3Dcloudfunctions%26redirect%3Dhttps://cloud.google.com/functions/docs/tutorials/http&followup=https://console.cloud.google.com/flows/enableapi?apiid%3Dcloudfunctions%26redirect%3Dhttps://cloud.google.com/functions/docs/tutorials/http&osid=1&passive=1209600&service=cloudconsole&ifkv=ASKV5Mh81NGNsqcJqhx7hst0KFnyA0MJ-2zay8ovyluBfpvDoM820nF9Wq_SKbC1m_sjQvvRNoKSuA)
1. [Installera och initiera SDK i molnet](https://cloud.google.com/sdk/docs/)
1. Uppdatera och installera Gcloud-komponenter

   **gcloud components update &amp;&amp;** **Gcloud components installerar beta**

1. Förbered din miljö för Node.js-utveckling: [GÅ TILL INSTALLATIONSGUIDEN](https://cloud.google.com/nodejs/docs/setup)

### Implementering av poängJämför molnfunktionen

1. Skapa en molnlagringsbucket för att mellanlagra dina molnfunktionsfiler. Du kan göra det med kommandoraden:

   `gsutil mb gs://[YOUR_STAGING_BUCKET_NAME]`

   eller från webbgränssnittet i Google Cloud genom att välja ditt projekt och klicka på menyn Lagring:
   * Ge lagringskassetten ett unikt namn
   * Välj standardlagringsklass
   * Välj den lämpligaste regionala platsen

1. Skapa en katalog på ditt lokala system för programkoden.
1. Skapa en index.js-fil i den här katalogen med följande JavaScript-kod: koden är verkligen enkel att förstå. Den tolkar de två indataparametrarna från HTTP-begäranstexten i JSON, bearbetar och kodar i JSON i HTTP-svaret.

```javascript
 /\*\*
     \* HTTP scoreCompare Cloud Function.
     \*
     \* @param {Object} req Cloud Function request context.
     \* @param {Object} res Cloud Function response context.
     \*/
    exports.scoreCompare = function scoreCompare (req, res) {
     var onlineScore=parseInt(req.body.onlineScore);
     var offlineScore=parseInt(req.body.offlineScore);
     console.log('/scoreCompare: got values onlineScore =' + onlineScore + ', offlineScore =' + offlineScore);
     var result;
     if (onlineScore>offlineScore) {result = 'online';} else {result = 'offline';}
     console.log('/scoreCompare: and result is ' + result);
     res.status(200).json({output: result}).end();
    };
```

Distribuera funktionspoängen Jämför med en HTTP-utlösare. Kör följande kommando från katalogen:

**gcloud beta-funktioner distribuerar [FUNCTION] —stage-bucket [YOUR_STAGING_BUCKET_NAME] —trigger-http**

Där [YOUR_STAGING_BUCKET_NAME] är namnet på din lagringsbucket i mellanlagringsmolnet. I vårt exempel:

**gcloud beta-funktioner distribuerar scoreCompare —stage-bucket mktostorage —trigger-http**

1. Observera molnfunktionens URL (httpsTrigger URL) från konsolutdata som ser ut så här: `https://[YOUR_REGION]-[YOUR_PROJECT_ID].cloudfunctions.net/[FUNCTION]` där

   * [YOUR_REGION] är den region där funktionen är distribuerad. Detta syns i terminalen när funktionen har distribuerats klart.
   * [YOUR_PROJECT_ID] är ditt projekt-ID i molnet. Detta syns i terminalen när funktionen har distribuerats klart.
   * [FUNCTION] är ditt funktionsnamn.

   I vårt exempel: [**https://us-central1-marketo-cloud-logic.cloudfunctions.net/scoreCompare**](https://us-central1-marketo-cloud-logic.cloudfunctions.net/scoreCompare)
1. Testa din funktion med ett verktyg som [Postman](https://www.postman.com/):
   * HTTP-verb: POST
   * URL: [https://us-central1-marketo-cloud-logic.cloudfunctions.net/scoreCompare](https://us-central1-marketo-cloud-logic.cloudfunctions.net/scoreCompare)
   * Headers: content-type = application/json
   * Brödtext: {&quot;onlineScore&quot;:110, &quot;offlineScore&quot;:200}Output ska ge: {&quot;output&quot;: &quot;offline&quot;}.

### Anropa molnfunktionen från en Marketo-webkrok

Följande tre anpassade fält måste skapas på Lead-posten i Marketo:

* **OnlinePreference**: Integer
* **OfflinePreference**: Heltal
* **Inställningar**: Sträng

Skapa följande webkrok från Marketo administratörsgränssnitt genom att använda molnfunktionen scoreCompare och tokens för det anpassade fältet. Testa webbkroken med en smart kampanj som utlösts av Marketo.

* **Marketo webbhooks kan bara anropas från utlösta smarta kampanjer, inte smarta gruppkampanjer.**
* **Om du inte använder din molnfunktion kan du ta bort den eller ta bort hela projektet för att undvika att debiteras ditt Google Cloud Platform-konto.**

Vi hoppas att den här självstudiekursen var värd din tid och att den kommer att få dig att tänka på mer avancerade scenarier som omfattar avancerad bearbetning och tredjepartstjänster. Ett bra exempel är att utnyttja Google Cloud AI, maskininlärningstjänsterna från Google. Du kan till exempel tolka fritext från ett Marketo-formulär och be Google Natural Language API att visa textens struktur och innebörd och sedan spara analysen i Marketo, bara att öppna floodgaten för idéer.

Anslaget _2017-11-21_ av _Philippe_

## Uppdateringar hösten 2017

I höstens version 2017 har vi släppt flera förbättringar av våra tillgångs-API:er. Se hela listan med uppdateringar nedan.

### Bläddra bland program efter datumintervall

Vi har lagt till möjligheten att hämta program efter datumintervall till slutpunkten för [Hämta program](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneEmailUsingPOST). Detta görs med parametrarna `earliestUpdatedAt` och `latestUpdatedAt`. Du kan ställa in någon av eller båda parametrarna med valfritt datetime för att returnera endast program som har skapats eller uppdaterats mellan de två datumtiderna.

### Förhandsgranska e-post

Nu kan du förhandsgranska ett e-postmeddelande med slutpunkten [Hämta fullständigt e-postinnehåll](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailFullContentUsingGET) som returnerar den serialiserade HTML-versionen av ett e-postmeddelande. Alla variabler, kodfragment, dynamiskt innehåll och inbäddade komponenter återges fullständigt. En valfri **leadId**-parameter kan skickas för att personifiera ett visst lead.

### Ersätt HTML med e-post 2.0

Vi har lagt till slutpunkten [Uppdatera e-post med fullständigt innehåll](https://developer.adobe.com/marketo-apis/api/asset/#operation/createEmailFullContentUsingPOST) så att du kan ersätta block med e-postinnehåll från HTML. Om du redigerar HTML-koden i ett Marketo-e-postmeddelande med Marketo e-postredigerare 2.0 bryts relationen mellan e-postmeddelandet och dess mall, mer om [här](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/edit-an-emails-html). Med den här slutpunkten kan du uppdatera HTML-innehållet i ett e-postmeddelande vars relation har brutits. Dessutom har vi ändrat alla andra livscykelrelaterade slutpunkter för e-post så att de är kompatibla med e-postmeddelanden där relationen har brutits:

* Godkänn e-postutkast
* Ogodkänd e-post
* Ta bort e-post
* Ignorera e-postutkast
* Klona e-post
* Uppdatera e-postmetadata

### Andra förbättringar

* Den maximala tidsrymden för datumintervallfilter har ökats till 31 dagar. Detta gäller för [filtren Bulk Lead Extract](/help/rest-api/bulk-lead-extract.md) (createdAd eller updatedAt) och [filtret Bulk Activity Extract](/help/rest-api/bulk-activity-extract.md) (createdAt).

Publicerad _2017-12-15_ av _David_

## TEST - External video link on Community

[Självstudievideo om Power Pack-leverans för e-post](https://nation.marketo.com:443/t5/product-space-archive-videos/email-deliverability-power-pack-tutorial-video/m-p/283550)  

Publicerad _1970-01-01_ av _David_

## Vintern 2018 - uppdateringar

I vinterversionen 2018 har vi släppt några förbättringar av våra API:er. Se hela listan med uppdateringar nedan.

### Aktivera/inaktivera utlösarkampanjer

Vi har lagt till möjligheten att aktivera och inaktivera utlösarkampanjer, vilket kan förenkla processen att automatisera programmallar. Detta uppnås genom att två nya slutpunkter anropas: [Aktivera smart kampanj](https://developer.adobe.com/marketo-apis/api/asset/#operation/activateSmartCampaignUsingPOST), [Inaktivera smart kampanj](https://developer.adobe.com/marketo-apis/api/asset/#operation/deactivateSmartCampaignUsingPOST). Mer information finns i avsnittet Utlösare i dokumentationen för [kampanjer](/help/rest-api/assets.md).

### Hämta program efter namn

Två parametrar har lagts till i slutpunkten [Get Program by Name](https://developer.adobe.com/marketo-apis/api/asset/#operation/getProgramByNameUsingGET) för att underlätta hämtning av programkostnader och programtaggar. Mer information finns i parametrarna **includeCosts** och **includeTags** i [Programs](/help/rest-api/assets.md) -dokumentationen.

### Andra förbättringar

API:t för gruppextrahering är nu &quot;workspace aware&quot;. När du skapar en API-användare för en [anpassad tjänst](/help/rest-api/custom-services.md) måste du välja en användarroll med API-åtkomst för en eller flera arbetsytor. Tidigare beviljades den anpassade tjänsten åtkomst till alla arbetsytor. Nu får den anpassade tjänsten endast åtkomst till den eller de arbetsytor som valdes när endast API-användare skapades.

Publicerad _2018-03-02_ av _David_

## Uppdateringar våren 2018

I vårutgåvan 2018 släpper vi nya REST API:er och förbättrad webbspårningssekretess. Se hela listan med uppdateringar nedan.

REST API

### Statisk lista-CRUD

Tillåter användare att fjärrskapa, läsa, uppdatera och ta bort statiska listposter. Möjliggör hantering av en statisk listas hela livscykel via REST API:er, inklusive ifyllande och underhåll av medlemskap. Information finns [här](/help/rest-api/static-lists.md).

### Metadata för anpassad aktivitet

Tillåter användare att fjärrskapa anpassade aktivitetsposter. Möjliggör hantering av hela livscykeln för anpassade aktiviteter via REST API:er, inklusive hantering av typer och typattribut. Information finns [här](/help/rest-api/activities.md).

### Metadata för smart lista

Tillåter användare att fjärrläsa, klona och ta bort smarta listposter. Möjliggör hantering av smarta listor via REST API:er. Information finns [här](/help/rest-api/smart-lists.md).

### Sekretess för webbspårning

Munchkin JavaScript webbspårningskod har förbättrats och innehåller följande integritetsrelaterade förbättringar:

* Avanmäl dig - Möjlighet att låta besökare välja bort webbspårning permanent. Information finns [här](/help/javascript-api/lead-tracking.md).
* Anonymisering av IP-adresser - Följ lokala och internationella sekretessregler genom att tillhandahålla möjligheten att anonymisera IP-adresserna för webbbesökare. Se **anonymiszeIP** konfigurationsparameter [här](/help/javascript-api/configuration.md).

### Andra förbättringar

* Slutpunkten [Get Folders](https://developer.adobe.com/marketo-apis/api/asset/#operation/getFolderUsingGET) returnerar nu alla mappar när ett överordnat objekt som inte är root och maxDepth=1 har angetts.
* Slutpunkten [Get Landing Page by Id](https://developer.adobe.com/marketo-apis/api/asset/#operation/getLandingPageByIdUsingGET) returnerar nu URL-attribut med protokollet i samtliga fall (http:// eller https://).

Publicerad _2018-06-29_ av _David_

## Uppdateringar sommaren 2018

Sommarversionen 2018 är i första hand en underhållsrelease som innehåller mindre förbättringar och defekta lösningar. Se hela listan med uppdateringar nedan.

### REST API

* Stöd för e-postdispositionsfält som har utelämnats i onödan. Dessa fält kommer nu att vara tillgängliga för läsning och skrivning över REST, beroende på vad som är lämpligt.
   * Svartlistad
   * Marknadsföring har pausats
   * E-post pausad
   * Relativ brådskande situation
* Slutpunkten [Hämta leads efter filtertyp](https://developer.adobe.com/marketo-apis/api/lead-database-endpoint-reference/#/Leads/getLeadsByFilterUsingGET) har nu stöd för leadPartionId som en filterType.

### Defekt lösningar

**REST-slutpunkt**

**Beskrivning**

[Godkänn program](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveProgramUsingPOST)

Om du hade angett Blockera icke-operativa e-postmeddelanden till false när du skapade programmet återställs ett anrop till Godkänn program till true.

[Massextrahering](/help/rest-api/bulk-extract.md)

Vissa Unicode-tecken är skadade i extraheringsutdatafilen.

[Klona program](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)

* Om du klonade e-postprogrammet återställs SmartList-filterlogiken till&quot;Alla&quot; i det resulterande programmet, oavsett den ursprungliga inställningen.
* Om du försökte klona ett program som innehöll en statisk lista (som togs bort) fick du felmeddelandet&quot;The following assets are unsupported:List&quot;.
* Om du försökte klona ett program över arbetsytorna fick du felmeddelandet&quot;Det går inte att klona programmet&quot; 611.

[Hämta statisk lista med ID](https://developer.adobe.com/marketo-apis/api/asset/#operation/getStaticListByIdUsingGET)

Om din anpassade tjänst hade skrivskyddad resursrollbehörighet så fick du felmeddelandet 603&quot;Åtkomst nekad&quot;.

[Överför lead till Marketo](https://developer.adobe.com/marketo-apis/api/mapi/#operation/pushToMarketoUsingPOST)

Om attributet **cookies** angavs i ett **input** -matrislead-objekt, associerades inte tidigare anonym aktivitet med nyligen skapat lead.

[Schemalägg kampanj](https://developer.adobe.com/marketo-apis/api/mapi/#operation/scheduleCampaignUsingPOST)

Om du angav ett **runAt**-datum långt in i framtiden kördes aldrig kampanjen och **success=true** returnerades. Om datumet **runAt** ligger längre än två år framåt returneras ett fel [1042](/help/rest-api/error-codes.md).

[Synkronisera leads](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST)

Om du angav `action=createDuplicate` och en `externalCompanyId`-parameter (för att associera den nya lead-instansen med ett befintligt företag) associerades denna lead med ett tomt företag (i stället för det angivna företaget).

#### Diverse

* Följande dataändringsvärde har tagits bort från alla slutpunktssvar: mktoClientReqId. Detta var endast avsett för internt bruk.
* Lagt till felsökningsfunktioner. Ange en felkod för REST API i sökrutan och välj i listan Komplettera automatiskt under för att gå till felbeskrivningen.
* Sidan [Referens för slutpunkt](/help/rest-api/endpoint-reference.md) har lagts till. Det här är en sorterbar lista över alla REST API-slutpunkter på ett och samma ställe. Du kan också använda den här sidan för att generera den minimala uppsättning behörigheter som krävs för ditt program. Detta är praktiskt när du skapar en anpassad tjänst.

Publicerad _2018-10-12_ av _David_

## Uppdateringar hösten 2018

Versionen från hösten 2019 är främst en underhållsrelease som består av mindre förbättringar och defekta lösningar. Se hela listan med uppdateringar nedan.

### Förbättringar

* [CC-fält för e-post](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/general/email-cc) har lagts till med stöd för [Resurs-API](/help/rest-api/assets.md). CC-fältinställningarna sprids som förväntat under godkännande/kloning (utkastgodkännande via e-post eller e-postmall, e-post eller programklon). Alla e-postrelaterade slutpunkter returnerar nu CC-fältvärden i egenskapen **ccFields** . Rulla ned i svaret nedan för att se ett exempel. Den här ändringen påverkar följande slutpunkter: [Hämta e-post med ID](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailByIdUsingGET), [Hämta e-post med namn](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailByNameUsingGET), [Hämta e-post](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailUsingGET), [Godkänn e-postutkast](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveDraftUsingPOST), [Godkänn e-postmallutkast](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveDraftUsingPOST_1), [Klona e-post](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneEmailUsingPOST), [Klonprogram.](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)

```json
{
    "success": true,
    "errors": [],
    "requestId": "cc96#166e836348b",
    "warnings": [],
    "result": [
        {
            "id": 2061,
            "name": "Test Email",
            "description": "This is a test",
            "createdAt": "2018-11-06T05:27:10Z+0000",
            "updatedAt": "2018-11-06T08:33:16Z+0000",
            "url": "<https://app-sjqe.marketo.com/#EM2061A1LA1>",
            "subject": {
                "type": "Text",
                "value": "This is a test with CC Fields"
            },
            "fromName": {
                "type": "Text",
                "value": "Tommy Tester"
            },
            "fromEmail": {
                "type": "Text",
                "value": "<tommy.tester@marketo.com>"
            },
            "replyEmail": {
                "type": "Text",
                "value": "<tommy.tester@marketo.com>"
            },
            "folder": {
                "type": "Program",
                "value": 7494,
                "folderName": "Initiative"
            },
            "operational": false,
            "textOnly": false,
            "publishToMSI": false,
            "webView": false,
            "status": "approved",
            "template": 1218,
            "workspace": "Default",
            "version": 2,
            "autoCopyToText": true,
            "ccFields": [
                {
                    "attributeId": "855",
                    "objectName": "lead",
                    "displayName": "emailcc",
                    "apiName": "emailcc"
                },
                {
                    "attributeId": "857",
                    "objectName": "lead",
                    "displayName": "leadDetails",
                    "apiName": "leadDetails"
                },
                {
                    "attributeId": "859",
                    "objectName": "company",
                    "displayName": "headquarters",
                    "apiName": "headquarters"
                }
            ]
        }
    ]
}
```

### Defekt lösningar

* Justerat stöd för [Flera profileringsdomäner](https://experienceleague.adobe.com/sv/docs/marketo/using/home) för [Resurs-API](/help/rest-api/assets.md). Tidigare spreds inte inställningarna för flera profileringsdomäner när du godkänner ett e-postutkast, klonar ett e-postmeddelande eller klonar ett program. Detta har korrigerats. Den här ändringen påverkar följande slutpunkter: [Godkänn e-postutkast](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveDraftUsingPOST), [Klona e-post](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneEmailUsingPOST), [Klonprogram.](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST)
* [apiOnly](/help/javascript-api/configuration.md)-konfigurationsinställningen har lagts till. Som standard utlöser webbsidor som innehåller Munchkin-taggen händelsen&quot;Besök webbsida&quot; när webbsidan läses in i webbläsaren. I vissa fall är detta inte önskvärt. Webbprogram med en sida som behöver fullständig kontroll över när händelsen utlöses. Vi har lagt till en ny konfigurationsinställning för **apiOnly** som stöd för det här användningsfallet. Om värdet är true genereras ingen aktivitet av typen &quot;Besök webbsida&quot; vid sidinläsning.
* Konfigurationsinställningen [domainSelectorV2](/help/javascript-api/configuration.md) har lagts till. Som standard hanterar inte Munchkin-taggen webbsidor som finns på webbplatser med [landskodens toppnivådomäner](https://en.wikipedia.org/wiki/Country_code_top-level_domain) med två bokstäver (exempel: .io, .co, .ly). Detta gör att Munchkin cookie-domänattribut ställs in felaktigt. Vi har lagt till en ny konfigurationsinställning för **domainSelectorV2** för att få en bättre upplevelse. Om värdet är true används en förbättrad algoritm för att automatiskt ställa in Munchkin cookie-domänattributet.
* Cookidomänen [Opt-Out](/help/javascript-api/lead-tracking.md) har justerats. I vissa fall var domänattributet för Munchkin-cookie för avanmälan (mkto_opt_out) felaktigt inställt. Munchkin-cookie för avanmälan använder nu samma logik som Munchkin-cookien (_mkto_trk) för att fastställa domänens cookie-attribut, inklusive att konfigurationsinställningen **domainLevel** respekteras.
* Android programutvecklare kan nu direkt använda Google [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) (FCM) med denna SDK. Information finns [här](/help/mobile/installation.md).

Publicerad _2018-12-07_ av _David_

## Uppdateringar våren 2019

Versionen våren 2019 är främst en underhållsrelease som består av mindre förbättringar och defekta lösningar. Se hela listan med uppdateringar nedan.

Publicerad _2019-03-19_ av _David_

## Uppdateringar från juni 2019

Juniversionen 2019 är främst en underhållsrelease som består av mindre förbättringar, och defekta lösningar. Se hela listan med uppdateringar nedan.

### REST API

1. En kontrollsumma har lagts till i statusslutpunkterna för massexport. Du kan jämföra kontrollsumman med en hash av den hämtade filen för att verifiera integriteten hos den hämtade filen. Kontrollsumman är en SHA-256-hash av den exporterade filen och lagras i attributet fileCheckSum när ett exportjobb har slutförts.

Följande slutpunkter returnerar kontrollsumman: [Get Export Lead Job Status](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsStatusUsingGET), [Get Export Lead Jobs](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsFileUsingGET), [Get Export Activity Status](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsStatusUsingGET), [Get Export Activity Jobs](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportActivitiesUsingGET)

#### Defekt lösningar

1. Korrigerat problem med [Massimport av anpassat objekt](/help/rest-api/bulk-custom-object-import.md) vid import av decimaltal till heltalsfält. Före korrigeringen konverterades decimaltalet till ett heltal genom att hela taldelen tilldelades och fraktionsandelen ignorerades (t.ex. konverterades 5,432 till 5). Nu genereras ett fel av typen&quot;Ogiltig datatyp i fältet Source ID&quot; för varje rad som innehåller ett datamatchningsfel.
1. Ett problem har korrigerats där ett e-postprogram som skapats med slutpunkten för [klonprogrammet](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST) inte uppfyllde inställningarna för kommunikationsgränser i vissa fall.
1. Korrigerat problem med slutpunkten [Godkänn landningssidans utkast](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveLandingPageUsingPOST) där det skulle returnera 611. &quot;Systemfel&quot; när landningssidan innehöll formuläret för e-postavanmälan.
1. Korrigerat problem med slutpunkten [Godkänn landningssidans utkast](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveLandingPageUsingPOST) där det skulle returnera 611. &quot;Systemfel&quot; när landningssidan har klonats med slutpunkten [Klonlandningssida](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneLandingPageUsingPOST).

#### Utgånget

1. Som en del av [borttagningen av e-postredigeraren 1.0](https://nation.marketo.com:443/t5/knowledgebase/email-editor-1-0-is-being-deprecated-june-18th/ta-p/250666) blir 1.0 e-post-Assets skrivskyddat i slutet av 2019. Alla 1.0-e-postmeddelanden från Assets måste konverteras till 2.0 som descriDiscard e-postutkast, inbäddat [här](https://nation.marketo.com:443/t5/knowledgebase/email-editor-1-0-is-being-deprecated-june-18th/ta-p/250666). För att hjälpa utvecklare att förbereda sig för den händelsen har vi lagt till varningar för alla e-postrelaterade slutpunkter som försöker ändra 1.0-e-post-Assets. Här är ett exempelsvar som innehåller varningen:

`{` `"success": true,` `"errors": [],` `"requestId": "15c57#16b338d6e75",` `"warnings": [` `"This is a v1 email asset. API support for modifying v1 emails is being dropped, and this operation will not work on v1 emails in the future. To avoid service interruptions, upgrade this and related assets by editing them in the User Interface."` `],` `"result": [` `"{\"service\":\"sendTestEmail\",\"result\":true}"` `]` `}`

Följande e-postrelaterade slutpunkter returnerar varningen:

* Uppdatera e-postens fullständiga innehåll
* Uppdatera e-postinnehåll
* Skicka exempelmejl
* Ogodkänd e-post
* Klonprogram
* Klona e-post
* Godkänn e-postutkast
* Uppdatera avsnitt för dynamiskt e-postinnehåll
* Uppdatera e-postmetadata
* Godkänn program

E-postskript (Apache-hastighet)

#### Utgånget

1. En delmängd av Velocity Script-funktionen inaktiverades av säkerhetsskäl. Detta inkluderar följande metoder och variabler: getClass(), $class, $context, $text. Mer information finns [här](https://nation.marketo.com:443/t5/knowledgebase/unsupported-velocity-tools-disabled-in-june-2019-release/ta-p/251177).

Publicerad _2019-06-14_ av _David_

## Uppdateringar från augusti 2019

I augusti 2019 släpper vi nya REST API:er, förbättrar befintliga API:er och åtgärdar fel. Se hela listan med uppdateringar nedan.

### Förbättringar

1. Förbättrade funktioner för livscykeln för Smart Campaign. Nya slutpunkter har lagts till så att du kan utföra följande åtgärder på smarta kampanjer: hämta efter namn, skapa, uppdatera, klona och ta bort. Fullständig information finns [här](/help/rest-api/smart-campaigns.md).
1. Förbättrade Smart List-slutpunkter som förbättrar frågefunktionerna.
   1. [Get Smart List by Id](https://developer.adobe.com/marketo-apis/api/asset/#operation/getSmartListByIdUsingGET)-slutpunkten returnerar nu beskrivningar av Smart List-regler (utlösare och filter) när du skickar den booleska parametern **includeRules**.
   1. Med slutpunkten [Hämta smarta listor](https://developer.adobe.com/marketo-apis/api/asset/#operation/getSmartListsUsingGET) kan du nu filtrera resultaten efter datumintervall när du skickar parametrarna **firstUpdatedAt** och **latestUpdatedAt**. Dessutom returnerar den här slutpunkten nu smarta listor som är medlemmar i kampanjer och e-postprogram.
1. Lagt till slutpunkter för extrahering av definitioner för smarta listor.
   1. Get [Smart List by Smart Campaign Id](https://developer.adobe.com/marketo-apis/api/asset/#operation/getSmartListBySmartCampaignIdUsingGET) -slutpunkten returnerar den smarta listposten för ett visst smart kampanj-ID.
   1. Get [Smart List by Program Id](https://developer.adobe.com/marketo-apis/api/asset/#operation/getSmartListByProgramIdUsingGET) endpoint returnerar den smarta listposten för ett visst program-ID.
1. Förbättrade slutpunkten [Uppdatera e-postinnehåll](https://developer.adobe.com/marketo-apis/api/asset/#operation/updateEmailContentUsingPOST) så att e-postrubrikfält kan uppdateras för e-postmeddelanden som har brutits från deras mall (ämne, från namn, från e-post, svara till). Bruten från mallen beskrivs [här](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/edit-an-emails-html).

### Defekt lösningar

1. Ett problem har korrigerats där [Ta bort landningssida](https://developer.adobe.com/marketo-apis/api/asset/#operation/deleteLandingPageByIdUsingPOST) på en godkänd landningssida skulle ta bort landningssidan. Det returnerar nu korrekt felet&quot;709, Godkänd landningssida kan inte tas bort&quot;. [LM-127271]
1. Korrigerat problem med slutpunkten [Skicka exempelmeddelande](https://developer.adobe.com/marketo-apis/api/asset/#operation/sendSampleEmailUsingPOST) där den returnerade 611. &quot;Systemfel&quot; när e-post hade brutits från mallen. [LM-127288]
1. Korrigerat problem med slutpunkten [Ta bort program](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST) där ett program som används i vissa fall skulle tas bort i stället för att ett 709-program skulle utfärdas. Programmet kan inte tas bort. Resurserna används någon annanstans eller tas inte bort&quot;. [LM-125431]

### Utgånget

1. API-stöd för e-postredigeraren 1.0 är inaktuellt i januari 2020. Kom ihåg att konvertera dina mediefiler till 2.0 innan dess. Försök att skriva till eller klona e-post 1.0-resurser efter januari resulterar i fel istället för varningar. Läs mer om e-post-API:er [här](https://nation.marketo.com:443/t5/knowledgebase/email-2-0-and-email-api-faq-s/ta-p/251423).
1. I linje med Adobe säkerhetsstandard i världsklass kommer vi att ta bort stödet för TLS 1.0 och 1.1 från och med 13 december 2019. System som integreras med Marketo som inte är kompatibla med 1.2-protokollet kan förlora åtkomsten till Marketo Engage tjänster. Se till att alla klientsystem är kompatibla med TLS 1.2 före den 13 december 2019 för att behålla din Marketo Engage-åtkomst. Mer detaljerad information finns [här](https://nation.marketo.com:443/t5/knowledgebase/tls-1-0-1-1-deprecation-faq/ta-p/249085).

1. Allt innehåll relaterat till Smart Campaign finns nu i menyalternativet [Smarta kampanjer](/help/rest-api/smart-campaigns.md) (under REST API > Assets).

Publicerad _2019-08-16_ av _David_

## Uppdateringar januari 2020

I januari 2020 släpper vi nya REST API:er, förbättrar befintliga API:er och åtgärdar fel. Se hela listan med uppdateringar nedan.

* Lagt till möjlighet att programmässigt skapa anpassade objektschemadefinitioner. På så sätt kan du definiera ett anpassat objekt en gång och tilldela det till så många instanser som behövs. På så sätt kan du göra det möjligt för användare att utnyttja sandlådor och modeller som bygger på hög kvalitet. ISV:er kan även förenkla kundintroduktionsprocessen. Du behöver en lämplig prenumerationstyp för att komma åt API:t för anpassade objektmetadata.
* Lagt till möjlighet att importera och exportera flera programmedlemmar samtidigt. Den nya uppsättningen slutpunkter följer det befintliga Marketo REST API-mönstret för att skapa asynkrona gruppbearbetningsjobb. Poster för programmedlemmar kan innehålla anpassade fält för programmedlemmar och/eller lead-fält.
* Lagt till Hämta tillgängliga formulärprogrammedlemsfält som stöd för användningen av anpassade fält för programmedlemmar som formulärfält. Detta returnerar en lista med alla anpassade fält för programmedlemmar som kan användas i ett Marketo-formulär.
* Lagt till [Hämta e-postmall som används av en &#x200B;](/help/rest-api/email-templates.md)-slutpunkt som returnerar en lista med e-postresurser som är beroende av en viss e-postmall. På så sätt kan ni snabbt förstå effekten av en eventuell ändring av e-postmallar och enklare hantera dessa beroenden.

Publicerad _2020-01-17_ av _David_

## Så här hämtar du alla anpassade objekt

Vi får ofta frågan om hur vi ska använda Marketo API för att få en lista över alla [anpassade objekt](https://experienceleague.adobe.com/sv/docs/marketo/using/home) (CO). För att fråga efter CO krävs mer än namnet: viss _a priori_ kunskap om varje CO krävs också. Metoderna för att få den kunskapen kanske inte är uppenbara eftersom API:t inte innehåller någon metod för att fråga direkt. Precis som med många mål i Marketo Engage ger smarta listor ett svar på frågor om COs som är kopplade till människor (leads). Smarta listor fungerar annorlunda med företag och du får en lista över alla personer vars företag är länkade till filtrets objekttyp så att du kanske behöver deduplicera företag beroende på dina mål. När ett nytt anpassat objekt godkänns skapas ett associerat filter. Det får ett namn i formatet **Har CO NAME**. I exemplet nedan är det anpassade objektnamnet **Konferensspårsprenumeration** och filtret **Har konferensspårsprenumeration**. När du har skapat den smarta listan kan du hämta den information som behövs för att fråga efter associerade CO:er med [slutpunkten för anpassade objekt](/help/rest-api/custom-objects.md). Exportera listan så att det länkade fältet inkluderas (antingen ID eller e-postadress). Du kan exportera med [Bulk Lead Extract API](/help/rest-api/bulk-lead-extract.md)-filtrering genom **smartListName**- eller **smartListId**-filtret eller [exportera från gränssnittet](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/core-marketo-concepts/smart-lists-and-static-lists/managing-people-in-smart-lists/export-people-to-excel-from-a-list-or-smart-list). Du använder varje värde för länkat fält för att fråga anpassade objekt individuellt i nästa steg. Det anpassade objektets namn är **&quot;Konferensspårsprenumeration&quot;** i det här exemplet, och dess API-namn är **ConferenceTrackSubscription_c**. Du hittar API-namnet både i gränssnittet som **API-namn** och via API:t som **namn**.  Administratör | Marketo Custom Objects[/caption] Här är ett fragment som returneras av [List Custom Objects API](https://developer.adobe.com/marketo-apis/api/mapi/#operation/listCustomObjectsUsingGET) -slutpunkten:

```json
{
    "name": "conferenceTrackSubscription_c",
    "displayName": "Conference Track Subscription",
    "description": "Track subscription for conference attendees",
    "createdAt": "2020-01-13T19:50:06Z",
    "updatedAt": "2020-01-13T19:50:06Z",
    "idField": "marketoGUID",
    "dedupeFields": [
        "subscriptionID"
    ],
    "searchableFields": [
        [
            "subscriptionID"
        ],
        [
            "marketoGUID"
        ],
        [
            "leadID"
        ]
    ],
    "relationships": [
        {
            "field": "leadID",
            "type": "child",
            "relatedTo": {
                "name": "Lead",
                "field": "Id"
            }
        }
    ]
}
```

Om du vill hämta anpassade objekt som är kopplade till ett (1:1) eller ett till många (1:N) med personer i din smarta lista gör du en sådan här begäran:

`GET /rest/v1/customobjects/conferenceTrackSubscription_c.json?filterType=leadID&filterValues=1000302,1000303,1000304,1000306,1000307`

I det här exemplet är det här anpassade objektet länkat till Personer av fältet **leadID** så filtertypen är **leadID**. Parametern för filtervärden är en kommaavgränsad lista över ID:n som tagits från exporten av den smarta listan. Begäran kan innehålla så många filtervärden som du får plats i en enda URI för begäran: upp till 8 K tecken. Begäranden som överskrider den här längden returnerar en felkod på 414 HTTP-nivå. Svaret kan returneras i mer än ett segment. I så fall blir **moreResult** **true** och **nextPageToken** inkluderas. Du måste sedan [bläddra igenom](/help/rest-api/paging-tokens.md) resultaten tills **moreResult** är **false**. Här är en del av resultatet för ovanstående API-begäran:

```json
"result": [
    {
        "seq": 0,
        "marketoGUID": "d6b3ed3d-4eb8-4066-a9cd-184c8d385cfe",
        "leadID": "1000302",
        "subscriptionID": "4ad59184-6bf1-4eeb-a583-d82aeee68210",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    },
    {
        "seq": 1,
        "marketoGUID": "e5e0aba4-f27f-494d-93ed-9cb580989bf3",
        "leadID": "1000303",
        "subscriptionID": "fc5596d5-6fa2-4848-b4a2-89d96e245c59",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    },
    {
        "seq": 2,
        "marketoGUID": "e65007cd-86b1-4c17-8d55-057c96e1788a",
        "leadID": "1000304",
        "subscriptionID": "7e54b8a0-2170-4d81-a809-4eac349508d0",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    },
    {
        "seq": 3,
        "marketoGUID": "39d956b2-85e2-4c24-94e7-e9fa5a09d3d0",
        "leadID": "1000306",
        "subscriptionID": "644c8e5b-fc0c-4d4a-80f8-358a65ce0a68",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    },
    {
        "seq": 4,
        "marketoGUID": "a2151350-50c8-437f-bc71-7a054bb601f0",
        "leadID": "1000307",
        "subscriptionID": "bf14218c-ae6a-42b3-a14e-f7182903cbcd",
        "createdAt": "2020-01-13T21:24:04Z",
        "updatedAt": "2020-01-13T21:24:04Z"
    }
```

Du har nu värden för varje anpassat objekt som är direkt associerade med personerna i din smarta lista och du kan använda **marketoGUID** till [Update](/help/rest-api/custom-objects.md) eller [Delete](/help/rest-api/custom-objects.md) för att hämta värdena. För anpassade objekt som är kopplade till Personer i en många till många relationer (N:N) returnerar tekniken ovan det första nivåobjektet som är mellannivåobjektet som kopplar varje person till flera andra nivå-CO:er.

Om du vill hämta CO:er på den andra nivån startar du en ny uppsättning frågor för CO-typen på den andra nivån genom att filtrera länkfältet och de värden som extraheras från mellannivåobjektet på den första nivån. Objektet **Konferensspårsprenumeration** kan till exempel ha en annan objektnivå som representerar sessioner som kallas **&quot;Session&quot;** och som troligen skulle länkas av **subscriptionID**. Begäran om att hämta sessioner som är länkade till ovan nämnda konferensspårsprenumerationer skulle då se ut så här:

`GET /rest/v1/customobjects/session_c.json?filterType=subscriptionID&filterValues=4ad59184-6bf1-4eeb-a583-d82aeee68210,e5e0aba4-f27f-494d-93ed-9cb580989bf3,e65007cd-86b1-4c17-8d55-057c96e1788a,39d956b2-85e2-4c24-94e7-e9fa5a09d3d0,bf14218c-ae6a-42b3-a14e-f7182903cbcd`

_Fotnot_ _1)**smartListName**&#x200B;och **smartListId**&#x200B;är inte tillgängliga för vissa prenumerationer. Om den inte är tillgänglig för din prenumeration får du ett fel när du anropar slutpunkten för Skapa Lead-jobb (**&quot;1035, filtertypen stöds inte för målprenumerationen&quot;**). Kunder kan kontakta Marketo Support för att aktivera den här funktionen i prenumerationen._

Anslaget _2020-01-14_ av _Tony_

## Hämta varje person (lead)

Vi får många frågor om de processer som krävs för att hämta varje person (lead) från en Marketo Engage-instans. Vi har gett många användbara svar, men inget har varit så komplett som detta. Jag har identifierat några viktiga koncept som behövs för att extrahera varje lead med Marketo Bulk Extract API. Alla andra detaljer kan du lära dig av demonstrationskoden som jag skapade för att följa med. När du har läst det här inlägget och utforskat demokoden får du all information du behöver veta för att hämta alla leads från en Marketo Engage-instans.

### Översikt

Kärntekniken använder [API:t för extrahering av grupplead](/help/rest-api/bulk-lead-extract.md). Du kanske förväntar dig att kunna skapa ett exportjobb för grupplead utan filter, men du kan inte: **API kräver ett filter**. De tillgängliga filtren är **createdAt**, **staticListName**, **staticListId,** **updatedAt**, **smartListName** och **smartListId**. Filtrering med Smart List utan filter verkar också attraktivt. Testa det och se att systemet är smart nog att behandla en smart lista utan filter: API:t kräver också ett filter här. Eftersom vi behöver ett filter är det tillförlitliga och kanoniska filter som ska användas **createdAt**. Den här filtertypen tillåter datetime-intervall på upp till 31 dagar, så vi måste köra flera jobb och kombinera resultaten. Vi börjar med att hitta det äldsta möjliga skapandedatumet för en lead i målinstansen. Med början vid det äldsta möjliga datumet skapar vi jobb som sträcker sig över 31 dagar minus en sekund (mer senare). När varje jobb har skapats placeras det i kö och väntar på att slutföras. Sedan hämtar vi den resulterande filen och kontrollerar dess integritet med en kontrollsumma. Och slutligen, ta bort dubbletter av leads efter ID och skriv unika leads till en CSV-utdatafil.

### Hitta din äldsta lead

Jag använder lite &quot;trick&quot; för att få fram det äldsta möjliga datumet för en lead i målinstansen. Det finns ingen API-slutpunkt för den uppgiften, så vi behöver lite kreativitet. Jag frågar alla mappar med ett **maxDepth = 1** som ger oss en lista över alla mappar på den översta nivån i instansen. Sedan samlar jag in **createdAt**-datumen, tolkar dem och hittar det äldsta datumet. Den här metoden fungerar eftersom vissa standardmappar på den översta nivån skapas med instansen och inga leads kan skapas innan dess.

### Välj obligatoriska fält

Du måste bestämma vilka fält du vill extrahera. Hitta de tillgängliga fälten för målinstansen med [Beskriv lead 2-slutpunkten](https://developer.adobe.com/marketo-apis/api/mapi/#operation/describeUsingGET_2). Svaret på den begäran kommer att innehålla en lista med namnet&quot;fält&quot;. Här är ett utdrag från ett exempelsvar:

```json
  "fields": [
      {
          "name": "AccountSource",
          "displayName": "Account Source",
          "dataType": "string",
          "length": 40,
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "acquisitionProgramId",
          "displayName": "Acquisition Program",
          "dataType": "reference",
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "Active_c",
          "displayName": "Active",
          "dataType": "string",
          "length": 255,
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "address",
          "displayName": "Address",
          "dataType": "text",
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "Address_lead",
          "displayName": "Address (L)",
          "dataType": "string",
          "length": 255,
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "annualRevenue",
          "displayName": "Annual Revenue",
          "dataType": "currency",
          "updateable": true,
          "crmManaged": false
      },
      {
          "name": "anonymousIP",
          "displayName": "Anonymous IP",
          "dataType": "string",
          "length": 255,
          "updateable": true,
          "crmManaged": false
      }, ...
```

Den här slutpunkten returnerar en fullständig lista som innehåller både standardfält och anpassade fält. Ju fler fält du begär, desto längre tid tar det att slutföra exportjobbet och desto större blir filen. Du bör vanligtvis välja bara de fält du behöver. Inget hindrar dig från att begära alla tillgängliga fält, så det är vad jag håller på att degradera. Fältidentifieraren som krävs när ett exportjobb skapas är värdet **name**. Jag extraherar namnvärdena till en lista med alla fältnamn. Sedan använder jag den för att begära alla tillgängliga fält när jag skapar varje exportjobb.

### Exportera datumintervall för jobb: 31 dagar per

Varje exportjobb kan omfatta upp till 31 dagar. Demoversionen som jag använder skapades i augusti 2016, så jag måste skapa lite över 40 jobb idag. Det är antalet dagar sedan det första skapade datumet delat med 31 avrundat uppåt. API:t tillåter att två exportjobb bearbetas samtidigt så att du kan extrahera med två jobb som körs parallellt. Massextraheringsjobb är en resurs som delas med alla andra integreringar, så jag kommer att vara trevlig. Jag lämnar det andra jobbet tillgängligt för andra integreringar och visar att jag kör enstaka jobb efter det andra. Datumen som används för filtret **createdAt** formateras med specifikationen [ISO 8601](https://www.w3.org/TR/NOTE-datetime). De anges alltid i GMT (Z+0000), så tidszonen representeras helt enkelt av &quot;Z&quot; eller &quot;+00:00&quot;. 1 augusti 2016 är **2016-08-01T00:00:00+00:00** och 31 dagar senare är 1 september 2016 vilket är **2016-09-01T00:00:00+0 0:00.** Både start- och sluttider är inkluderande, så jag subtraherar 1 sekund från sluttiden: **2016-09-01T00:00:00+00:00** blir **2016-08-31T23:59:59+00:00**. Genom att subtrahera en sekund undviker du överlappande tider. Eftersom GMT är standardvärdet kan du också lämna **Z** eller **+00:00** i rabatt.

### Deduplicering

Även om jag har råkat ut för problem med att undvika överlappande tider har jag också implementerat borttagning av dubbletter. Jag gjorde det eftersom det finns vissa kantfall när tider ändras ([Sommartid](https://en.wikipedia.org/wiki/Daylight_saving_time)) vilket resulterar i tvetydiga värden, och som ett resultat av detta kan Marketo Bulk Extract API returnera annars oväntade dubblettleads. Det är sällsynt men det måste beaktas i alla integreringar som använder datetime-filterintervall. Jag tog bort en sekund för att klargöra att tiden är inkluderande. Jag vill inte att du ska tro att du skapar ett jobb med **createdAt** och **endAt** gånger **2016-08-01T00:00:00Z** och **2016-09-01T00:00:00Z** inkluderar inte leads som skapades **2016-09-01T00:00:00Z**. Det kommer att ingå.

### Skapa ett jobb

Det första steget är att skapa ett jobb med slutpunkten [Skapa exportlead-jobb](https://developer.adobe.com/marketo-apis/api/mapi/#operation/createExportLeadsUsingPOST). I den här demon ser begäran om att skapa vårt första exportjobb ut så här:

`POST /bulk/v1/leads/export/create.json`

```json
{ "filter": {"createdAt": {"startAt": "2016-08-01T00:00:00",
                           "endAt": "2016-09-01T00:00:00"}},
"fields":["AccountSource","acquisitionProgramId","Active_c","address","Address_lead","annualRevenue","anonymousIP","BillingAddress","billingCity","billingCountry","BillingGeocodeAccuracy","BillingLatitude","BillingLongitude","billingPostalCode","billingState","billingStreet","blackListed","blackListedCause","city","CleanStatus","CleanStatus_account","company","CompanyDunsNumber","contactCompany","cookies","country","createdAt","customfield","DandbCompanyId","DandbCompanyId_account","dateOfBirth","dddS","department","doNotCall","doNotCallReason","dS","dueDate","DunsNumber","email","EmailBouncedDate","EmailBouncedReason","emailInvalid","emailInvalidCause","emailSuspended","emailSuspendedAt","emailSuspendedCause","externalCompanyId","externalSalesPersonId","facebookDisplayName","facebookId","facebookPhotoURL","facebookProfileURL","facebookReach","facebookReferredEnrollments","facebookReferredVisits","fax","firstName","gender","GeocodeAccuracy","holll","id","industry","inferredCity","inferredCompany","inferredCountry","inferredMetropolitanArea","inferredPhoneAreaCode","inferredPostalCode","inferredStateRegion","interested","interestedIn","isAnonymous","IsEmailBounced","isLead","iSTRUE","Jigsaw","JigsawCompanyId_account","JigsawContactId_lead","Jigsaw_account","Languages_c","lastName","LastReferencedDate","LastReferencedDate_account","lastReferredEnrollment","lastReferredVisit","LastViewedDate","LastViewedDate_account","Latitude","leadPartitionId","leadPerson","leadRevenueCycleModelId","leadRevenueStageId","leadRole","leadScore","leadSource","leadStatus","linkedInDisplayName","linkedInId","linkedInPhotoURL","linkedInProfileURL","linkedInReach","linkedInReferredEnrollments","linkedInReferredVisits","links","Longitude","MailingAddress","MailingGeocodeAccuracy","MailingLatitude","MailingLongitude","mainPhone","marketingSuspended","marketingSuspendedCause","middleName","mktoAcquisitionDate","mktocomment1","mktocomments2","mktoCompanyNotes","mktoDoNotCallCause","mktoIsCustomer","mktoIsPartner","mktoName","mktoPersonNotes","mktosync","mktotest1","mobile","mobilePhone","NaicsCode","NaicsDesc","newcustom","numberOfEmployees","originalReferrer","originalSearchEngine","originalSearchPhrase","originalSourceInfo","originalSourceType","OtherAddress","OtherGeocodeAccuracy","OtherLatitude","OtherLongitude","personPrimaryLeadInterest","personTimeZone","personType","phone","PhotoUrl","PhotoUrl_account","postalCode","priority","ProductInterest_c","rating","referal","registrationSourceInfo","registrationSourceType","relativeScore","relativeUrgency","requiredNumberofCylinder","salutation","sfdcAccountId","sfdcContactId","sfdcId","sfdcLeadId","sfdcLeadOwnerId","sfdcType","ShippingAddress","ShippingGeocodeAccuracy","ShippingLatitude","ShippingLongitude","sicCode","SicDesc","site","state","surveyAnswers","syndicationId","testBooleanField","testscore","title","totalReferredEnrollments","totalReferredVisits","Tradestyle","twitterDisplayName","twitterId","twitterPhotoURL","twitterProfileURL","twitterReach","twitterReferredEnrollments","twitterReferredVisits","uNSUBSCIBE","unsubscribed","unsubscribedReason","updatedAt","urgency","url","website","YearStarted"]}
```

Svaret ser ut så här:

```json
{
  "requestId": "6902#16fb52118bf",
  "result": [
      {
          "exportId": "4f2b9115-c3f2-4e40-a87c-bf803bbfed99",
          "format": "CSV",
          "status": "Created",
          "createdAt": "2020-01-17T20:10:43Z"
      }
  ],
  "success": true
}
```

### Köa jobbet

Jobbet har nu skapats, men bara sitter där och gör ingenting. För att köra jobbet måste vi anropa [enqueue-slutpunkten](https://developer.adobe.com/marketo-apis/api/mapi/#operation/enqueueExportLeadsUsingPOST) med värdet **exportId** för att skapa URI:n för begäran. Så här ser det ut:

`POST /bulk/v1/leads/export/4f2b9115-c3f2-4e40-a87c-bf803bbfed99/enqueue.json`

Det finns inget innehåll för denna POST, vi använder bara POST HTTP-verbet här. Begäran kommer att generera ett svar som detta:

```json
{
  "requestId": "1836a#16fb5238a48",
  "result": [
      {
          "exportId": "4f2b9115-c3f2-4e40-a87c-bf803bbfed99",
          "format": "CSV",
          "status": "Queued",
          "createdAt": "2020-01-17T20:10:43Z",
          "queuedAt": "2020-01-17T20:13:23Z"
      }
  ],
  "success": true
}
```

Som jag nämnde tidigare är antalet jobb som kan köras samtidigt begränsat. Det finns också en gräns för hur många jobb som köas samtidigt: 10. Vi behöver mer än 40 så att gränsen förhindrar oss från att skapa alla jobb samtidigt. Andra integreringar kan också köra jobb, så vi måste ta hänsyn till möjligheten att alla platser är fulla. Om du försöker placera ett nytt jobb i kö när det redan finns 10 jobb i kö genereras ett [1029](/help/rest-api/error-codes.md)-fel. När du får en **1029** ska du använda en exponentiell säkerhetskopiering tills jobbet kan ställas i kö. Jag väntar en minut och dubblerar det värdet varje gång jag får en **1029** -felkod på upp till 4 minuter mellan förfrågningarna, men aldrig längre än så. Den här tekniken kallas [Trunkerad binär exponentiell säkerhetskopiering](https://devopedia.org/binary-exponential-backoff) och är den bästa metoden för återställningsbara fel och statuskontroller.

### Vänta på att jobbet slutförs

Varje jobb tar lite tid att köra så vi anropar [statusslutpunkten](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsStatusUsingGET) för att övervaka förloppet. Vi kommer återigen att inkludera **exportId** i URI för begäran så här:

`GET /bulk/v1/leads/export/4f2b9115-c3f2-4e40-a87c-bf803bbfed99/status.json`

Innan jobbet är klart får du ett svar som ser ut så här:

```json
{
    "requestId": "153cb#16fb525435d",
    "result": [
        {
            "exportId": "4f2b9115-c3f2-4e40-a87c-bf803bbfed99",
            "format": "CSV",
            "status": "Processing",
            "createdAt": "2020-01-17T20:10:43Z",
            "queuedAt": "2020-01-17T20:13:23Z",
            "startedAt": "2020-01-17T20:13:49Z"
        }
    ],
    "success": true
}
```

Jag kör samma exponentiella säkerhetskopiering (1 minut upp till 4 minuter) medan jobbstatusen är&quot;Köad&quot;. Statusen är inte i realtid; den uppdateras en gång per minut och det finns mycket liten fördel med att avfråga snabbare. Jag återställer säkerhetskopian när jobbstatusen ändras till &quot;Bearbetar&quot;. Vi väntar på statusen&quot;Slutförd&quot;, som ser ut så här:

```json
{
  "requestId": "10ad9#16fb5268f9b",
  "result": [
      {
          "exportId": "4f2b9115-c3f2-4e40-a87c-bf803bbfed99",
          "format": "CSV",
          "status": "Completed",
          "createdAt": "2020-01-17T20:10:43Z",
          "queuedAt": "2020-01-17T20:13:23Z",
          "startedAt": "2020-01-17T20:13:49Z",
          "finishedAt": "2020-01-17T20:15:28Z",
          "numberOfRecords": 59,
          "fileSize": 74436,
          "fileChecksum": "sha256:de553362e7ffad6556ae9ea749655c35010c7f0e944fc5a85782183130dca18d"
      }
  ],
  "success": true
}
```

Värdet **numberOfRecords** är noll när begäran inte returnerar några leads. Jag kontrollerar värdet och hoppar över nästa steg när det passar. När leads returneras extraherar jag värdet **fileChecksum**. Vi använder den för att kontrollera filens integritet när den hämtas.

### Hämta leads

Om **numberOfRecords** är större än noll hämtar du den exporterade filen med hjälp av [Get Export Lead File](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportLeadsFileUsingGET) med en sådan begäran:

`GET /bulk/v1/leads/export/4f2b9115-c3f2-4e40-a87c-bf803bbfed99/file.json`

Kontrollera att filen överfördes utan fel: beräkna kontrollsumman för filen och jämför med **fileChecksum** som vi sparade tidigare. Beräkna kontrollsumman med hjälp av [SHA-2](https://en.wikipedia.org/wiki/SHA-2) och speciellt SHA256-hash-funktionen. Om de beräknade kontrollsummorna inte överensstämmer uppstod ett fel i filöverföringen och du kan antingen försöka överföra igen eller avbryta och återställa manuellt.

### Sammanställ data

Efter att ha bläddrat igenom var 31:e dag från första lead till idag har du en komplett uppsättning. Du kommer också att ha en fil för varje intervall. Det enklaste sättet att skapa en enda aggregerad fil med varje lead är att sammanfoga dessa filer efter att rubrikraden tagits bort för alla utom den första filen. Om du gör det, glöm inte att vänta dig och planera för eventuell duplicering senare i databehandlingsflödet. I min demonstration bearbetar jag filerna när jag laddar ned dem. Innan jag lägger till varje datarad i utdatafilen tar jag bort dubbletten genom att kontrollera om radens lead-ID redan har skrivits.

Jag har utvecklat demonstrationskod [här](https://github.com/Marketo/REST-Sample-Code/tree/master/python/LeadDatabase/Leads) som förhoppningsvis fyller i informationen om den här processen och kan fungera som mall för din egen utveckling. Demo-koden är avsedd att vara ett inlärningsverktyg så det finns förbättringar av tillförlitligheten som krävs för ett produktionssystem. **Koden tillhandahålls som AS-IS** under MIT-licensen, men den är antagligen tillräckligt bra för engångsanvändning med mänsklig övervakning. Inget hindrar dig nu! När du följer den här processen extraherar du alla leads med Marketo Bulk Extract API och eventuellt alla fält för Marketo Engage målinstans. Om du vill utöka dina data ytterligare hämtar du varje leads aktiviteter med hjälp av teknikerna.

Anslaget _2020-03-05_ av _Tony_

## Uppdateringar från februari 2020

I februari 2020 släpper vi nya REST API:er. Se hela listan med uppdateringar nedan.

### Meddelanden

* Efter september 2020 kommer [Resurs-API](/help/rest-api/assets.md)-slutpunkter inte längre att acceptera frågeparametern **_method**. Detta användes för att skicka frågeparametrar i ett POST-brödtext för att kringgå URI-längdbegränsningar. För att tillgodose begäranden som kräver den här parametern ökas URI-gränsen för tillgångs-API:er från 6KiB till 65KiB.
* Vad gäller vår position på ITP, se thisMarketo Community-posten: [Webbläsarcookie-uppdateringar: Hur Marketo/Munchkin påverkas](https://nation.marketo.com:443/t5/knowledgebase/browser-cookie-updates-how-marketo-munchkin-is-affected/ta-p/251524)
* En ändring gjordes i aktiviteten &quot;Ändra status i progression&quot;. Attributet &quot;Program Member ID&quot; lades till som stöd för den kommande funktionen &quot;Program Member Custom Fields&quot;.

Publicerad _2020-02-26_ av _David_

## Borttagning av Munchkin Associate Lead Method

I nästa version av Munchkin JavaScript Client, version 159, börjar borttagningen av Munchkin [Associate Lead-metoden](/help/javascript-api/api-reference.md). Från och med den här versionen, när metoden anropas, visas en varning i webbläsarkonsolen om att metoden kommer att tas bort i en framtida version. När metoden har tagits bort kommer försök att använda metoden att misslyckas. Marketo-kunder som vi har identifierat som de nyligen har använt denna metod kommer att meddelas separat om hur de använder den. Mer information om Munchkin 159 finns i [det här Marketing Nation-inlägget](https://nation.marketo.com:443/t5/product-documents/munchkin-javascript-version-159-amp-associate-lead-deprecation/ta-p/299687).

### Vanliga frågor

Hur vet jag om jag är påverkad?

Adobe meddelar kunderna om vi har observerat att den här metoden har använts för deras prenumeration och kommer att göra det flera gånger under borttagningsperioden.

När tas metoden bort?

Den här metoden kommer att tas bort i Munchkin JS v161, som är schemalagd för allmän tillgänglighet, tillsammans med Marketo-versionen från oktober 2021.

Varför tas den här metoden bort?

Fler prestandaförbättringar för att uppfylla samma användningsfall har implementerats och släppts sedan den här metoden introducerades. För att förbättra prestanda och hälsa för våra tjänster är det ibland nödvändigt att ta bort funktioner som inte uppfyller godtagbara standarder.

Vad ska jag använda istället för den här metoden?

Munchkin Associate Lead har två primära användningsområden: överföring av persondata och association av en webbläsar-tracking-cookie till motsvarande personpost i Marketo. Sedan Munchkin Associate Lead introducerades har mer effektiva sätt att skicka data och associera cookies implementerats.

#### Inlämning på serversidan

Om du inte behöver skicka data via webbläsaren erbjuder REST API många metoder för [överföring av persondata](/help/rest-api/leads.md) och en [specialbyggd metod för att associera cookies med personposter](/help/rest-api/leads.md).

När rullar version 159 av Munchkin ut?

Version 159 har varit allmänt tillgänglig sedan oktober 2020-utgåvan av Marketo.

Publicerad _2020-05-06_ av _Kenny_

## Skicka ett Marketo-formulär i bakgrunden

När organisationen har många olika plattformar för webbinnehåll och kunddata blir det ganska vanligt att man behöver parallella inskick från ett formulär så att de resulterande uppgifterna kan samlas in på olika plattformar. Det finns flera strategier för att göra detta, men den bästa är ofta den enklaste: Använda API:t för Forms 2 för att skicka in ett dolt Marketo-formulär. Detta fungerar med alla nya Marketo-formulär, men du bör helst skapa ett tomt formulär för detta, som inte har några fält. Detta säkerställer att formuläret inte läser in mer data än nödvändigt eftersom vi inte behöver återge någonting. Hämta nu bara [inbäddningskoden](https://experienceleague.adobe.com/sv/docs/marketo/using/home) från formuläret och lägg till den i brödtexten på den önskade sidan, vilket gör en liten ändring. Din inbäddningskod innehåller ett formulärelement som detta:

`<form id="mktoForm_1068"></form>`

Du vill lägga till &quot;style=&quot;display:none&quot; i elementet så att det inte syns, så här:

`<form id="mktoForm_1068" style="display:none"></form>`

När formuläret väl är inbäddat och dolt är koden för att skicka formuläret mycket enkel:

```javascript
var myForm = MktoForms2.allForms()[0];
myForm.addHiddenFields({
 //These are the values which will be submitted to Marketo
 "Email":"<test@example.com>",
 "FirstName":"John",
 "LastName":"Doe"
 });
myForm.submit();
```

Forms har lämnat in en sådan ansökan kommer att bete sig exakt som om leadet hade fyllt i och skickat in ett synligt formulär. Hur du utlöser inlämningen varierar mellan olika implementeringar eftersom var och en har olika åtgärder för att föreslå det, men du kan få det att ske för i stort sett alla åtgärder. Den viktiga delen är att du ställer in fält och värden korrekt. Se till att du använder SOAP API-namnet för de fält som du kan hitta med exportfältnamn för att försäkra dig om att värdena skickas korrekt.

### Migrera från Munchkin Associate Lead

Inlämning av bakgrundsformulär är en av de rekommenderade ersättningsmetoderna för Munchkin Associate Lead. HTML exempelsida nedan demonstrerar migrering på en hög nivå genom att återanvända samma värden som skickas till Associate Lead.

```html
<html>
<head>
    <!--
      Munchkin Code
      Replace with your own instance code
    -->
    <script type="text/javascript">
        (function() {
          var didInit = false;
          function initMunchkin() {
            if(didInit === false) {
              didInit = true;
              Munchkin.init('CHANGE ME');
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
          document.getElementsByTagName['head'](0).appendChild(s);
        })();
        </script>
</head>

<body>
  <!--
    Start Embed code.
    Pasted from Form Actions -> Embed Code except for addition of 'style="display:none"' to the form tag in order to hide it, and instance-specific codes redacted
    Replace with your own code for testing
  -->
  <script src="CHANGE ME"></script>
  <form id="CHANGE ME" style="display:none"></form>
  <script>MktoForms2.loadForm("CHANGE ME", "CHANGE ME", "CHANGE ME TO AN INTEGER ID");</script>
  <!--End Embed Code-->

  <!--Demo code-->
    <script>
        //The same map which is assembled for the Munchkin submission can be reused for the form submission
        let values = {
            "Email": "email@example.com",
            "FirstName": "Test",
            "LastName": "Record"
        }
        //whenReady lets us apply a callback to all mkto forms on the page
        //the callback function fires whenever a form is completely loaded
        //for most use cases this will just be used to capture a reference to the form for later usage
        //submission is done in line here for demonstration only
        MktoForms2.whenReady(function(form){

            //the addHiddenFields methods lets us add arbitrary fields to the form as well as their values
            form.addHiddenFields(values);

            //pass the same set of values to associateLead
            //hashString: secret + email
            Munchkin.munchkinFunction('associateLead', values, "CHANGE ME");

            //submit the form
            form.submit();


        })
    </script>
</body>

</html>
```

Publicerad _2020-05-26_ av _Kenny_

## Uppdateringar juli 2020

I juli 2020 släpper vi nya REST API:er, förbättrar befintliga API:er och åtgärdar fel. Se hela listan med uppdateringar nedan.

* Två slutpunkter har lagts till som gör att du kan fråga efter och ta bort användare som ännu inte har accepterat inbjudan (dvs. väntande användare). Med slutpunkten [Hämta inbjuden användare via ID](/help/rest-api/user-management.md) kan du fråga en väntande användare. Med slutpunkten [Ta bort inbjuden användare](/help/rest-api/user-management.md) kan du ta bort en väntande användare.
* Slutpunkten [Bjud in användare](/help/rest-api/user-management.md) har uppdaterats för att acceptera ISO 8601-kompatibla datumtidssträngar för parametern **expirresAt**.
* Både slutpunkterna [Get User by Id](/help/rest-api/user-management.md) och [Update User Attribute](/help/rest-api/user-management.md) har uppdaterats för att returnera den senaste inloggningstiden för användaren i attributet **lastLoginAt**.
* Ett problem har korrigerats där slutpunkten [Skapa statisk lista](https://developer.adobe.com/marketo-apis/api/asset/#operation/createStaticListUsingPOST) returnerade felet&quot;611, systemfel&quot; när du försökte skapa en statisk lista som redan fanns. Ändrad till returfelet&quot;709, Statisk lista med samma namn finns redan&quot;. [LM-135934]
* Ett problem där [Skapa e-post](https://developer.adobe.com/marketo-apis/api/asset/#operation/createEmailUsingPOST)-slutpunkten returnerade felet&quot;611, systemfel&quot; när du försökte skapa ett e-postmeddelande som redan fanns har åtgärdats. Ändrad till returfelet&quot;709, det finns redan e-post med samma namn&quot;. [LM-138648]
* Ett problem har korrigerats där startsidans frågeslutpunkter returnerade felaktiga **createdAt**-värden. Slutpunkterna returnerade den tid då en landningssida senast godkändes. De återgår nu till den tidpunkt då en landningssida skapades. [LM-138648]
* Ett problem har korrigerats där slutpunkten [Sammanfogningsleads](https://developer.adobe.com/marketo-apis/api/mapi/#operation/mergeLeadsUsingPOST) returnerade felmeddelandet 611, systemfel för ogiltig sammanfogningsåtgärd. Uppstod när sammanfogningen resulterade i en dubblett av lead och **mergeinCRM** angavs till true. Ändrad till returfelet&quot;712, Du skapar en dubblettpost. Vi rekommenderar att du använder en befintlig post istället.&quot; [LM-137463]

Publicerad _2020-08-01_ av _David_

## Gästpost - djupdykning: Anpassat objekt eller Anpassade aktiviteter jämfört med Anpassat fält

**Det här är en gästpost från Amit Jain, Marketo Champion 2020, MarTech IT Specialist** Som automatiseringsplattform för marknadsföring på företagsnivå, hanterar Marketo din användar-/kund-/kundinformation från flera källor och upprätthåller den i Marketo för bättre personalisering, segmentering och rapportering. Källorna kan vara allt från webbplatsformulären till listor som bygger på CRM-data till e-handelsdata. Marketo erbjuder standardobjekt, t.ex. Lead, Company, Opportunity och Activity. Dessa standardobjekt räcker ibland inte för att uppfylla de nya marknadsföringsbehoven hos utökade datauppsättningar som ska vara tillgängliga i Marketo.

Exempel: artiklar som lagts till i kundvagnen, studerande har ansökt om olika kurser, produkter som ägs av en viss person samt samtycke till olika prenumerationer osv. Den här informationen kan vara viktig för marknadsförarna att hålla kunden/potentiella kunder engagerade genom att tillhandahålla mer personaliserat innehåll och en enhetlig användarupplevelse på olika plattformar. För att tillgodose dessa affärsbehov erbjuder Marketo anpassade sätt att lagra den här typen av data i anpassade fält, anpassade aktiviteter och anpassade objekt. Jag har observerat att folk har problem med att förstå när de ska använda vilka av alternativen. I det här blogginlägget kommer **vi att i detalj förstå vad dessa tre saker faktiskt är och när vi ska använda det ena med några exempel.**

**Anpassade fält**

Objektet &quot;Lead&quot; i Marketo är huvudobjektet och allt annat är kopplat till det här, direkt eller indirekt. Med Marketo kan du skapa anpassade fält på Lead-objektet, &quot;Company&quot;-objektet och nyligen meddelade de att anpassade fält för &quot;Program-medlemmar&quot; stöds. Dessa anpassade fält uppfyller ditt behov av att lagra vissa typer av data. Du kan till exempel behöva lagra jobbfunktionen eller andra samtycke från användaren. Det finns två typer av anpassade fält i Marketo:

1. **Synkroniserat från CRM-fält:** Som en del av den automatiska synkroniseringen av Marketo ↔︎ SFDC synkroniserar Marketo alla anpassade fält som är synliga för Marketo-användare i SFDC på lead-, kontakt-, konto- och säljprojektsobjekt.
1. **Endast Marketo-fält:** Du kan skapa ett fält direkt i Marketo. Data från dessa fält kommer inte att synkroniseras med SFDC.

**Snabbtips**

* Har du bara ett fält från Marketo och vill nu synkronisera data i SFDC? Kolla in [den här bloggen](https://themarketingautomationblog.com/2019/12/06/field-management-merging-remapping-hiding-marketo-fields/) och se hur du kan göra det.
* Läs mer om anpassade fält [här](https://themarketingautomationblog.com/2019/11/15/knowing-your-marketo-fields/).
* Marketo API:er stöder inte uppdatering/skapande av anpassade fält just nu.

**Egna objekt**

Förutom standardobjekt kan du med Marketo skapa egna anpassade objekt. _Du kan skapa anpassade objekt och relatera till antingen objektet Company eller Lead eller något annat anpassat objekt._ Ett anpassat objekt är en uppsättning anpassade poster som kompletterar vanliga lead- och företagsposter. Med anpassade objekt kan du lagra ytterligare data på ett skalbart sätt och länka dessa data till en lead- eller företagspost. Du kan skapa ett anpassat objekt med valfri kombination av standardfält (Länkfält) och anpassade fält, fylla i fälten för att skapa anpassade _poster_ och sedan länka dessa poster till en lead- eller företagspost. Kraftfulla och flexibla, länkade poster berikar era segment, smarta listor och kampanjer genom att ni kan skapa dessa resurser med information som inte finns i lead-fälten och företagsfälten. Ett anpassat objekt kan ha någon av följande typer av relationer:

* **En-till-en-relation**: där varje anpassat objekt har en enskild lead-/företagsobjektpost.
* **En-till-många-relation**: där varje anpassat objekt innehåller flera anpassade objektposter som är relaterade till en lead/ett företag.
* **Många-till-många-relation:** där flera anpassade objektposter kan länka till flera lead-/company-objekt. Flera elever kan till exempel delta i flera kurser från en kurskatalog.

**Snabbtips**

* Läs mer om hur du konfigurerar anpassade objekt [här](https://experienceleague.adobe.com/sv/docs/marketo/using/home).
* Du kan använda det anpassade Marketo-objektet som ett mellanliggande objekt och även använda anpassade objekt för anpassade objekt.

### Varför egna objekt?

Med anpassade objekt kan du kompilera och använda unika data som är relevanta för ett företag eller en lead, men som inte nödvändigtvis är statisk information om företaget eller själva ledningen. Medan lead-fält relaterar till en persons lead-information (_E-postadress_, _Postnummer_ och så vidare), företagsinformation (_Jobbtitel_, _Bransch_ och så vidare) eller systemstyrd information (_Marketo ID_, ett SFDC-id eller Skapa datum osv.), är anpassade objektfält helt anpassningsbara. Använd till exempel anpassade objekt för att lagra information som följande:

* En användares köphistorik.
* Kundinformation.
* Oavsett om en kund har använt en av dina tidsbegränsade kampanjrabattkoder eller inte.
* Kompletterande information från undersökningsresultat, intervjuer och närvaro vid olika evenemang.
* En användares testinformation, inklusive URL för testinstans, startdatum, slutdatum, antal användare osv.

### Anpassade objektbegränsningar för Marketo

1. Som standard kan du skapa 10 anpassade objekt i Marketo. Du kan öka gränsen med en extra abonnemangsavgift.
1. Standardantalet fält per objekt är 50, men du kan begära Marketo-stöd för ytterligare fält om det behövs. Tack [Michael Florin](https://www.linkedin.com/in/michaelflorin/) för ytterligare information här.
1. Det finns en gräns för hur många poster du kan ha i alla anpassade objekt. Det beror på din prenumeration på Marketo. Denna gräns kan höjas med en extra abonnemangsavgift.
1. Om ett anpassat objekt har skapats med API:t tillåter inte Marketo att CO-schemat redigeras från Marketo användargränssnitt.

**Snabbtips**

* Marketo API:s stöd för CRUD-åtgärder (Skapa, Läs, Uppdatera och Ta bort) för anpassade objekt.
* Du kan använda dessa anpassade objektdata för e-postpersonalisering med hjälp av Snabb skriptning.
* Du hittar alla anpassade objektrelaterade slutpunkter [här](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getExportProgramMembersStatusUsingGET).

### Terminologi för anpassade objekt

**Anpassat objekt**: En behållare som innehåller en gruppering av alla anpassade objektposter. Formeligen känt som en uppsättning datakort eller anpassad tabell. **Anpassad objektpost**: Datapost som innehåller ytterligare fältinformation som kan kopplas till ett lead eller företag. En post kan bestå av standardfält för lead eller företag samt anpassade objektpostfält. Formeligen känt som ett datakort eller en datatabellrad. **Postfält för anpassat objekt**: Helt anpassningsbara fält för att samla in unik eller tillfällig information. Dessa fält skapas och placeras inuti det anpassade objektet. Formeligen känt som ett fält eller en databastabell. **Länkfält**: Specialtyp för postfält för anpassade objekt som definierar relationen mellan en anpassad objektpost och en länkad lead-/företagsobjektpost. När du skapar anpassade objekt måste du tillhandahålla länkfält för att kunna koppla den anpassade objektposten till rätt överordnade post.

* Om du vill ha en anpassad struktur som är en-till-många eller en-till-en använder du länkfältet i det anpassade objektet för att ansluta det till en person eller ett företag.
* För många-till-många-strukturer använder du två länkfält som är anslutna från ett separat skapat mellanliggande objekt (som också är en typ av anpassat objekt). En länk ansluter till personer eller företag i din databas och den andra ansluter till det anpassade objektet. I det här fallet finns inte länkfältet i det anpassade objektet.

### Anpassade aktiviteter

**Det finns flera sätt för någon att interagera med vår organisation. De kan besöka företagets webbplats, delta i något av dina bildspel eller klicka på en länk i ett e-postmeddelande som du skickar. Dessa åtgärder är aktiviteter**, och oavsett vilka åtgärder de vidtar, fångar Marketo in dem så att era marknadsförings- och säljteam bättre kan förstå användarens beteende för personaliserat och enhetligt engagemang. **_Anpassade aktiviteter_** _kan hjälpa dig att spåra en aktivitet som inte är relaterad till ett Marketo-formulär, e-postmeddelande eller en landningssida_. Om du till exempel vill spåra när någon tittade på en video på en webbplats eller tog en undersökning, använder du en anpassad aktivitet. Anpassade aktiviteter skiljer sig från anpassade objekt. Använd anpassade objekt när värdet kan ändras (d.v.s.&quot;bilfärgen&quot; ändras från blå till röd). Använd anpassade aktiviteter när du spårar stunder som har inträffat och deras information inte kan ändras (dvs.&quot;köpt bil&quot;). Som standard är gränsen för maximalt antal anpassade aktiviteter som kan definieras 10. Detta kan höjas med en extra abonnemangsavgift. Enligt [Marketo datalagringspolicy](https://nation.marketo.com/t5/knowledgebase/tkb-p/support_solutions-documents) tas anpassade aktiviteter bort automatiskt efter 25 månader.

**Anpassad aktivitet:** Icke-Marketo-händelser som du vill spåra i Marketo. **Anpassat aktivitets-ID:** Marketo tilldelar ett numeriskt ID till den anpassade aktiviteten som kan användas när aktivitetsdata skickas/hämtas med Marketo API. **Anpassade aktivitetsfält:** Aktivitetsmetadata kan lagras i ett aktivitetsfält. Om du till exempel spårar vyer på video kan fälten vara sidans URL, videotitel osv. **Primärt fält för anpassad aktivitet:** Anpassade aktivitetsfält som kan användas som villkor för smart listfilering.

**Snabbtips**

* API-slutpunkterna för anpassade aktiviteter är tillgängliga [här.](https://developer.adobe.com/marketo-apis/api/mapi/#operation/addCustomActivityUsingPOST)

## Anpassat objekt kontra anpassad aktivitet

**Eget objekt**

**Anpassad aktivitet**

1

Max 10 anpassade objekt som standard per instans.

Max 10 anpassade aktiviteter som standard.

2

Max 50 anpassade objektfält per anpassat objekt.

Varje anpassad aktivitetstyp kan ha upp till 20 sekundära attribut.

3

Maximalt antal anpassade objektposter på 1 MM. Maj är baserad på din prenumeration.

Efter 25 månader tas anpassade aktiviteter bort enligt Marketo datalagringspolicy.

4

Kan användas som filter och utlösare i smarta listor och smarta kampanjer.

Kan användas som filter och utlösare i smarta listor och smarta kampanjer.

5

Kan användas för att anpassa e-postinnehållet.

Kan inte användas för att anpassa e-postinnehållet.

6

Kan utföra en CRUD-åtgärd på en anpassad objektpost.

Endast Skapa och Läs är tillåtet i anpassade aktiviteter.

7

Använd anpassade objekt när värdet kan ändras (d.v.s.&quot;bilfärgen&quot; ändras från blå till röd).

Använd anpassade aktiviteter när du spårar stunder som har inträffat och deras information inte kan ändras (dvs.&quot;köpt bil&quot;).

8

Egna objekt talar om det för dig. dvs. nuvärde.

Anpassade aktiviteter talar om vilka händelser som har hänt tidigare.

Bokfört _2020-10-18_ av _Amit_

## Uppdateringar januari 2021

I januari 2021 släpper vi nya REST API:er och åtgärdar flera fel. Se hela listan med uppdateringar nedan.

* [Slutpunkten Skicka formulär](/help/rest-api/leads.md) har lagts till, vilket gör att du kan skicka in programmatiska formulär. Blanketter från tredje part kan nu integreras med Marketo-blanketter för att dra nytta av befintliga arbetsflöden för marknadsföring.
* [Slutpunkten Hämta startsida med fullständigt innehåll](/help/rest-api/landing-pages.md) har lagts till. Den returnerar den serialiserade HTML-versionen av en startsida. Låter dig återge helt personliga förhandsvisningar av landningssidor utan att behöva logga in på Marketo Engage. Detta kan effektivisera redigerings- och översättningsarbetsflödena i integrerade program.
* Nu kan du konfigurera antalet anpassade objekt som ska vara tillgängliga via Velocity-skriptet. Konfigurationsanvisningar finns [här](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/administration/email-setup/change-custom-object-retrieval-limits-in-velocity-scripting).

### Defekt lösningar

* Ett problem har korrigerats där slutpunkten [Ta bort användare](/help/rest-api/user-management.md) skulle göra det möjligt att ta bort en API-användare som används av en anpassad tjänst. Nu returneras felet&quot;611, Du kan inte ta bort en API-användare som används i API-tjänsten&quot;. [LM-141893]
* Ett problem har korrigerats där slutpunkten [Hämta användare](/help/rest-api/user-management.md) i vissa fall returnerade borttagna användare. [LM-141542]
* Ett problem där slutpunkten för [klonprogrammet](https://developer.adobe.com/marketo-apis/api/asset/#operation/cloneProgramUsingPOST) har korrigerats. Om du angav ett programnamn som överskrider 255 tecken returneras&quot;611, Det går inte att klona programfel&quot;. Nu returneras &quot;701, namnet får inte vara längre än 255 tecken&quot;. [LM-143436]
* Korrigerat problem med slutpunkten [Godkänn startsidans utkast](https://developer.adobe.com/marketo-apis/api/asset/#operation/approveLandingPageUsingPOST). När du har godkänt en landningssida där mobilversionen är aktiverad ser du i vissa fall innehåll från mobilversionen i datorversionen. [LM-146867]
* Korrigerat problem med slutpunkten [Ogodkänd landningssida](https://developer.adobe.com/marketo-apis/api/asset/#operation/unapproveLandingPageByIdUsingPOST) som gjorde att du kunde avgodkänna en landningssida som användes som en uppföljningssida av ett eller flera formulär. Nu returneras felmeddelandet&quot;709, Unapproved landing page failed. Landningssidan används av ett eller flera formulär som en uppföljningssida med formulär-ID:[_formId1,formId2,.._]. [LM-143326]

Publicerad _2021-01-15_ av _David_

## Munchkin 160 Beta och Beacon API

**Den 27 januari 2021:** Vissa Marketo-användare som påverkas av borttagningen av Associate Lead fick ett e-postmeddelande som anger att inställningen Munchkin Beta är aktiverad på en eller flera instanser. Den här versionen hålls tills rätt målgrupp kan meddelas. Från och med version 160 av Munchkin JavaScript blir [Beacon API](https://developer.mozilla.org/en-US/docs/Web/API/Beacon_API) standardsättet som Munchkin kommunicerar med Marketo serverdel på. Detta blev tillgängligt som ett alternativ sommaren 2020 i och med att version 159 släpptes via konfigurationsparametern **useBeaconAPI** . Beacon API har flera fördelar jämfört med den gamla XMLHttpRequest-metoden, men den största förbättringen är att det är ett icke-blockerande asynkront API för HTTP-kommunikation som är tillgänglig för användning i alla moderna webbläsare. De flesta användare av Munchkin kommer inte att märka någon förändring av webbplatsbeteendet, men den här uppdateringen förhindrar Munchkin från att blockera navigeringen medan de väntar på att skicka en klickningshändelse till serverdelen, eller mer enkelt, det här, men eliminerar möjligheten att Munchkin orsakar att webbläsaren&quot;hänger&quot; efter att en länk till en ny sida har klickats. Detta har varit en ovanlig men frustrerande händelse för vissa Marketo-kunder.

Från och med den 27 januari 2021 är lanseringen av den här versionen spärrad i väntan på omplanering. Inga problem med den här förändringen förväntas, och inga har identifierats under testningen, men det är omöjligt för Marketo att testa alla möjliga distributionskonfigurationer för Munchkin, och du kanske vill testa dessa ändringar i förväg eller avstå från ändringen tills den här versionen är allmänt tillgänglig. Instruktioner för olika scenarier presenteras nedan.

### Test Beacon API

Om du vill testa det uppdaterade Beacon API:t i väntan på den kommande versionen kan du göra det genom att lägga till parametern **useBeaconAPI** i din Munchkin-konfiguration på en extern testsida. Testet fungerar antingen med den allmänt tillgängliga eller betaversionen av Munchkin. Konfigurationsparametern visas nedan i det andra argumentet för anropet av metoden `Munchkin.init()` på rad 7: `{ 'useBeaconAPI': true}`

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      Munchkin.init('299-BYM-827', {"useBeaconAPI":true});
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
  document.getElementsByTagName['head'](0).appendChild(s);
})();
</script>
```

### Inaktivera Munchkin Beta på Marketo landningssidor

Om du vill inaktivera Munchkin Beta på Marketo landningssidor behöver du komma åt menyn [Resursschema](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/administration/settings/enable-or-disable-treasure-chest-features) i administratörsdelen av prenumerationen och ändra inställningen för Munchkin Beta på landningssidor till inaktiverad.

### Inaktivera Munchkin Beta på externa sidor

Om du har distribuerat Beta-versionen av Munchkin JavaScript till externa webbsidor, och vill avstå från denna ändring tills den blir allmänt tillgänglig, måste du ändra ditt Munchkin JS-fragment för att rikta in dig på **munchkin.**&#x200B;**js**-filen i stället för **munchkin-beta.**&#x200B;**js** fil. I exemplet nedan är det här värdet på variabeln **s.src** på rad 11. Ditt kodutdrag liknar kanske inte exemplet, eller kan användas av en tagghanterare på externa sidor och du kan behöva kontakta din IT-avdelning eller någon som hanterar dina webbplatser med Munchkin tracking aktiverat.

```javascript
<script type="text/javascript">
(function() {
  var didInit = false;
  function initMunchkin() {
    if(didInit === false) {
      didInit = true;
      Munchkin.init('299-BYM-827');
    }
  }
  var s = document.createElement('script');
  s.type = 'text/javascript';
  s.async = true;
  s.src = '//munchkin.marketo.net/munchkin.js';//This line should have the munchkin.js file, not munchkin-beta.js
  s.onreadystatechange = function() {
    if (this.readyState == 'complete' || this.readyState == 'loaded') {
      initMunchkin();
    }
  };
  s.onload = initMunchkin;
  document.getElementsByTagName['head'](0).appendChild(s);
})();
</script>
```

Publicerad _2021-01-08_ av _Kenny_

## Slutlig API-borttagning av e-post V1

[Borttagningen av e-post V1 började för nästan två år sedan](https://nation.marketo.com:443/t5/knowledgebase/email-editor-1-0-is-being-deprecated-june-18th/ta-p/250666) och börjar med underhållsutgåvan från mars till London och Nederländerna den 17 mars 2021 och alla andra prenumerationer den 19 mars 2021 avslutas alla API-stöd för V1-e-postmeddelanden. Efter den här versionen kommer eventuella försök att interagera med V1-e-postmeddelanden via resurs-API:erna att resultera i fel och inga åtgärder vidtas. Alla kända återstående användare sedan 24 februari 2021 har underrättats, men det är möjligt att det fortfarande finns integreringar som kan försöka interagera med dessa resurser. De vanligaste typerna av berörda integreringar är tjänster som erbjuder hantering, översättning och lokalisering av digitala resurser. Om du upptäcker integreringsfel som ett resultat av den här ändringen kan [du fortfarande uppgradera problematiska resurser genom att redigera och godkänna dem](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/general/email-editor-2/transitioning-to-email-editor-2-0). När en e-postresurs har uppgraderats till V2 bör du kunna fortsätta använda den med integrerade tjänster.

Publicerad _2021-03-17_ av _Kenny_

## Uppdateringar maj 2021

I maj 2021 släpper vi nya REST API:er, förbättrar befintliga REST API:er och åtgärdar flera brister. Se hela listan med uppdateringar nedan.

* Lagt till API:er för programmedlemmar som gör att du kan hämta, uppdatera och ta bort poster för programmedlemskap. Mer information finns i [REST API > Leaddatabas > Programmedlemmar](/help/rest-api/program-members.md).
* Lagt till API:er för extrahering av anpassade objekt i grupp som gör att du kan exportera anpassade objektposter på första nivån i Marketo som är kopplade till leads i en 1:N-relation. Mer information finns i [REST API > Massextrahering > Extrahera anpassat objekt gruppvis](/help/rest-api/bulk-custom-object-extract.md).
* Vi har förbättrat både [Lead-API](/help/rest-api/leads.md) och [Bulk Lead Extract API](/help/rest-api/bulk-lead-extract.md) så att användare kan hämta Adobe Experience Cloud ID (ECID). Detta gör att användare som [Synkroniserar målgrupper från Adobe Experience Cloud](https://experienceleague.adobe.com/docs/marketo/using/product-docs/core-marketo-concepts/miscellaneous/set-up-adobe-experience-cloud-audience-sharing.html?lang=sv-SE) kan identifiera leads som har associerade ECID:n. Detta öppnar för [integreringsmöjligheter](https://adobeexchangeec.zendesk.com/hc/en-us/articles/360024277392-Adobe-Experience-Cloud-Using-the-ECID-for-integration) med andra Adobe Experience Cloud-produkter.
* Vi har förbättrat [API:t för massradsimport](/help/rest-api/bulk-lead-import.md) så att det går att lägga till leads till företagsposter under importprocessen. Detta görs genom att fältet **externalCompanyId** inkluderas i importfilen.
* Vi har förbättrat flera programslutpunkter så att vi kan erbjuda paritet med de funktioner som finns i Marketo Engage användargränssnitt. Vi har förbättrat slutpunkterna [Skapa program](/help/rest-api/assets.md) och [Klonprogram](https://developer.adobe.com/marketo-apis/api/asset/) så att du kan skapa, klona eller flytta åtgärder i händelseprogram. Detta är till för användare som organiserar händelseprogram genom att&quot;kapsla&quot; dem under andra programtyper. Vi har också förbättrat slutpunkten [Ta bort program](https://developer.adobe.com/marketo-apis/api/asset/) så att program som innehåller följande resurser kan tas bort: push-meddelanden, meddelanden i appen, rapporter, landningssidor med inbäddad social Assets.
* Som Marketo-administratör kan du [markera ett specifikt fält som&quot;känsligt&quot;](https://experienceleague.adobe.com/sv/docs/marketo/using/home) så att dess värden [aldrig fylls i i formulär](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/demand-generation/forms/form-fields/disable-pre-fill-for-a-form-field) i förväg, vilket skyddar användarens känsliga data. Vi har förbättrat flera slutpunkter för formulärfält så att de är paritetsfria med den här funktionen som finns i användargränssnittet i Marketo Engage.

### Defekt lösningar

* Korrigerat problem med borttagning av programslutpunkt. Om du försökte ta bort ett program i en delad mapp skulle det returnera &quot;611, systemfel&quot;. Nu returneras &quot;Målprogrammet finns i en delad mapp och kan inte tas bort. Mappen måste inte delas ut innan du försöker ta bort den.&quot;
* Korrigerat problem med slutpunkten för klonprogrammet. Om du försökte klona ett program som innehöll ett DateTime-objekt i ett flödessteg returneras &quot;611, systemfel&quot;. Nu har programmet klonats.
* Korrigerat problem med slutpunkten Skapa program som av misstag gjorde att du kunde skapa ett program under ett e-postprogram (vilket inte är tillåtet).
* Korrigerat problem med slutpunkten för klonprogrammet. Om du klonade ett program som innehöll en landningssida saknade namnet på landningssidan i målprogrammet ett understreck mellan programnamnet och namnet på landningssidan. t.ex. `http://<_pod_\>.marketo.com/lp/<_munchkin_\>/<_program name_\>**_**<_LP name_\>.html`

Publicerad _2021-05-07_ av _David_

## Tidsbegränsat erbjudande om att gå med i Adobe Exchange

Stöd från Marketo Engage partners är en av hörnstenarna i våra kunders framgång. Vi vill vara säkra på att Marketo Engage Integration-ekosystemet är väl representerat på Exchange Marketplace och har ett specialerbjudande för LaunchPoint-partners. Under en mycket begränsad period som inte kommer att förlängas erbjuder vi våra LaunchPoint-partners ett kostnadsfritt innovativt partnerskap i Exchange-programmet fram till slutet av 2022 (värde ca 15 kB USD). Vi har utformat det här erbjudandet för att uppmuntra LaunchPoint-partners att skapa sina integreringslistor i Exchange Partner Portal, som sedan blir sökbara offentligt på Adobe Exchange Marketplace. En fullständig lista över de innovativa partnerfördelarna som du får utan kostnad fram till december 2022.

1. Gå till [Adobe Exchange Partner Support Center](https://adobeexchangeec.zendesk.com/hc/en-us?mkt_tok=NjA4LURIVi05MTUAAAF-P5lIeVWOuBmKMS_uE_NpgFKtC0ukt7z_ksnq_Sbzb6mzXUuXpqpqQeujtPdZ24WcjMdptygQSR9XrYt_Cw)
1. Klicka på&quot;Skicka en begäran&quot; i det övre högra hörnet
1. I listrutan **Välj ditt problem nedan** och välj sedan Adobe Exchange Support
1. Ange din e-postadress i **din e-postadress**
1. Ange&quot;LaunchPoint Offer&quot; i rutan **Ämne**
1. Ange&quot;LaunchPoint Offer&quot; i rutan **Beskrivning**
1. I listrutan **Supporttyp** väljer du &quot;Program Support&quot;
1. I listrutan **Adobe Exchange Product** väljer du &quot;Adobe Exchange Program&quot;
1. Skicka in formuläret. Vårt team har kontakt med dig inom kort!

Publicerad _2021-07-22_ av _David_

## Uppdateringar från augusti 2021

I augusti 2021 förbättrar vi befintliga REST-API:er och åtgärdar flera brister. Se hela listan med uppdateringar nedan.

* Vi har förbättrat API:t för extrahering av gruppaktivitet så att användare kan filtrera med primära attribut för sex olika aktivitetstyper. Mer information finns i [Extrahera gruppaktivitet](/help/rest-api/bulk-activity-extract.md).
* För att ge Marketo Sales Connect-användare bättre tillgång till säljaktivitetsdata aktiverade vi ytterligare attribut för försäljningsaktivitet. Vi lade till följande attribut för Skicka e-post, Öppna e-post och Klicka på E-postaktiviteter för försäljning:

* Marketo Säljpersons-ID - Unikt ID för personpost i Sales Connect
* Namn på försäljningskampanj - namn på försäljningskampanj, om e-post ingår i en försäljningskampanj
* Försäljningskampanj-URL - Försäljningskampanj-URL för försäljningskampanj om e-postadressen ingår i en försäljningskampanj
* Namn på försäljningsmall - Namn på e-postmall i Sales Connect, om en mall användes
* URL för försäljningsmall - URL för försäljningskontakt för e-postmall, om en mall användes

### E-post

* Vi har förbättrat slutpunkten Hämta e-post genom att lägga till filtret `earliestUpdatedAt`/`latestUpdatedAt`. Detta gör att du kan använda fältet `updatedAt` för att bara söka efter en delmängd av e-postmeddelanden, vilket tillåter inkrementell synkronisering.
* Vi har förbättrat slutpunkterna Hämta e-post, Hämta e-post efter namn, Hämta e-post via ID för att stödja hämtning av e-postposter av typen [Champion och Challenger](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/email-tests-champion-challenger/add-an-email-champion-challenger).

### Defekt lösningar

* Ett problem med slutpunkten Hämta användare har korrigerats. Användare som har fått en licens för [marknadsföringskalender](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/core-marketo-concepts/marketing-calendar/understanding-the-calendar/issue-revoke-a-marketing-calendar-license) returnerades inte. Användare av marknadsföringskalendern returneras nu korrekt.
* Ett problem med slutpunkten för Skicka formulär har korrigerats. Om det finns dubblerade lead-poster används Skicka formulär för att utfärda felet&quot;1007, Multiple lead match lookup&quot;. Skicka formulär uppdaterar nu den senast uppdaterade posten på samma sätt som API:t för [Forms 2.0](/help/javascript-api/forms-api-reference.md).
* Förbättrade flera felmeddelanden som returnerats av Update Lead Field och Create Lead Fields endpoints. [LM-151890, LM-151888, LM-151889]
* Ett problem med slutpunkterna Hämta lead-fält efter namn och Hämta lead-fält har korrigerats. Båda slutpunkterna kan potentiellt returnera något inaktuell information. De returnerar nu alltid aktuell information.
* Korrigerat problem med [API för massextrahering](/help/rest-api/bulk-extract.md) när HTTP-huvudet för intervallet användes för partiell hämtning där den sista byten i intervallet inte returnerades.
* Korrigerat problem med metadataslutpunkten för uppdateringsfragment. Vid uppdatering av fragmentets namn eller beskrivning ändrades fragmentets status till&quot;godkänt med utkast&quot;. Nu förblir utdragsstatusen oförändrad när du har uppdaterat fragmentnamnet eller beskrivningen.
* Ett problem med Lägg till e-postmodulens slutpunkt har korrigerats. När du lade till en modul som innehöll ett fragment, returnerades &quot;611, Systemfel&quot;. Modulen läggs nu till korrekt i e-postmeddelandet.
* Korrigerat problem med borttagning av programslutpunkt. När ett program som innehöll en lokal resurs för meddelanden i appen togs bort returnerade det &quot;611, systemfel&quot;. Programmet tas nu bort korrekt.

Publicerad _2021-08-22_ av _David_

## Munchkin version 161 - utrullning

Den 7 september 2021 börjar version 161 av Munchkin att lansera till 10 % av prenumerationerna med Munchkin Beta aktiverat, följt av 50 % den 16 september och 100 % den 30 september. Ändringen påverkar Marketo landningssidor och den version av filen munchkin-beta.js som används för externa landningssidor som läses in från prenumerationer som den nya versionen har lanserats till. I den här versionen har Lead-metoden för Munchkin Associate tagits bort, vilket är en funktion som tillåter överföring av persondata till en Marketo-prenumeration och associerade webbläsarhistorik med en känd personpost. Associate Lead tas bort till förmån för mer moderna och säkra alternativ, som [Forms JS API](/help/javascript-api/forms-api-reference.md), API:t för formulärsändning och [Associate Lead REST API](/help/rest-api/leads.md). Om du eller din organisation använder den här metoden bör du migrera från användning senast den 12 oktober 2021 när lanseringen av oktober är planerad att börja. Om du inte längre vill delta i betaversionen av Munchkin kan du inaktivera användningen på Marketo landningssidor genom att växla funktionen Munchkin Beta på landningssidor till `disabled` på menyn [Treasure Chest](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/administration/settings/enable-or-disable-treasure-chest-features). Om du har distribuerat Munchkin Beta JavaScript till externa webbsidor och vill växla till standardkanalen för Munchkin måste du uppdatera kodfragmentet för att läsa in Munchkin JavaScript från munchkin.js i stället för munchkin-beta.js.

Publicerad _2021-08-24_ av _Kenny_

## TLS 1.0 och TLS 1.1 Slutet av livscykeln för Munchkin

Från och med den 21 oktober 2021 kommer munchkin.marketo.net, som används för att betjäna Munchkin JavaScript för besökare, inte längre att acceptera anslutningar med [TLS 1.0 eller TLS 1.1.](https://en.wikipedia.org/wiki/Transport_Layer_Security) Dessa krypteringsstandarder accepteras inte längre som en del av bästa praxis för webbsäkerhet och stöds inte längre av moderna webbläsarversioner. Denna förändring förväntas inte få några negativa konsekvenser.

Publicerad _2021-10-04_ av _Kenny_

## Uppdateringar från oktober 2021

I oktober 2021 förbättrar vi befintliga REST-API:er och åtgärdar flera brister. Se hela listan med uppdateringar nedan.

* Vi har förbättrat slutpunkten [Skicka formulär](https://developer.adobe.com/marketo-apis/api/mapi/#operation/SubmitFormUsingPOST) så att den stöder anpassade fält som är programmedlemmar som en del av formuläröverföringen. Ett program kan också anges som det program som formuläret ska läggas till i och/eller det program som anpassade fält för programmedlemmar ska läggas till i enligt beskrivningen [här](/help/rest-api/leads.md).
Vi har förbättrat slutpunkten [Get Program Members](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getProgramMembersUsingGET) så att den stöder datumintervallsbaserade frågor baserade på attributet updatedAt. Detta görs genom att skicka start- och slutdatetime-parametrar enligt beskrivningen [här](/help/rest-api/program-members.md).
* Vi har förbättrat API:erna för [Leadfält](/help/rest-api/leads.md) så att de stöder [känsliga fält](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/administration/field-management/mark-a-field-as-sensitive). [Hämta lead-fält efter namn](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadFieldByNameUsingGET), [Hämta lead-fält](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getLeadFieldsUsingGET), [Skapa lead-fält](https://developer.adobe.com/marketo-apis/api/mapi/#operation/createLeadFieldUsingPOST) och [Uppdatera lead-fält](https://developer.adobe.com/marketo-apis/api/mapi/#operation/updateLeadFieldUsingPOST) har nu stöd för attributet isSensitive.

### Defekt lösningar

* Ett problem med [API:t för användarhantering](/help/rest-api/user-management.md) har korrigerats. Bevarar Marketo-användare som är konfigurerade för användning med [Sales Insight](https://business.adobe.com/se/products/marketo/sales-insight.html). De här användarna returneras nu av slutpunkten [Hämta användare](https://developer.adobe.com/marketo-apis/api/user/#operation/getUsersUsingGET), och de här användarna kan nu tas bort med slutpunkten [Ta bort användare](https://developer.adobe.com/marketo-apis/api/user/#operation/deleteUserUsingPOST). [LM-155864]
* Ett problem med slutpunkten Lägg till [RTF-fält](https://developer.adobe.com/marketo-apis/api/asset/#tag/Form-Fields/addRichTextFieldUsingPOST) har korrigerats. När du lägger till ett RTF-fält som är längre än 65 kB i ett e-postmeddelande, en landningssida, ett utdrag eller ett formulär returnerade det&quot;611, systemfel&quot;. Det returnerar nu felet &quot;701, Operation kan inte slutföras. &#39;content&#39; överskrider en maxlängd på 65 535 byte&quot;.

Publicerad _2021-10-25_ av _David_

## Uppdateringar januari 2022

I januari 2022 förbättrar vi befintliga REST-API:er och åtgärdar flera brister. Se hela listan med uppdateringar nedan.

* Vi har förbättrat API:t [Bulk Custom Object Extract](/help/rest-api/bulk-custom-object-extract.md) så att användare kan filtrera med ett **updatedAt**-datumintervall.
* Lagt till API:er för programmedlemsfält som gör att du kan skapa, uppdatera och hämta metadata för programmedlemsfält. Mer information finns i [Programmedlemmar > Fält](/help/rest-api/program-members.md).
* Metadata-API:er för företagsfält har lagts till så att du kan hämta metadata för företagsfält. Mer information finns i [Företag > Fält](/help/rest-api/companies.md).
* Metadata-API:er för säljprojektsfält har lagts till så att du kan hämta metadata för säljprojektsfält. Mer information finns i [Affärsmöjligheter > Fält](/help/rest-api/opportunities.md).
* Metadata för fält med namngivet konto har lagts till så att du kan hämta metadata för fält med namngivet konto. Mer information finns i [Namngivna konton > Fält](/help/rest-api/named-accounts.md).
* Uppdaterade slutpunkter för fältmetadata för att returnera den nya booleska egenskapen **isApiCreated** för att ange om ett fält skapades av REST API.

### Defekt lösningar

* Ett problem med fördröjning mellan tidpunkten för anropet till [Skapa lead-fält](https://developer.adobe.com/marketo-apis/api/mapi/#operation/createLeadFieldUsingPOST)-slutpunkten och tiden när nytt lead-fält var tillgängligt i den smarta listan har åtgärdats. [LM-152838]
* Korrigerat problem med slutpunkten [Skapa lead-fält](https://developer.adobe.com/marketo-apis/api/mapi/#operation/createLeadFieldUsingPOST) där skapade fält inte var tillgängliga i formulärfältslistrutan som användes för att [lägga till fält i formulär](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/demand-generation/forms/creating-a-form/add-a-field-to-a-form) i Marketo Engage-användargränssnittet. [LM-158243]
* Korrigerat problem med slutpunkten [Get Campaigns](https://developer.adobe.com/marketo-apis/api/mapi/#operation/getCampaignsUsingGET) där utlösande kampanjer inte returnerades när parametern isTriggerable=true angavs. [LM-158283]
* Ett problem har korrigerats där [Get Leads by List Id](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteTokenByNameUsingPOST)-slutpunkten returnerade felet 611, systemfel i vissa fall. [LM-157214]
* Rensade flera felmeddelanden som returnerades av [Update Lead Field](/help/rest-api/leads.md) -slutpunkten. [LM-151886, LM-151888, LM-151889]

Publicerad _2022-01-27_ av _David_

## Uppdateringar mars 2022

I mars 2022 förbättrar vi befintliga REST-API:er och åtgärdar flera brister. Se hela listan med uppdateringar nedan.

* Vi har lagt till fältet **actionResult** i exportfilen som skapas av API:t för extrahering av gruppaktivitet. Det här fältet kan användas för att skilja mellan lyckade, hoppade över och misslyckade aktiviteter.
* Vi har lagt till fältet **isOpenTrackingDisabled** i svar från [e-post-API:t](/help/rest-api/emails.md). Det här fältet kan användas för att avgöra om funktionen [Inaktivera Open Tracking](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/general/email-editor-2/email-editor-v2-0-overview) är aktiverad.
* Vi har lagt till två slutpunkter som gör att du kan hantera programtaggar selektivt. Med slutpunkten [Uppdatera programtaggar](/help/rest-api/programs.md) kan du selektivt uppdatera en programtagg. Med slutpunkten [Ta bort programtaggar](/help/rest-api/programs.md) kan du ta bort en programtagg selektivt.
* Vi har lagt till parametern **isExecutable** i slutpunkten för [Klona smart kampanj](/help/rest-api/smart-campaigns.md). Med den här parametern kan du klona ett program som ett körbart program.
* Vi har lagt till fältet **headStart** i [Program-API](/help/rest-api/programs.md). Detta gör att du kan skapa, uppdatera och hämta inställningen [Head Start](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/email-programs/email-program-actions/head-start-for-email-programs) för e-postprogram.

### Defekt lösningar

* Korrigerat problem med slutpunkten [Hämta dynamiskt e-postinnehåll](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailDynamicContentUsingGET). När du försöker hämta ämnesrader med dynamiskt innehåll från e-postmeddelanden som hade brutna mallrelationer returnerades ett fel, 709, &quot;&quot;API tillåter bara åtgärder för e-postmeddelanden med en mall&quot;. Slutpunkten returnerar nu det dynamiska innehållet. [LM-152331]
* Korrigerat problem med slutpunkten [Synkronisera leads](https://developer.adobe.com/marketo-apis/api/mapi/#operation/syncLeadUsingPOST). När externalSalesPersonId används för att associera säljare med ett lead med hjälp av externalSalesPersonId och åtgärd = createDuplicate används inte associationen för säljare. [LM-158990]

### Integrering med Adobe IMS

* De som har anslutit sig till [Adobe IMS](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/administration/marketo-with-adobe-identity/adobe-identity-management-overview) kan inte utnyttja alla [Marketo API:er för användarhantering](/help/rest-api/user-management.md). Följande slutpunkter returnerar ett fel vid anrop till Marketo-instanser som har integrerats med Adobe IMS: [Bjud in användare](https://developer.adobe.com/marketo-apis/api/user/#operation/inviteUserUsingPOST), [Hämta inbjudna användare via ID](https://developer.adobe.com/marketo-apis/api/user/#operation/getInvitedUserUsingGET), [Uppdatera användarattribut](https://developer.adobe.com/marketo-apis/api/user/#operation/updateUserAttributeUsingPOST), [Ta bort användare](https://developer.adobe.com/marketo-apis/api/user/#operation/deleteUserUsingPOST) och [Ta bort inbjudna användare](https://developer.adobe.com/marketo-apis/api/user/#operation/deleteInvitedUserUsingPOST). Som ersättning bör [Adobe API:er för användarhantering](https://developer.adobe.com/umapi/) användas.

Publicerad _2022-03-14_ av _David_

## Maj 2022 - uppdateringar

I maj 2022 förbättrar vi befintliga REST-API:er och åtgärdar flera brister. Se hela listan med uppdateringar nedan.

* Vi har lagt till möjligheten att hämta poster för [Företag](/help/rest-api/companies.md), [säljprojekt](/help/rest-api/opportunities.md) och [säljare](/help/rest-api/sales-persons.md) när antingen [SFDC Sync](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/crm-sync/salesforce-sync/sfdc-sync-details/sfdc-sync-field-sync) eller [Microsoft Dynamics Sync](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/crm-sync/microsoft-dynamics/microsoft-dynamics-sync-details/microsoft-dynamics-sync-user-sync) är aktiverade i din Marketo Engage-instans.
* Vi har uppdaterat slutpunkten [Hämta dynamiskt e-postinnehåll](https://developer.adobe.com/marketo-apis/api/asset/#operation/getEmailDynamicContentUsingGET) så att du kan hämta [dynamiskt innehåll](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/general/functions-in-the-editor/using-dynamic-content-in-an-email) från en e-postämnesrad. Detta fungerar oavsett om det angivna e-postmeddelandet är länkat till en e-postmall eller inte.

`POST /rest/asset/v1/form/{id}/field/State.json?values=[{"label":"Alaska"},{"value":"AK"},{"label":"West Virginia","value":"WV"},{"label":"Wyoming","value":"WY"}]`

* Vi har uppdaterat slutpunkten [Lägg till visningsregler för formulärfält](https://developer.adobe.com/marketo-apis/api/asset/#operation/getAllProgramMemberFieldsUsingGET) så att du kan lägga till flera jämförelsevärden för **isNot** type [Invisibility Rules](/help/rest-api/forms.md) . Här är ett exempel:

`POST /rest/asset/v1/form/{id}/field/LastName/visibility.json?visibilityRule={"ruleType":"show","rules":[{"subjectField":"LastName","operator":"isNot","values":["A","B","C"]}`

### Defekt lösningar

* Ett problem med slutpunkten [Submit Form](/help/rest-api/leads.md) som uppstod när null skickades för ett attribut i parametern [leadFormFields](/help/rest-api/leads.md) har åtgärdats. Ett fel returnerades, &quot;611, System Error&quot;. Det returnerar nu korrekt felmeddelandet&quot;1003, Formulärvalidering misslyckades&quot;. [LM-162213]

Publicerad _2022-05-09_ av _David_

## Uppdateringar augusti 2022

I augusti 2022 förbättrar vi befintliga REST API:er. Se hela listan med uppdateringar nedan.

Vi har lagt till flera nya filter som kan användas vid anrop av slutpunkten Skapa jobb för medlem i exportprogram. Observera att många av filtren kan användas tillsammans för att förfina den extraherade datauppsättningen.

* Filtret **programIds** kan användas för att ange upp till 10 programidentifierare, vilket kan förbättra genomströmningen.
* Filtret **isExvested** kan användas för att filtrera poster för [personer som har slut på innehåll](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/email-marketing/drip-nurturing/using-engagement-programs/people-who-have-exhausted-content).
* Filtret **SköldstCadence** kan användas för att filtrera poster baserat på [programstängsel för engagemang](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/program-flow-actions/change-engagement-program-cadence).
* Filtret **statusNames** kan användas för att filtrera poster för en eller flera [programstatusar](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/core-marketo-concepts/programs/creating-programs/understanding-program-membership).
* Filtret **updatedAt** kan användas för att filtrera poster baserat på ett datumintervall.

### Meddelanden

* Beteendet för [Identity](https://developer.adobe.com/marketo-apis/api/identity/#operation/identityUsingGET)-slutpunkten har ändrats. När du anropar slutpunkten och inte innehåller någon **access_token** -parameter, returneras felet &quot;603, Åtkomst nekad&quot;. Tidigare returnerades felet &quot;600, tom åtkomsttoken&quot;. Observera att felet &quot;600, tom åtkomsttoken&quot; har tagits bort.

Publicerad _2022-09-03_ av _David_

## Uppdateringar från oktober 2022

I oktober 2022 förbättrar vi befintliga REST API:er. Se hela listan med uppdateringar nedan.

* Vi har förbättrat [API:t för massradsimport](/help/rest-api/bulk-lead-import.md) så att det går att lägga till leads till säljpersonsposter under importprocessen. Detta gör du genom att ta med fältet **externalSalesPersonId** i importfilen.
* Korrigerat problem med slutpunkten [Skapa lead-fält](/help/rest-api/leads.md) som uppstod när poängtypsfält skapades. Dessa fält var inte tillgängliga för användning i flödesåtgärden [Ändra poäng](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/core-marketo-concepts/smart-campaigns/flow-actions/change-score) i Marketo Engage-gränssnittet. [LM-166815]

### Meddelanden

* Attributet `acquiredBy` för programmedlemskap har ändrats så att det kan uppdateras.

Publicerad _2022-10-18_ av _David_

## Kommande ändring av Marketo Forms REST AP

Från och med version 202.R2, som är schemalagd till veckan den 24 mars 2023, returnerar Adobe Marketo Engage Forms Asset-API:er konsekvent bara formulärets namn utan ett prefix-programnamn, oavsett om formuläret är underordnat ett program eller inte. Den här ändringen kommer att få Forms API:ts beteenden med avseende på resursnamn att överensstämma med resten av Adobe Marketo Engage-API:erna. För att undvika avbrott i tjänsten bör du granska alla integreringar som använder Marketo Engage Forms API:er och arbeta med dina integratörer för att se om det behövs några ändringar för att tillgodose detta. Före den här ändringen har namn som returneras av Forms API:er felaktigt prefixerat ett programnamn för formulär som är underordnade program i marknadsföringsaktiviteter. Ett formulär med namnet&quot;Formulär 1&quot;, som var underordnat&quot;Program 1&quot;, kan ha sitt namn som returneras av API:t som: Program 1.Formulär 1 eller Formulär 1 Från och med version 2022.R2, så returneras alltid namnet på ett formulär utan ett fördefinierat programnamn. Med samma exempel blir namnet alltid: Formulär 1

Publicerad _2022-11-04_ av _Kenny_

## Uppdateringar januari 2023

I januari 2023 gjorde vi en API-relaterad förbättring av administratörsgränssnittet och håller på att göra två meddelanden. Se hela listan med uppdateringar nedan.

Administratörsgränssnitt

### Bulkladsextrahering

* Vi har förbättrat användargränssnittet för Marketo Engage Admin så att du kan visa den dagliga kapacitetstilldelningen för din prenumeration för API:t för massutdrag. Dessutom kan du visa kapacitet som används av API-användare under de senaste 7 dagarna. Mer information finns [här](https://experienceleague.adobe.com/sv/docs/marketo/using/product-docs/administration/settings/bulk-export-api-information).

### Defekt lösningar

* Korrigerat problem med slutpunkten [Ta bort affärsmöjligheter](https://developer.adobe.com/marketo-apis/api/mapi/#operation/deleteOpportunitiesUsingPOST). I vissa fall genererades inte aktiviteten Ta bort från affärsmöjlighet. [LM-172208]

### Meddelanden

* Läs [den här artikeln](https://nation.marketo.com/t5/product-documents/upcoming-change-to-marketo-rest-api/ta-p/331698) om Marketo Community angående REST API och en ändring av HTTP-svarsmeddelandet [orsaksfras](https://www.rfc-editor.org/rfc/rfc7230#section-3.1.2).
* Attributet **statusReason** för programmedlemskap har ändrats till uppdateringsbart.

Publicerad _2023-01-21_ av _David_
