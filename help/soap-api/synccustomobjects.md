---
title: syncCustomObjects
feature: SOAP
description: Använd Marketo syncCustomObjects SOAP för att infoga, uppdatera eller uppdatera upp till 100 anpassade objekt per anrop med resultat per post och exempel på XML och PHP.
exl-id: dbdd7ee6-f83f-4e20-b847-25a61f0f6046
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '248'
ht-degree: 0%

---

# syncCustomObjects

Accepterar en array med anpassade objekt som ska skapas eller uppdateras, upp till högst 100 per anrop. Den returnerar åtgärdens resultat (status) (SKAPAD, UPPDATERAD, MISSLYCKAD, UNCHANGED, SKIPPED) och de anpassade objekt som bearbetades. API:t anropas i ett av tre åtgärdslägen:

1. INSERT - endast infoga nya objekt, hoppa över befintliga objekt
1. UPPDATERA - uppdatera endast befintliga objekt, hoppa över nya objekt
1. UPSERT - infoga nya objekt och uppdatera befintliga objekt

I ett enda API-anrop kan vissa uppdateringar lyckas och vissa kan misslyckas. Ett felmeddelande returneras för varje fel.

För anpassade objekt som har etablerats med det nya användargränssnittet för anpassade objekt kan bara fält som har angetts som `dedupe` fält i användargränssnittet skickas som attribut i `CustomObjKeyList`. Länkfält som inte är `dedupe` fält måste skickas som ett attribut i `customObjAttributeList`.

## Begäran

| Fältnamn | Obligatoriskt/valfritt | Beskrivning |
| --- | --- | --- |
| operation | Obligatoriskt | &quot;INSERT&quot;, &quot;UPDATE&quot; eller &quot;UPSERT&quot; |
| objectTypeName | Obligatoriskt | Namnet på det anpassade objektet |
| customObjectList->customObject->customObjKeyList->attribute | Obligatoriskt | Attributet är ett nyckel/värde-par som används för att identifiera det anpassade objektet. Du kan ha flera attribut i customObjKeyList |
| customObjectList->customObject->customObjAttributeList->attribute | Obligatoriskt | Nyckel-/värdepar, där namnet är fältnamnet och värdet är det värde som du vill infoga i customObject |

## Begär XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809934544BFABAE58E5D27</mktowsUserId>
      <requestSignature>ca67d597ced5bfce7f638e96204d6e1f38d3ffe7</requestSignature>
      <requestTimestamp>2013-08-20T10:26:07-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsSyncCustomObjects>
      <objTypeName>RoadShow</objTypeName>
      <customObjList>
        <customObj>
          <customObjKeyList>
            <attribute>
              <attrName>MKTOID</attrName>
              <attrValue>1090177</attrValue>
            </attribute>
            <attribute>
              <attrName>rid</attrName>
              <attrValue>rid1</attrValue>
            </attribute>
          </customObjKeyList>
          <customObjAttributeList>
            <attribute>
              <attrName>city</attrName>
              <attrValue>SanMateo</attrValue>
            </attribute>
            <attribute>
              <attrName>zip</attrName>
              <attrValue>94404</attrValue>
            </attribute>
            <attribute>
              <attrName>state</attrName>
              <attrValue>California</attrValue>
            </attribute>
          </customObjAttributeList>
        </customObj>
      </customObjList>
      <operation>UPSERT</operation>
    </ns1:paramsSyncCustomObjects>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## XML för svar

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successSyncCustomObjects>
      <result>
        <syncCustomObjStatusList>
          <syncCustomObjStatus>
            <objTypeName>Roadshow</objTypeName>
            <customObjKeyList>
              <attribute>
                <attrName>MKTOID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1090177</attrValue>
              </attribute>
              <attribute>
                <attrName>rid</attrName>
                <attrType xsi:nil="true" />
                <attrValue>rid1</attrValue>
              </attribute>
            </customObjKeyList>
            <status>UPDATED</status>
            <error xsi:nil="true" />
          </syncCustomObjStatus>
        </syncCustomObjStatusList>
      </result>
    </ns1:successSyncCustomObjects>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## Exempelkod - PHP

