---
title: scheduleCampaign
feature: SOAP, Smart Campaigns
description: scheduleCampaign SOAP-samtal
exl-id: a9ef2c16-34ef-4e0f-b765-e332335b0b81
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '252'
ht-degree: 0%

---

# scheduleCampaign

Den här funktionen ställer in schemat för en batch Smart Campaign så att den körs antingen direkt eller vid ett framtida datum. Det kräver en befintlig Smart Campaign för att slutföras. Detta kan användas med importToList för att överföra en lista med leads och sedan köra en batchkampanj mot den nyligen skapade listan.

## Programtoken som tillval

På samma sätt som funktionen requestCampaign kan du skicka en array med Mina token till detta API-anrop som åsidosätter befintliga token. När kampanjen har körts ignoreras token.

Om du använder den här valfria parametern med [importToList](importtolist.md) prioriteras token i följande ordning:

1. importToList per lead-tokens
1. scheduleCampaign per kampanjtoken
1. Mina token i programmet

## Begäran

| Fältnamn | Obligatoriskt/valfritt | Beskrivning |
| --- | --- | --- |
| programName | Obligatoriskt | Namnet på det innehållande programmet |
| campaignName | Obligatoriskt | Namnet på den smarta kampanjen |
| campaignRunAt | Valfritt | Den tid det tar att köra den schemalagda kampanjen (W3C WSDL-datumformat). |
| cloneToProgramName | Valfritt | När det här attributet finns klonas kampanjens överordnade program och den nyligen skapade kampanjen schemaläggs. Attributet anger det önskade namnet för det resulterande programmet. Obs! Endast 10 anrop per dag tillåts när det här fältet används. |
| programTokenList->attrib->name | Valfritt | Namnet på den token som du vill skicka ett nytt värde för. Använd det fullständiga tokenformatet på samma sätt som i Marketo-gränssnittet. Det vill säga {{my.message}} |
| programTokenList->attrib->value | Valfritt | Värdet för det associerade tokennamnet. |

## Begär XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809224544BFABAE58E5D27</mktowsUserId>
      <requestSignature>b578495dfdd03231455bb7671f02d2fe0a9edf79</requestSignature>
      <requestTimestamp>2013-08-03T21:39:34-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsScheduleCampaign>
      <programName>Trav-Demo-Program</programName>
      <campaignName>Batch Campaign Example</campaignName>
      <campaignRunAt>2013-08-03T21:39:34-07:00</campaignRunAt>
      <cloneToProgramName>TestProgramCloneFromSOAP</cloneToProgramName>
      <programTokenList>
        <attrib>
          <name>{{my.message}}</name>
          <value>Updated message</value>
        </attrib>
        <attrib>
          <name>{{my.other token}}</name>
          <value>Value for other token</value>
        </attrib>
      </programTokenList>
    </ns1:paramsScheduleCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## XML för svar

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successScheduleCampaign>
      <result>
        <success>true</success>
      </result>
    </ns1:successScheduleCampaign>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## Exempelkod - PHP

```php
 <?php

  $debug = true;

  $marketoSoapEndPoint     = "";  // CHANGE ME
  $marketoUserId           = "";  // CHANGE ME
  $marketoSecretKey        = "";  // CHANGE ME
  $marketoNameSpace        = "http://www.marketo.com/mktows/";

  // Create Signature
  $dtzObj = new DateTimeZone("America/Los_Angeles");
  $dtObj  = new DateTime('now', $dtzObj);
  $timeStamp = $dtObj->format(DATE_W3C);
  $encryptString = $timeStamp . $marketoUserId;
  $signature = hash_hmac('sha1', $encryptString, $marketoSecretKey);

  // Create SOAP Header
  $attrs = new stdClass();
  $attrs->mktowsUserId = $marketoUserId;
  $attrs->requestSignature = $signature;
  $attrs->requestTimestamp = $timeStamp;
  $authHdr = new SoapHeader($marketoNameSpace, 'AuthenticationHeader', $attrs);
  $options = array("connection_timeout" => 20, "location" => $marketoSoapEndPoint);
  if ($debug) {
    $options["trace"] = true;
  }

  // Create Request
  $params = new stdClass();
  $params->programName = "Trav-Demo-Program";
  $params->campaignName = "Batch Campaign Example";
  $dtObj = new DateTime('now', $dtzObj);
  $params->campaignRunAt = $dtObj->format(DATE_W3C);
  $params->cloneToProgramName = "TestProgramCloneFromSOAP";

  $token = new stdClass();
  $token->name = "{{my.message}}";
  $token->value = "Updated message";

  $params->programTokenList = array("attrib" => $token);
  $params = array("paramsScheduleCampaign" => $params);

  $soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
  try {
    $response = $soapClient->__soapCall('scheduleCampaign', $params, $options, $authHdr);
  }
  catch(Exception $ex) {
    var_dump($ex);
  }
  if ($debug) {
    print "RAW request:\n" .$soapClient->__getLastRequest() ."\n";
    print "RAW response:\n" .$soapClient->__getLastResponse() ."\n";
  }
  print_r($response);

?>
```

