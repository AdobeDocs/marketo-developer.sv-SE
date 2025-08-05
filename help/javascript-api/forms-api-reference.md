---
title: Forms API-referens
description: Forms API-referens
feature: Forms, Javascript
exl-id: 0f8d242f-0b27-4087-b080-3d41ebaa25b3
source-git-commit: 3649db037a95cfd20ff0a2c3d81a3b40d0095c39
workflow-type: tm+mt
source-wordcount: '1327'
ht-degree: 0%

---

# Forms API-referens

Det finns två huvudobjekt som du kan interagera med med Forms 2.0 API. Objektet `MktoForms2` och objektet `Form`. Objektet `MktoForms2` är det allmänt synliga namnutrymmet på den översta nivån för Forms2-funktioner och innehåller funktioner för att skapa, läsa in och hämta formulärobjekt.

## MKTOForms2-metoder

<table>
  <tbody>
    <tr valign="top">
      <td><strong>Metod</strong></td>
      <td><strong>Beskrivning</strong></td>
      <td><strong>Parametrar</strong></td>
      <td><strong>Returnerar</strong></td>
    </tr>
    <tr valign="top">
      <td>.loadForm(baseUrl, munchkinId, formId, callback)</td>
      <td>Läser in en formulärbeskrivning från Marketo-servrar och skapar ett nytt Form-objekt.</td>
      <td> baseUrl(String) - URL till Marketo-serverinstansen för prenumerationen</td>
      <td>undefined</td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>munchkinId (String) -Munchkin ID för prenumerationen</td>
      <td></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>formId (String eller Number) - Formulärversions-ID (Vid) för formuläret som ska läsas in</td>
      <td></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>callback (valfri) (Function) - en callback-funktion som skickar det konstruerade Form-objektet till när det har lästs in och initierats.</td>
      <td></td>
    </tr>
    <tr valign="top">
      <td>.lightbox(form, opts)</td>
      <td>Återger en modal dialogruta av ljuslådeformat med formulärobjektet i.</td>
      <td>form (formulärobjekt) - en instans av ett formulärobjekt som du vill ha återgivet i en ljuslåda.</td>
      <td>Ett ljuslådeobjekt med metoderna .show() och .hide().</td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>opts (optional)(Object) - Ett objekt med alternativ som skickas till ljuslådeobjektet</td>
      <td></td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>onSuccess(Function) - Ett återanrop som aktiveras när formuläret skickas.</td>
      <td></td>
    </tr>
    <tr>
          <td></td>
      <td></td>
      <td>closeBtn(Boolean) default true - Kontrollerar om en stängningsknapp (X) visas i ljuslådedialogrutan.</td>
      <td></td>
    </tr>
    <tr valign="top">
      <td>.newForm(formData, callback)</td>
      <td>Skapar ett nytt formulärobjekt från ett JS-objekt för en formulärbeskrivning. Lägger till en återanropsfunktion som anropas när alla formatmallar och känd lead-information har hämtats och formulärobjektet har skapats.</td>
      <td>formData (Form Descriptor Object) - ett formulärbeskrivningsobjekt som skapats av Marketo Forms V2 Editor</td>
      <td>undefined</td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>callback (valfri)(Function) - Det här återanropet anropas med ett argument, en ny instans av Form-objektet.</td>
      <td></td>
    </tr>
    <tr valign="top">
      <td>.getForm(formId)</td>
      <td>Hämtar ett tidigare skapat formulärobjekt per formulärets identifierare</td>
      <td> formId (Number eller String) - Vid-identifierare för formulär.</td>
      <td>Formulärobjekt</td>
    </tr>
    <tr valign="top">
      <td>.allForms()</td>
      <td>Hämtar en array med alla formulärobjekt som tidigare har skapats på sidan.</td>
      <td>n/a</td>
      <td>Array med formulärobjekt</td>
    </tr>
    <tr valign="top">
      <td>.getPageFields()</td>
      <td>Hämtar ett JS-objekt som innehåller data från URL:en och referenten som kan vara intressanta i spårningssyfte.</td>
      <td>n/a</td>
      <td>Objekt</td>
    </tr>
    <tr valign="top">
      <td>.whenReady(callback)</td>
      <td>Lägger till ett återanrop som anropas exakt en gång för varje formulär på sidan som blir"klart". Beredskap innebär att formuläret finns, har återgetts initialt och att dess initiala återanrop har anropats. Om det redan finns ett formulär som är klart när den här funktionen anropas anropas det skickade återanropet omedelbart.</td>
      <td>callback(Function) - callback-funktionen skickas som ett argument, ett formulärobjekt.</td>
      <td>MKTOForms2-objekt</td>
    </tr>
    <tr valign="top">
      <td>.onFormRender(callback)</td>
      <td>Lägger till ett återanrop som anropas varje gång något formulär på sidan återges. Forms återges när det skapas, och sedan igen varje gång som synlighetsreglerna ändrar formulärets struktur.</td>
      <td>callback (Function) - callback-funktionen skickas som ett argument, formulärobjektet för det formulär som återgavs.</td>
      <td>MKTOForms2-objekt</td>
    </tr>
    <tr valign="top">
      <td>.whenRendered(callback)</td>
      <td>Precis som onFormRender lägger detta till ett återanrop som anropas varje gång ett formulär återges. Dessutom anropas återanropet omedelbart för alla formulär som redan har återgetts.</td>
      <td>callback(Function) - återanropet skickas som ett argument, formulärobjektet för det återgivna formuläret.</td>
      <td></td>
    </tr>
