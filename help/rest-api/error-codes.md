---
title: Felkoder
feature: REST API
description: Marketo felkodbeskrivningar.
exl-id: a923c4d6-2bbc-4cb7-be87-452f39b464b6
source-git-commit: d0750eab0a37df0b7f80c6252f46c95068975000
workflow-type: tm+mt
source-wordcount: '2273'
ht-degree: 2%

---

# Felkoder

Nedan finns listor över felkoder för REST API och en förklaring av hur fel returneras till programmen.

## Hantera och logga undantag

När du utvecklar för Marketo är det viktigt att begäranden och svar loggas när ett oväntat undantag inträffar. Vissa typer av undantag, till exempel autentisering som har upphört att gälla, kan hanteras säkert genom återautentisering, men andra kan kräva supportinteraktioner, och begäranden och svar kommer alltid att begäras i det här scenariot.

## Feltyper

Marketo REST API kan returnera tre olika typer av fel vid normal användning:

* HTTP-nivå: Dessa fel indikeras av en `4xx`-kod.
* Svarsnivå: Dessa fel ingår i arrayen &quot;errors&quot; i JSON-svaret.
* Postnivå: Dessa fel ingår i arrayen &quot;result&quot; i JSON-svaret och anges på individuell postbasis med arrayen &quot;status&quot; och &quot;reasons&quot;.

För feltyperna Svarsnivå och Postnivå returneras HTTP-statuskoden 200. För alla feltyper ska HTTP-orsaksfrasen inte utvärderas eftersom den är valfri och kan ändras.

### HTTP-nivåfel

Under normala driftförhållanden bör Marketo endast returnera två HTTP-statuskodfel, `413 Request Entity Too Large` och `414 Request URI Too Long`. Båda kan återställas genom att felet identifieras, begäran ändras och nya försök görs, men med smart kodning bör du aldrig stöta på detta i verkligheten.

Marketo returnerar 413 om nyttolasten för begäran överstiger 1 MB, eller 10 MB vid import av lead. I de flesta fall är det osannolikt att de här gränserna kommer att nås, men om man lägger till en kontroll av begärans storlek och flyttar poster, vilket gör att gränsen överskrids för en ny begäran, bör det inte finnas några omständigheter som kan leda till att felet returneras av någon slutpunkt.

