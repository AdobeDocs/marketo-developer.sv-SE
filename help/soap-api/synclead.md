---
title: syncLead
feature: SOAP
description: syncLead SOAP anrop
exl-id: e6cda794-a9d4-4153-a5f3-52e97a506807
source-git-commit: ebe8faf41dff0e0ba5f4323f5909cc3c9813fd10
workflow-type: tm+mt
source-wordcount: '518'
ht-degree: 0%

---

# syncLead

Den här funktionen infogar eller uppdaterar en enstaka lead-post. När du uppdaterar ett befintligt lead identifieras leadet med en av följande nycklar:

- MARKETO ID
- Sekundärt system-ID (implementerat som `foreignSysPersonId`)
- Marketo Cookie (skapad av Munchkin JS-skript)
- E-post

Om en matchning hittas utför anropet en uppdatering. Annars infogas och skapas ett lead. Anonyma leads kan uppdateras med Marketo cookie-ID och kommer att bli kända vid uppdateringen.

Förutom för e-post behandlas alla dessa identifierare som unika nycklar. Marketo-id:t har företräde framför alla andra nycklar. Om både `foreignSysPersonId` och Marketo ID finns i lead-posten har Marketo-ID företräde och `foreignSysPersonId` uppdateras för leadet. Om bara `foreignSysPersonId` anges används den som en unik identifierare. Om både `foreignSysPersonId` och e-postadressen finns men Marketo-id:t inte finns, prioriteras `foreignSysPersonId` och e-postadressen uppdateras för leadet.

Du kan också ange ett kontexthuvud för att namnge målarbetsytan.

När Marketo-arbetsytor är aktiverat och sidhuvudet används används följande regler:

- Om tilldelningsregler anges och en ny lead kvalificerar sig för någon av de konfigurerade reglerna, skapas nya leads i den partition som definieras av tilldelningsregeln. Annars skapas nya leads i den primära partitionen av den namngivna arbetsytan.
- Leads som matchas av Marketo Lead ID, ett externt system-ID eller en Marketo Cookie måste finnas i den primära partitionen för den namngivna arbetsytan, annars returneras ett fel
- Om ett befintligt lead matchas med e-post ignoreras den namngivna arbetsytan och leadet uppdateras i sin aktuella partition

När Marketo-arbetsytor är aktiverade och rubriken INTE används gäller följande regler:

- Om tilldelningsregler anges och en ny lead kvalificerar sig för någon av de konfigurerade reglerna, skapas nya leads i den partition som definieras av tilldelningsregeln. Annars skapas nya leads i den primära partitionen av arbetsytan Standard.
- Befintliga leads uppdateras i den aktuella partitionen

Om Marketo-arbetsytor INTE är aktiverade MÅSTE målarbetsytan vara standardarbetsytan. Du behöver inte skicka sidhuvudet.

## Begäran

| Fältnamn | Obligatoriskt/valfritt | Beskrivning |
| --- | --- | --- |
| leadRecord->ID | Obligatoriskt - Endast när e-post eller `foreignSysPersonId` inte finns | Marketo-ID för lead-posten |
| leadRecord->Email | Obligatoriskt - Endast när ID eller `foreignSysPersonId` inte finns | E-postadressen som är associerad med lead-posten |
| leadRecord->`foreignSysPersonId` | Obligatoriskt - Endast när ID eller E-post saknas | ID för det främmande system som är associerat med lead-posten |
| leadRecord->foreignSysType | Valfritt - Endast obligatoriskt när `foreignSysPersonId` finns | Typen av externt system. Möjliga värden: ANPASSAD, SFDC, NETSUITE |
| leadRecord->leadAttributeList->attribute->attrName | Obligatoriskt | Namnet på lead-attributet som du vill uppdatera värdet för. |
| leadRecord->leadAttributeList->attribute->attrValue | Obligatoriskt | Värdet som du vill ange som lead-attribut i attrName. |
| returnLead | Obligatoriskt | När värdet är true returneras den fullständiga uppdaterade lead-posten vid uppdateringen. |
| marketoCookie | Valfritt | [Munchkin javascript](../javascript-api/lead-tracking.md)-cookien |

