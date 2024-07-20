---
title: getLeadActivity
feature: SOAP
description: getLeadActivity SOAP anrop
exl-id: f38dee95-235f-4dc2-8839-61d6008132a5
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '346'
ht-degree: 0%

---

# getLeadActivity

Den här funktionen hämtar aktivitetshistoriken för ett enskilt lead som identifieras av den angivna nyckeln. Du kan ange vilka aktivitetstyper du vill returnera i resultatet. Om du vill att alla aktivitetstyper ska skickas ett tomt värde. Om du har mer än en aktivitetstyp skickar du en lista med aktivitetstyper. När du begär flera aktiviteter är det återstående antalet inte ett korrekt tal, utan bör behandlas som en flagga som anger att det finns fler aktiviteter när det återstående antalet är > 0.

En [strömposition](stream-position.md) kan användas för att paginera genom stora resultatuppsättningar.

## Begäran

| Fältnamn | Obligatoriskt/valfritt | Beskrivning |
| --- | --- | --- |
| leadKey->keyType | Obligatoriskt | keyType gör att du kan ange fältet som du vill fråga lead efter. Möjliga värden är: `IDNUM`, `COOKIE`, `EMAIL`, `SFDCLEADID`, `LEADOWNEREMAIL`, `SFDCACCOUNTID`, `SFDCCONTACTID`, `SFDCLEADID`, `SFDCLEADOWNERID`, `SFDCOPPTYID` |
| leadKey->keyValue | Obligatoriskt | `keyValue` är det värde som du vill fråga om leadet efter. |
| activityFilter->includeAttributes->activityType | Valfritt | Begränsar svaret till att endast inkludera de aktivitetstyper som anges. Se WSDL för alla aktivitetstyper. |
| activityFilter->excludeAttributes->activityType | Valfritt | Begränsar svaret så att de aktivitetstyper som anges exkluderas. Se WSDL för alla aktivitetstyper. Obs! Du kan inte ange både `includeAttributes` och `excludeAttributes` inom samma anrop. |
| batchSize | Valfritt | Maximalt antal poster som ska returneras. Systemet kommer att begränsas till 100 eller `batchSize`, beroende på vilket som är lägst. |
| startPosition->offset | Valfritt | Används för att paginera genom ett stort antal aktivitetssvar. Förskjutningsvärdet returneras av föregående svarsfält `newStartPosition->offset` för anrop. |
| startPosition->aktivitetCreatedAt | Valfritt | Används för att paginera genom ett stort antal aktivitetssvar. activityCreatedAt returneras av det föregående anropets svarsfält `newStartPosition->activityCreatedAt`. (W3C WSDL-datumformat). |
| startPosition->senasteSkapadVid | Valfritt | Används för att paginera genom ett stort antal aktivitetssvar. Den senasteCreatedAt returneras av det föregående anropets svarsfält `newStartPosition->latestCreatedAt`. (W3C WSDL-datumformat). |
| startPosition->äldstCreatedAt | Valfritt | Används för att paginera genom ett stort antal aktivitetssvar. Den äldstaCreatedAt returneras av det föregående anropets svarsfält `newStartPosition->oldestCreatedAt`. (W3C WSDL-datumformat). |