</table>

## Formulärmetoder

<table>
  <tbody>
    <tr valign="top">
      <td><strong>Metod</strong></td>
      <td><strong>Beskrivning</strong></td>
      <td><strong>Parametrar</strong></td>
      <td><strong>Returnerar</strong></td>
    </tr>
    <tr valign="top">
      <td>.render(formElem)</td>
      <td>Återger ett formulärobjekt och returnerar ett jQuery-objekt som kapslar in ett formulärelement som innehåller formuläret. Om ett formElem skickas kommer det att användas som formulärelement, annars skapas ett nytt.</td>
      <td>formElem (valfritt) - Ett jQuery-objektomslutet formulärelement som ska återges.</td>
      <td> Ett jQuery-objektomslutet formulärelement som innehåller det återgivna formuläret.</td>
    </tr>
    <tr valign="top">
      <td>.getId()</td>
      <td>Hämtar formulärets ID.</td>
      <td>n/a</td>
      <td>Number - ID:t för det formulärobjekt som det här formuläret representerar</td>
    </tr>
    <tr valign="top">
      <td>.getFormElem()</td>
      <td>Hämtar det jQuery-figursatta formulärelementet för ett återgivet formulär.</td>
      <td>n/a</td>
      <td>Ett jQuery-objektomslutet formulärelement eller null om formuläret ännu inte har renderats med metoden render().</td>
    </tr>
    <tr valign="top">
      <td>.validate()</td>
      <td>Tvingar formuläret att validera, markera eventuella fel som kan finnas och returnera resultatet. Skickar inte formuläret.</td>
      <td>n/a</td>
      <td>Boolean - Returnerar true om alla validerare i formuläret skickas. Annars returneras false.</td>
    </tr>
    <tr valign="top">
      <td>.onValidate(callback)</td>
      <td>Lägger till ett återanrop för validering som anropas när valideringen utlöses.</td>
      <td>callback(Function) - ett återanrop som aktiveras när valideringen inträffar. Återanropet skickas med en parameter, ett booleskt värde som anger om valideringen lyckades.</td>
      <td>Formulärobjekt - Samma formulärobjekt som metoden anropades på, för kedjeändamål.</td>
    </tr>
    <tr valign="top">
      <td>.submit()</td>
      <td>Startar formulärets skicka-händelse. Detta startar formuläret från skicka-flödet, utför validering, aktiverar eventuella onSubmit-händelser, skickar formuläret och aktiverar eventuella onSuccess-händelser om formuläret kunde skickas.</td>
      <td>n/a</td>
      <td>Formulärobjekt - Samma formulärobjekt som metoden anropades på, för kedjeändamål.</td>
    </tr>
    <tr valign="top">
      <td>.onSubmit(callback)</td>
      <td>Lägger till ett återanrop som anropas när formuläret skickas. Detta utlöses när inlämningen börjar, innan det går att avgöra om begäran är slutförd eller inte.</td>
      <td>callback - En funktion som anropas när formuläret skickas. Det här återanropet skickas med ett argument, det här formulärobjektet.</td>
      <td>Formulärobjekt - Samma formulärobjekt som metoden anropades på, för kedjeändamål.</td>
    </tr>
    <tr valign="top">
      <td>.onSuccess(callback)</td>
      <td>Lägger till ett återanrop som anropas när formuläret har skickats men innan leadet vidarebefordras till uppföljningssidan. Kan användas för att förhindra att leadet vidarebefordras till uppföljningssidan efter att det har skickats.</td>
      <td>callback - En funktion som anropas när formuläret har skickats. Det här återanropet skickas med två argument. Ett JS-objekt som innehåller de värden som skickades och en sträng-URL för den uppföljningssida som användaren kommer att vidarebefordras till, eller null eller tom sträng om det inte finns någon konfigurerad uppföljningssida. Speciellt beteende: Om återanropet returnerar "false" (mätt med ===) vidarebefordras besökaren INTE till uppföljningssidan och sidan läses INTE in igen. Detta gör att användaren kan utföra extra bearbetning på uppföljnings-URL:en eller vidta åtgärder på en sida med JavaScript istället för att lämna sidan.</td>
      <td>Formulärobjekt - Samma formulärobjekt som metoden anropades på, för kedjeändamål.</td>
    </tr>
    <tr valign="top">
      <td>.submitTable(canSubmit) <em>är även tillgänglig som:</em> <em>.splatable(canSubmit)</em></td>
      <td>Hämtar eller anger om formuläret kan skickas. Om det anropas utan argument hämtas värdet, om det anropas med ett argument, och värdet ställs in. Detta kan användas för att förhindra att ett formulär skickas, medan andra villkor utanför det normala formuläret måste uppfyllas.</td>
      <td>canSubmit (valfritt)(Boolean) - Anger att formuläret ska kunna skickas eller inte skickas.</td>
      <td>Boolesk eller Formulärobjekt - Om anropas utan argument returneras ett booleskt värde som anger om formuläret kan skickas. Om det anropas med ett argument returneras det här formulärobjektet för kedjeändamål. </td>
    </tr>
    <tr valign="top">
      <td>.allFieldsFilled()</td>
      <td>Returnerar true om alla fält i formuläret har värden som inte är tomma.</td>
      <td>n/a</td>
      <td>Boolean - True om alla fält har värden som inte är tomma/tomma/unset/null, annars false.</td>
    </tr>
    <tr valign="top">
      <td>.setValues(vals)</td>
      <td>Anger värden för ett eller flera fält i formuläret.</td>
      <td>vals - Ett JS-objekt. För varje nyckel/värde-par i objektet ställs formulärfältet med namnet key in på value.</td>
      <td>undefined</td>
    </tr>
    <tr valign="top">
      <td>.getValues()</td>
      <td>Hämtar alla värden för alla fält i formuläret.</td>
      <td>n/a</td>
      <td>Object - Ett JS-objekt som innehåller nyckel/värde-par som representerar namnen och värdena för fälten i formuläret.</td>
    </tr>
    <tr valign="top">
      <td>.addHiddenFields(values)</td>
      <td>Lägger till indatatyp=dolda fält i formuläret.</td>
      <td>values - A JS Object containing key/value pairs representing the names and values of the hidden fields to add to the form.</td>
      <td>undefined</td>
    </tr>
    <tr valign="top">
      <td>.vals(values)</td>
      <td>jQuery style .vals() setter/getter. Om det anropas utan argument motsvarar det att anropa getValues(). Om det anropas med ett argument, motsvarar det anropande setValues()</td>
      <td>värden (valfritt) - Objekt</td>
      <td>undefined</td>
    </tr>
    <tr valign="top">
      <td>.showErrorMessage(msg, elements)</td>
      <td>Visar ett felmeddelande som pekar på ett element.</td>
      <td>msg (String of HTML) - en sträng som innehåller texten för det fel som du vill visa.</td>
            <td>Formulärobjekt - det här formulärobjektet, för kedjning.</td>
    </tr>
    <tr>
      <td></td>
      <td></td>
      <td>element (valfritt)(jQuery-objekt) - Det element som felet ska peka på. Om det inte är aktiverat används formulärets Skicka-knapp.</td>
<td></td>
    </tr>
  </tbody>
</table>
