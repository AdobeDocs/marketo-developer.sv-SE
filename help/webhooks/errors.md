---
title: Fel
feature: Webhooks
description: Felkoder för webhooks
exl-id: adce40c3-87b1-4f31-8995-eb64e8a72b55
source-git-commit: 66add4c38d0230c36d57009de985649bb67fde3e
workflow-type: tm+mt
source-wordcount: '232'
ht-degree: 0%

---

# Fel

På den här sidan visas felsvarskoder för webbhooks i Marketo.

1000 och 1001 genereras av Marketo, och 2xx till 5xx är fel som returneras från det system som Marketo webbkrok anropar.

För att Marketo ska kunna mappa tillbaka värden till ett fält måste webkroks svarskod vara av typen 2xx. Om webbockens avsikt är att ändra värden i Marketo lead-posten via svaret, måste webbtjänsten som anropas returnera 2xx, så kommer alla andra svarskoder att ignoreras för uppdatering av lead-postvärden.

| Svarskod | Beskrivning |
| --- | --- |
| 1000 | Detta indikerar att anropswebbhollösningen håller på att hållas i en gruppkampanj. Webbhooks kan bara utlösas från utlösarkampanjer. |
| 1001 | Detta anger att webbtjänsten skickade ut en tom svarstext. |

## Fånga ett webkrok-fel

Fel från Webhooks kan fångas upp och hanteras av [!UICONTROL Webhook is Called]-utlösaren:

![Webkrok anropas](assets/webhook-called.png)

* Svar - Svar är den literala svarsnyttolasten som togs emot av begäran.
* Feltyp - Detta motsvarar HTTP-statusmeddelandets orsak-fras.

Dessa kan användas för att hantera och reagera på förutsägbara fel och undantag. Beroende på vilken tjänst du integrerar med kan det vara möjligt att automatiskt återställa vissa felklasser, medan varningar kan skapas för att meddela användare om oväntade fel.
