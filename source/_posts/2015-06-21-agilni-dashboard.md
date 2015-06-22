title: Agilní dashboard 2.0
date: 2016/06/21 12:00
authorId: JCE
tags: [dashboard, continuous integration]
---
S rostoucí poptávkou po agilních metodikách se rozmohlo i využívání sofistikovanějších nástrojů pro trackování stavu projektu. S některými z nástrojů jako je Jira, Team Foundation Server, Kanbanize či Trello se již dnes setkal asi každý z oblasti vývoje software a není pochyb o tom, že svou roli sledování stavu projektu plní na výbornou. Nicméně každý projekt potřebuje i další nástroje pro týmovou komunikaci či Continuous Integration a integrace těchto nástrojů dohromady v dnešní době není… tedy až doposud!

<!-- more -->

### One Dashboard to rule them all

Výše zmíněné důvody a [veleúspěšný pilot zavedení “Dashing” ve firmě](/2014/02/01/teamovy-dashboard/) nás vedl k myšlence vytvořit interní projekt Dashboard, který by měl 3 cíle:
*     Vytvoření aplikace, která by sloužila vývojářům, projektovým manažerům a samotným zákazníkům pro centralizaci důležitých informací o projektu na jednom místě
*     Odhalovat problémy včas a transparentně komunikovat směrem k zákazníkovi
*     Vyškolení junior vývojářů v oblasti Angular JS, Express JS, Twitter Bootstrap, REST API, … .

V současné chvíli se CN Dashboard skládá ze 3 částí:

*     **Frontend** postavený na Angular JS a Twitter Bootstrap
*     **Backend** napsaný v Express JS a databáze v Mongo DB
*     **Admin** modul pro konfiguraci v Angularu a Twitter Bootstrap

Dashboard se skládá z boxů neboli tzv. „tilů“, které krom toho, že se různí svou velikostí, prezentují různé metriky integrované z různých služeb (Burndown chart, Build history, atd.. ). Komunikace s jednotlivými službami probíhá přes REST API a integrace nových služeb je tedy závislá na tom, zda je služba přes REST API dostupná.

{% img  center-block /attachments/2015-06-21-agilni-dashboard/dashboard.png 800 %}

Tříčlenný tým ve dvoutýdenních iteracích neustále vyvíjí nové metriky a integrují nové služby. Níže je uvedený seznam některých z nich:

**Služby:**

*     Team Foundation Server
*     JIRA
*     Kanbanize
*     Slack
*     Jenkins

**Metriky:**

*    Burndown chart (Sprint / Projekt)
*     Velocity
*     Build history
*     Nové vs
*     Open vs total User Stories
*     Impediment list
*     Code coverage
*     Messages (Slack/Manual)
*     Entertainment (Slack/Manual)
*     Weather

Frontend část, tedy dashboard jako takový slouží zejména pro spouštění na velkých LCD televizích, které jsou fyzicky umístěny u každého týmu tak, aby měli všichni členové týmu informace dobře na očích. Dashboard, který běží na interním serveru je samozřejmě dostupný i přes webové rozhraní a díky responzivnímu designu přizpůsoben pro náhled na mobilních zařízení. Nezáleží jestli jste vývojář, projektový manažer či obchodní zástupce firmy komunikující se zákazníkem, dashboard je přístupný každému a kdykoliv (má-li samozřejmě člověk dostatečné oprávnění).

Tento projekt je nyní v pilotní fázi a je nasazen na 2x vzorových projektech za účelem získání zpětné vazby a neustálého vylepšování. Ostrá verze aplikace, která bude dostupná nejen týmu ale i koncovému zákazníkovi, je naplánována na začátek září.


{% img /attachments/2015-06-21-agilni-dashboard/mobile.jpg  380 %}
{% img /attachments/2015-06-21-agilni-dashboard/in-use.jpg  380 %}