## Exempelkod - Java

```java
import com.marketo.mktows.*;
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

public class ScheduleCampaign {


    public static void main(String[] args) {
        System.out.println("Executing Schedule Campaign");
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
            ParamsScheduleCampaign request = new ParamsScheduleCampaign();

            request.setProgramName("Trav-Demo-Program");
            request.setCampaignName("Batch Campaign Example");

            // Create setCampaignRunAt timestamp
            GregorianCalendar gc = new GregorianCalendar();
            gc.setTimeInMillis(new Date().getTime());

            DatatypeFactory factory = DatatypeFactory.newInstance();
            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<XMLGregorianCalendar> setCampaignRunAtValue = objectFactory.createParamsScheduleCampaignCampaignRunAt(factory.newXMLGregorianCalendar(gc));
            request.setCampaignRunAt(setCampaignRunAtValue);

            request.setCloneToProgramName("TestProgramCloneFromSOAP");

            ArrayOfAttrib aoa = new ArrayOfAttrib();

            Attrib attrib = new Attrib();
            attrib.setName("{{my.message}}");
            attrib.setValue("Updated message");

            aoa.getAttribs().add(attrib);

            JAXBElement<ArrayOfAttrib> arrayOfAttrib = objectFactory.createParamsScheduleCampaignProgramTokenList(aoa);
            request.setProgramTokenList(arrayOfAttrib);

            SuccessScheduleCampaign result = port.scheduleCampaign(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessScheduleCampaign.class);
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

## Exempelkod - Ruby

```ruby
require 'savon' # Use version 2.0 Savon gem
require 'date'

mktowsUserId = "" # CHANGE ME
marketoSecretKey = "" # CHANGE ME
marketoSoapEndPoint = "" # CHANGE ME
marketoNameSpace = "http://www.marketo.com/mktows/"

#Create Signature
Timestamp = DateTime.now
requestTimestamp = Timestamp.to_s
encryptString = requestTimestamp + mktowsUserId
digest = OpenSSL::Digest.new('sha1')
hashedsignature = OpenSSL::HMAC.hexdigest(digest, marketoSecretKey, encryptString)
requestSignature = hashedsignature.to_s

#Create SOAP Header
headers = {
    'ns1:AuthenticationHeader' => { "mktowsUserId" => mktowsUserId, "requestSignature" => requestSignature,
    "requestTimestamp"  => requestTimestamp
    }
}

client = Savon.client(wsdl: 'http://app.marketo.com/soap/mktows/2_3?WSDL', soap_header: headers, endpoint: marketoSoapEndPoint, open_timeout: 90, read_timeout: 90, namespace_identifier: :ns1, env_namespace: 'SOAP-ENV')

#Create Request
request =  {
    :program_name => "Trav-Demo-Program",
    :campaign_name => "Batch Campaign Example",
    :campaign_run_at => requestTimestamp,
    :clone_to_program_name => "TestProgramCloneFromSOAP",
    :program_token_list => {
        :attrib => {
            :name => "{{my.message}}",
            :value => "Updated message" },
        :attrib! => {
            :name => "{{my.other token}}",
            :value => "Value for other token" }
    }
}

response = client.call(:schedule_campaign, message: request)

puts response
```