```php
<?php
$debug = true;
$marketoSoapEndPoint    = "";  // CHANGE ME
$marketoUserId      = "";  // CHANGE ME
$marketoSecretKey   = ""; // CHANGE ME
$marketoNameSpace   = "http://www.marketo.com/mktows/";

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
$options = array("connection_timeout" => 15, "location" => $marketoSoapEndPoint);
if ($debug) {
  $options["trace"] = 1;
}

// Create Request
$keyAttrib1 = new stdClass();
$keyAttrib1->attrName = 'MKTOID';
$keyAttrib1->attrValue = '1090177';
$keyAttrib2 = new stdClass();
$keyAttrib2->attrName = 'rid';
$keyAttrib2->attrValue ='rid1';
$keyAttrList = array($keyAttrib1, $keyAttrib2);
$keyList = new stdClass();
$keyList->attribute = $keyAttrList;
$attr1 = new stdClass();
$attr1->attrName = 'city';
$attr1->attrValue = 'SanMateo';
$attr2 = new stdClass();
$attr2->attrName = 'zip';
$attr2->attrValue = '94404';
$attr3 = new stdClass();
$attr3->attrName = 'state';
$attr3->attrValue = 'California';
$attrListArray = array($attr1, $attr2, $attr3);
$attrList = new stdClass();
$attrList->attribute = $attrListArray;
$custObj = new stdClass();
$custObj->customObjKeyList = $keyList;
$custObj->customObjAttributeList = $attrList;
$custObjList = new stdClass();
$custObjList->customObj = array($custObj);
$params = new stdClass();
$params->operation = 'UPSERT';
$params->objTypeName = 'RoadShow';
$params->customObjList = $custObjList;
$soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
try {
  $leads = $soapClient->__soapCall('syncCustomObjects', array($params), $options, $authHdr);
  //      print_r($leads);
}
catch(Exception $ex) {
  var_dump($ex);
}
if ($debug) {
  print "RAW request:\n" .$soapClient->__getLastRequest() ."\n";
  print "RAW response:\n" .$soapClient->__getLastResponse() ."\n";
}
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


public class SyncCustomObjects {
    public static void main(String[] args) {
        System.out.println("Executing Sync Custom Objects");
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
            ParamsSyncCustomObjects request = new ParamsSyncCustomObjects();
            request.setObjTypeName("RoadShow");
            JAXBElement<SyncOperationEnum> operation = new ObjectFactory().createParamsSyncCustomObjectsOperation(SyncOperationEnum.UPSERT);
            request.setOperation(operation);

            ArrayOfCustomObj customObjects = new ArrayOfCustomObj();

            CustomObj customObj = new CustomObj();

            ArrayOfAttribute arrayOfKeyAttributes = new ArrayOfAttribute();
            Attribute attr = new Attribute();
            attr.setAttrName("MKTOID");
            attr.setAttrValue("1090177");

            Attribute attr2 = new Attribute();
            attr2.setAttrName("rid");
            attr2.setAttrValue("rid1");

            arrayOfKeyAttributes.getAttributes().add(attr);
            arrayOfKeyAttributes.getAttributes().add(attr2);


            JAXBElement<ArrayOfAttribute> keyAttributes = new ObjectFactory().createCustomObjCustomObjKeyList(arrayOfKeyAttributes);
            customObj.setCustomObjKeyList(keyAttributes);
            ArrayOfAttribute arrayOfValueAttributes = new ArrayOfAttribute();

            Attribute city = new Attribute();
            city.setAttrName("city");
            city.setAttrValue("SanMateo");

            Attribute zip = new Attribute();
            zip.setAttrName("zip");
            zip.setAttrValue("94404");

            Attribute state = new Attribute();
            state.setAttrName("state");
            state.setAttrValue("California");

            arrayOfValueAttributes.getAttributes().add(city);
            arrayOfValueAttributes.getAttributes().add(state);
            arrayOfValueAttributes.getAttributes().add(zip);

            JAXBElement<ArrayOfAttribute> valueAttributes = new ObjectFactory().createCustomObjCustomObjAttributeList(arrayOfValueAttributes);
            customObj.setCustomObjAttributeList(valueAttributes);

            customObjects.getCustomObjs().add(customObj);

            SuccessSyncCustomObjects result = port.syncCustomObjects(request, header);

            JAXBContext context = JAXBContext.newInstance(SuccessSyncCustomObjects.class);
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

client = Savon.client(wsdl: 'http://app.marketo.com/soap/mktows/2_3?WSDL', soap_header: headers, endpoint: marketoSoapEndPoint, namespaces: namespaces, open_timeout: 90, read_timeout: 90, namespace_identifier: :ns1, env_namespace: 'SOAP-ENV')

#Create Request
request = {
    :obj_type_name => "RoadShow",
    :custom_obj_list => {
        :custom_obj => {
            :custom_obj_key_list => {
                :attribute => {
                    :attr_name => "MKTOID",
                    :attr_value => "1090177" },
                :attribute! => {
                    :attr_name => "rid",
                    :attr_value => "rid1" }
            },
            :custom_obj_attribute_list => {
                :attribute => {
                    :attr_name => "city",
                    :attr_value => "SanMateo" },
                :attribute! => {
                    :attr_name => "zip",
                    :attr_value => "94404" },
                :attribute! => {
                    :attr_name => "state",
                    :attr_value => "California" }
            }
        }
    },
    :operation => "UPSERT"
}

response = client.call(:sync_custom_objects, message: request)

puts response
```