## Begär XML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Header>
    <ns1:AuthenticationHeader>
      <mktowsUserId>demo17_1_809939874BFABAE58E5D27</mktowsUserId>
      <requestSignature>04c4f6df99a2271e0fc42dde67939defd2528a74</requestSignature>
      <requestTimestamp>2013-07-31T17:34:01-07:00</requestTimestamp>
    </ns1:AuthenticationHeader>
  </SOAP-ENV:Header>
  <SOAP-ENV:Body>
    <ns1:paramsGetLeadActivity>
      <leadKey>
        <keyType>EMAIL</keyType>
        <keyValue>leademail@company.com</keyValue>
      </leadKey>
      <activityFilter>
        <includeTypes>
          <activityType>VisitWebpage</activityType>
          <activityType>FillOutForm</activityType>
        </includeTypes>
      </activityFilter>
      <startPosition>
        <latestCreatedAt />
        <offset />
      </startPosition>
      <batchSize>10</batchSize>
    </ns1:paramsGetLeadActivity>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## XML för svar

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ns1="http://www.marketo.com/mktows/">
  <SOAP-ENV:Body>
    <ns1:successGetLeadActivity>
      <leadActivityList>
        <returnCount>10</returnCount>
        <remainingCount>4</remainingCount>
        <newStartPosition>
          <latestCreatedAt>2013-07-31T21:33:59-05:00</latestCreatedAt>
          <oldestCreatedAt xsi:nil="true" />
          <activityCreatedAt xsi:nil="true" />
          <offset>ID:1024814</offset>
        </newStartPosition>
        <activityRecordList>
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
              <attribute>
                <attrName>Query Parameters</attrName>
                <attrType xsi:nil="true" />
                <attrValue>aliId=26419</attrValue>
              </attribute>
              <attribute>
                <attrName>Referrer URL</attrName>
                <attrType xsi:nil="true" />
                <attrValue>http://localhost/~tkaufman/index.php</attrValue>
              </attribute>
              <attribute>
                <attrName>Client IP Address</attrName>
                <attrType xsi:nil="true" />
                <attrValue>12.234.36.130</attrValue>
              </attribute>
              <attribute>
                <attrName>User Agent</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_3) AppleWebKit/536.28.10 (KHTML, like Gecko) Version/6.0.3 Safari/536.28.10</attrValue>
              </attribute>
              <attribute>
                <attrName>Message Id</attrName>
                <attrType xsi:nil="true" />
                <attrValue>44490</attrValue>
              </attribute>
              <attribute>
                <attrName>Created At</attrName>
                <attrType xsi:nil="true" />
                <attrValue>2013-07-09 16:44:28</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1089965</attrValue>
              </attribute>
            </activityAttributes>
            <campaign />
            <personName xsi:nil="true" />
            <mktPersonId>1089965</mktPersonId>
            <foreignSysId xsi:nil="true" />
            <orgName xsi:nil="true" />
            <foreignSysOrgId xsi:nil="true" />
          </activityRecord>
          <activityRecord>
            <id>1030678</id>
            <activityDateTime>2013-07-09T16:44:15-05:00</activityDateTime>
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
              <attribute>
                <attrName>Query Parameters</attrName>
                <attrType xsi:nil="true" />
                <attrValue>aliId=26418</attrValue>
              </attribute>
              <attribute>
                <attrName>Referrer URL</attrName>
                <attrType xsi:nil="true" />
                <attrValue />
              </attribute>
              <attribute>
                <attrName>Client IP Address</attrName>
                <attrType xsi:nil="true" />
                <attrValue>12.234.36.130</attrValue>
              </attribute>
              <attribute>
                <attrName>User Agent</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_3) AppleWebKit/536.28.10 (KHTML, like Gecko) Version/6.0.3 Safari/536.28.10</attrValue>
              </attribute>
              <attribute>
                <attrName>Message Id</attrName>
                <attrType xsi:nil="true" />
                <attrValue>44488</attrValue>
              </attribute>
              <attribute>
                <attrName>Created At</attrName>
                <attrType xsi:nil="true" />
                <attrValue>2013-07-09 16:44:15</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1089965</attrValue>
              </attribute>
            </activityAttributes>
            <campaign />
            <personName xsi:nil="true" />
            <mktPersonId>1089965</mktPersonId>
            <foreignSysId xsi:nil="true" />
            <orgName xsi:nil="true" />
            <foreignSysOrgId xsi:nil="true" />
          </activityRecord>
          <activityRecord>
            <id>1030607</id>
            <activityDateTime>2013-07-09T16:41:33-05:00</activityDateTime>
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
              <attribute>
                <attrName>Query Parameters</attrName>
                <attrType xsi:nil="true" />
                <attrValue>aliId=26416</attrValue>
              </attribute>
              <attribute>
                <attrName>Referrer URL</attrName>
                <attrType xsi:nil="true" />
                <attrValue>http://localhost/~tkaufman/index.php</attrValue>
              </attribute>
              <attribute>
                <attrName>Client IP Address</attrName>
                <attrType xsi:nil="true" />
                <attrValue>12.234.36.130</attrValue>
              </attribute>
              <attribute>
                <attrName>User Agent</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_3) AppleWebKit/536.28.10 (KHTML, like Gecko) Version/6.0.3 Safari/536.28.10</attrValue>
              </attribute>
              <attribute>
                <attrName>Message Id</attrName>
                <attrType xsi:nil="true" />
                <attrValue>44485</attrValue>
              </attribute>
              <attribute>
                <attrName>Created At</attrName>
                <attrType xsi:nil="true" />
                <attrValue>2013-07-09 16:41:33</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1089965</attrValue>
              </attribute>
            </activityAttributes>
            <campaign />
            <personName xsi:nil="true" />
            <mktPersonId>1089965</mktPersonId>
            <foreignSysId xsi:nil="true" />
            <orgName xsi:nil="true" />
            <foreignSysOrgId xsi:nil="true" />
          </activityRecord>
          <activityRecord>
            <id>1030597</id>
            <activityDateTime>2013-07-09T16:41:30-05:00</activityDateTime>
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
              <attribute>
                <attrName>Query Parameters</attrName>
                <attrType xsi:nil="true" />
                <attrValue>aliId=26417</attrValue>
              </attribute>
              <attribute>
                <attrName>Referrer URL</attrName>
                <attrType xsi:nil="true" />
                <attrValue>http://go.kokopop.com/ClickDemo.html?aliId=26416</attrValue>
              </attribute>
              <attribute>
                <attrName>Client IP Address</attrName>
                <attrType xsi:nil="true" />
                <attrValue>12.234.36.130</attrValue>
              </attribute>
              <attribute>
                <attrName>User Agent</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_3) AppleWebKit/536.28.10 (KHTML, like Gecko) Version/6.0.3 Safari/536.28.10</attrValue>
              </attribute>
              <attribute>
                <attrName>Message Id</attrName>
                <attrType xsi:nil="true" />
                <attrValue>44484</attrValue>
              </attribute>
              <attribute>
                <attrName>Created At</attrName>
                <attrType xsi:nil="true" />
                <attrValue>2013-07-09 16:41:30</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1089965</attrValue>
              </attribute>
            </activityAttributes>
            <campaign />
            <personName xsi:nil="true" />
            <mktPersonId>1089965</mktPersonId>
            <foreignSysId xsi:nil="true" />
            <orgName xsi:nil="true" />
            <foreignSysOrgId xsi:nil="true" />
          </activityRecord>
          <activityRecord>
            <id>1030593</id>
            <activityDateTime>2013-07-09T16:41:29-05:00</activityDateTime>
            <activityType>Fill Out Form</activityType>
            <mktgAssetName>WebHookForm</mktgAssetName>
            <activityAttributes>
              <attribute>
                <attrName>Webform ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1455</attrValue>
              </attribute>
              <attribute>
                <attrName>Form Fields</attrName>
                <attrType xsi:nil="true" />
                <attrValue>a:25:{s:6:"module";s:11:"leadCapture";s:6:"action";s:4:"save";s:5:"Email";s:7:"leademail@company.com";s:17:"_marketo_comments";s:0:"";s:4:"lpId";s:4:"3441";s:5:"subId";s:2:"21";s:10:"munchkinId";s:11:"100-AEK-913";s:2:"kw";s:9:"not found";s:2:"cr";s:9:"not found";s:9:"searchstr";s:9:"not found";s:5:"lpurl";s:63:"http://go.kokopop.com/ClickDemo.html?cr={creative}&amp;kw={keyword}";s:6:"formid";s:4:"1455";s:9:"returnURL";s:36:"http://go.kokopop.com/ClickDemo.html";s:6:"retURL";s:36:"http://go.kokopop.com/ClickDemo.html";s:10:"returnLPId";s:4:"3441";s:9:"_mkt_disp";s:6:"return";s:8:"_mkt_trk";s:57:"id:100-AEK-913&amp;token:_mch-kokopop.com-1372890712966-47583";s:17:"_comments_marketo";s:0:"";s:13:"_mkto_version";s:5:"2.4.7";s:26:"MarketoSocialSyndicationId";s:0:"";s:3:"sub";s:2:"21";s:2:"lp";s:4:"3441";s:4:"form";s:4:"1455";s:1:"q";s:9:"not found";s:3:"ret";s:36:"http://go.kokopop.com/ClickDemo.html";}</attrValue>
              </attribute>
              <attribute>
                <attrName>Webpage ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>2547</attrValue>
              </attribute>
              <attribute>
                <attrName>Query Parameters</attrName>
                <attrType xsi:nil="true" />
                <attrValue>aliId=26416</attrValue>
              </attribute>
              <attribute>
                <attrName>Referrer URL</attrName>
                <attrType xsi:nil="true" />
                <attrValue>http://go.kokopop.com/ClickDemo.html?aliId=26416</attrValue>
              </attribute>
              <attribute>
                <attrName>Client IP Address</attrName>
                <attrType xsi:nil="true" />
                <attrValue>12.234.36.130</attrValue>
              </attribute>
              <attribute>
                <attrName>User Agent</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_3) AppleWebKit/536.28.10 (KHTML, like Gecko) Version/6.0.3 Safari/536.28.10</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1089965</attrValue>
              </attribute>
            </activityAttributes>
            <campaign />
            <personName xsi:nil="true" />
            <mktPersonId>1089965</mktPersonId>
            <foreignSysId xsi:nil="true" />
            <orgName xsi:nil="true" />
            <foreignSysOrgId xsi:nil="true" />
          </activityRecord>
          <activityRecord>
            <id>1030596</id>
            <activityDateTime>2013-07-09T16:41:22-05:00</activityDateTime>
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
              <attribute>
                <attrName>Query Parameters</attrName>
                <attrType xsi:nil="true" />
                <attrValue>aliId=26416</attrValue>
              </attribute>
              <attribute>
                <attrName>Referrer URL</attrName>
                <attrType xsi:nil="true" />
                <attrValue>http://localhost/~tkaufman/index.php</attrValue>
              </attribute>
              <attribute>
                <attrName>Client IP Address</attrName>
                <attrType xsi:nil="true" />
                <attrValue>12.234.36.130</attrValue>
              </attribute>
              <attribute>
                <attrName>User Agent</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_3) AppleWebKit/536.28.10 (KHTML, like Gecko) Version/6.0.3 Safari/536.28.10</attrValue>
              </attribute>
              <attribute>
                <attrName>Message Id</attrName>
                <attrType xsi:nil="true" />
                <attrValue>44483</attrValue>
              </attribute>
              <attribute>
                <attrName>Created At</attrName>
                <attrType xsi:nil="true" />
                <attrValue>2013-07-09 16:41:22</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1089965</attrValue>
              </attribute>
            </activityAttributes>
            <campaign />
            <personName xsi:nil="true" />
            <mktPersonId>1089965</mktPersonId>
            <foreignSysId xsi:nil="true" />
            <orgName xsi:nil="true" />
            <foreignSysOrgId xsi:nil="true" />
          </activityRecord>
          <activityRecord>
            <id>1030595</id>
            <activityDateTime>2013-07-09T16:40:56-05:00</activityDateTime>
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
              <attribute>
                <attrName>Query Parameters</attrName>
                <attrType xsi:nil="true" />
                <attrValue>aliId=26416</attrValue>
              </attribute>
              <attribute>
                <attrName>Referrer URL</attrName>
                <attrType xsi:nil="true" />
                <attrValue>http://localhost/~tkaufman/index.php</attrValue>
              </attribute>
              <attribute>
                <attrName>Client IP Address</attrName>
                <attrType xsi:nil="true" />
                <attrValue>12.234.36.130</attrValue>
              </attribute>
              <attribute>
                <attrName>User Agent</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_3) AppleWebKit/536.28.10 (KHTML, like Gecko) Version/6.0.3 Safari/536.28.10</attrValue>
              </attribute>
              <attribute>
                <attrName>Message Id</attrName>
                <attrType xsi:nil="true" />
                <attrValue>44482</attrValue>
              </attribute>
              <attribute>
                <attrName>Created At</attrName>
                <attrType xsi:nil="true" />
                <attrValue>2013-07-09 16:40:56</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1089965</attrValue>
              </attribute>
            </activityAttributes>
            <campaign />
            <personName xsi:nil="true" />
            <mktPersonId>1089965</mktPersonId>
            <foreignSysId xsi:nil="true" />
            <orgName xsi:nil="true" />
            <foreignSysOrgId xsi:nil="true" />
          </activityRecord>
          <activityRecord>
            <id>1030552</id>
            <activityDateTime>2013-07-09T16:37:04-05:00</activityDateTime>
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
              <attribute>
                <attrName>Query Parameters</attrName>
                <attrType xsi:nil="true" />
                <attrValue>aliId=26415</attrValue>
              </attribute>
              <attribute>
                <attrName>Referrer URL</attrName>
                <attrType xsi:nil="true" />
                <attrValue>http://localhost/~tkaufman/index.php</attrValue>
              </attribute>
              <attribute>
                <attrName>Client IP Address</attrName>
                <attrType xsi:nil="true" />
                <attrValue>12.234.36.130</attrValue>
              </attribute>
              <attribute>
                <attrName>User Agent</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_3) AppleWebKit/536.28.10 (KHTML, like Gecko) Version/6.0.3 Safari/536.28.10</attrValue>
              </attribute>
              <attribute>
                <attrName>Message Id</attrName>
                <attrType xsi:nil="true" />
                <attrValue>44478</attrValue>
              </attribute>
              <attribute>
                <attrName>Created At</attrName>
                <attrType xsi:nil="true" />
                <attrValue>2013-07-09 16:37:04</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1089965</attrValue>
              </attribute>
            </activityAttributes>
            <campaign />
            <personName xsi:nil="true" />
            <mktPersonId>1089965</mktPersonId>
            <foreignSysId xsi:nil="true" />
            <orgName xsi:nil="true" />
            <foreignSysOrgId xsi:nil="true" />
          </activityRecord>
          <activityRecord>
            <id>1024819</id>
            <activityDateTime>2013-07-03T17:54:51-05:00</activityDateTime>
            <activityType>Fill Out Form</activityType>
            <mktgAssetName>WebHookForm</mktgAssetName>
            <activityAttributes>
              <attribute>
                <attrName>Webform ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1455</attrValue>
              </attribute>
              <attribute>
                <attrName>Form Fields</attrName>
                <attrType xsi:nil="true" />
                <attrValue>a:25:{s:6:"module";s:11:"leadCapture";s:6:"action";s:4:"save";s:5:"Email";s:7:"leademail@company.com";s:17:"_marketo_comments";s:0:"";s:4:"lpId";s:4:"3441";s:5:"subId";s:2:"21";s:10:"munchkinId";s:11:"100-AEK-913";s:2:"kw";s:9:"not found";s:2:"cr";s:9:"not found";s:9:"searchstr";s:9:"not found";s:5:"lpurl";s:63:"http://go.kokopop.com/ClickDemo.html?cr={creative}&amp;kw={keyword}";s:6:"formid";s:4:"1455";s:9:"returnURL";s:36:"http://go.kokopop.com/ClickDemo.html";s:6:"retURL";s:36:"http://go.kokopop.com/ClickDemo.html";s:10:"returnLPId";s:4:"3441";s:9:"_mkt_disp";s:6:"return";s:8:"_mkt_trk";s:57:"id:100-AEK-913&amp;token:_mch-kokopop.com-1372890712966-47583";s:17:"_comments_marketo";s:0:"";s:13:"_mkto_version";s:5:"2.4.7";s:26:"MarketoSocialSyndicationId";s:0:"";s:3:"sub";s:2:"21";s:2:"lp";s:4:"3441";s:4:"form";s:4:"1455";s:1:"q";s:9:"not found";s:3:"ret";s:36:"http://go.kokopop.com/ClickDemo.html";}</attrValue>
              </attribute>
              <attribute>
                <attrName>Webpage ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>2547</attrValue>
              </attribute>
              <attribute>
                <attrName>Query Parameters</attrName>
                <attrType xsi:nil="true" />
                <attrValue />
              </attribute>
              <attribute>
                <attrName>Referrer URL</attrName>
                <attrType xsi:nil="true" />
                <attrValue>http://go.kokopop.com/ClickDemo.html</attrValue>
              </attribute>
              <attribute>
                <attrName>Client IP Address</attrName>
                <attrType xsi:nil="true" />
                <attrValue>172.249.34.240</attrValue>
              </attribute>
              <attribute>
                <attrName>User Agent</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_3) AppleWebKit/536.28.10 (KHTML, like Gecko) Version/6.0.3 Safari/536.28.10</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1089965</attrValue>
              </attribute>
            </activityAttributes>
            <campaign />
            <personName xsi:nil="true" />
            <mktPersonId>1089965</mktPersonId>
            <foreignSysId xsi:nil="true" />
            <orgName xsi:nil="true" />
            <foreignSysOrgId xsi:nil="true" />
          </activityRecord>
          <activityRecord>
            <id>1024814</id>
            <activityDateTime>2013-07-03T17:54:50-05:00</activityDateTime>
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
              <attribute>
                <attrName>Query Parameters</attrName>
                <attrType xsi:nil="true" />
                <attrValue>aliId=26198</attrValue>
              </attribute>
              <attribute>
                <attrName>Referrer URL</attrName>
                <attrType xsi:nil="true" />
                <attrValue>http://go.kokopop.com/ClickDemo.html</attrValue>
              </attribute>
              <attribute>
                <attrName>Client IP Address</attrName>
                <attrType xsi:nil="true" />
                <attrValue>172.249.34.240</attrValue>
              </attribute>
              <attribute>
                <attrName>User Agent</attrName>
                <attrType xsi:nil="true" />
                <attrValue>Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_3) AppleWebKit/536.28.10 (KHTML, like Gecko) Version/6.0.3 Safari/536.28.10</attrValue>
              </attribute>
              <attribute>
                <attrName>Message Id</attrName>
                <attrType xsi:nil="true" />
                <attrValue>44443</attrValue>
              </attribute>
              <attribute>
                <attrName>Created At</attrName>
                <attrType xsi:nil="true" />
                <attrValue>2013-07-03 17:54:50</attrValue>
              </attribute>
              <attribute>
                <attrName>Lead ID</attrName>
                <attrType xsi:nil="true" />
                <attrValue>1089965</attrValue>
              </attribute>
            </activityAttributes>
            <campaign />
            <personName xsi:nil="true" />
            <mktPersonId>1089965</mktPersonId>
            <foreignSysId xsi:nil="true" />
            <orgName xsi:nil="true" />
            <foreignSysOrgId xsi:nil="true" />
          </activityRecord>
        </activityRecordList>
      </leadActivityList>
    </ns1:successGetLeadActivity>
  </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

