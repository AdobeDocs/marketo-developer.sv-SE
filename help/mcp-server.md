---
title: MCP-server
description: Lär dig hur du ansluter en AI-assistent till Marketo med hjälp av MCP-servern. Konfigurera Claude Desktop, Cursor, Claude Code eller VS Code med dina Marketo-uppgifter.
hidefromtoc: true
exl-id: ab446e56-6250-4af5-b03e-162991d09a5c
source-git-commit: 85285b49ce2540542d6169cd1466ced02955b586
workflow-type: tm+mt
source-wordcount: '1278'
ht-degree: 0%

---

# [!DNL Marketo] MCP-server

Model Context Protocol (MCP) är en öppen standard som gör att AI-verktyg kan kommunicera med externa tjänster. MCP-servern [!DNL Marketo] fungerar som en brygga mellan AI-assistenten och [!DNL Marketo]. Den visar över 100 operationer för formulär, program, smarta kampanjer, leads, e-post, fragment, listor och mappar.

När AI-verktyget anropar MCP-servern, kör servern motsvarande REST API-anrop åt dig med de autentiseringsuppgifter som du anger i varje begäran. Du behöver inte installera, distribuera eller köra någon programvara på serversidan.

## Förutsättningar

- En [!DNL Marketo]-instans med REST API-åtkomst aktiverad
- Administratörsåtkomst för att skapa API-autentiseringsuppgifter i [!DNL Marketo] LaunchPoint
- Ett av följande AI-verktyg: Claude Desktop, Cursor, Claude Code (CLI) eller VS Code med GitHub Copilot
- Nätverksåtkomst till MCP-serverns URL: `https://marketo-mcp.adobe.io/mcp`

## Hämta Marketo-inloggningsuppgifter

Du behöver följande värden från din [!DNL Marketo]-instans:

- **Klient-ID**
- **Klienthemlighet**
- **Munchkin konto-ID**
- **REST API-slutpunkt**

