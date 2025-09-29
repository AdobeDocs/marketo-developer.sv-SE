---
title: Metodtips för Marketo-integrering
feature: REST API
description: Bästa tillvägagångssätt för Marketo API-integreringar som omfattar kvoter, frekvens och samtidighetsgränser, batchbearbetning, bulkimport och -export, cachelagring och latensplanering.
exl-id: 1e418008-a36b-4366-a044-dfa9fe4b5f82
source-git-commit: 7557b9957c87f63c2646be13842ea450035792be
workflow-type: tm+mt
source-wordcount: '966'
ht-degree: 0%

---

# Metodtips för Marketo-integrering

## API-gränser

- **Daglig kvot:** De flesta prenumerationer tilldelas 50 000 API-anrop per dag (som återställs dagligen med 12:00AM CST). Du kan öka din dagliga kvot med din kontohanterare.
- **Hastighetsgräns:** API-åtkomst per instans begränsad till 100 anrop per 20 sekunder.
- **Samtidighetsgräns:**  Max tio samtidiga API-anrop.
- **Batchstorlek:** Lead DB - 300 poster; Tillgångsfråga - 200 poster
- **REST API-nyttolaststorlek:** 1 MB
- **Storlek på massimportfil:** 10 MB
- **Maximal batchstorlek för SOAP:** 300 poster
- **Massextraheringsjobb:** 2 körning; 10 köade (inklusive)

## Snabbtips

- Anta att ditt program kommer att konkurrera om resurser för kvot, betygsättning och samtidighet med andra program och ange försiktiga användningsgränser.
- Använd Marketo bulk- och batch-metoder när de är tillgängliga och lämpliga. Använd bara enstaka post eller enstaka resultatanrop när det behövs.
- Använd [exponentiell säkerhetskopiering](https://en.wikipedia.org/wiki/Exponential_backoff) för att försöka utföra API-anrop igen som misslyckas på grund av hastighets- eller samtidighetsbegränsningar.
- Undvik att göra samtidiga API-anrop om ditt användningsexempel inte har någon nytta av det.

## Gruppering

För att säkerställa bästa möjliga prestanda för dina integreringar bör poster grupperas i så få transaktioner som möjligt när du utför infogningar eller uppdateringar. När du hämtar poster från ett datalager för överföring bör posterna alltid sammanställas innan de skickas, i stället för att en begäran skickas för varje enskild ändring.

## Godtagbar svarstid

Om du fastställer dina latenstoleranser, eller den maximala tid som kan gå innan du skickar ett API-anrop, kommer många, om inte de flesta, att informera om de beslut du fattar när du designar din integrering till Marketo. Marketo har många olika metoder och konfigurationsalternativ som är lämpliga för olika användningsfall, och olika latensklasser. En realtidsintegrering för att meddela en säljare om att en användare registrerar sig för en testversion kanske bara skickar in batchar av en om en uppföljning krävs. I de flesta fall krävs dock inte detta, det kan tolerera ytterligare latens och kan hanteras mer effektivt genom köer och gruppanrop.

| Godtagbar svarstid | Önskade metoder | Anteckningar |
|---|---|---|
| Låg (&lt;10s) | Synkrona API:er (grupperade eller grupperade) | Se till att ditt användningsexempel kräver detta. Om du skickar omedelbara och synkrona anrop för användning i stora volymer kan du snabbt få en daglig API-kvot och eventuellt överskrida både hastighets- och samtidighetsgränserna. |
| Medium(10-60 m) | Synkrona API:er (grupperade) | Vi rekommenderar starkt att du använder en kö med både ålder och storlek för inkommande dataintegreringar till Marketo. När någon av gränserna nås tömmer du kön och skickar din API-begäran med de samlade posterna. Detta är en stark kompromiss mellan hastighet och effektivitet som säkerställer att dina förfrågningar görs vid den gräns som krävs, samtidigt som så många poster som köns ålder tillåter grupperas. |
| Hög (>60 m) | Importera/exportera satsvis (om det stöds) | För inkommande dataintegreringar bör poster ställas i kö och skickas via Marketo Bulk API:er när de är tillgängliga. |

## Dagliga gränser

Alla API-aktiverade instanser av Marketo har en daglig allokering av minst 10 000 REST API-anrop per dag, men oftast 50 000 eller mer, och 500 MB eller mer av kapaciteten för Bulk Extract. Även om ytterligare dagskapacitet kan köpas som en del av en Marketo-prenumeration, bör du i din programdesign ta hänsyn till de gemensamma gränserna för Marketo prenumerationer.

Eftersom kapaciteten delas mellan alla API-tjänster och användare i en instans är det bästa sättet att eliminera redundanta anrop och att gruppera poster i så få anrop som möjligt. Det mest effektiva sättet att importera poster är att använda Marketo-API:er för bulkimport, som är tillgängliga för [Leads/People](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Bulk-Import-Leads/operation/importLeadUsingPOST) och [Custom Objects](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Snippets/operation/createSnippetUsingPOST). Marketo tillhandahåller också massutdrag för [leads](bulk-lead-extract.md) och [aktiviteter](bulk-activity-extract.md).

### Cachning

Resultat från följande åtgärder kan vanligtvis cachelagras på klientsidan under en dag eller mer, eftersom de ändras sällan:

- Resultat från Beskriv-åtgärder
- [Aktivitetstyper](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Activities/operation/getAllActivityTypesUsingGET)
- [Partitioner](https://developer.adobe.com/marketo-apis/api/mapi/#tag/Leads/operation/getLeadPartitionsUsingGET)

Cachelagring av vissa resurstyper, som program, e-post och mappar, är också lämpligt för vissa användningsfall, t.ex. databerikning för lead- eller aktivitetsposter.

## Hastighetsgräns

Varje Marketo-instans har en hastighetsgräns på 100 anrop per 20 sekunder, som delas av alla API-tjänster från tredje part. Om den här gränsen överskrids svarar API med en felkod på 606 som anger att hastighetsgränsen har överskridits. I allmänhet bör tredjepartsintegreringar begränsa användningen av dem till 50 anrop per 20 sekunder eller mindre för att möjliggöra en rättvis användning av tariffgränserna för flera API-integreringar och användare. Även om det kan vara lämpligt att i vissa fall mätta denna gräns är i allmänhet program som använder gruppbearbetning och målar sin genomströmning till mindre än denna gräns mer responsiva och konsekventa i sin funktion, till en liten kostnad för ökad fördröjning.

## Samtidighetsgräns

Varje Marketo-instans har en delad gräns på tio pågående REST API-anrop. Precis som den dagliga kvoten och tariffgränsen delas, så du bör inte anta att din ansökan kommer att vara den enda konsumenten av denna gräns. Marketo räknar antalet samtidiga anrop som de som behandlas och ännu inte har returnerats, så när ett anrop returneras räknas det inte längre mot gränsen för samtidiga anrop.

De flesta fall där integreringen används har ingen nytta av samtidiga anrop. Därför bör du överväga om ditt program är fördelaktigt innan du bestämmer dig för att skicka samtidiga begäranden till Marketo. Om du vill implementera samtidighet bör du begränsa antalet samtidiga begäranden till fem eller färre i den ursprungliga designen och bara öka detta efter att du har fastställt att programmet kräver mer.

## Fel

Förutom i några sällsynta fall returnerar API-begäranden HTTP-statuskoden 200. Affärslogikfelen returnerar också 200, men innehåller detaljerad information i svarsdelen. Mer information finns i [Felkoder](error-codes.md). HTTP-orsaksfrasen ska inte utvärderas eftersom den är valfri och kan ändras.