414 returneras när URI:n för en GET-begäran överstiger 8 kB. För att undvika det bör du kontrollera mot längden på frågesträngen för att se om den överskrider den här gränsen. Om den ändrar din begäran till en POST-metod anger du frågesträngen som begärandebrödtext med den extra parametern `_method=GET`. Detta innebär att begränsningen för URI:er ignoreras. I de flesta fall är det ovanligt att den här gränsen nås, men det är något vanligt när du hämtar stora grupper med poster med långa enskilda filtervärden som GUID.
[Identity](https://developer.adobe.com/marketo-apis/api/identity/)-slutpunkten kan returnera ett 401 otillåtet fel. Detta beror vanligtvis på ett ogiltigt klient-ID eller ogiltig klienthemlighet. Felkoder på HTTP-nivå

<table>
  <thead>
    <tr>
      <th> Svarskod </th>
      <th> Beskrivning </th>
      <th colspan="1"> Kommentar </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a name="413"></a>413</td>
      <td>Begärandeentiteten är för stor</td>
      <td>Nyttolasten överskrider gränsen på 1 MB.</td>
    </tr>
    <tr>
      <td><a name="414"></a>414</td>
      <td>Begärande-URI är för lång</td>
      <td>URI för begäran överskred 8 kB. Begäran ska provas igen som en POST med param &grave;_method=GET" i URL:en och resten av frågesträngen i begärans innehåll.</td>
    </tr>
  </tbody>
</table>


#### Fel på svarsnivå

Svarsnivåfel förekommer när parametern `success` i svaret är inställd på false och är strukturerade på följande sätt:

```json
{
    "requestId": "e42b#14272d07d78",
    "success": false,
    "errors": [
        {
            "code": "601",
            "message": "Unauthorized"
        }
    ]
}
```

Varje objekt i arrayen &quot;errors&quot; har två medlemmar, `code`, som är ett heltal med citattecken mellan 601 och 799, och en `message` som anger varför felet beror på texten. 6xx-koder anger alltid att en begäran misslyckades helt och inte kördes. Ett exempel är 601, &quot;Åtkomsttoken invalid&quot;, som kan återställas genom återautentisering och genom att skicka den nya åtkomsttoken med begäran. 7xx-fel indikerar att begäran misslyckades, antingen på grund av att inga data returnerades, eller på grund av att begäran parametriserades felaktigt, till exempel att ett ogiltigt datum inkluderades, eller att en obligatorisk parameter saknades.

#### Felkoder på svarsnivå

Ett API-anrop som returnerar den här svarskoden räknas inte av mot din dagliga kvot eller din avgiftsgräns.

<table>
  <thead>
    <tr>
      <th> Svarskod </th>
      <th> Beskrivning </th>
      <th> Kommentar </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a name="502"></a>502</td>
      <td>Felaktig gateway</td>
      <td>Fjärrservern returnerade ett fel. Troligen en timeout. Ett nytt försök att utföra begäran bör göras med exponentiell säkerhetskopiering.</td>
    </tr>
    <tr>
      <td><a name="601"></a>601*</td>
      <td>Ogiltig åtkomsttoken</td>
      <td>En Access Token-parameter inkluderades i begäran, men värdet var inte en giltig åtkomsttoken.</td>
    </tr>
    <tr>
      <td><a name="602"></a>602*</td>
      <td>Åtkomsttoken har upphört</td>
      <td>Åtkomsttoken som ingår i samtalet är inte längre giltig på grund av förfallodatum.</td>
    </tr>
    <tr>
      <td><a name="603"></a>603</td>
      <td>Åtkomst nekad</td>
      <td>Autentiseringen lyckades, men användaren har inte tillräcklig behörighet för att anropa API:t. [Ytterligare behörigheter](custom-services.md) kan behöva tilldelas användarrollen, eller så kan <a href="https://experienceleague.adobe.com/en/docs/marketo/using/product-docs/administration/additional-integrations/create-an-allowlist-for-ip-based-api-access">Tillåtelselista för IP-baserad API-åtkomst</a> aktiveras.</td>
    </tr>
    <tr>
      <td><a name="604"></a>604*</td>
      <td>Timeout för begäran</td>
      <td>Begäran kördes för länge (t.ex. påträffade databaskonflikter) eller så överskred den timeout-period som angavs i huvudet för anropet.</td>
    </tr>
    <tr>
      <td><a name="605"></a>605*</td>
      <td>HTTP-metoden stöds inte</td>
      <td>GET stöds inte för slutpunkten Synkronisera leads. POST måste användas.</td>
    </tr>
    <tr>
      <td><a name="606"></a>606</td>
      <td>Maxhastighetsgränsen "%s"; överskriden med "%s" sekunder</td>
      <td>Antalet anrop under de senaste 20 sekunderna var större än 100</td>
    </tr>
    <tr>
      <td><a name="607"></a>607</td>
      <td>Kvot per dag har nåtts</td>
      <td>Antalet anrop i dag överskred prenumerationens kvot (återställs dagligen kl. 12:00 CST).&gt;Din kvot finns på menyn Admin-&gt;Webbtjänster. Du kan öka din kvot med din kontohanterare.</td>
    </tr>
    <tr>
      <td><a name="608"></a>608*</td>
      <td>API är inte tillgängligt för tillfället</td>
      <td></td>
    </tr>
    <tr>
      <td><a name="609"></a>609</td>
      <td>Ogiltig JSON</td>
      <td>Innehållet i begäran är inte en giltig JSON.</td>
    </tr>
    <tr>
      <td><a name="610"></a>610</td>
      <td>Begärd resurs hittades inte</td>
      <td>URI:n i anropet matchade inte en REST API-resurstyp. Detta beror ofta på en felaktigt stavad eller felaktigt formaterad URI för begäran</td>
    </tr>
    <tr>
      <td><a name="611"></a>611*</td>
      <td>Systemfel</td>
      <td>Alla ohanterade undantag</td>
    </tr>
    <tr>
      <td><a name="612"></a>612</td>
      <td>Ogiltig innehållstyp</td>
      <td>Om det här felet uppstår lägger du till en innehållstypsrubrik som anger JSON-format i din begäran. Prova till exempel med "innehållstyp: program/json". <a href="https://stackoverflow.com/questions/28181325/why-invalid-content-type">Mer information finns i den här StackOverflow-frågan</a>.</td>
    </tr>
    <tr>
      <td><a name="613"></a>613</td>
      <td>Ogiltig multipart-begäran</td>
      <td>POSTENS multipart-innehåll har inte formaterats korrekt</td>
    </tr>
    <tr>
      <td><a name="614"></a>614</td>
      <td>Ogiltig prenumeration</td>
      <td>Målprenumerationen kan inte hittas eller går inte att nå. Detta indikerar vanligtvis temporär otillgänglighet.</td>
    </tr>
    <tr>
      <td><a name="615"></a>615</td>
      <td>Gränsen för samtidig åtkomst har nåtts</td>
      <td>Begäranden behandlas som mest av alla prenumerationer 10 i taget. Detta returneras om det redan finns 10 pågående begäranden.</td>
    </tr>
    <tr>
      <td><a name="616"></a>616</td>
      <td>Ogiltig prenumerationstyp</td>
      <td>Du måste ange lämplig prenumerationstyp för Marketo för att få tillgång till API:t för anpassade objektmetadata. Mer information finns i din CSM.</td>
    </tr>
    <tr>
      <td><a name="701"></a>701</td>
      <td>%s får inte vara tomt</td>
      <td>Det rapporterade fältet får inte vara tomt i begäran</td>
    </tr>
    <tr>
      <td><a name="702"></a>702</td>
      <td>Inga data hittades för ett givet sökscenario</td>
      <td>Inga poster matchade angivna sökparametrar.
        Obs! Många misslyckade sökåtgärder returnerar "success = true" och inga fel, samt anger en informationssträng för varningar.</td>
    </tr>
    <tr>
      <td><a name="703"></a>703</td>
      <td>Funktionen är inte aktiverad för prenumerationen</td>
      <td>En betafunktion som inte har aktiverats i en användares prenumeration</td>
    </tr>
    <tr>
      <td><a name="704"></a>704</td>
      <td>Ogiltigt datumformat</td>
      <td><ul>
          <li>Ett datum som inte hade rätt format angavs</li>
          <li>Ett ogiltigt ID för dynamiskt innehåll angavs</li>
        </ul></td>
    </tr>
    <tr>
      <td><a name="709"></a>709</td>
      <td>Affärsregelöverträdelse</td>
      <td>Anropet kan inte utföras eftersom det bryter mot ett krav på att skapa eller uppdatera en resurs, till exempel att försöka skapa ett e-postmeddelande utan en mall. Det går också att få följande fel när du försöker:
        <ul>
          <li>Hämta innehåll för landningssidor som innehåller socialt innehåll.</li>
          <li>Klona ett program som innehåller vissa resurstyper (mer information finns i <a href="programs.md#clone">Programklon</a>).</li>
          <li>Godkänn en tillgång som inte har något utkast (det vill säga som redan har godkänts).</li>
        </ul></td>
    </tr>
    <tr>
      <td><a name="710"></a>710</td>
      <td>Överordnad mapp hittades inte</td>
      <td>Det gick inte att hitta den angivna överordnade mappen</td>
    </tr>
    <tr>
      <td><a name="711"></a>711</td>
      <td>Inkompatibel mapptyp</td>
      <td>Den angivna mappen var inte av rätt typ för att uppfylla begäran</td>
    </tr>
    <tr>
      <td><a name="712"></a>712</td>
      <td>Åtgärden Koppla till person-konto är ogiltig</td>
      <td>Ett anrop om kopplade leads misslyckades på grund av ett försök att sammanfoga leads som är Salesforce personkonton.  Salesforce personkonton måste sammanfogas i Salesforce.</td>
    </tr>
    <tr>
      <td><a name="713"></a>713</td>
      <td>Övergående fel</td>
      <td>En systemresurs var tillfälligt otillgänglig vid tiden för API-anropet. När det här felet inträffar bör du vänta en stund och sedan försöka utföra begäran igen.</td>
    </tr>
    <tr>
      <td><a name="714"></a>714</td>
      <td>Det går inte att hitta standardposttypen</td>
      <td>Det gick inte att anropa kopplade leads eftersom det inte gick att hitta någon standardposttyp.</td>
    </tr>
    <tr>
      <td><a name="718"></a>718</td>
      <td>ExternalSalesPersonID hittades inte</td>
      <td>Ett Sync Opportunity-anrop gjordes med ett värde som inte finns, ExternalSalesPersonID.</td>
    </tr>
    <tr>
      <td>719</td>
      <td>Timeoutundantag för låsning av väntetid</td>
      <td>Ett Clone Program-anrop gjordes och timeout uppstod i väntan på ett lås.</td>
    </tr>
  </tbody>
</table>

### Postnivå {#record_level_errors}

Postnivåfel indikerar att en åtgärd inte kunde slutföras för en enskild post, men själva begäran var giltig. Ett svar med fel på postnivå följer det här mönstret:


#### Svar

```json
{  
   "requestId":"e42b#14272d07d78",
   "success":true,
   "result":[  
      {  
         "id":50,
         "status":"created"
      },
      {  
         "id":51,
         "status":"created"
      },
      {  
         "status":"skipped",
         "reasons":[  
            {  
               "code":"1005",
               "message":"Lead already exists"
            }
         ]
      }
   ]
}
```

Poster som ingår i den resulterande arrayen med anrop ordnas på samma sätt som indataarrayen för en begäran.
Varje post i en lyckad begäran kan lyckas eller misslyckas på individnivå, vilket anges av statusfältet för varje post som ingår i resultatarrayen i ett svar. Fältet &quot;status&quot; för de här posterna hoppas över och arrayen &quot;reasons&quot; finns. Varje orsak innehåller en&quot;kodmedlem&quot; och en&quot;meddelandemedlem&quot;. Koden är alltid 1xxx och meddelandet anger varför posten hoppades över. Ett exempel är när en begäran om synkronisering av leads har &quot;action&quot; inställt på &quot;createOnly&quot;, men det redan finns en lead för en av nycklarna i de skickade posterna. I det här fallet returneras koden 1005 och meddelandet&quot;Lead already exists&quot; (Lead finns redan) visas ovan.

#### Felkoder på postnivå

<table>
  <tbody>
    <tr>
      <td>Svarskod</td>
      <td>Beskrivning</td>
      <td>Kommentar</td>
    </tr>
    <tr>
      <td><a name="1001"></a>1001</td>
      <td>Ogiltigt värde, %s. Krävs av typen "%s"</td>
      <td>Ett fel genereras när ett parametervärde har ett typmatchningsfel. Strängvärdet som anges för en heltalsparameter.</td>
    </tr>
    <tr>
      <td><a name="1002"></a>1002</td>
      <td>Värde saknas för den obligatoriska parametern %s</td>
      <td>Ett fel genereras när en obligatorisk parameter saknas i begäran</td>
    </tr>
    <tr>
      <td><a name="1003"></a>1003</td>
      <td>Ogiltiga data</td>
      <td>När inskickade data inte är en giltig typ för den angivna slutpunkten eller det angivna läget, till exempel när ID skickas för en lead med åtgärden createOnly eller när Request Campaign används för en batchkampanj.</td>
    </tr>
    <tr>
      <td><a name="1004"></a>1004</td>
      <td>Lead hittades inte</td>
      <td>För syncLead, när åtgärden är "updateOnly" och om lead inte hittas</td>
    </tr>
    <tr>
      <td><a name="1005"></a>1005</td>
      <td>Lead finns redan</td>
      <td>För syncLead, när åtgärden är"createOnly" och om det redan finns en lead</td>
    </tr>
    <tr>
      <td><a name="1006"></a>1006</td>
      <td>Fältet "%s" hittades inte</td>
      <td>Ett inkluderat fält i anropet är inte ett giltigt fält.</td>
    </tr>
    <tr>
      <td><a name="1007"></a>1007</td>
      <td>Flera leads matchar sökvillkoren</td>
      <td>Flera leads matchar sökvillkoren. Uppdateringar kan bara utföras när nyckeln matchar en enskild post</td>
    </tr>
    <tr>
      <td><a name="1008"></a>1008</td>
      <td>Åtkomst nekad till partitionen %s</td>
      <td>Användaren för den anpassade tjänsten har inte åtkomst till en arbetsyta med partitionen där posten finns.</td>
    </tr>
    <tr>
      <td><a name="1009"></a>1009</td>
      <td>Partitionsnamnet måste anges</td>
      <td></td>
    </tr>
    <tr>
      <td><a name="1010"></a>1010</td>
      <td>Partitionsuppdatering tillåts inte</td>
      <td>Den angivna posten finns redan i en separat huvudpartition.</td>
    </tr>
    <tr>
      <td><a name="1011"></a>1011</td>
      <td>Fältet "%s" stöds inte</td>
      <td>Vid uppslagsfält eller filtertyp som anges med standardfält som inte stöds (t.ex. firstName, lastName)</td>
    </tr>
    <tr>
      <td><a name="1012"></a>1012</td>
      <td>Ogiltigt cookie-värde "%s"</td>
      <td>Kan inträffa när <a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/associateLeadUsingPOST">Associate Lead</a> anropas med ett ogiltigt värde för parametern "cookie".
        Detta inträffar också när <a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadsByFilterUsingGET">Hämta leads efter filtertyp</a> anropas med filtertyp=cookies och ett ogiltigt värde för parametern filterValues.</td>
    </tr>
    <tr>
      <td><a name="1013"></a>1013</td>
      <td>Objektet hittades inte</td>
      <td>Get object (list, campaign) by id returnerar den här felkoden</td>
    </tr>
    <tr>
      <td><a name="1014"></a>1014</td>
      <td>Det gick inte att skapa objektet</td>
      <td>Det gick inte att skapa objektet (listan)</td>
    </tr>
    <tr>
      <td><a name="1015"></a>1015</td>
      <td>Lead finns inte i listan</td>
      <td>Den angivna leadet är inte medlem i mållistan</td>
    </tr>
    <tr>
      <td><a name="1016"></a>1016</td>
      <td>För många importer</td>
      <td>För många importer står i kö. Högst 10 är tillåtet</td>
    </tr>
    <tr>
      <td><a name="1017"></a>1017</td>
      <td>Objektet finns redan</td>
      <td>Det gick inte att skapa eftersom posten redan finns</td>
    </tr>
    <tr>
      <td><a name="1018"></a>1018</td>
      <td>CRM aktiverat</td>
      <td>Åtgärden kunde inte utföras eftersom instansen har en intern CRM-integrering aktiverad.</td>
    </tr>
    <tr>
      <td><a name="1019"></a>1019</td>
      <td>Import pågår</td>
      <td>Mållistan importeras redan till</td>
    </tr>
    <tr>
      <td><a name="1020"></a>1020</td>
      <td>För många kloner att programmera</td>
      <td>Prenumerationen har nått den tilldelade användningen av cloneToProgramName i schemaprogrammet för dagen</td>
    </tr>
    <tr>
      <td><a name="1021"></a>1021</td>
      <td>Företagsuppdatering tillåts inte</td>
      <td>Företagsuppdatering tillåts inte under syncLead</td>
    </tr>
    <tr>
      <td><a name="1022"></a>1022</td>
      <td>Objekt som används</td>
      <td>Borttagning tillåts inte när ett objekt används av ett annat objekt</td>
    </tr>
    <tr>
      <td><a name="1025"></a>1025</td>
      <td>Programstatus hittades inte</td>
      <td>En status angavs för att ändra status för Lead Program som inte matchade en status som är tillgänglig för programmets kanal.</td>
    </tr>
    <tr>
      <td><a name="1026"></a>1026</td>
      <td>Anpassat objekt är inte aktiverat</td>
      <td>Åtgärden kunde inte utföras eftersom instansen inte har aktiverat integrering av anpassade objekt.</td>
    </tr>
    <tr>
      <td><a name="1027"></a>1027</td>
      <td>Maximal begränsning för aktivitetstyp har uppnåtts</td>
      <td>Prenumerationen har nått det maximala antalet tillgängliga anpassade aktivitetstyper.</td>
    </tr>
    <tr>
      <td><a name="1028"></a>1028</td>
      <td>Maximal fältgräns har nåtts</td>
      <td>Anpassade aktiviteter har högst 20 sekundära attribut.</td>
    </tr>
    <tr>
      <td><a name="1029"></a>1029</td>
      <td><ul>
          <li>För många jobb i kön</li>
          <li>Exportera dagskvot har överskridits</li>
        </ul></td>
      <td><ul>
          <li>Prenumerationer tillåts maximalt 10 massextraheringsjobb i kön vid en given tidpunkt.</li>
          <li>Extraheringsjobb som standard är begränsade till 500 MB per dag (återställs dagligen till 01:00 CST).</li>
        </ul></td>
    </tr>
    <tr>
      <td><a name="1035"></a>1035</td>
      <td>Filtertypen stöds inte</td>
      <td>I vissa prenumerationer stöds inte följande filtertyper för Bulk Lead Extract: updatedAt, smartListId, smartListName.</td>
    </tr>
    <tr>
      <td><a name="1036"></a>1036</td>
      <td>Dubblettobjekt hittades i indata</td>
      <td>Ett anrop gjordes för att uppdatera två eller flera poster med samma sekundärnyckel. Ett Sync Companies-anrop använder till exempel samma externalCompanyId för mer än ett företag.</td>
    </tr>
    <tr>
      <td><a name="1037"></a>1037</td>
      <td>Lead hoppades över</td>
      <td>Lead hoppades över eftersom den redan är i eller förbi den här statusen.</td>
    </tr>
    <tr>
      <td><a name="1042"></a>1042</td>
      <td>Ogiltigt körningsdatum</td>
      <td>Det runAt-datum som angavs för Schedule Campaign var för långt in i framtiden (det maximala antalet är 2 år).</td>
    </tr>
    <tr>
      <td><a name="1048"></a>1048</td>
      <td>Utkastet för borttagning av anpassat objekt misslyckades</td>
      <td>Ett anrop gjordes för att ta bort utkastversionen av ett anpassat objekt.</td>
    </tr>
    <tr>
      <td><a name="1049"></a>1049</td>
      <td>Det gick inte att skapa aktiviteten</td>
      <td>Attributmatrisen är för lång.
        Arrayen med attribut som skickades till posten överskred den maximala längden på 65536 byte</td>
    </tr>
    <tr>
      <td><a name="1076"></a>1076</td>
      <td><a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/mergeLeadsUsingPOST">Anropet </a> Merge Leads med mergeInCRM-flaggan är 4.</td>
      <td>Du skapar en dubblettpost. Du bör använda en befintlig post i stället.
        Detta är felmeddelandet som Marketo får när de sammanfogar i Salesforce.</td>
    </tr>
    <tr>
      <td><a name="1077"></a>1077</td>
      <td>Anropet <a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/mergeLeadsUsingPOST">Kopplingsleads</a> misslyckades på grund av längden "SFDC Field"</td>
      <td>Ett anrop av typen Merge Leads med mergeInCRM inställt på true misslyckades eftersom "SFDC Field" överskrider gränsen för tillåtna tecken. Korrigera genom att minska längden på "SFDC Field" eller genom att ange mergeInCRM till false.</td>
    </tr>
    <tr>
      <td><a name="1078"></a>1078</td>
      <td>Anropet <a href="https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/mergeLeadsUsingPOST">Kopplingsleads</a> misslyckades på grund av att entiteten tagits bort, inte en lead/kontakt eller att fältfiltervillkoren inte matchar.</td>
      <td>Sammanfogningsfel, det gick inte att utföra sammanfogningsåtgärden i det internt synkroniserade CRM
        Detta är felmeddelandet som Marketo får när de sammanfogar i Salesforce.</td>
    </tr>
  </tbody>
</table>