Observera att elementet `id` i `activityRecord`-elementen ersätts av elementet `marketoGUID` som en unik identifierare.  Denna ändring kommer att ske i vårversionen 2017.

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
  $leadKey = array("keyType" => "EMAIL", "keyValue" => "two@t.com");
  
  $includeTypes = array("VisitWebpage", "FillOutForm" );
  $actIncludeArray = new stdClass();
  $actIncludeArray->activityType = $includeTypes;
  
  $filter = new stdClass();
  $filter->includeTypes = $actIncludeArray;
  
  $startPosition = new stdClass();
  $startPosition->latestCreatedAt= "";
  $startPosition->offset = "";
 
 
  $leadKeyParams = array("leadKey" => $leadKey, "activityFilter" => $filter, "batchSize" => 10, "startPosition" => $startPosition);
  $params = array("paramsGetLeadActivity" => $leadKeyParams);
 
 
  $soapClient = new SoapClient($marketoSoapEndPoint ."?WSDL", $options);
  try {
    $leadActivity = $soapClient->__soapCall('getLeadActivity', $params, $options, $authHdr);  }
  catch(Exception $ex) {
    var_dump($ex);
  }
  if ($debug) {
    print "RAW request:\n" .$soapClient->__getLastRequest() ."\n";
    print "RAW response:\n" .$soapClient->__getLastResponse() ."\n";
  }
  print_r($leadActivity);
 
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
 