## Begär XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
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
        <Email>t@t.com</Email>
        <leadAttributeList>
          <attribute>
            <attrName>FirstName</attrName>
            <attrValue>George</attrValue>
          </attribute>
          <attribute>
            <attrName>LastName</attrName>
            <attrValue>of the Jungle</attrValue>
          </attribute>
        </leadAttributeList>
      </leadRecord>
      <returnLead>false</returnLead>
    </ns1:paramsSyncLead>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## XML för svar

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successSyncLead>
      <result>
        <leadId>1089965</leadId>
        <syncStatus>
          <leadId>1089965</leadId>
          <status>UPDATED</status>
          <error xsi:nil="true" />
        </syncStatus>
        <leadRecord xsi:nil="true" />
      </result>
    </ns1:successSyncLead>
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
  $options = array("connection_timeout" =20, "location" =$marketoSoapEndPoint);
  if ($debug) {
    $options["trace"] = true;
  }
 
  // Create Request
  $leadKey = new stdClass();
  $leadKey->Email = "george@jungle.com";
 
  // Lead attributes to update
  $attr1 = new stdClass();
  $attr1->attrName  = "FirstName";
  $attr1->attrValue = "George";
 
  $attr2= new stdClass();
  $attr2->attrName  = "LastName";
  $attr2->attrValue = "of the Jungle";
 
  $attrArray = array($attr1, $attr2);
  $attrList = new stdClass();
  $attrList->attribute = $attrArray;
  $leadKey->leadAttributeList = $attrList;
 
  $leadRecord = new stdClass();
  $leadRecord->leadRecord = $leadKey;
  $leadRecord->returnLead = false;
  $params = array("paramsSyncLead" =$leadRecord);
 
  $soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
  try {
    $result = $soapClient->__soapCall('syncLead', $params, $options, $authHdr);
  }
  catch(Exception $ex) {
    var_dump($ex);
  }
 
  if ($debug) {
    print "RAW request:\n" .$soapClient->__getLastRequest() ."\n";
    print "RAW response:\n" .$soapClient->__getLastResponse() ."\n";
  }
  print_r($result);
 
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
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import org.apache.commons.codec.binary.Hex;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;
 
 
public class SyncLead {
 
    public static void main(String[] args) {
        System.out.println("Executing syncLead");
        try {
            URL marketoSoapEndPoint = new URL("https://100-AEK-913.mktoapi.com/soap/mktows/2_1" + "?WSDL");
            String marketoUserId = "demo17_1_809934544BFABAE58E5D27";
            String marketoSecretKey = "27272727aa";
             
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
            ParamsSyncLead request = new ParamsSyncLead();
            LeadRecord key = new LeadRecord();
             
            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Stringemail = objectFactory.createLeadRecordEmail("george@jungle.com");
            key.setEmail(email);
            request.setLeadRecord(key);
             
            Attribute attr1 = new Attribute();
            attr1.setAttrName("FirstName");
            attr1.setAttrValue("George2");
             
            Attribute attr2 = new Attribute();
            attr2.setAttrName("LastName");
            attr2.setAttrValue("of the Jungle");
             
            ArrayOfAttribute aoa = new ArrayOfAttribute();
            aoa.getAttributes().add(attr1);
            aoa.getAttributes().add(attr2);
             
            QName qname = new QName("http://www.marketo.com/mktows/", "leadAttributeList");
            JAXBElement<ArrayOfAttributeattrList = new JAXBElement(qname, ArrayOfAttribute.class, aoa);         
            key.setLeadAttributeList(attrList);
             
            MktowsContextHeader headerContext = new MktowsContextHeader();
            headerContext.setTargetWorkspace("default");
             
            SuccessSyncLead result = port.syncLead(request, header, headerContext);
 
            JAXBContext context = JAXBContext.newInstance(SuccessSyncLead.class);
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
    'ns1:AuthenticationHeader' ={ "mktowsUserId" =mktowsUserId, "requestSignature" =requestSignature, 
    "requestTimestamp"  =requestTimestamp 
    }
}

client = Savon.client(wsdl: 'http://app.marketo.com/soap/mktows/2_3?WSDL', soap_header: headers, endpoint: marketoSoapEndPoint, open_timeout: 90, read_timeout: 90, namespace_identifier: :ns1, env_namespace: 'SOAP-ENV')

#Create Request
request = {
    :lead_record ={
        :Email ="t@t.com",
          :lead_attribute_list ={
              :attribute ={
                :attr_name ="FirstName",
                :attr_value ="George" },
              :attribute! ={
                :attr_name ="LastName",
                :attr_value ="of the Jungle" }
        }
    },
    :return_lead ="false"
}

response = client.call(:sync_lead, message: request)

puts response
```