Om du redan har dem går du till [Konfigurera ditt AI-verktyg](#configure-your-ai-tool).

### Klient-ID och klienthemlighet

1. Gå till **[!UICONTROL Admin]** > **[!UICONTROL LaunchPoint]**.
1. Klicka på API-tjänsten. Om du inte har någon väljer du **[!UICONTROL New]** > **[!UICONTROL New Service]**, väljer **[!UICONTROL Custom]** som tjänstetyp och tilldelar en dedikerad API-användare.
1. Klicka på **[!UICONTROL View Details]** och kopiera värdena **[!UICONTROL Client ID]** och **[!UICONTROL Client Secret]**.

### Munchkin konto-ID

1. Gå till **[!UICONTROL Admin]** > **[!UICONTROL Munchkin]**.
1. Kopiera **[!UICONTROL Munchkin Account ID]**. Formatet är `XXX-XXX-XXX` och matchar prefixet för din instans-URL.

### REST API-slutpunkt

1. Gå till **[!UICONTROL Admin]** > **[!UICONTROL Web Services]**.
1. Kopiera URL:en **[!UICONTROL Endpoint]** under **[!UICONTROL REST API]**. Formatet är `https://XXX-XXX-XXX.mktorest.com`.

## Konfigurera ditt AI-verktyg

Varje AI-verktyg läser MCP-serverkonfiguration från en annan plats. Hitta verktyget nedan och följ stegen för att lägga till MCP-servern [!DNL Marketo].

### Claude Desktop

Konfigurationsfilen är `claude_desktop_config.json`. Öppna den från någon av följande platser:

- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`
- **Linux**: `~/.config/Claude/claude_desktop_config.json`

Om filen redan innehåller andra MCP-servrar lägger du till posten `marketo` under `mcpServers`. I följande exempel visas det fullständiga `mcpServers`-blocket:

```json
{
  "mcpServers": {
    "marketo": {
      "type": "http",
      "url": "https://marketo-mcp.adobe.io/mcp",
      "headers": {
        "X-Marketo-Client-Id": "YOUR-CLIENT-ID",
        "X-Marketo-Client-Secret": "YOUR-CLIENT-SECRET",
        "X-Marketo-Munchkin-Id": "YOUR-MUNCHKIN-ID",
        "X-Marketo-Endpoint": "YOUR-REST-API-ENDPOINT"
      }
    }
  }
}
```

Spara filen, avsluta Claude Desktop och öppna den igen.

### Markör

Om MCP-konfigurationen för pekaren redan innehåller andra servrar lägger du till posten `marketo` under `mcpServers`. I följande exempel visas det fullständiga `mcpServers`-blocket i **[!UICONTROL Settings]** > **[!UICONTROL MCP]** eller `.cursor/mcp.json` i din projektkatalog:

```json
{
  "mcpServers": {
    "marketo": {
      "type": "http",
      "url": "https://marketo-mcp.adobe.io/mcp",
      "headers": {
        "X-Marketo-Client-Id": "YOUR-CLIENT-ID",
        "X-Marketo-Client-Secret": "YOUR-CLIENT-SECRET",
        "X-Marketo-Munchkin-Id": "YOUR-MUNCHKIN-ID",
        "X-Marketo-Endpoint": "YOUR-REST-API-ENDPOINT"
      }
    }
  }
}
```

Starta om markören.

### Claude Code (CLI)

Kör följande kommando i terminalen och ersätt dina autentiseringsuppgifter:

```bash
claude mcp add --transport http marketo \
  https://marketo-mcp.adobe.io/mcp \
  --header "X-Marketo-Client-Id: YOUR-CLIENT-ID" \
  --header "X-Marketo-Client-Secret: YOUR-CLIENT-SECRET" \
  --header "X-Marketo-Munchkin-Id: YOUR-MUNCHKIN-ID" \
  --header "X-Marketo-Endpoint: YOUR-REST-API-ENDPOINT"
```

### VS-kod med GitHub Copilot

Öppna VS-koden `settings.json` genom att trycka på **[!UICONTROL Ctrl+Shift+P]** eller **[!UICONTROL Cmd+Shift+P]** på macOS och sedan välja **[!UICONTROL Preferences: Open User Settings (JSON)]**. Lägg till följande exempel:

```json
{
  "mcp": {
    "servers": {
      "marketo": {
        "type": "http",
        "url": "https://marketo-mcp.adobe.io/mcp",
        "headers": {
          "X-Marketo-Client-Id": "YOUR-CLIENT-ID",
          "X-Marketo-Client-Secret": "YOUR-CLIENT-SECRET",
          "X-Marketo-Munchkin-Id": "YOUR-MUNCHKIN-ID",
          "X-Marketo-Endpoint": "YOUR-REST-API-ENDPOINT"
        }
      }
    }
  }
}
```

Tryck på **[!UICONTROL Ctrl+Shift+P]** (eller **[!UICONTROL Cmd+Shift+P]** på macOS), skriv **[!UICONTROL Reload Window]** och tryck på Retur.

>[!NOTE]
>
>Av säkerhetsskäl bör du använda miljövariabel interpolation i konfigurationsfiler i stället för att klistra in autentiseringsuppgifter direkt. Du kan referera till variabler med syntax som `${MARKETO_CLIENT_SECRET}` och ange dem i din miljö. Detta förhindrar att autentiseringsuppgifter lagras i oformaterad text i filer som kan implementeras för versionskontroll.

## Tillgängliga åtgärder

När du är ansluten kan du be AI-assistenten att utföra åtgärder i följande kategorier.

### Forms

Bläddra, skapa, klona och godkänn formulär. Lägg till eller ta bort fält, konfigurera regler för fältsynlighet och identifiera var formulär är inbäddade.

Exempelmeddelanden:

- &quot;Visa alla godkända formulär&quot;
- &quot;Klona formuläret Kontakta oss till mappen Q2 Campaign&quot;
- &quot;Lägg till ett företagsfält i formuläret för demonstrationsbegäran&quot;

### Smarta kampanjer

Skapa smarta kampanjer, konfigurera smarta listfilter, lägg till flödessteg och aktivera eller inaktivera kampanjer.

Exempelmeddelanden:

- &quot;Vilka smarta kampanjer är aktiva just nu?&quot;
- &quot;Skapa en ny smart kampanj med namnet Lead Scoring Update i mappen Operations&quot;
- &quot;Visa mig flödesstegen i välkomstkampanjen via e-post&quot;

### Leads och listor

Hitta leads via e-postadress, skapa eller uppdatera lead-poster och hantera statiskt listmedlemskap.

Exempelmeddelanden:

- &quot;Hitta leads med e-post jane@example.com&quot;
- &quot;Lägg till lead-ID 12345 i Q2 MQL-listan&quot;
- &quot;Skapa en ny statisk lista som heter Sommarevent-deltagare&quot;

### Program

Skapa, klona och tagga program. Bläddra bland program efter typ, kanal eller datumintervall.

Exempelmeddelanden:

- &quot;Clone the Q4 Webinar program into the 2026 Events folder&quot;
- &quot;Skapa ett nytt e-postprogram som heter Sommarförsäljning i mappen Campaigns&quot;
- &quot;Visa alla program som är taggade som webbinarier&quot;

### Mejl och textutdrag

Bläddra bland e-postmeddelanden, skapa e-postmeddelanden från mallar, uppdatera innehållsavsnitt och hantera återanvändbara fragment.

Exempelmeddelanden:

- &quot;Visa mig alla e-postutkast&quot;
- &quot;Uppdatera rubrikdelen i välkomstmeddelandet&quot;
- &quot;Vilka resurser använder Kodavsnittet för helgerbjudanden?&quot;

### Instansstruktur

Bläddra bland mappar, kanaler, taggtyper och aktivitetstyper för att förstå din [!DNL Marketo]-konfiguration.

Exempelmeddelanden:

- &quot;Lista alla mappar i Marketo&quot;
- &quot;Visa alla tillgängliga kanaler&quot;
- &quot;Vilka taggtyper har konfigurerats?&quot;

### Massåtgärder

Exportera lead-data i bulk och kontrollera import- eller exportjobbstatus.

Exempelmeddelanden:

- &quot;Skapa en bulkexport av leads som skapats de senaste 30 dagarna&quot;
- &quot;Kontrollera status för exportjobb xx&quot;

## Felsökning

| Fel | Orsak | Korrigera |
| ------- | ------- | ----- |
| &quot;Marketo-slutpunkt saknas&quot; | Rubriken `X-Marketo-Endpoint` saknas i konfigurationen. | Kontrollera MCP-konfigurationen igen och bekräfta att alla fyra rubrikerna finns. |
| &quot;Marketo-autentiseringsuppgifter har inte angetts&quot; | En eller flera av `X-Marketo-Client-Id`, `X-Marketo-Client-Secret` eller `X-Marketo-Munchkin-Id` saknas. | Kontrollera att alla fyra rubrikerna finns i konfigurationen. |
| &quot;Autentiseringsfel&quot; | Dina autentiseringsuppgifter är ogiltiga eller har upphört att gälla. | Kontrollera ditt klient-ID och klienthemlighet igen i **[!UICONTROL Admin]** > **[!UICONTROL LaunchPoint]**. |
| &quot;403 Förbjuden&quot; | Ditt Munchkin-ID finns inte på tillåtelselista på servern. | Kontakta din [!DNL Marketo] MCP-administratör för att lägga till ditt Munchkin-ID. |
| Anslutningens timeout eller nekade | Det går inte att nå MCP-servern från nätverket. | Bekräfta att du kan nå serverns URL från din miljö. Kontrollera VPN-kraven om tillämpligt. |
| Verktygsanrop returnerar tomma resultat | API-användaren saknar behörigheter för den begärda resurstypen. | Be din [!DNL Marketo]-administratör att granska API-användarrollen och behörigheter. |

## Frågor och svar

+++Är mina data säkra?
Autentiseringsuppgifter överförs i HTTP-huvuden med varje enskild begäran. Servern lagrar inte eller cachelagrar autentiseringsuppgifter mellan sessioner, och varje begäran är helt isolerad.
+++

+++Kan flera personer använda detta samtidigt?
Ja. Servern är multi-tenant. Varje användare ansluter med sina egna inloggningsuppgifter och förfrågningar isoleras från varandra.
+++

+++Vad händer om min åtkomsttoken upphör att gälla?
När du autentiserar med Klient-ID och Klienthemlighet hanterar servern automatiskt tokenuppdatering. Du behöver inte vidta några åtgärder.
+++

+++Behöver jag installera eller köra någonting?
Nej. MCP-servern hanteras av Adobe. Du behöver bara konfigurera AI-verktyget för att ansluta till det.
+++

+++Vilka [!DNL Marketo] behörigheter behöver min API-användare?
API-användaren behöver åtkomst till de resurstyper som du tänker hantera. Tilldela minst en skrivskyddad roll för bläddringsåtgärder och en skrivskyddad roll för att skapa eller ändra resurser. Använd din [!DNL Marketo]-administratör för att tilldela lämpliga behörigheter.
+++

+++Vilka är rabattgränserna?
MCP-servern ärver API-hastighetsgränserna för Marketo-instansen. Använd en dedikerad API-användare för att spåra och hantera kvotförbrukning.
+++

+++Vilka AI-verktyg stöds?
Claude Desktop, Cursor, Claude Code (CLI) och VS Code med GitHub Copilot. Alla AI-verktyg som stöder Model Context Protocol över HTTP bör fungera.
+++

+++Kan jag ansluta till flera [!DNL Marketo]-instanser?
Ja. Lägg till flera poster i AI-verktygets MCP-konfiguration, var och en med ett unikt namn och autentiseringsuppgifter för motsvarande instans. Du kan till exempel konfigurera `marketo-prod` och `marketo-staging` som separata servrar.
+++

## Säkerhetsaspekter

>[!IMPORTANT]
>
>Använd en dedikerad API-användare i [!DNL Marketo] med endast de behörigheter som krävs för ditt arbete. Återanvänd inte administratörsautentiseringsuppgifter för API-åtkomst.

- **Autentiseringsuppgifter per begäran.** Klient-ID, Klienthemlighet, Munchkin-ID och REST API-slutpunkten överförs i HTTP-huvuden vid varje begäran. Servern lagrar eller cachelagrar dem inte.
- **Multi-tenant-isolering.** Varje begäran använder sin egen uppsättning med autentiseringsuppgifter. Dina data korsar inte någon annan användares session.
- **Munchkin ID tillåtelselista.** Servern accepterar endast begäranden för godkända [!DNL Marketo] instanser. Begäranden som använder ett obehörigt Munchkin-ID avvisas med ett 403-fel.
- **Håll autentiseringsuppgifterna utanför versionskontrollen.** Använd interpolation med miljövariabel (`${MARKETO_CLIENT_SECRET}`) om AI-verktyget stöder det, så autentiseringsuppgifterna lagras inte i oformaterad text i filer som implementeras i en databas.