public class GetLeadActivity {
 
 
    public static void main(String[] args) {
        System.out.println("Executing Get Lead Activity");
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
            ParamsGetLeadActivity request = new ParamsGetLeadActivity();
            LeadKey key = new LeadKey();
            key.setKeyType(LeadKeyRef.EMAIL);
            key.setKeyValue("t@t.com");
            request.setLeadKey(key);
 
            ObjectFactory objectFactory = new ObjectFactory();
            JAXBElement<Integer> batchSize = objectFactory.createParamsGetLeadActivityBatchSize(10);
            request.setBatchSize(batchSize);
             
            ActivityTypeFilter atv = new ActivityTypeFilter();
            ArrayOfActivityType aatt = new ArrayOfActivityType();
             
            aatt.getActivityTypes().add(ActivityType.VISIT_WEBPAGE);
            aatt.getActivityTypes().add(ActivityType.FILL_OUT_FORM);
             
            atv.setIncludeTypes(aatt);
            JAXBElement<ActivityTypeFilter> typeFilter = objectFactory.createParamsGetLeadActivityActivityFilter(atv);
            request.setActivityFilter(typeFilter);
             
            SuccessGetLeadActivity result = port.getLeadActivity(request, header);
 
            JAXBContext context = JAXBContext.newInstance(SuccessGetLeadActivity.class);
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
request = {
    :lead_key => {
        :key_type => "EMAIL",
        :key_value => "leademail@company.com" },
    :activity_filter => {
        :include_types => {
            :activity_type => ["VisitWebpage", "FillOutForm"]
        }
    },
    :start_position => {
        :"last_created_at/" => "",
        :"offset/" => "" },
    :batch_size => "10" 
}

response = client.call(:get_lead_activity, message: request)

puts response
```
